TOOLS_DIR := ../hack/tools
TOOLS_BIN_DIR := $(TOOLS_DIR)/bin
KUSTOMIZE := $(abspath $(TOOLS_BIN_DIR)/kustomize)

kustomize: $(KUSTOMIZE) ## Build a local copy of kustomize.

$(KUSTOMIZE): # Build kustomize from tools folder.
	../hack/ensure-kustomize.sh

.PHONY: ocp-manifests
ocp-manifests:  $(KUSTOMIZE)
	$(KUSTOMIZE) build ../config/default > operator-components.yaml
