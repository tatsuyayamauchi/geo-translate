# geo_translate with GDAL

GDAL を使ってデータの変換処理を行うことができます。サンプルコードを記載。

## GeoTiff から MTBiles への変換

```bash
make build
make shell

root@bb9bb6b66c39:/app# gdal_translate -of MBTiles input/file.tif output/file.tif.mbtiles
```
