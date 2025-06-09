FROM ghcr.io/osgeo/gdal:ubuntu-small-3.11.0

WORKDIR /app

RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    pipx \
    sqlite3 && \
    apt-get -y clean && \
    rm -rf /var/lib/apt/lists/*

RUN pipx ensurepath

RUN pipx install gdal==3.11.0

CMD ["/bin/bash"]