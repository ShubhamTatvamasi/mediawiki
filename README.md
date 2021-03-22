# mediawiki

create a deployment and service:
```bash
kubectl create deployment mediawiki --image=mediawiki
kubectl expose deployment mediawiki --port=80 --name=mediawiki
```

Ingress value for mediawiki
```bash
kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mediawiki
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
      - mediawiki.k8s.shubhamtatvamasi.com
    secretName: mediawiki-tls
  rules:
  - host: mediawiki.k8s.shubhamtatvamasi.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mediawiki
            port:
              number: 80
EOF
```

copy LocalSettings.php to the pod:
```bash
kubectl exec -it deploy/mediawiki -- bash
kubectl cp LocalSettings.php mediawiki-7f464d9684-9928n:/var/www/html
```

delete everything:
```bash
kubectl delete deploy/mediawiki svc/mediawiki ing/mediawiki
```
