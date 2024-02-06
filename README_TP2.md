# TP2 Github actions

mvn clean verify : ne marche pas (java 11 sur pc de cpe qui doit être incompatible ... j'ai le message "annot find symbol")

J'ai placé mes TPs à la racine de mon repo pour que le worflow fonctionne avec git (j'avais fait un dossiser par TP avant cela)

Après avoir corrigé plusieurs choses suite à des bug (re-récupération des sources de api-student ...) mon workflow a bien marché :

![alt text](image.png)

J'ai ensuite créé mes variables d'environnement login et mdp