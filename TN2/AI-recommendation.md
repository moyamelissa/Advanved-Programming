# 📋 Correction TN2 — Workflow Git & Go (INF2007)

**Étudiant(e) :** Melissa Moya  
**Dépôt :** `moyamelissa/Advanced-Programming`, dossier `TN2/`  
**Évaluation produite par :** GitHub Copilot (assistant IA) — révision recommandée par les instructeurs avant utilisation officielle.

---

## Rubrique d'évaluation

Source : `TN2-Homework-Instructions.md`, lignes 98–103.

| Critère | Points max |
|---|---|
| Correction Git | /40 |
| Code et tests | /30 |
| Documentation | /15 |
| Rapport | /15 |
| **Total** | **/100** |

---

## CRITÈRE 1 — Correction Git (40 points)

### Éléments vérifiés

**`word-stats/history.txt`** montre l'historique complet en 8 commits :

```
4dcb9c1  Ajout des tests unitaires (couverture 100%)
1499678  Ajout de history.txt et README.md
4138585  Résolution du conflit: affichage mots + caractères
bbc7efd  Affichage mots dans main()
db6fa55  Affichage caractères dans main()
de5287d  Ajout de countChars
e41d9b3  Ajout de countWords
39849ae  Initial commit: ajout de countLines
```

Correspondance avec les exigences :

| Étape | Commit | Statut |
|---|---|---|
| Commit initial sur `main` (`countLines`) | `39849ae` | ✅ |
| Commit sur `count-words` (`countWords`) | `e41d9b3` | ✅ |
| Branche `count-chars` créée depuis `main` + `countChars` | `de5287d` | ✅ |
| Modification `main()` dans `count-chars` (affichage caractères) | `db6fa55` | ✅ |
| Modification `main()` dans `main` (affichage mots) | `bbc7efd` | ✅ |
| Commit de résolution du conflit (mots + caractères) | `4138585` | ✅ |
| Screenshots step1a.PNG → step7.PNG | tous présents | ✅ |

### Raisonnement des instructeurs

**Instructeur A (workflow Git) :**
> L'historique montre 8 commits cohérents couvrant toutes les étapes obligatoires. La séquence prouve que `count-chars` a bien été branchée depuis `main` avant la fusion de `count-words` (car `de5287d` et `e41d9b3` sont des branches parallèles reconvergeant en `4138585`). Le commit de fusion résulte d'un vrai conflit résolu manuellement.

**Instructeur B (preuves et screenshots) :**
> Les 11 screenshots fournis (step1a.PNG à step7.PNG) documentent chaque étape visuellement. `step4-merge-no-conflict.PNG` confirme la fusion fast-forward, `step5a-conflict.PNG` confirme le conflit détecté, `step5b-conflict_resolution.PNG` confirme la résolution. Le guide d'expérimentation (`TN2-Experimentation-Guide.md`) décrit la démarche avec les résultats attendus.

**Instructeur C (historique et encodage) :**
> Un bémol : `word-stats/history.txt` est enregistré en **UTF-16 LE** (généré par `git log --oneline > history.txt` sous Windows cmd.exe), ce qui le rend illisible comme texte brut (chaque caractère séparé par un espace). Ce n'est pas une erreur de workflow Git mais un artefact de l'environnement Windows. Cela démontre néanmoins une attention insuffisante à la portabilité des livrables (-1 pt).

### 🏆 Conclusion

**Score : 39 / 40**

> Toutes les étapes Git sont correctement réalisées. La déduction (-1) concerne uniquement l'encodage du fichier `history.txt` qui n'est pas lisible en UTF-8 standard.

---

## CRITÈRE 2 — Code et Tests (30 points)

### Analyse de `word-stats/main.go`

**`countLines`** (lignes 9–13) :
```go
func countLines(text string) int {
    if text == "" { return 0 }
    return len(strings.Split(text, "\n"))
}
```
✅ Correcte. La vérification `text == ""` est **nécessaire** : sans elle, `strings.Split("", "\n")` retourne `[""]` (longueur 1), donnant un résultat incorrect de 1 pour la chaîne vide.

**`countWords`** (lignes 17–21) :
```go
func countWords(text string) int {
    if text == "" { return 0 }
    return len(strings.Fields(text))
}
```
✅ Correcte. Utilise `strings.Fields` qui gère correctement les espaces multiples, tabs et sauts de ligne. La vérification `text == ""` est redondante ici (`strings.Fields("") → [] → len = 0`), mais inoffensive.

**`countChars`** (lignes 25–33) :
```go
func countChars(text string) int {
    count := 0
    for _, r := range text {
        if r != ' ' && r != '\n' && r != '\r' && r != '\t' { count++ }
    }
    return count
}
```
✅ Correcte. Itère sur les runes (Unicode), exclut espace, `\n`, `\r`, `\t`. Compatible Windows/Linux. Plus robuste que l'exemple minimal des instructions.

**`main()`** (lignes 35–39) :
```go
func main() {
    text := "Hello\nWorld\nGolang"
    fmt.Printf("Nombre de mots : %d\n", countWords(text))
    fmt.Printf("Nombre de caractères : %d\n", countChars(text))
}
```
✅ Conforme au résultat de la résolution de conflit (affichage mots + caractères), tel qu'imposé par l'étape 5.4 du guide d'expérimentation.

### Analyse de `word-stats/main_test.go` (10 tests)

| Test | Fonction testée | Vérification | Résultat |
|---|---|---|---|
| `TestMainOutput` | `main()` | Exécution sans erreur (via redirection stdout) | ✅ |
| `TestCountLines_Empty` | `countLines` | Chaîne vide → 0 | ✅ |
| `TestCountLines_TrailingNewline` | `countLines` | `"a\n"` → 2 | ✅ |
| `TestCountLines_SingleLine` | `countLines` | `"Hello"` → 1 | ✅ |
| `TestCountWords_Empty` | `countWords` | Chaîne vide → 0 | ✅ |
| `TestCountWords_MultipleSpacesAndNewlines` | `countWords` | Whitespace multiple → 3 | ✅ |
| `TestCountWords_OnlySpaces` | `countWords` | Uniquement espaces → 0 | ✅ |
| `TestCountChars_Empty` | `countChars` | Chaîne vide → 0 | ✅ |
| `TestCountChars_IgnoresSpacesAndNewlines` | `countChars` | `"H i\n"` → 2 | ✅ |
| `TestCountChars_OnlyWhitespace` | `countChars` | `" \n\r\t "` → 0 | ✅ |

**Résultat réel des tests (vérifié indépendamment) :**
```
PASS
coverage: 100.0% of statements
ok  word-stats  0.002s
```

### Raisonnement des instructeurs

**Instructeur A (implémentation Go) :**
> Les trois fonctions sont idiomatiques et correctes en Go. L'utilisation de `strings.Fields` pour `countWords` est meilleure que l'exemple minimal des instructions (qui utilisait implicitement `strings.Split(text, " ")`), car elle gère nativement les whitespace multiples. La vérification de chaîne vide dans `countLines` est essentielle et correctement motivée.

**Instructeur B (tests) :**
> 10 tests couvrent toutes les branches de code. La couverture 100% est vérifiée. `TestMainOutput` est une astuce valide pour couvrir `main()` en test unitaire. Un point de fragilité : `os.Create(os.DevNull)` (`main_test.go`, ligne 12) crée un descripteur de fichier jamais fermé — `defer` restaure `os.Stdout` mais ne ferme pas le fichier. C'est un minor resource leak, sans impact pratique pour ce projet.

**Instructeur C (cas manquants et cohérence) :**
> Les instructions demandaient explicitement le test `countLines("Hello\nWorld") → 2` (`TN2-Homework-Instructions.md`, ligne 85). Ce test exact est absent, mais `TestCountLines_TrailingNewline` (`"a\n"→2`) et `TestCountLines_SingleLine` (`"Hello"→1`) couvrent fonctionnellement les mêmes chemins. Aucun test nominal pour `countWords("Hello World Golang")→3` — mais la couverture est 100%, donc c'est un enrichissement suggéré, non une lacune critique.

### 🏆 Conclusion

**Score : 29 / 30**

> Code fonctionnel, idiomatique, bien commenté. Tests complets à 100% de couverture. Déduction (-1) pour le minor resource leak dans `TestMainOutput` (descripteur de fichier `/dev/null` non fermé, `main_test.go` ligne 12).

---

## CRITÈRE 3 — Documentation (15 points)

### `word-stats/README.md`
✅ Contient : description du projet, tableau des fonctions, structure du projet, prérequis, commandes d'exécution et de test, tableau des branches, liste des commandes Git, étapes du workflow, description de la résolution de conflit.  
✅ **Bonus** : badges CI/CD (GitHub Actions + Codecov) — va au-delà des exigences.

### `word-stats/history.txt`
⚠️ Présent et généré avec `git log --oneline`, mais encodé en **UTF-16 LE** avec BOM (artefact Windows CMD). Le contenu est déchiffrable mais n'est pas un fichier texte brut lisible en standard.

### Commentaires dans le code
✅ Chaque fonction dans `main.go` a un commentaire style GoDoc (lignes 8, 16, 24).  
✅ Chaque test dans `main_test.go` a un commentaire explicatif.

### `TN2-Experimentation-Guide.md`
✅ Guide de ~870 lignes, très détaillé, avec commandes, résultats attendus et explications des avertissements (CRLF, etc.). Va bien au-delà de ce qui est requis.

### Raisonnement des instructeurs

**Instructeur A :**
> Le README est exemplaire : structuré, complet, avec des tableaux clairs et des exemples de résultats. Les badges CI dénotent une maturité au-delà du niveau attendu du cours.

**Instructeur B :**
> Le guide d'expérimentation est remarquablement complet. Il reproduit toutes les étapes avec les sorties attendues. Cependant, le livrable `history.txt` présente un problème d'encodage. Sur d'autres systèmes (Linux/macOS), le fichier sera illisible sans conversion explicite.

**Instructeur C :**
> Les commentaires dans `main.go` et `main_test.go` sont pertinents et cohérents. Un point à améliorer : `countLines` n'est pas utilisée dans `main()` au final — le README pourrait mentionner explicitement pourquoi (résultat de la résolution de conflit) afin d'éviter toute confusion pour un lecteur externe.

### 🏆 Conclusion

**Score : 14 / 15**

> Documentation exhaustive et de qualité supérieure aux attentes. Seule déduction pour l'encodage UTF-16 de `history.txt` (-1).

---

## CRITÈRE 4 — Rapport (15 points)

### `TN2-report.md`

Sections présentes :

| Section | Contenu | Statut |
|---|---|---|
| Workflow Git | Stratégie de branches, commits atomiques, fusion fast-forward | ✅ |
| Résolution du conflit | Marqueurs de conflit, résolution sémantique, commit de finalisation | ✅ |
| Défis rencontrés | Couverture de `main()`, solution avec `os.DevNull` | ✅ |

Qualité du contenu :
- ✅ Vocabulaire technique approprié : "fast-forward", "commits atomiques", "systèmes distribués", "divergence dans le graphe des commits"
- ✅ Mise en contexte avec les concepts du cours (développement parallèle, cohérence fonctionnelle)
- ✅ Liens vers tous les fichiers livrables

Problèmes de format :
- ⚠️ Le rapport est en **Markdown (.md)**, non en **PDF** comme explicitement demandé (`TN2-Homework-Instructions.md`, ligne 91 : *"Soumettez un rapport (1 page, PDF)"*).
- ⚠️ Structure en 3 blocs de paragraphes non articulés — lisible mais moins clair qu'un format avec sous-sections ou listes à puces.

### Raisonnement des instructeurs

**Instructeur A :**
> Le contenu est solide et témoigne d'une vraie compréhension des systèmes de contrôle de version distribués. La description de la fusion fast-forward et des commits atomiques est correcte et bien contextualisée.

**Instructeur B :**
> Le format Markdown n'est pas le format PDF demandé. En milieu professionnel ou académique, ne pas respecter le format de livraison est une erreur à ne pas répéter. Le rapport en .md est néanmoins parfaitement lisible dans GitHub.

**Instructeur C :**
> La section "Défis" est honnête et techniquement pertinente (redirection de stdout pour les tests). On aurait pu mentionner également le défi de l'encodage UTF-16 du fichier `history.txt`, qui illustre un autre problème de portabilité rencontré.

### 🏆 Conclusion

**Score : 13 / 15**

> Contenu de qualité, bien rédigé, terminologie correcte. Déductions : format Markdown au lieu de PDF (-1), structure en blocs de paragraphes peu articulés (-1).

---

## 📊 Note Finale

| Critère | Max | Score |
|---|---|---|
| Correction Git | 40 | **39** |
| Code et tests | 30 | **29** |
| Documentation | 15 | **14** |
| Rapport | 15 | **13** |
| **TOTAL** | **100** | **95 / 100** |

---

## 🔧 Actions correctives pour atteindre 100%

### Git & Documentation (-1 pt chacun) — `word-stats/history.txt`

Le fichier `history.txt` est en UTF-16 LE (artefact de `cmd.exe` sous Windows). Pour corriger :

**Option 1 — PowerShell :**
```powershell
git log --oneline | Out-File -Encoding utf8 history.txt
```

**Option 2 — Git Bash :**
```bash
git log --oneline > history.txt
```

**Option 3 — VS Code :** Ouvrir `history.txt` → clic sur l'encodage en bas à droite → "Save with Encoding" → "UTF-8".

---

### Code/Tests (-1 pt) — `word-stats/main_test.go`, ligne 12

Fermer le descripteur de fichier ouvert dans `TestMainOutput` :

```go
// Avant (resource leak)
os.Stdout, _ = os.Create(os.DevNull)

// Après (corrigé)
devNull, _ := os.Create(os.DevNull)
defer devNull.Close()
os.Stdout = devNull
```

Alternative plus idiomatique — restructurer `main()` pour accepter un `io.Writer` :
```go
func run(w io.Writer) {
    text := "Hello\nWorld\nGolang"
    fmt.Fprintf(w, "Nombre de mots : %d\n", countWords(text))
    fmt.Fprintf(w, "Nombre de caractères : %d\n", countChars(text))
}
func main() { run(os.Stdout) }
```
Cela permet de tester proprement avec `run(io.Discard)` sans ouvrir de fichier.

---

### Rapport (-2 pts) — `TN2-report.md`

1. **Convertir en PDF** : export depuis VS Code (extension Markdown PDF) ou Pandoc :
   ```bash
   pandoc TN2-report.md -o TN2-report.pdf
   ```
2. **Restructurer** avec des sous-sections et des listes à puces pour améliorer la lisibilité.

---

## ✅ Points forts notables

- **100% de couverture de tests** vérifiée indépendamment — objectif dépassé (minimum requis : ≥ 80%).
- **Utilisation de `strings.Fields`** au lieu de `strings.Split(text, " ")` pour `countWords` — plus robuste, gère tous les whitespace.
- **Gestion multi-plateforme dans `countChars`** : exclusion explicite de `\r` et `\t` en plus de `\n` et ` `.
- **Transparence sur l'utilisation de l'IA** (`TN2-AI-Prompts.md`) — bonne pratique académique.
- **Badges CI/CD dans le README** — initiative dépassant les exigences du cours.
- **Guide d'expérimentation** (`TN2-Experimentation-Guide.md`) — documentation step-by-step exemplaire, incluant les avertissements Windows (CRLF), les résultats attendus et les explications pédagogiques.
