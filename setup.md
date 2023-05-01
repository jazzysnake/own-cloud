# Setup documentation

1. buy a VPS with debian
2. secure pw upon buying vps
3. ssh root@<ip in email> and pw later
4. apt update && apt upgrade
5. apt install sudo neovim tmux docker docker-compose git
6. adduser <username>
7. visudo copy root user setting with <username>
8. su <username>
9. sudo reboot
10. curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s - # installs k3s
11. curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash # installs helm
12. helm repo add jetstack https://charts.jetstack.io # adds cert-manager repo
13. export KUBECONFIG=/etc/rancher/k3s/k3s.yaml # lets helm know about the config location
14. helm repo update # updates local cache
15. helm install \ # installs cert-manager in it's own namespace with it's CRDs
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.11.0 \
  --set installCRDs=true
creates staging cluster-issuer (single issuer for whole cluster, certs will not be namespaced)
16. kubectl apply -f letsencrypt-issuer-staging.yaml -n cert-manager
17. kubectl get clusterissuer # check if it's created correctly
set up demo app
18. curl  https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/deployment.yaml > deployment.yaml
19. curl https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/service.yaml > service.yaml
20. curl https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/ingress.yaml > ingress.yaml
21. kubectl apply -f <all 3 files> -n demo-app
22. kubectl get certificate # after applying the ingress.yaml you should see a cert
you can inspect the cert further by kubectl describe certificate <cert name>
after verifying it's there and it's valid (valid date, it's not a production cert) by visting your demo webpage's https version
and inspecting the cert by clicking in the top left where usually the lock is
you can delete the staging one, duplicate the file change the url to the prod one in the letsencrypt-issuer-staging.yaml
23. kubectl delete secret <cert name>
change the secretname and issuername to match the one you gave prod in ingress.yaml
24. kubectl apply -f ingress.yaml -n demo-app
25. kubectl get certificate # after applying the ingress.yaml you should see a cert
after a few minutes you will see the cert propagate to the demo website (open it in new tab)
26. kubectl apply -f redirect-to-https.yaml # set up http to https redirect globally
you can delete the demo app now and set up your desired services the same way
