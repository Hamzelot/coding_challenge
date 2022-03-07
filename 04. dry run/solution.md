# Wie kann man Fehler im Templating vor einem Deployment erkennen?

## Zeitaufwand 
~ 10min

## Antwort

Man kann das Deployment Manifest in einen `dry-run` einmal testen.

## Kommandos

```
kubectl apply -f .\deployment.yaml --dry-run=server -o yaml
```

## Screenshots

![Validate](../images/04_dry-run.PNG)