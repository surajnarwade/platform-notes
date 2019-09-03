Vault-Kubernetes Auth method
============================

Purpose: to put token into kubernetes pods


* Enabling Kubernetes Auth

```
vault auth enable kubernetes
```

* Configuring

```
vault write auth/kubernetes/config \
    token_reviewer_jwt="service-account-token" \
    kubernetes_host=https://192.168.99.100:8443 \
    kubernetes_ca_cert=@ca.crt 
```


* Creating role

```
vault write auth/kubernetes/role/demo \
    bound_service_account_names=vault-auth \
    bound_service_account_namespaces=default \
    policies=default \
    ttl=1h
```

Note: Use proper policy here which can have access to database stuff in vault
* Enable this

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: role-tokenreview-binding
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
- kind: ServiceAccount
  name: vault-auth
  namespace: default
```

--------------------------------------------------------------------------------

* Authenticate

```
vault write auth/kubernetes/login role=demo  jwt=<token>
```




### Reference

* https://www.vaultproject.io/docs/auth/kubernetes.html
* https://medium.com/@jackalus/vault-kubernetes-auth-and-database-secrets-engine-6551d686a12
* https://blog.kubernauts.io/managing-secrets-in-kubernetes-with-vault-by-hashicorp-f0db45cc208a
