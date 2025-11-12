# Calculette de Rentabilité Locative

Une calculette web simple et élégante pour estimer la rentabilité d'un investissement immobilier locatif.

## 🚀 Fonctionnalités

- ✅ Calcul de la rentabilité brute locative
- 💰 Calcul des mensualités de prêt
- 📊 Coût total du crédit
- 🎨 Interface moderne et responsive
- 📱 Progressive Web App (PWA) - Installable sur mobile
- 🌐 Fonctionne hors ligne

## 🛠️ Technologies

- HTML5
- CSS3 (avec gradients et responsive design)
- JavaScript vanilla (pas de framework)
- PWA (Service Worker + Manifest)

## 📱 Installation sur Mobile

1. Ouvrez l'app dans Chrome ou Firefox sur votre téléphone
2. Cliquez sur "Installer" ou "Ajouter à l'écran d'accueil"
3. L'application apparaît comme une app native
4. Utilisez-la même hors ligne !

## 💻 Utilisation Locale

Ouvrez simplement `calculette-rentabilite.html` dans votre navigateur.

Pour tester en tant que PWA localement :
```bash
python3 -m http.server 8000
```
Puis accédez à `http://localhost:8000/calculette-rentabilite.html`

## 📊 Calculs

**Rentabilité brute** = (Loyer mensuel × 12 / Coût total du projet) × 100

**Mensualités** = Formule d'amortissement standard

**Coût du crédit** = (Mensualité × Nombre de mois) - Montant emprunté

## 📝 Note

Une rentabilité brute de 6% est généralement considérée comme correcte en France.

## 🎨 Couleurs

- Vert : ≥6% (bonne rentabilité)
- Orange : 4-6% (rentabilité modérée)
- Rouge : <4% (rentabilité faible)

## 📄 Licence

Libre d'utilisation

---

Créé avec Claude Code
