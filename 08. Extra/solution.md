## Wie stelle ich die Applikationen hinter einen Ingress Controller und wofür werden Ingress Controller verwendet?

Der Ingress Controller ist dafür verantwortlich, Ingress Ressourcen in einem Cluster bereitzustellen. Eine Ingress Ressource ist im prinzip ein Load Balancer, der vor einer Service Ressource sitz. Der externe Travic wird über den Ingress Controller zum Service und von dort aus zu den unterschiedlichen Endpoints im Cluster geleitet. 

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kuard-ingress
spec:
  backend:
    serviceName: kuard-service
    servicePort: 80
```
Unter Kind muss aber das Cluster entsprechend konfiguriert werden 
https://kind.sigs.k8s.io/docs/user/ingress/, dann kann man einen Ingress Controller auf Nginx Basis für die Demo Anwendung erstellen.

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: kuard-service
            port:
              number: 80
```
Der Dienst ist dann unter http://127.0.0.1/ ohne `port-forwarding` aufrufbar.

## Wie würde ein alerting im Kubernetes Cluster aussehen? Welche Möglichkeiten gibt es?

Ich kenne nur die Möglichkeit in der Anwendung Prometheus Alerting einzuschalten, das bei bestimmten Grenzüberschreitungen der gesammelten Metriken alarmiert wird.

## Welche Möglichkeiten gibt es die TSDB (Time-Series-Database) eines Prometheus Servers mit Metriken zu befüllen?

1. Pull-Prinzip, Prometheus fragt die Anwendung nach Metriken ab.
1. Push-Prinzip, die Anwendung sendet Metriken nach Prometheus.

## Welche Möglichkeit gibt es schützenswerte Daten wie z.B. Secrets in einem GIT Repository abzulegen und für welche würdest du dich entscheiden?

1. Verschlüsselung des Passworts in der Datei.
1. Auslagerung des Passwortes in einer nicht verfolgten (git) Datei mit Verweis auf diese.
1. Passwortmanager  

Passwörter haben aus meiner Sicht nichts in einem Git Repository verloren, ich bevorzuge also die Variante, das Passwort aus einem Passwortmanager zu entnehmen. 

## Welche Möglichkeit gibt es schützenswerte Daten die fälschlicherweise im Klartext im GIT Repository abgelegt wurden vollständig zu entfernen?

Die Git History muss dafür verändert werden. Sollte der Commit bereits in ein remote Repository gepush worden sein, kann es zu Probleme kommen mit den Entwickler:innen die bereits den "Problem Commit" heruntergeladen haben.

Der fehlerhafte Commit kann mit `git rebase -i` (interaktiv) entfernt werden, 

```bash
git log --oneline
2a97efa (HEAD -> master) Another change
25f9f2d Remove password (useless for history)
8e92746 Plain password
a5dbbb8 inital

git rebase -i a5dbbb8
 e 8e92746 Plain password
 pick 25f9f2d Remove password (useless for history)
 pick 2a97efa Another change
# Remove plain PW in pw.txt
git add pw.txt
git commit --amend
git rebase --continue

git push --force
````

Alternativ dazu, kann der Commit mittels `git reset --soft 8e92746` verändert werden, dadurch werden aber alle jüngeren Commits in die `Staging Area` geschoben. Sollte es noch ***keinen*** jüngeren Commit geben, kann der Commit auch mit `git reset --hard 8e92746` komplett gelöscht werden. 


## Zeitaufwand 
~ 35min