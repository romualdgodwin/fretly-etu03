# Audit initial de sécurité — Fretly

Date : 28 octobre 2026

## 1. Résultats de l'audit

Outil utilisé : Trivy.

| Élément                 | Type                   | Gravité        | Risque                                    | Correction                 |
| ----------------------- | ---------------------- | -------------- | ----------------------------------------- | -------------------------- |
| python-multipart 0.0.6  | 5 CVE                  | HIGH           | Déni de service et écriture non autorisée | Mise à jour vers 0.0.30    |
| legacy/deploy_manuel.sh | Clé SSH privée         | HIGH           | Accès non autorisé au serveur             | Exclusion du nouveau dépôt |
| .env                    | Identifiants sensibles | HIGH si publié | Exposition de mots de passe               | Exclusion avec .gitignore  |

## 2. Résultat après correction

Trivy ne détecte plus de vulnérabilité HIGH ou CRITICAL dans le nouveau projet analysé.

La version de Trivy utilisée signale toutefois que certaines règles nécessitent une mise à jour.

## 3. Sécurisation Git

- Nouvel historique Git propre.
- Fichier .gitignore configuré.
- Aucun secret ajouté au nouveau dépôt.
- Fichier .env.dist avec des valeurs fictives.

## 4. Questions de sécurité

### Une clé SSH publiée sur GitHub

1. Révoquer immédiatement la clé compromise.
2. Générer et déployer une nouvelle clé.
3. Supprimer le secret de l'historique Git.
4. Vérifier les journaux d'accès.
5. Informer les personnes concernées.

Un simple commit supprimant le fichier ne suffit pas : la clé reste accessible dans l'historique.

### Limites de Trivy

Une absence de détection ne garantit pas l'absence de risque. Trivy peut manquer des secrets non reconnus, des erreurs de logique métier ou des vulnérabilités inconnues.

## 5. Annexes

- [Audit avant correction](audit-avant.txt)
- [Audit après correction](audit-apres.txt)
