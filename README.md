# CrewAI-Automaton
# Générer une animation GIF d'un diagramme d'états à partir d'un fichier JSON

## 1. Générer les frames PlantUML

Utilisez le script Python pour générer un fichier `.puml` par étape (frame) :

```bash
python generate_state_animation_frames.py -i system_events.json -d frames/
```
- Cela crée un dossier `frames/` contenant des fichiers `state_diagram_frame_000.puml`, `state_diagram_frame_001.puml`, etc.

---

## 2. Générer les images PNG à partir des fichiers .puml

Utilisez PlantUML pour convertir chaque `.puml` en `.png` :

```bash
for f in frames/*.puml; do plantuml -tpng "$f"; done
```
- Vous obtiendrez des fichiers `state_diagram_frame_000.png`, `state_diagram_frame_001.png`, etc. dans le dossier `frames/`.

---

## 3. Assembler les PNG en un GIF animé

Utilisez ImageMagick (commande `convert`) pour assembler les images en un GIF animé :

```bash
convert -delay 100 -loop 0 frames/*.png state_diagram_animated.gif
```
- `-delay 100` : durée de chaque frame (en 1/100e de seconde, ici 1 seconde)
- `-loop 0` : boucle infinie

---

## 4. Résultat

- Le fichier `state_diagram_animated.gif` est votre animation finale, chaque transition active étant en **rouge**.

---

## 5. Dépendances nécessaires

- **Python 3** (pour exécuter le script)
- **PlantUML** (pour générer les PNG)
- **ImageMagick** (pour assembler les PNG en GIF, commande `convert`)

### Installation rapide (macOS) :
```bash
brew install plantuml imagemagick
```

### Installation rapide (Ubuntu/Debian) :
```bash
sudo apt-get install plantuml imagemagick
```

---

## 6. Astuces

- Pour changer la couleur de la flèche active, modifiez la variable `color = '#red' ...` dans le script Python.
- Pour ajuster la vitesse de l'animation, changez la valeur de `-delay` dans la commande `convert`.

---

## 7. Nettoyage

Pour supprimer les fichiers temporaires PNG après création du GIF :
```bash
rm frames/*.png
```

---

## 8. Générer la version animée en se basant sur le fichier des évènements

Cette partie consiste à générer le fichier plantuml pret à être utilisé pour générer le gif :

```bash
python json_to_plantuml_animated.py -i system_events.json  
```

le fichier est généré dans le dossier animated/state_diagram_animated.puml

Ensuite, on peut lancer la génération du fichier gif
```bash
animate-puml -i animated/state_diagram_animated.puml -o state_diagram_animated.gif  
```

---

## 9. Agrégation des transitions répétitives

### Fonctionnalité d'agrégation

Les deux versions (simple et animée) supportent maintenant l'agrégation des transitions répétitives. Cette fonctionnalité permet de :

- **Réduire la complexité visuelle** en regroupant les transitions identiques
- **Afficher le nombre d'occurrences** sur chaque transition agrégée
- **Améliorer la lisibilité** des diagrammes avec beaucoup de répétitions

### Utilisation

#### Version simple (`json_to_plantuml.py`) :
```bash
# Sans agrégation (comportement par défaut)
python json_to_plantuml.py
# Répondez 'n' ou appuyez sur Entrée

# Avec agrégation
python json_to_plantuml.py
# Répondez 'y' à la question d'agrégation
```

#### Version animée (`json_to_plantuml_animated.py`) :
```bash
# Sans agrégation (comportement par défaut)
python json_to_plantuml_animated.py -i system_events.json

# Avec agrégation
python json_to_plantuml_animated.py -i system_events.json -a
```

### Exemple de résultat

**Sans agrégation** : Chaque transition est affichée individuellement
```
LLM_CALL_STARTED --> LLM_CALL_COMPLETED : "role=user"
LLM_CALL_STARTED --> LLM_CALL_COMPLETED : "role=assistant"
LLM_CALL_STARTED --> LLM_CALL_COMPLETED : "role=user"
...
```

**Avec agrégation** : Les transitions répétitives sont regroupées
```
LLM_CALL_STARTED --> LLM_CALL_COMPLETED : "role=user\nx 9"
```

### Avantages

1. **Clarté visuelle** : Moins de flèches enchevêtrées
2. **Informations quantitatives** : Comptage des occurrences
3. **Performance** : Génération plus rapide pour les gros fichiers
4. **Analyse** : Identification facile des patterns répétitifs

### Cas d'usage typiques

- **Logs de développement** : Identifier les appels LLM répétitifs
- **Analyse de performance** : Voir les transitions les plus fréquentes
- **Debugging** : Simplifier la lecture des flux complexes
- **Documentation** : Créer des diagrammes plus lisibles

### Dépannage

Si vous rencontrez des erreurs :
- Assurez-vous que le fichier JSON d'entrée existe et est valide
- Vérifiez que vous avez les permissions d'écriture dans le dossier de sortie
- Pour la version simple, répondez uniquement 'y' ou 'n' (pas 'yes' ou 'True') 
