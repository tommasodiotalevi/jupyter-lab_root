FROM jupyter/datascience-notebook:python-3.10.11

USER root
RUN apt-get update -y &&\
    apt-get install dpkg-dev cmake binutils libx11-dev libxpm-dev libxft-dev libxext-dev libssl-dev libxml2-dev graphviz -y &&\
    apt-get clean 

# Build ROOT 6.28

RUN git clone --branch latest-stable --depth=1 https://github.com/root-project/root.git root_src
RUN mkdir root_build
RUN cmake -Dgminimal=ON -Dpyroot=ON -Ddataframe=ON -Dgnuinstall=ON -Ddev=ON \
          -Dbuiltin_xrootd=ON -Dxrootd=ON -Dbuiltin_davix=ON -Ddavix=ON  \
          -Dtmva=ON -Dmlp=ON -Dtmva-pymva=ON -Dtmva-cpu=ON -Dtmva-sofie=ON \
          -Dasimage=ON -Druntime_cxxmodules=ON \
          -B root_build -S root_src
RUN cmake --build root_build -- install -j$(nproc)
# Remove artifacts
RUN rm -rf root_build root_src

# Change environment so that libraries are properly found
RUN echo /usr/local/lib/root >> /etc/ld.so.conf && ldconfig
ENV PYTHONPATH /usr/local/lib/root:$PYTHONPATH
ENV CPPYY_BACKEND_LIBRARY /usr/local/lib/root/libcppyy_backend.so
ENV CLING_STANDARD_PCH none

USER 1000
RUN pip install tensorflow==2.12.0 metakernel mplhep seaborn pydot graphviz scikit-learn qkeras matplotlib pandas sympy hls4ml[profiling]==1.1.0
RUN mkdir -p ~/.local/share/jupyter/kernels &&\ 
    cp -r /usr/local/etc/root/notebook/kernels/root ~/.local/share/jupyter/kernels

RUN pip cache purge

RUN mv /home/jovyan/work /home/jovyan/persistent-storage
CMD ["jupyter", "lab"]