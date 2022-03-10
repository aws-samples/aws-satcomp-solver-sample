################### Build Mallob
FROM satcomp-base:worker AS builder
USER root
#  Install required softwares
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt install -y cmake build-essential zlib1g-dev libopenmpi-dev wget unzip build-essential zlib1g-dev cmake python3 build-essential gfortran wget curl

# Build Mallob
RUN git clone https://github.com/domschrei/mallob
WORKDIR mallob
RUN cd lib && bash fetch_and_build_sat_solvers.sh && cd ..
RUN mkdir build
RUN cd build && cmake -DCMAKE_BUILD_TYPE=RELEASE -DMALLOB_USE_RESTRICTED=1 .. && VERBOSE=1 make && cd ..

################### Extract Mallob in run stage
FROM satcomp-base:worker AS mallob_liaison
WORKDIR /
# Copy mallob
COPY --from=builder /mallob/build/mallob mallob
COPY --from=builder /mallob/build/mallob_sat_process mallob_sat_process
COPY --chown=ecs-user /worker /competition

RUN chmod +x /competition/worker
