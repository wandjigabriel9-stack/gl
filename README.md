# DocuHumanizer : humanisation de documentation logicielle

Groupe 15, INF4027 Génie logiciel (Université de Yaoundé 1).
**Langue : français. Lecteur cible : développeur expert.**
Règle centrale : *variation stylistique autorisée ; erreur technique interdite.*

## Lancer

```bash
pip install -r requirements.txt
streamlit run app.py                 # interface web
python cli.py data/input/guide_meteolink_exemple.md --glossaire MeteoLink   # démonstration en ligne de commande
python -m unittest discover -s tests -v                                     # 27 tests
```
Dans l'interface, le bouton « Utiliser le document d'exemple » charge un guide fictif (MeteoLink) rédigé
dans le style d'un texte généré.

## Flux
`document` -> verrouillage des éléments techniques (jetons) -> analyse -> réécriture déterministe,
journalisée -> réorganisation par tâches -> **contrôle de conformité** -> document + rapport + journal.
Si le contrôle échoue, le moteur renvoie le document **original** (mode sécurité) et le signale.

## Ce que fait le moteur (règles, voir `docs/REFERENCES.md` pour les fondements)
| Règle | Exemple |
|---|---|
| Tournure impersonnelle -> impératif direct | « Il est nécessaire de vérifier X » -> « Vérifiez X » ; « Il est essentiel de ne jamais laisser X » -> « Ne laissez jamais X » |
| Nom d'action -> verbe | « Procédez à la vérification de la version » -> « Vérifiez la version » |
| Verbe direct | « X permet de collecter, de stocker et de visualiser » -> « X collecte, stocke et visualise » |
| Phrases creuses supprimées (journalisées) | « Dans le monde d'aujourd'hui, … joue un rôle crucial … » |
| Formules simplifiées | « afin de » -> « pour », « à l'aide de » -> « avec » |
| Phrases longues coupées | coupure à « ; », « , mais », « , donc », « , ce qui », « , puis » |
| Répétition du sujet -> pronom | « La configuration … La configuration … » -> « Elle … » |
| Instructions -> étapes numérotées | suite d'au moins 3 verbes à l'impératif |

Éléments **toujours préservés** : blocs de code (même non refermés), code intégré, commandes `$ ...`,
URL, chemins (Linux, relatifs, Windows), options, versions, unités, variables d'environnement,
fonctions, nombres, termes du glossaire.

## Résultat sur l'exemple fourni
513 -> 387 mots ; formules de remplissage 3,1 -> 0 pour 100 mots ; noms d'action 4,9 -> 3,4 pour 100 mots ;
38 éléments techniques intacts ; une deuxième passe ne modifie plus rien (idempotent).

## Arborescence
```
app.py                       interface Streamlit
cli.py                       démonstration reproductible
modules/
  document_importer.py       lecture et contrôle du fichier (UTF-8, Windows-1252)
  technical_analyzer.py      détection et verrouillage des éléments techniques
  document_parser.py         découpage Markdown en segments
  linguistic_analyzer.py     indicateurs (lisibilité, diversité lexicale, remplissage...)
  lexicon.py                 listes françaises, conjugaison
  humanizer.py               règles de réécriture (cœur)
  task_reorganizer.py        réorganisation par tâches
  compliance_checker.py      contrôle de conformité
  report_generator.py        rapport avant/après et journal JSON
  diff_view.py               comparaison surlignée
tests/                       27 tests (règles, sécurité, entrées hostiles)
data/input, data/output      documents d'exemple et sorties
reports/                     rapports et journaux
docs/REFERENCES.md           références scientifiques
```

## Limites connues (à déclarer dans le chapitre)
- Règles déterministes en **français** ; pas de réécriture libre : les phrases longues sans point de
  coupure, les répétitions de mots et le vocabulaire de remplissage restant sont **signalés**, pas réécrits.
- Les études qui motivent les règles portent sur l'anglais : les listes françaises sont une adaptation
  à valider sur le corpus.
- Les indicateurs sont des estimations automatiques. Ils ne mesurent ni la compréhension ni l'exactitude,
  et ne prouvent pas l'origine d'un texte. L'évaluation par des lecteurs (J9) reste la référence.
- La dispersion des longueurs de phrase (CV) est affichée à titre indicatif et n'est pas optimisée.
- Le moteur n'est pas conçu pour contourner un détecteur d'IA, et aucune règle n'introduit de faute.
# gl
