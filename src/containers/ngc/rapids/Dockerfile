# https://ngc.nvidia.com/catalog/containers/nvidia:rapidsai:rapidsai
FROM nvcr.io/nvidia/rapidsai/rapidsai:0.19-cuda11.2-runtime-ubuntu18.04

# RAPIDS is installed using conda and we need to work from this environment
ENV CONDA_ENV rapids

# Install some extra packages to ease development
RUN source activate ${CONDA_ENV} && \
    apt-get update && \
    apt-get install -y screen unzip git vim htop font-manager && \
    rm -rf /var/lib/apt/*

# Install Jupyterlab manager
RUN source activate $CONDA_ENV && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager

# Install ipyvolume for clean HTML5 visualizations
RUN source activate ${CONDA_ENV} && \
    conda install -y -c conda-forge ipyvolume==0.5.2 && conda clean -yac * && \
    jupyter labextension install ipyvolume

# Add deck.gl Jupyer visualization tools
RUN source activate $CONDA_ENV && \
    conda install -y -c conda-forge pydeck && \
    DECKGL_SEMVER=`python -c "import pydeck; print(pydeck.frontend_semver.DECKGL_SEMVER)"` && \
    jupyter labextension install @deck.gl/jupyter-widget@$DECKGL_SEMVER

RUN source activate $CONDA_ENV && \
    conda install -y -c conda-forge ipympl==0.7.0 && \
    jupyter labextension install jupyter-matplotlib@0.9.0

# Install toc to build table of ontents in Jupyter, not available through Conda
RUN source activate ${CONDA_ENV} && \
    jupyter labextension install @jupyterlab/toc

# Install graphviz for clean graph/node/edge rendering
RUN source activate ${CONDA_ENV} && \
    conda install -c conda-forge python-graphviz=0.16 graphviz=2.47.1 && conda clean -yac *

# Install dask_kubernetes for deploying works through K8S and monitoring through Jupyter
RUN source activate ${CONDA_ENV} && \
    conda install -c conda-forge dask-kubernetes==2021.3.1 && conda clean -yac *

# Expose Jupyter and Dask ports
EXPOSE 8888
EXPOSE 8787

# /rapids contains NVIDIA & contrib tutorials and example code
WORKDIR /rapids

# Start using the built in RAPIDS conda environment
ENTRYPOINT ["/bin/sh"]
CMD ["-c", "/opt/conda/envs/${CONDA_ENV}/bin/jupyter lab  --notebook-dir=/rapids --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]
