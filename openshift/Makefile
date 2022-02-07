BIN_DIR := bin
TOOLS_DIR := tools

KUSTOMIZE := tools/bin/kustomize

$(KUSTOMIZE):
	./tools/ensure-kustomize.sh

.PHONY: ocp-manifests
ocp-manifests: $(KUSTOMIZE) ## Builds openshift specific manifests
	$(KUSTOMIZE) build ../config/default > operator-components.yaml
