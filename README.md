# Diffie-Hellman, RSA et ECDSA — implémentation from scratch

Projet de cryptographie (M2 Big Data) : implémentation en **Python pur**, sans bibliothèque cryptographique, de trois briques fondamentales de la cryptographie à clé publique :

1. **Échange de clés Diffie-Hellman**
2. **Chiffrement RSA**
3. **Signature ECDSA** sur la courbe elliptique `y² = x³ + 1`

Tous les paramètres sont dérivés d'un « numéro étudiant » `N` et d'une date de naissance, ce qui rend chaque exécution reproductible. Les valeurs du dépôt sont **fictives** (`N = 12345678`, `15/07/2001`).

> ⚠️ Projet pédagogique : les tailles de clés (~24 à 47 bits) sont volontairement minuscules. Ne jamais utiliser ce code pour protéger des données réelles.

## Contenu

| Fichier | Description |
|---|---|
| [`crypto_DH_RSA_ECDSA.ipynb`](crypto_DH_RSA_ECDSA.ipynb) | Notebook complet : code commenté + résultats |

## Ce qui est implémenté

### Outils arithmétiques
- Test de primalité **Miller-Rabin** (déterministe pour `n < 2^64`)
- Recherche du **premier le plus proche** d'un entier (avec règle de départage)
- **Algorithme d'Euclide étendu** et inverse modulaire
- **Exponentiation rapide** (square-and-multiply)
- Recherche d'une **racine primitive** de `(Z/pZ)*` par factorisation de `p-1`
- Racine carrée modulaire par **Tonelli-Shanks**, symbole de Legendre

### Partie 1 — Diffie-Hellman
| Étape | Résultat |
|---|---|
| Premier `p` le plus proche de `N = 12345678` | `p = 12345701` |
| Générateur `g` de `(Z/pZ)*` | `g = 2` |
| Clés privées `x = JJMM`, `y = AAAA` | `x = 1507`, `y = 2001` |
| Clés publiques `A = g^x`, `B = g^y` mod p | `A = 3829910`, `B = 5232420` |
| Secret partagé `B^x = A^y` mod p | `10303966` ✅ |

### Partie 2 — RSA
| Étape | Résultat |
|---|---|
| `p` ≈ N/2, `q` ≈ 2N | `p = 6172847`, `q = 24691357` |
| Module `n = pq` | `152415968983379` |
| Plus petit `e` premier avec φ(n) | `e = 5` |
| `d = e⁻¹ mod φ(n)` (Euclide étendu) | `d = 121932750495341` |
| Chiffrement de `M = 15072001` | `C = 1901943064234` |
| Déchiffrement (exponentiation rapide) | `15072001` ✅ |

### Partie 3 — ECDSA sur `y² = x³ + 1` (mod p)
| Étape | Résultat |
|---|---|
| Point `P` d'ordre > 3600 | `P = (3, 8816852)` |
| Ordre exact de `P` (baby-step giant-step + borne de Hasse) | `n = 6172851` |
| Clé publique `Q = sP`, `s = 15072001` | `Q = (9451938, 11041015)` |
| Signature de `M = "15/07/2001"` (SHA-256) | `(r, s) = (6083121, 4952944)` |
| Vérification / message altéré | ✅ acceptée / ✅ rejetée |

Opérations sur la courbe : addition, doublement, multiplication scalaire **double-and-add**.

> **Remarque.** La courbe `y² = x³ + 1` a toujours des points de 2- et 3-torsion : l'ordre de `P` est composé (ici `3·113·131·139`). L'ECDSA standard exige un ordre premier ; le notebook signe dans `⟨P⟩` en vérifiant que `k` et `s` sont inversibles modulo `n`.

## Lancer le projet

Le code n'utilise que la bibliothèque standard (`random`, `hashlib`, `math`).

```bash
git clone https://github.com/filou337/HR-et-RSA-.git
cd HR-et-RSA-
pip install -r requirements.txt   # installe Jupyter
jupyter notebook crypto_DH_RSA_ECDSA.ipynb
```

Le notebook fonctionne aussi sur **Google Colab** (Fichier → Ouvrir un notebook → GitHub).

Pour utiliser vos propres valeurs, modifiez `N_ETUDIANT`, `JOUR_NAISS`, `MOIS_NAISS` et `ANNEE_NAISS` dans la cellule « Paramètres utilisateur » (partie 1) : toutes les parties en dépendent.

## Auteur

**Philippe Roumbo** — M2 Big Data

## Licence

[MIT](LICENSE)
