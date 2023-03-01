To deny access to all secrets in a Kubernetes namespace to a specific set of users using RBAC, you can create a Role or ClusterRole that includes a rule that denies access to all secrets in the namespace. Here's an example:

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: my-namespace
  name: secret-denier
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
  # Deny access to all secrets in the namespace
  # for all users and ServiceAccounts
  # who are not explicitly granted access
  # by a binding to this Role.
  #
  # Note that "null" means "all users and ServiceAccounts
  # who are not explicitly specified."
  #
  # You can also specify specific users or ServiceAccounts
  # to deny access to by using their names instead of "null".
  #
  # For example, if you want to deny access to all secrets
  # in the namespace for the users named "jane" and "bob",
  # you can replace "null" with ["jane", "bob"].
  nonResourceURLs:
  - "/api/v1/namespaces/my-namespace/secrets"
```

In this example, the Role named "secret-denier" denies access to all secrets in the "my-namespace" namespace for all users and ServiceAccounts who are not explicitly granted access by a binding to this Role.

Then, you can create a RoleBinding that binds the "secret-denier" Role to the users or ServiceAccounts you want to deny access to:

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: deny-all-secrets
  namespace: my-namespace
subjects:
- kind: User
  name: alice
  apiGroup: rbac.authorization.k8s.io
- kind: User
  name: bob
  apiGroup: rbac.authorization.k8s.io
- kind: ServiceAccount
  name: my-serviceaccount
  namespace: my-namespace
roleRef:
  kind: Role
  name: secret-denier
  apiGroup: rbac.authorization.k8s.io
```

In this example, the RoleBinding named "deny-all-secrets" binds the "secret-denier" Role to the users named "alice" and "bob" and the ServiceAccount named "my-serviceaccount" in the "my-namespace" namespace. These users and ServiceAccounts are now denied access to all secrets in the namespace. You can add or remove users or ServiceAccounts from the RoleBinding to grant or deny access as needed.
