Lors de ce test, je vais tenter d'éxécuter le fichier classify_pipeline.py qui doit tester le classificateur dans les closed world.

Un site est positif s’il a bien été identifié, négatif si on l’a classifié dans le mauvais label<br/>
*Precision* = total des vrais positifs/nombre de sites qu’on avait id positifs (faux + vrais positifs)<br/>
*Recall* = total des vrais positifs/total des positifs (vrais positifs + faux négatifs)<br/>
*F1-Score* = moyenne harmonisée de Precision et Recall<br/>
On va utiliser une validation croisée 10-fold pour éliminer les informations biaisées dues à la superposition des expérimentations.
<br/><br/>
**Evaluation des caractéristiques n-grammes :**<br/>
o	_Closed world :_ avec le data-set LOC1 on utilise 3 réglages :
- Un adversaire qui attaque le datas-set entier de 1500 sites web
- Un qui attaque le data-set après la sélection des données de 1414 sites web
- Un qui attaque le data-set entier mais qui considère les versions de pays des sites comme équivalentes (si sur le .fr et .uk  il y a erreur commune classifiée, on considère le site comme vrai positif).
<br/>On utilise un graphe de confusion (confusion graph) qui permet une représentation intuitive des erreurs de classification permettant de visualiser les collisions entre sites.
<br/><br/>On étudie ensuite la performance du classificateur pour des sites individuels : On utilise un scatterplot où chaque point=un site web, et leur couleur représente la différence absolue entre la Precision et le Recall (bleu si |Precison – Recall|= 0, rouge si |Precision – Recall| = 1).
- Si basse précision (d’autres sites sont souvent identifiés à ce site) mais haut Recall (souvent identifié par classificateur) -> good privacy
- Si bas Recall (casiment jamais identifié) mais haute précision (si id, l’adversaire est absolument sûr que c’est correct) -> permet au censeur de bloquer sans peur de dommages collatéraux.
<br/><br/><br/>
# Début du test<br/>
Je suis sur un PC windows 10, or l'outil pour installer le package tsfresh utilisé n'est pas compatible avec windows 10.<br/>
J'ai donc fait appel à un ami ayant un Linux pour tester le code avec moi.
<br/><br/>
Problème n°1 :
Le package code/classification/utils.util.py est en python 2.7 alors que le script classify_pipeline.py est en python3
<br/>-> on lance donc la commande _2to3 -w utils/util_ dans le terminal pour le convertir en python3.
<br/><br/>
On créé un LOC0 avec les 6 1ers fichiers json du LOC1 (pour tester rapidement le fonctionnement du code). <br/>
On remplace les appels à 'loc1' (qui ne marchaient pas dans le code) par 'LOC0' dans le code et on lance dans le terminal _python3 classify_pipeline.py normal_<br/>
On obtient le résultat suivant :<br/><br/>
louis@tuf  ~/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/code/classification   master ✚ ● ?  python classify_pipeline.py normal  <br/>                            SIGINT(2) ↵  9560  11:53:20
/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/pickles/LOC0.pickle<br/>
Loading /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0<br/>
Starting to parse<br/>
/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0<br/>
Number of selected files 6<br/>
1396 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/01-11-18_1_2.json<br/>
887 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/01-11-18_2.json<br/>
1499 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/02-11-18_1.json<br/>
1396 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/01-11-18_2_2.json<br/>
385 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/02-11-18_1_2.json<br/>
888 sites in /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0/01-11-18_1.json<br/>
Empty traces: 476<br/>
Pickling to /home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/dataset/LOC0.pickle<br/>
Trim data to required number of samples and classes<br/>
Traceback (most recent call last):<br/>
  File "/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/code/classification/classify_pipeline.py", line 327, in <module><br/>
    normal_experiment()<br/>
  File "/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/code/classification/classify_pipeline.py", line 283, in normal_experiment<br/>
    df_trimmed = trim_sample_df(df, num_samples, map(str, range(num_classes)))<br/>
  File "/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/code/classification/utils/util.py", line 119, in trim_sample_df<br/>
    df = sample_classes(df, classes)<br/>
  File "/home/louis/Documents/JetBrains/PycharmProjects/doh_traffic_analysis/code/classification/utils/util.py", line 78, in sample_classes<br/>
    raise Exception("Type of classes not recognized.")<br/>
Exception: Type of classes not recognized.<br/><br/><br/>
  
 Le problème n°2 arrive alors : df_trimmed = trim_sample_df(df, num_samples, map(str, range(num_classes))) est un dataframe vide.<br/>
 Cela vient de la fonction *df_trim* qui ne renvoie que des _False_ dans le df (dataframe). Par conséquent on se retrouve avec un dataframe vide pour la cross-validation et cela créé des erreurs.
