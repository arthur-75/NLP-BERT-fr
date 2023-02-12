WSD par fine-tuning d'un modèle *BERT
On utilise ici les données du French FrameNet "ASFALDA": dans ces données, certains mots ont été associés à un frame FrameNet

ces mots sont les "targets"
associer le bon frame à un target correspond à une tâche de WSD
modulo le fait qu'un même frame regroupe plusieurs entrées lexicales (par exemple FR_Commerce_buy => acheter.v, achat.n, acquérir.v, etc...)
dans les données, les phrases contenant plusieurs targets ont été dupliquées: on a une ligne par couple phrase + target
Les données FrameNet comprennent également l'annotation des arguments sémantiques, et leur typage au moyen d'un rôle (Buyer, Seller, Goods ...), que l'on ignorera ici.

On va construire un classifieur :

entrée = un target et sa phrase de contexte
sortie = une distribution de probas sur les différents sens
ici les sens sont des frames
on peut ou pas contraindre que les sens "permis" pour un target soient uniquement ceux vus à l'entraîînement pour ce target (pour ce lemme)
On utilisera un modèle *BERT pour obtenir une représentation contextuelle du mot target.

Mais BERT donne des vecteurs contextuels pour chaque token, un token pouvant être un sous-mot. Dans la version de base, vous utiliserez le vecteur *BERT du premier token du mot target.

Ainsi pour le target comprenions dans:

Nous comprenions bien le cours

tokenisé en :

'<s>', 'Nous</w>', 'compren', 'ions</w>', 'bien</w>', 'le</w>', 'cours</w>', '.</w>, '</s>'

vous utiliserez le vecteur caché du sous-mot "compren".

Le classifieur dans la version de base sera un réseau de neurones constitué

d'un réseau *BERT
dont on récupère le vecteur caché du 1er sous-mot du target
et une couche linéaire + softmax sur les différents frames présents dans les données d'entraînement.
Le classifieur est unique pour tous les lemmes, et peut prédire tout sens(frame) pour tout lemme target, même s'il s'agit d'un sens non vu pour ce lemme dans les données d'apprentissage.
