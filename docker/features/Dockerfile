FROM opnfv/functest-core

ARG BRANCH=master
ARG OPENSTACK_TAG=stable/ocata

COPY thirdparty-requirements.txt thirdparty-requirements.txt
RUN apk --no-cache add --update nodejs nodejs-npm python3 sshpass && \
    apk --no-cache add --virtual .build-deps --update \
        python-dev python3-dev build-base linux-headers libffi-dev \
        openssl-dev libjpeg-turbo-dev && \
    wget -q -O- https://git.openstack.org/cgit/openstack/requirements/plain/upper-constraints.txt?h=$OPENSTACK_TAG | \
        sed -E s/^tempest==+\(.*\)$/-e\ git+https:\\/\\/github.com\\/openstack\\/tempest@\\1#egg=tempest/ | \
        sed s/^requests===.*$/requests===2.13.0/ \
        > upper-constraints.txt && \
    pip install --no-cache-dir --src /src -cupper-constraints.txt \
        -chttps://git.opnfv.org/functest/plain/upper-constraints.txt?h=$BRANCH \
        -rthirdparty-requirements.txt && \
    python3 -m pip install --no-cache-dir --src /src -cupper-constraints.txt \
        -chttps://git.opnfv.org/functest/plain/upper-constraints.txt?h=$BRANCH \
        doctor-tests && \
    npm -g install npm@latest && \
    (cd /src/promise/source && npm install) && \
    rm -r upper-constraints.txt thirdparty-requirements.txt && \
    apk del .build-deps
COPY testcases.yaml /usr/lib/python2.7/site-packages/functest/ci/testcases.yaml
CMD ["bash","-c","prepare_env start && run_tests -t all"]
