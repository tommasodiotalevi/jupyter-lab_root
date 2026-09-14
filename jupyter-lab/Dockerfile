ARG BASE_IMAGE=quay.io/jupyter/scipy-notebook
FROM ${BASE_IMAGE} AS root-builder

USER root

RUN apt-get update \
    && apt-get install --yes --no-install-recommends \
        binutils \
        build-essential \
        cmake \
        dpkg-dev \
        git \
        graphviz \
        libssl-dev \
        libx11-dev \
        libxext-dev \
        libxft-dev \
        libxml2-dev \
        libxpm-dev \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --branch latest-stable --depth 1 \
        https://github.com/root-project/root.git /tmp/root-src \
    && cmake \
        -Dgminimal=ON \
        -Dpyroot=ON \
        -Ddataframe=ON \
        -Dgnuinstall=ON \
        -Ddev=ON \
        -Dbuiltin_xrootd=ON \
        -Dxrootd=ON \
        -Ddavix=ON \
        -Dbuiltin_gif=ON \
        -Dbuiltin_jpeg=ON \
        -Dbuiltin_lz4=ON \
        -Dbuiltin_lzma=ON \
        -Dbuiltin_zstd=ON \
        -Dssl=ON \
        -Dtmva=ON \
        -Dmlp=ON \
        -Dtmva-pymva=ON \
        -Dtmva-cpu=ON \
        -Dtmva-sofie=ON \
        -Dasimage=ON \
        -Druntime_cxxmodules=ON \
        -DCMAKE_BUILD_TYPE=Release \
        -S /tmp/root-src \
        -B /tmp/root-build \
    && cmake --build /tmp/root-build --parallel "$(nproc)" \
    && DESTDIR=/tmp/root-install cmake --install /tmp/root-build --strip


FROM ${BASE_IMAGE} AS runtime

USER root

# Install runtime libraries, not their larger development packages.
RUN apt-get update \
    && apt-get install --yes --no-install-recommends \
        graphviz \
        libx11-6 \
        libxext6 \
        libxft2 \
        libxml2 \
        libxpm4 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=root-builder /tmp/root-install/usr/local/ /usr/local/

RUN echo /usr/local/lib/root > /etc/ld.so.conf.d/root.conf \
    && ldconfig

ENV PYTHONPATH=/usr/local/lib/root:${PYTHONPATH} \
    CPPYY_BACKEND_LIBRARY=/usr/local/lib/root/libcppyy_backend.so \
    CLING_STANDARD_PCH=none

USER 1000

# Avoid persisting downloaded wheels and archives in an image layer.
RUN python -m pip install --no-cache-dir \
        metakernel \
        mplhep \
        seaborn \
        pydot \
        graphviz \
        matplotlib \
        pandas \
    && mkdir -p /home/jovyan/.local/share/jupyter/kernels \
    && cp -r /usr/local/etc/root/notebook/kernels/root \
        /home/jovyan/.local/share/jupyter/kernels/ \
    && mv /home/jovyan/work /home/jovyan/persistent-storage

CMD ["jupyter", "lab"]