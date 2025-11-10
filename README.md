 1. Architecture de Déploiement

 Le projet est composé de deux parties principales

Front-end :

  - React + TypeScript
  - Dossier de build : "admin-dashboard/dist/"
  - Le build échoue actuellement à cause :
  de la version de Node (le projet demande >= v20, mais GitHub Actions utilise v18) d’erreurs TypeScript ("Binding element 'title' implicitly has an 'any' type", "'React' is declared but its value is never read")

  conclusion: Le front n’est donc pas déployé automatiquement pour le moment, mais la partie CI/CD est prête à fonctionner dès que ces erreurs seront corrigées.

Back-end :

  - Node.js + Express 
  - Déployé sur un VPS via GitHub Actions.  
  - Le déploiement se fait automatiquement à chaque push sur la branche "main".  
  - La commande "pm2 restart backend" permet de relancer l’API après le transfert des fichiers.


VPS :

  - PM2 gère les processus front et back
  - Clés SSH configurées pour un accès sécurisé depuis GitHub Actions
  - Les fichiers sont synchronisés via `rsync`



2. Secrets Utilisés

Pour le workflow CI/CD, j’ai utilisé les secrets suivants  :

- VPS_HOST : adresse IP du serveur VPS
- VPS_USER : utilisateur SSH
-  VPS_PATH : chemin de déploiement sur le VPS
- VPS_KEY : clé privée SSH pour se connecter au VPS

3. Triggers du Workflow

Le workflow se déclenche automatiquement sur push sur la branche main

- Les workflows sont déclenchés sur les pushs vers la branche "main".  
- Pour le backend : build et déploiement automatiques sur chaque push.  
- Pour le frontend : prévu pour faire la même chose, mais actuellement le build bloque.

 Le workflow vérifie si 'admin-dashboard/dist/' existe avant le déploiement pour éviter d’écraser le serveur avec un build vide.



4. Rollback

Si le déploiement échoue :Le workflow s’arrête automatiquement pour revenir à une version précédente 
Le rollback est manuel pour le moment, car il n’y a pas de versioning automatique sur le serveur pour le front-end.


5. Checklist de Validation

-  Back-end build complet et fonctionnel
-  Fichiers back-end transférés correctement sur le VPS
-  PM2 redémarre correctement le back-end
-  API back-end accessible via l’IP du VPS
-  Front-end build échoue (`admin-dashboard/dist/` n’est pas généré)
-  Aucun fichier sensible (clé privée, node_modules, dist) dans le dépôt GitHub
-  Logs PM2 propres, sans erreurs
-  Workflow GitHub Actions passe pour le back-end

