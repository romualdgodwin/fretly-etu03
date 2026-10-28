# Audit initial de sécurité — Fretly

## 1. Analyse du projet

J'ai commencé par analyser le projet avec Trivy pour rechercher les vulnérabilités, les secrets et les mauvaises configurations.

Commande utilisée :

`trivy fs --scanners vuln,secret,misconfig --severity HIGH,CRITICAL .`

L'analyse a permis d'identifier plusieurs problèmes de sécurité.

**Problème 1 : Bibliothèque python-multipart vulnérable**

- Élément concerné : `requirements.txt`
- Type : 5 vulnérabilités CVE
- Gravité : HIGH
- Risque : possibilité de provoquer un déni de service ou une écriture non autorisée.
- Correction : passage de la version 0.0.6 à la version 0.0.30.

**Problème 2 : Clé SSH présente dans le projet**

- Élément concerné : `legacy/deploy_manuel.sh`
- Type : clé privée SSH
- Gravité : HIGH
- Risque : une personne pourrait récupérer la clé et tenter d'accéder à un serveur.
- Correction : exclusion du script du nouveau dépôt Git.

**Problème 3 : Fichier contenant des identifiants**

- Élément concerné : `.env`
- Type : données sensibles
- Gravité : risque élevé en cas de publication
- Risque : exposition des mots de passe et des accès à l'application.
- Correction : ajout du fichier `.env` dans `.gitignore`.

Ce dernier problème a été identifié lors de ma vérification manuelle des fichiers.

## 2. Corrections effectuées

Après avoir identifié ces problèmes, j'ai effectué plusieurs modifications :

- Mise à jour de `python-multipart` vers la version 0.0.30.
- Création d'un nouveau dossier de projet sans reprendre l'ancien historique Git.
- Exclusion du dossier `legacy`.
- Création d'un `.gitignore` pour ne pas publier les fichiers sensibles.
- Création d'un `.env.dist` avec des valeurs d'exemple.

J'ai ensuite relancé Trivy pour vérifier les corrections.

**Résultat :** aucune vulnérabilité HIGH ou CRITICAL n'a été détectée lors de cette deuxième analyse.

Cependant, Trivy a signalé que certaines règles nécessitaient une version plus récente de l'outil.

## 3. Sécurisation Git

Avant de réaliser mon premier commit, j'ai vérifié les fichiers qui allaient être ajoutés avec la commande :

`git add --dry-run .`

Cette vérification m'a permis de confirmer que le fichier `.env` et le dossier `legacy` n'étaient pas présents parmi les fichiers à ajouter au nouveau dépôt.

## 4. Questions de sécurité

### Que faire si une clé SSH est publiée sur GitHub ?

Si une clé privée est publiée sur GitHub, il faut la considérer comme compromise.

Les actions à réaliser sont les suivantes :

1. Révoquer la clé pour empêcher son utilisation.
2. Générer une nouvelle clé et remplacer l'ancienne.
3. Supprimer la clé de l'historique Git.
4. Vérifier les éventuels accès suspects au serveur.
5. Prévenir les personnes concernées.

Supprimer le fichier dans un nouveau commit ne suffit pas, car la clé reste présente dans les anciens commits.

### Pourquoi Trivy ne détecte-t-il pas tous les risques ?

Trivy permet de détecter de nombreuses vulnérabilités connues, mais il ne peut pas tout identifier.

Par exemple, un mot de passe écrit directement dans un fichier peut ne pas être reconnu comme un secret.

Il faut donc compléter les analyses automatiques par une vérification manuelle des fichiers et du code.

## 5. Annexes

- [Audit avant correction](audit-avant.txt)
- [Audit après correction](audit-apres.txt)

## Conclusion

Cet audit m'a permis d'identifier plusieurs problèmes de sécurité dans le projet initial, notamment des dépendances vulnérables et la présence de données sensibles.

Après les corrections, le nouveau projet ne présente plus de vulnérabilité HIGH ou CRITICAL détectée par Trivy lors du second scan. Il reste nécessaire de maintenir les dépendances et les outils de sécurité à jour.