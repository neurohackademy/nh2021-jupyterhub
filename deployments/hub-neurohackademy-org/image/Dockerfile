# References regarding our base image:
# - ubuntu:20.04
# - pangeo/base-image definition:       https://github.com/pangeo-data/pangeo-docker-images/blob/master/base-image
# - pangeo/pangeo-notebook definition:  https://github.com/pangeo-data/pangeo-docker-images/tree/master/pangeo-notebook
# - pangeo/pangeo-notebook tags:        https://hub.docker.com/r/pangeo/pangeo-notebook/tags
# - pangeo-notebook conda package:      https://github.com/conda-forge/pangeo-notebook-feedstock/blob/master/recipe/meta.yaml
#
FROM pangeo/pangeo-notebook:2021.06.05
ARG DEBIAN_FRONTEND=noninteractive


USER root
# We only need to install packages not listed in this file already:
# https://github.com/pangeo-data/pangeo-docker-images/blob/master/pangeo-notebook/apt.txt
#
RUN echo "Installing apt-get packages..." \
 && apt-get update > /dev/null \
 && apt-get -qq install --yes \
        git-annex \
            # https://git-annex.branchable.com/install/
        groff \
            # The aws CLI apparently relies on "groff"
            # Issue about including it in future versions of pangeo-notebook:
            # https://github.com/pangeo-data/pangeo-docker-images/issues/216
        nano \
            # A terminal file editor, vim is already made available
        micro \
            # A friendly terminal editor, even easier than nano for new users
        mc \
            # powerful terminal-based file manager, better than the one in JLab
        build-essential gfortran \
            # Regular build tools for compiling common stuff
        texlive-xetex texlive-fonts-recommended texlive-plain-generic \
            # Dependencies for nbconvert
        # network debugging
        curl \
        dnsutils \
        iputils-ping \
        # common dependencies:
        graphviz \
        ffmpeg \
        # neuro dependencies
        inkscape \
            # pycortex dependency
 && rm -rf /var/lib/apt/lists/*
USER ${NB_USER}


# We only need to install packages not listed in this file already:
# https://github.com/pangeo-data/pangeo-docker-images/blob/master/pangeo-notebook/packages.txt
#
RUN echo "Installing conda packages..." \
 && mamba install -n ${CONDA_ENV} -y \
        #
        # visualization:
        altair \
        bqplot \
        plotly \
        seaborn \
        ipycanvas \
        ipympl \
        jupyter_bokeh \
        jupyterlab-geojson \
        #
        # tests and formatting:
        black \
        flake8 \
        pep8 \
        pyflakes \
        pylint \
        pytest \
        pytest-cov \
        #
        # documentation:
        jupyter-book \
        jupytext \
        numpydoc \
        sphinx \
        #
        # data:
        ipydatagrid \
        ipyparallel \
        lxml \
        pytables \
        statsmodels \
        xlrd \
        jupyter-repo2docker \
        # jupyterlab-drawio \
            # DISABLED: One can't save work so it could be a disservice to have
            #           it enabled: https://github.com/QuantStack/jupyterlab-drawio/issues/6
        jupyterlab-link-share \
            # ref: https://github.com/jupyterlab-contrib/jupyterlab-link-share
        jupyterlab-git \
        jupyterlab-system-monitor \
        jupyterlab-variableinspector \
        nbdime \
        retrolab \
        #
        # other
        cython \
        fortran-magic \
        google-cloud-sdk \
        nodejs \
        sympy \
 && echo "Installing conda packages complete!"


# We use a conda first approach in this Dockerfile, so only install pip packages
# if you have a clear reason to not use conda.
# https://github.com/pangeo-data/pangeo-docker-images/blob/master/pangeo-notebook/packages.txt
#
RUN echo "Installing pip packages..." \
 && export PATH=${NB_PYTHON_PREFIX}/bin:${PATH} \
 && pip install --no-cache-dir \
        jupyterlab==3.1.0b1 \
            # ref: https://github.com/jupyterlab/jupyterlab
        plotly-geo \
            # NOTE: This package is not available in conda (conda-forge or
            #       plotly), even though they describe it to be.
            # ref: https://github.com/plotly/plotly.py#extended-geo-support
        #
        # neurohackademy 2020 - note that this list was longer but entries
        # already installed were removed
        datalad \
        dipy \
        git+https://github.com/consideRatio/pycortex.git@pep518 \
        git+https://github.com/nipype/pydra.git@50051730c4bf894ce9ba4833f8c267b5993e0ce0 \
        git+https://github.com/ReproNim/neurodocker.git@e64f58cc2d93620e6537d9efb0104cc87a99e5ca \
        indexed-gzip \
        ipyvolume==0.6.0a8 \
        line_profiler \
        memory_profiler \
        mne \
        neuropythy \
        nibabel \
        nimare==0.0.10rc1 \
        nilearn \
        nipype \
        nitime \
        nitransforms \
        pybids \
        pydra-ml \
        pyld \
        rise \
        sklearn \
        tables \
        templateflow \
        testkraken \
        transforms3d \
        pytri \
        intern \
        torch \
        torchvision \
&& echo "Installing pip packages complete!"


# We install this explicitly for ipyvolume currently, see:
# https://github.com/maartenbreddels/ipyvolume#for-jupyter-lab-users
#
RUN echo "Installing jupyterlab extensions..." \
 && export PATH=${NB_PYTHON_PREFIX}/bin:${PATH} \
 && jupyter labextension install -y --clean \
        ipyvolume \
        jupyter-threejs \
 && echo "Installing jupyterlab extensions complete!"


# Configure conda/mamba to create new environments within the home folder by
# default. This allows the environments to remain in between restarts of the
# container if only the home folder is persisted.
#
RUN conda config --system --prepend envs_dirs '~/.conda/envs'

# Set up micro as the default EDITOR (git, etc). Advanced users will reconfigure
# this to vim/emacs/etc, but this will ensure that less unix-experienced ones
# have a good first experience
#
ENV EDITOR=micro
ENV VISUAL=micro
