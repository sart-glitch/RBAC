create an iam user with the below listed:

inlinepolicy
------------
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "eks:DescribeNodegroup",
                "eks:ListNodegroups",
                "eks:DescribeCluster",
                "eks:ListClusters",
                "eks:AccessKubernetesApi",
                "ssm:GetParameter",
                "eks:ListUpdates",
                "eks:ListFargateProfiles"
            ],
            "Resource": "*"
        }
    ]
}

Then do aws configure

create role or clusterrole
create rolebinding or clusterrolebindig
change the aws-auth config map as shown below:

----------
role.yaml 
-----------
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer-user-role
  namespace: dev
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]


rolebinding.yaml
-------------------
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-user-role-binding
  namespace: dev
subjects:
- kind: User
  name: developer-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-user-role
  apiGroup: rbac.authorization.k8s.io

kubectl edit configmap aws-auth -n kube-system
----------------------------------------------

  data:
  mapUsers: |
    - userarn: arn:aws:iam::12345678:user/developer-user
      username: developer-user
      groups:
      - developer-user-role
