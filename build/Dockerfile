FROM quay.io/operator-framework/helm-operator:v0.15.1

# Required OpenShift Labels
LABEL name="Sonatype Nexus Repository Manager Operator" \
      vendor="Sonatype" \
      version="3.20.1-01" \
      release="1" \
      summary="The Nexus Repository Manager server \
                        with universal support for popular component formats." \
      description="The Nexus Repository Manager server \
                        with universal support for popular component formats."

# Required Licenses
COPY licenses /licenses

COPY watches.yaml ${HOME}/watches.yaml
COPY helm-charts/ ${HOME}/helm-charts/
