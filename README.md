<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FZ Calculatrice</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
  <style>
    /* Styles généraux du corps */
    body {
      font-family: 'Roboto', Arial, sans-serif; /* Police améliorée */
      background: #f0f2f5; /* Couleur de fond plus douce */
      margin: 0;
      padding: 20px; /* Augmentation du padding */
      box-sizing: border-box;
      display: flex; /* Utilisation de flexbox pour centrer le contenu */
      flex-direction: column;
      align-items: center; /* Centrage horizontal */
      min-height: 100vh; /* Prend toute la hauteur de la fenêtre */
    }

    /* Conteneur principal de la calculatrice */
    .calculator-container {
      background: #ffffff; /* Fond blanc pour la calculatrice */
      border-radius: 15px; /* Bords plus arrondis */
      box-shadow: 0 8px 20px rgba(0,0,0,0.1); /* Ombre plus prononcée */
      padding: 30px;
      width: 100%;
      max-width: 500px; /* Largeur maximale pour la calculatrice */
      box-sizing: border-box;
    }

    h1, h2 {
      text-align: center;
      margin: 15px 0 20px; /* Marges ajustées */
      color: #333; /* Couleur de texte plus foncée */
    }

    /* Zone d'affichage des opérations/résultats */
    #operation {
      background: #eef1f5; /* Fond légèrement grisé pour l'affichage */
      padding: 15px;
      border-radius: 10px;
      box-shadow: inset 0 0 8px rgba(0,0,0,0.05); /* Ombre interne discrète */
      margin-bottom: 25px; /* Marge augmentée */
      font-size: 1.2em; /* Taille de police légèrement plus grande */
      text-align: center;
      min-height: 60px; /* Hauteur minimale pour l'affichage */
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      color: #444;
      line-height: 1.5; /* Espacement de ligne */
    }

    /* Style des conteneurs de boutons */
    .buttons {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(65px, 1fr)); /* Taille min/max ajustée */
      gap: 10px; /* Espacement des boutons */
      margin-bottom: 20px;
    }

    /* Style des boutons généraux */
    button {
      padding: 15px; /* Padding augmenté */
      font-size: 1.1em; /* Taille de police plus grande */
      background-color: #4285f4; /* Bleu Google */
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      transition: background-color 0.2s ease, transform 0.1s ease; /* Transition pour le hover/active */
      box-shadow: 0 4px 8px rgba(0,0,0,0.1); /* Ombre pour les boutons */
    }

    button:hover {
      background-color: #357ae8; /* Bleu plus foncé au survol */
      transform: translateY(-2px); /* Léger soulèvement au survol */
    }
    button:active {
      transform: translateY(0); /* Retour à la position normale au clic */
      box-shadow: 0 2px 4px rgba(0,0,0,0.1); /* Ombre réduite au clic */
    }

    /* Styles spécifiques pour les boutons Voix et Réinitialiser */
    .mic {
      background-color: #ea4335; /* Rouge Google */
    }
    .mic:hover {
      background-color: #d13225;
    }

    .reset {
      background-color: #fbbc05; /* Jaune Google */
      color: #333; /* Texte plus foncé pour le jaune */
    }
    .reset:hover {
      background-color: #e0ac03;
    }

    /* Styles pour les "chips" de notes */
    .note-chip {
      display: inline-block;
      margin: 5px; /* Marge légèrement augmentée */
      padding: 10px 15px;
      background-color: #34a853; /* Vert Google */
      color: white;
      border-radius: 25px; /* Plus arrondi pour un look de "chip" */
      cursor: pointer;
      font-weight: bold;
      font-size: 1em;
      transition: background-color 0.2s ease, transform 0.1s ease;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    .note-chip:hover {
      background-color: #2c8e47;
      transform: translateY(-1px);
    }
    .note-chip:active {
      transform: translateY(0);
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
    }

    hr {
        border: none;
        border-top: 1px solid #ccc;
        margin: 15px 0;
    }

    /* Styles pour les messages de résultats */
    .result-line {
        margin: 5px 0;
        font-weight: bold;
        color: #333;
    }
    .average-line {
        color: #007bff; /* Couleur distincte pour la moyenne finale */
        font-size: 1.3em;
    }

    /* Responsive adjustments */
    @media (max-width: 600px) {
        body {
            padding: 10px;
        }
        .calculator-container {
            padding: 20px;
        }
        button {
            padding: 12px;
            font-size: 1em;
        }
        .note-chip {
            padding: 8px 12px;
            font-size: 0.9em;
        }
        #operation {
            font-size: 1.1em;
            min-height: 50px;
        }
    }
  </style>
</head>
<body>
  <div class="calculator-container">
    <h1>FZ Calculatrice</h1>
    <div id="operation">Aucune note ajoutée</div>

    <h2>Ajouter une note</h2>
    <div class="buttons" id="note-buttons"></div>

    <h2>Choisir le coefficient</h2>
    <div class="buttons" id="coef-buttons"></div>

    <div class="buttons">
      <button class="mic" onclick="startVoice()">🎤 Voix</button>
      <button class="reset" onclick="resetAll()">🔄 Réinitialiser</button>
    </div>
  </div>

  <script>
    const noteButtonsDiv = document.getElementById('note-buttons');
    const coefButtonsDiv = document.getElementById('coef-buttons');
    const operationDiv = document.getElementById('operation');

    let notes = [];
    let selectedCoef = null;

    // --- Génération des boutons de notes (0 à 8 par pas de 0.25) ---
    // Boucle de 0 à 5 par pas de 0.25
    for (let n = 0; n <= 5; n += 0.25) {
      const btn = document.createElement('button');
      // toFixed(2) pour s'assurer d'avoir 2 décimales pour .25, et parseFloat pour éviter les problèmes de virgule flottante
      const noteValue = parseFloat(n.toFixed(2));
      btn.textContent = noteValue.toLocaleString('fr-FR'); // Formatage pour la France (virgule décimale)
      btn.onclick = () => {
        notes.push(noteValue);
        updateOperation();
      };
      noteButtonsDiv.appendChild(btn);
    }
    // Ajout des notes 6, 7 et 8
    for (let n = 6; n <= 8; n++) {
      const btn = document.createElement('button');
      btn.textContent = n.toLocaleString('fr-FR');
      btn.onclick = () => {
        notes.push(n);
        updateOperation();
      };
      noteButtonsDiv.appendChild(btn);
    }

    // --- Génération des coefficients (1 à 8) ---
    for (let c = 1; c <= 8; c++) {
      const btn = document.createElement('button');
      btn.textContent = c.toLocaleString('fr-FR');
      btn.onclick = () => {
        selectedCoef = c;
        updateOperation();
      };
      coefButtonsDiv.appendChild(btn);
    }

    /**
     * Met à jour l'affichage de l'opération, des notes, du total, du coefficient et de la moyenne.
     * Cette fonction reconstruit entièrement le contenu de 'operationDiv' à chaque appel
     * pour s'assurer que tous les écouteurs d'événements des 'chips' restent fonctionnels.
     */
    function updateOperation() {
      operationDiv.innerHTML = ""; // VIDE ENTIÈREMENT LE CONTENU À CHAQUE MISE À JOUR

      // 1. Affichage des notes ajoutées sous forme de "chips"
      if (notes.length > 0) {
        const notesContainer = document.createElement('div');
        notesContainer.style.marginBottom = '10px'; // Marge sous les chips
        notes.forEach((note, index) => {
          const chip = document.createElement('span');
          chip.className = "note-chip";
          chip.textContent = note.toLocaleString('fr-FR'); // Formatage pour la France
          // Ajout de l'écouteur d'événement pour la suppression sur chaque chip
          chip.addEventListener('click', (e) => {
            e.stopPropagation(); // Empêche le clic de se propager à d'autres éléments
            notes.splice(index, 1); // Supprime la note de l'array
            updateOperation(); // Recalcule et met à jour l'affichage
          });
          notesContainer.appendChild(chip);
        });
        operationDiv.appendChild(notesContainer);
        operationDiv.appendChild(document.createElement("hr")); // Ligne de séparation
      }

      // 2. Affichage du total des notes
      if (notes.length > 0) {
        const total = notes.reduce((a, b) => a + b, 0);
        const totalText = document.createElement('div');
        totalText.className = 'result-line';
        totalText.textContent = `Total des notes : ${total.toLocaleString('fr-FR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`;
        operationDiv.appendChild(totalText);
      }

      // 3. Affichage du coefficient et de la moyenne
      if (selectedCoef !== null) {
        const coefText = document.createElement('div');
        coefText.className = 'result-line';
        coefText.textContent = `Coefficient choisi : ${selectedCoef.toLocaleString('fr-FR')}`;
        operationDiv.appendChild(coefText);

        if (notes.length > 0) {
          const total = notes.reduce((a, b) => a + b, 0);
          const moyenne = (total / selectedCoef);
          const moyenneText = document.createElement('div');
          moyenneText.className = 'result-line average-line';
          moyenneText.textContent = `Moyenne finale : ${moyenne.toLocaleString('fr-FR', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`;
          operationDiv.appendChild(moyenneText);
        } else {
            const noNotesForMoyenne = document.createElement('div');
            noNotesForMoyenne.className = 'result-line';
            noNotesForMoyenne.textContent = `Ajoutez des notes pour calculer la moyenne.`;
            operationDiv.appendChild(noNotesForMoyenne);
        }
      }

      // 4. Message par défaut si rien n'est sélectionné
      if (notes.length === 0 && selectedCoef === null) {
        operationDiv.textContent = "Aucune note ajoutée ni coefficient choisi";
      }
    }

    /**
     * Réinitialise toutes les notes et le coefficient sélectionné, puis met à jour l'affichage.
     */
    function resetAll() {
      notes = [];
      selectedCoef = null;
      updateOperation();
    }

    /**
     * Lance la reconnaissance vocale pour ajouter une note.
     */
    function startVoice() {
      if (!('webkitSpeechRecognition' in window)) {
        alert("Désolé, votre navigateur ne supporte pas la reconnaissance vocale. Essayez Chrome !");
        return;
      }
      const recognition = new webkitSpeechRecognition();
      recognition.lang = 'fr-FR'; // Langue de reconnaissance
      recognition.interimResults = false; // Ne renvoie que les résultats finaux
      recognition.maxAlternatives = 1; // Ne renvoie qu'une seule alternative
      recognition.start();

      recognition.onresult = function(event) {
        const spoken = event.results[0][0].transcript;
        // Remplace la virgule par un point pour parseFloat, puis convertit en nombre
        const number = parseFloat(spoken.replace(',', '.'));
        // Vérifie si c'est un nombre valide et dans la plage attendue
        if (!isNaN(number) && number >= 0 && number <= 8) {
          // Utilise toFixed(2) pour gérer les décimales comme 0.25, puis parseFloat
          notes.push(parseFloat(number.toFixed(2)));
          updateOperation();
        } else {
          alert(`"${spoken}" n'est pas une note valide (doit être un nombre entre 0 et 8).`);
        }
      };

      recognition.onerror = function(event) {
        console.error("Erreur de reconnaissance vocale :", event.error);
        alert("Une erreur est survenue avec la reconnaissance vocale : " + event.error);
      };

      recognition.onend = function() {
        // La reconnaissance s'est arrêtée
        console.log('Reconnaissance vocale terminée.');
      };
    }

    // --- Initialisation de l'affichage au chargement de la page ---
    updateOperation();
  </script>
</body>
</html>
