FROM --platform=linux/amd64 opensuse/leap:15.3

ARG SLURM_VERSION=22.05.6
ARG SLURM_ROOT=/opt/slurm-${SLURM_VERSION}
ARG SLURM_CONFDIR=${SLURM_ROOT}/etc

ENV SLURM_VERSION   ${SLURM_VERSION}
ENV SLURM_ROOT      ${SLURM_ROOT}
ENV SLURM_CONFDIR   ${SLURM_CONFDIR}


RUN zypper install -y       \
    munge                   \
    munge-devel             \
    libnuma1                \
    libnuma-devel           \
    librrd8                 \
    readline-devel          \
    hwloc                   \
    hwloc-devel             \
    hdf5                    \
    hdf5-devel              \
    lz4                     \
    liblz4-devel            \
    libz1                   \
    zlib-devel              \
    freeipmi                \
    freeipmi-devel          \
    dbus-1                  \
    dbus-1-devel            \
    make                    \
    gcc                     \
    gcc-c++                 \
    curl                    \
    tar                     \
    bzip2                   \
    python3                 \
    vim                     \
    ca-certificates         \
    less                    \
    mpich                   \
    mpich-devel             \
    sudo \
    openssh \ 
    openssh-server

RUN zypper install -y       \
    lua53                   \
    lua53-devel             \
    libmount-devel

RUN useradd -M slurm

# Iman
RUN groupadd -g 1001 group1 && \
    groupadd -g 1002 group2 && \
    groupadd -g 1003 group3 && \
    groupadd -g 1004 group4 && \
    groupadd -g 1005 group5 && \
    useradd -m -g group1 -u 1001 user1 && \
    useradd -m -g group1 -u 1002 user2 && \
    useradd -m -g group2 -u 1003 user3 && \
    useradd -m -g group2 -u 1004 user4 && \
    useradd -m -g group3 -u 1005 user5 && \
    useradd -m -g group3 -u 1006 user6 && \
    useradd -m -g group3 -u 1007 user7 && \
    useradd -m -g group4 -u 1008 user8 && \
    useradd -m -g group5 -u 1009 user9 && \
    useradd -m -g group5 -u 1010 user10

RUN echo "root:root" | chpasswd && \
    echo "user1:password" | chpasswd && \
    echo "user2:password" | chpasswd && \
    echo "user3:password" | chpasswd && \
    echo "user4:password" | chpasswd && \
    echo "user5:password" | chpasswd && \
    echo "user6:password" | chpasswd && \
    echo "user7:password" | chpasswd && \
    echo "user8:password" | chpasswd && \
    echo "user9:password" | chpasswd && \
    echo "user10:password" | chpasswd
# Iman-end

RUN mkdir -p /var/log/slurm
RUN mkdir -p /var/spool/slurmctld && chown slurm /var/spool/slurmctld && chmod u+rwx /var/spool/slurmctld
RUN mkdir -p /var/spool/slurmd    && chown slurm /var/spool/slurmd    && chmod u+rwx /var/spool/slurmd

RUN mkdir /var/run/sshd && \
    echo 'root:root' | chpasswd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    echo "PermitTunnel yes" >> /etc/ssh/sshd_config && \
    echo "AllowAgentForwarding yes" >> /etc/ssh/sshd_config && \
    echo "AllowTcpForwarding yes" >> /etc/ssh/sshd_config

COPY install_slurm.sh .

RUN  ./install_slurm.sh ${SLURM_VERSION} ${SLURM_ROOT} ${SLURM_CONFDIR} --enable-multiple-slurmd

RUN  mkdir -p       ${SLURM_CONFDIR}
COPY cgroup.conf    ${SLURM_CONFDIR}
COPY slurm.conf.in  ${SLURM_CONFDIR}

COPY entrypoint.sh .
ENTRYPOINT ["./entrypoint.sh"]
CMD ["bash"]

#COPY run_slurm_examples example.job mpi_example.job plugin.cpp mpi_hello.c .