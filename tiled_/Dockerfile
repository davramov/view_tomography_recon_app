FROM continuumio/miniconda3:25.1.1-2

# Install system dependencies
RUN apt-get update && \
    apt-get install -y git curl build-essential socat && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set up conda environment for Tiled with Zarr support
RUN conda create -y -n tiled_zarr_env python=3.12 && \
    echo "source activate tiled_zarr_env" > ~/.bashrc
ENV PATH /opt/conda/envs/tiled_zarr_env/bin:$PATH

# Install Python packages
RUN pip install --upgrade starlette==0.41.2 && \
    pip install zarr==2.18.3 numcodecs==0.13.1

# Clone and install Tiled fork with Zarr support
WORKDIR /app
RUN git clone -b add-zarr-forked https://github.com/davramov/tiled.git && \
    cd tiled && \
    pip install -e ".[all]"

COPY tiled-config.yml ./config.yml

# Tell Tiled where to find it:
ENV TILED_CONFIG=./config.yml

# Create data mount point
RUN mkdir -p /data


ENV DATA_DIR="/data"
ENV TILED_PORT=8000
ENV VIEWER_PORT=8082
ENV REACT_PORT=5174
ENV NGINX_PORT=8787
ENV TILED_ALLOW_ORIGINS="http://localhost:3000 http://localhost:${NGINX_PORT} http://localhost:${REACT_PORT} http://localhost:${VIEWER_PORT} http://127.0.0.1:${REACT_PORT} http://127.0.0.1:${VIEWER_PORT}"

EXPOSE 8000

CMD ["tiled", "serve", "directory", "/data", "--public", "--host", "0.0.0.0", "--port", "8000"]
