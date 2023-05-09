## Modification de wazo-auth
1. Copier le patch sur le serveur
```
cd
wget "https://raw.githubusercontent.com/pc-m/wazo-auth-split/main/wazo-auth-split-23.06.patch"
```
2. Appliquer le patch
```
patch -p1 -d /usr/lib/python3/dist-packages < ~/wazo-auth-split-23.06.patch
```
3. Redémarrer wazo-auth
```
systemctl restart wazo-auth
```
4. Vérifier que wazo-auth fonctionne bien
```
systemctl status wazo-auth
```
La commande suivante doit afficher un UUID
```
wazo-auth-cli token create
```

## Configurer la deuxième instance de wazo-auth
5. Copier la configuration sur le serveur
```
cd
wget "https://github.com/pc-m/wazo-auth-split/raw/main/wazo-auth-token.tar"
```
6. Extraire le tar à la racine
```
tar -C / -xvf wazo-auth-token.tar
```
7. Copier les fichiers de config wazo-auth vers auth-token
```
cd /etc/wazo-auth-token/conf.d/
cp /etc/wazo-auth/conf.d/*.yml .
```
8. Activer wazo-auth-token
```
systemctl enable wazo-auth-token
```
9. Démarrer wazo-auth-token
```
systemctl start wazo-auth-token
```

## Router les `POST /api/auth/0.1/token` vers le deuxième wazo-auth
10. Activer le reverse proxy vers wazo-auth-token
```
cd /etc/nginx/locations/https-enabled
ln -s ../https-available/wazo-auth-split .
rm wazo-auth
```
11. Vérifier la config nginx
```
nginx -t
```
12. rechargé la config nginx
```
systemctl reload nginx
```

## Retour en arrière
1. Déactiver le routage vers le deuxième wazo-auth
```
cd /etc/nginx/locations/https-enabled
ln -s ../https-available/wazo-auth .
rm wazo-auth-split
```
2. Vérifier la configuration Nginx
```
nginx -t
```
3. Recharger Nginx
```
systemctl reload nginx
```
