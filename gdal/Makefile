DOCKER_IMAGE = geo-translate-gdal
DOCKER_TAG = 0.0.1
CONTAINER_NAME = geo-translate-gdal

INPUT_DIR = $(PWD)/input
OUTPUT_DIR = $(PWD)/output
INPUT_FILE ?= input.tif
OUTPUT_FILE ?= $(INPUT_FILE).mbtiles
ZOOM_LEVELS ?= 1-10

.PHONY: $(shell egrep -o '^(\._)?[a-z_-]+:' $(MAKEFILE_LIST) | sed 's/://')

help:
	@echo "使用可能なコマンド:"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) \
		| sort \
		| awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-15s\033[0m %s\n", $$1, $$2}'
	@echo ""
	@echo "使用例:"
	@echo "  make build"
	@echo "  make run INPUT_FILE=sample.tif OUTPUT_FILE=sample.mbtiles"
	@echo "  make run INPUT_FILE=sample.tif OUTPUT_FILE=sample.mbtiles ZOOM_LEVELS=1-12"

build: ## Dockerイメージをビルド
	@echo "Dockerイメージをビルド中..."
	docker build -t $(DOCKER_IMAGE):$(DOCKER_TAG) .
	@echo "ビルド完了: $(DOCKER_IMAGE):$(DOCKER_TAG)"

setup: ## 必要なディレクトリの作成
	@echo "ディレクトリを作成中..."
	mkdir -p $(INPUT_DIR) $(OUTPUT_DIR)
	@echo "作成完了: $(INPUT_DIR), $(OUTPUT_DIR)"


run: setup ## 変換処理を実行
	@if [ ! -f "$(INPUT_DIR)/$(INPUT_FILE)" ]; then \
		echo "エラー: 入力ファイル $(INPUT_DIR)/$(INPUT_FILE) が見つかりません"; \
		exit 1; \
	fi
	@echo "変換処理を開始..."
	@echo "入力: $(INPUT_DIR)/$(INPUT_FILE)"
	@echo "出力: $(OUTPUT_DIR)/$(OUTPUT_FILE)"
	@echo "ズームレベル: $(ZOOM_LEVELS)"
	docker run --rm \
		--name $(CONTAINER_NAME) \
		-v $(INPUT_DIR):/app/input:ro \
		-v $(OUTPUT_DIR):/app/output \
		$(DOCKER_IMAGE):$(DOCKER_TAG) \
		python3 ./scripts/convert.py \
		/app/input/$(INPUT_FILE) \
		/app/output/$(OUTPUT_FILE) \
		--zoom $(ZOOM_LEVELS)

shell: setup ## コンテナ内でシェルを起動
	docker run --rm -it \
		--name $(CONTAINER_NAME)-shell \
		-v $(INPUT_DIR):/app/input \
		-v $(OUTPUT_DIR):/app/output \
		$(DOCKER_IMAGE):$(DOCKER_TAG) \
		/bin/bash

test: ## テスト実行
	@echo "テスト用のサンプルファイルを確認..."
	@if [ ! -f "$(INPUT_DIR)/test.tif" ]; then \
		echo "テスト用ファイル test.tif が $(INPUT_DIR) に見つかりません"; \
		echo "テスト用のGeoTIFFファイルを配置してください"; \
		exit 1; \
	fi
	$(MAKE) run INPUT_FILE=test.tif OUTPUT_FILE=test.mbtiles ZOOM_LEVELS=1-8

clean: ## クリーンアップ
	@echo "Dockerリソースをクリーンアップ中..."
	-docker stop $(CONTAINER_NAME) 2>/dev/null || true
	-docker rm $(CONTAINER_NAME) 2>/dev/null || true
	-docker rmi $(DOCKER_IMAGE):$(DOCKER_TAG) 2>/dev/null || true
	rm -rf output/*
	touch output/.keep
	@echo "クリーンアップ完了"

clean-output: ## 出力ディレクトリのクリーンアップ
	@echo "出力ディレクトリをクリーンアップ中..."
	rm -rf $(OUTPUT_DIR)/*
	@echo "出力ディレクトリのクリーンアップ完了"

logs: ## 開発用: ログを表示
	@docker logs $(CONTAINER_NAME) 2>/dev/null || echo "コンテナが実行されていません"

image-info: ## イメージサイズを確認
	@echo "Dockerイメージ情報:"
	@docker images $(DOCKER_IMAGE):$(DOCKER_TAG)