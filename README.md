# Repositório de Manifestos (GitOps) - Projeto CI/CD

Este repositório armazena os manifestos Kubernetes para a "API de Time Pokémon". Ele atua como a **Fonte Única da Verdade** para o *pipeline* de GitOps.

Este repositório é gerenciado por dois sistemas principais:
1.  **GitHub Actions (CI):** Atualiza automaticamente as *tags* de imagem no `k8s/kustomization.yml` a cada novo *build* bem-sucedido.
2.  **ArgoCD (CD):** Monitora este repositório e aplica (sincroniza) automaticamente qualquer mudança no cluster Kubernetes.

---

## 🔁 Fluxo de GitOps

1.  Um `push` no repositório da aplicação dispara o pipeline de CI.
2.  Após `lint`, `test` e `build`, o *job* `update-gitops-manifest` clona este repositório.
3.  O *job* usa `kustomize edit set image` para atualizar a `newTag` no arquivo `k8s/kustomization.yml`.
4.  O *job* faz `push` do *commit* de volta para este repositório (com a mensagem "Atualizado a tag da imagem...").
5.  O ArgoCD, que está monitorando este repositório, detecta o novo *commit*.
6.  O ArgoCD aplica automaticamente as mudanças no cluster, realizando o *rollout* da nova versão da aplicação.

---

## 📁 Estrutura do Repositório

* `/k8s/`: Contém os manifestos da aplicação (base + kustomization) que serão implantados.
    * `api-manifest.yml`: O `Deployment` e `Service` base do Kubernetes.
    * `kustomization.yml`: O arquivo do Kustomize que define qual *tag* de imagem deve ser usada. **Este é o arquivo atualizado pelo CI.**
* `/apps/`: Contém a definição da aplicação ArgoCD.
    * `app-cicd.yml`: O manifesto que diz ao ArgoCD para monitorar a pasta `/k8s/`.

---

## 🔗 Repositório da Aplicação

O código-fonte da API (Python/FastAPI) e a definição do *pipeline* de CI (GitHub Actions) podem ser encontrados no repositório principal:

**[https://github.com/devbrunofernandes/cicd-actions-pb](https://github.com/devbrunofernandes/cicd-actions-pb)**