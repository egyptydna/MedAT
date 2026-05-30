<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>MedAT BMS Quiz 2020</title>
  <style>
    :root {
      --bg: #f4f7fb;
      --card: #ffffff;
      --text: #182033;
      --muted: #667085;
      --primary: #2563eb;
      --primary-dark: #1d4ed8;
      --right: #16a34a;
      --wrong: #dc2626;
      --border: #d9e2ef;
      --shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
      line-height: 1.5;
    }

    header {
      background: linear-gradient(135deg, #1d4ed8, #2563eb, #60a5fa);
      color: white;
      padding: 28px 16px;
      text-align: center;
    }

    header h1 {
      margin: 0;
      font-size: 28px;
    }

    header p {
      margin: 8px 0 0;
      opacity: 0.95;
    }

    .container {
      max-width: 980px;
      margin: 0 auto;
      padding: 20px 14px 60px;
    }

    .topbar {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
      gap: 12px;
      margin-bottom: 18px;
    }

    .panel {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 16px;
      padding: 14px;
      box-shadow: var(--shadow);
    }

    .score {
      font-weight: 700;
      font-size: 18px;
    }

    select, button {
      width: 100%;
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 11px 12px;
      font-size: 15px;
      background: white;
    }

    button {
      cursor: pointer;
      background: var(--primary);
      color: white;
      border: none;
      font-weight: 700;
    }

    button:hover { background: var(--primary-dark); }

    .question-card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 18px;
      padding: 18px;
      margin-bottom: 16px;
      box-shadow: var(--shadow);
    }

    .meta {
      color: var(--muted);
      font-size: 14px;
      margin-bottom: 8px;
    }

    .question {
      font-size: 19px;
      font-weight: 700;
      margin-bottom: 14px;
    }

    .answers {
      display: grid;
      gap: 10px;
    }

    .answer {
      text-align: left;
      color: var(--text);
      background: #f8fafc;
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 12px 14px;
      font-weight: 500;
    }

    .answer:hover { background: #eef4ff; }

    .answer.correct {
      background: #dcfce7;
      border-color: var(--right);
      color: #14532d;
    }

    .answer.wrong {
      background: #fee2e2;
      border-color: var(--wrong);
      color: #7f1d1d;
    }

    .answer.disabled {
      cursor: default;
      pointer-events: none;
      opacity: 0.95;
    }

    .feedback {
      margin-top: 12px;
      font-weight: 700;
    }

    .note {
      margin-top: 10px;
      color: var(--muted);
      font-size: 14px;
    }

    .hidden { display: none; }
  </style>
</head>
<body>
  <header>
    <h1>MedAT BMS Quiz 2020</h1>
    <p>Biologie, Chemie, Physik und Mathematik als anklickbares Quiz</p>
  </header>

  <main class="container">
    <div class="topbar">
      <div class="panel">
        <label for="subject"><strong>Fach auswählen</strong></label>
        <select id="subject" onchange="renderQuiz()">
          <option value="Alle">Alle BMS-Fragen</option>
          <option value="Biologie">Biologie</option>
          <option value="Chemie">Chemie</option>
          <option value="Physik">Physik</option>
          <option value="Mathe">Mathe</option>
        </select>
      </div>
      <div class="panel score" id="scoreBox">Punkte: 0 / 0</div>
      <div class="panel"><button onclick="resetQuiz()">Quiz zurücksetzen</button></div>
    </div>

    <div class="panel note">
      Hinweis: Manche Fragen aus der Altfragen-Sammlung sind unvollständig oder als Mehrfachantwort markiert. Bei diesen Fragen können mehrere richtige Antworten hinterlegt sein.
    </div>

    <section id="quiz"></section>
  </main>

  <script>
    const questions = [
      // BIOLOGIE
      {subject:'Biologie', q:'In welchem Abschnitt des Zentralnervensystems befindet sich das Atem- und Kreislaufzentrum?', a:['Hirnstamm','Großhirn','Rückenmark','Zwischenhirn','Kleinhirn'], c:[0]},
      {subject:'Biologie', q:'Adenin liegt in einer DNA zu 27 % vor. Zu wie viel Prozent liegt Thymin vor?', a:['27 %','73 %','23 %','50 %','33 %'], c:[0]},
      {subject:'Biologie', q:'Blutserum und Blutplasma unterscheiden sich aufgrund des Vorhandenseins bzw. Fehlens von:', a:['Gerinnungsfaktoren','Immunglobulinen','Elektrolyten','Leukozyten','Erythrozyten'], c:[0]},
      {subject:'Biologie', q:'Aus welchem Keimblatt entsteht das Epithelgewebe des Urdarms?', a:['Entoderm','Ektoderm','Mesoderm','Exoderm','Uroderm'], c:[0]},
      {subject:'Biologie', q:'Woraus bestehen Mikrofilamente?', a:['Mikrotubuli','Thymin','Aktin','Myosin','Tubulin'], c:[2]},
      {subject:'Biologie', q:'Welches der genannten Gelenke ist ein Scharniergelenk?', a:['Hüftgelenk','Schulter','Fingerendgelenk','Zehengrundgelenk','Handgelenk'], c:[2]},
      {subject:'Biologie', q:'Welche Aussage zum Lungenkreislauf ist richtig?', a:['Sauerstoffreiches Blut fließt durch die Lungenvenen in den linken Vorhof','Sauerstoffarmes Blut fließt durch die Lungenvenen in den linken Vorhof','Sauerstoffreiches Blut fließt durch die Lungenarterien in den rechten Vorhof'], c:[0]},
      {subject:'Biologie', q:'Worin münden obere und untere Hohlvene?', a:['Rechter Vorhof','Linker Vorhof','Rechter und linker Vorhof','Linker Vorhof und linke Kammer','Obere in rechten Vorhof und untere in die Pfortader'], c:[0]},
      {subject:'Biologie', q:'Wo liegt der Pförtner?', a:['Am Ausgang vom Magen','Am Eingang des Magens'], c:[0]},
      {subject:'Biologie', q:'Wie wird der Zellhaufen ungefähr 100 Stunden nach der Befruchtung genannt?', a:['Morula','Blastula','Blastozyste','Zytoblast'], c:[2]},
      {subject:'Biologie', q:'In welchen Zellen wird Glykogen gespeichert?', a:['Leberzellen','Fettzellen','Inselzellen der Bauchspeicheldrüse','Nervenzellen'], c:[0]},
      {subject:'Biologie', q:'Was wurde durch das Miller-Urey-Experiment nachgewiesen?', a:['Abiotische Synthese von organischen Molekülen','Entstehung größerer organischer Moleküle aus kleineren','Synthese von biotischen Materialien von Organismen','Synthese von abiotischen Materialien von Organismen'], c:[0]},
      {subject:'Biologie', q:'Was gehört neben Milz, Lymphknoten und Mandeln noch zu den lymphatischen Organen?', a:['Knochenmark','Bauchspeicheldrüse','Epiphyse','Hypophyse','Leber'], c:[0]},
      {subject:'Biologie', q:'Was beeinträchtigt gemäß Hardy-Weinberg-Gesetz NICHT die Stabilität des Genpools?', a:['Kleine Population','Gendrift','Panmixie','Migration','Selektion'], c:[2]},
      {subject:'Biologie', q:'Ein Muskel kann sich zusammenziehen. Wie gelangt er wieder in seine Ausgangsposition zurück?', a:['Durch die aufgebaute Spannung in den Gelenken','Nach einer gewissen Zeit springt er automatisch zurück','Nach aktiver Kontraktion folgt spontane Rückkehr','Durch einen antagonistischen Muskel','Durch die Bindegewebshülle allein'], c:[3]},
      {subject:'Biologie', q:'Wie nennt man Fortsätze von Nervenzellen, die Erregungen vom Zellkörper wegleiten?', a:['Axone','Dendriten','Synapsen','Ranvier-Schnürringe','Aktionspotentiale'], c:[0]},
      {subject:'Biologie', q:'Welche Kreuzung kann Nachkommen mit gelben (rr) und roten Blüten (Rr/RR) hervorbringen?', a:['Rr × RR oder Rr × Rr','RR × rr oder Rr × Rr','rr × Rr oder Rr × Rr','rr × rr oder Rr × rr','rr × rr oder Rr × Rr'], c:[2]},
      {subject:'Biologie', q:'Was sind Desmosomen?', a:['Zellkontakte bei Epithelzellen','Zellorganellen','Fortsätze auf der Oberfläche der Zellen'], c:[0]},
      {subject:'Biologie', q:'Wie wird der energieverbrauchende Anteil des Stoffwechsels genannt?', a:['Anabolismus','Katabolismus','Metabolismus'], c:[0]},
      {subject:'Biologie', q:'Was kann bei Patienten zu einer längeren Blutungszeit führen?', a:['Geringe Thrombozytenzahl','Hohe Erythrozytenzahl','Niedrige Erythrozytenzahl','Hohe Thrombozytenzahl'], c:[0]},
      {subject:'Biologie', q:'Ökosystem: Welche Aussage ist falsch?', a:['Grüne Pflanzen sind Primärkonsumenten','Viele Pilze sind Destruenten','Menschen sind Konsumenten','Bakterien können Symbiosen eingehen','Ökosysteme bestehen aus biotischen und abiotischen Faktoren'], c:[0]},
      {subject:'Biologie', q:'Welche Aussage zu Chromosomen ist richtig?', a:['Eine ausgereifte Keimzelle besitzt 23 Chromosomen','Chromosomenzahl wird erst in der 4. Schwangerschaftswoche festgelegt','Frauen haben weniger Genmaterial als Männer','Bei Mitose gibt es zwei Teilungen','In Prophase I werden homologe Chromosomen auseinandergezogen'], c:[0]},
      {subject:'Biologie', q:'Welche Aussage zu Genommutationen ist am passendsten?', a:['Mutationshäufigkeit hängt von Mutationsrate ab','Beispiele für Genommutationen sind Deletion und Insertion','Genommutationen können rezessiv oder dominant sein'], c:[0]},
      {subject:'Biologie', q:'Welche Aussage zum Down-Syndrom stimmt?', a:['Wenn eine Eizelle von zwei Spermien befruchtet wird, ist das eine Aneuploidie','Genommutation der Gonosomen','Genommutation der Autosomen','Risiko wird geringer durch steigendes Alter der Mutter'], c:[2]},
      {subject:'Biologie', q:'Ein Partikel wird von der Plasmamembran aufgenommen und nach außen abgegeben. Wie nennt man diese Vorgänge?', a:['Exozytose und Endozytose','Nur Endozytose'], c:[0]},
      {subject:'Biologie', q:'Was beschreibt Endozytose?', a:['Einstülpung der Plasmamembran zur Aufnahme größerer Teilchen','Ausstülpung der Plasmamembran zur Abgabe von Teilchen'], c:[0]},
      {subject:'Biologie', q:'Welches Zellorganell entsteht im Golgi-Apparat und baut Makromoleküle ab?', a:['Mitochondrium','Lysosom','Peroxisom','Proteasom','Lysozym'], c:[1]},
      {subject:'Biologie', q:'Wo wird im Rahmen der Verdauung viel Wasser rückresorbiert?', a:['Dickdarm','Dünndarm','Magen','Speiseröhre'], c:[0]},
      {subject:'Biologie', q:'Was macht die große Absorptionsoberfläche des Dünndarms aus?', a:['Einschichtiges Epithel','Glatte Muskulatur','Mikrovilli'], c:[2]},
      {subject:'Biologie', q:'Was ist eine typische Eigenschaft der glatten Muskulatur des Darms?', a:['Willentliche Beeinflussbarkeit','Rasche Ermüdung','Schnelle Kontraktion','Aktive Streckung','Ausdauernde Tätigkeit'], c:[4]},
      {subject:'Biologie', q:'TTA wird zu TTT. Um welche Mutation handelt es sich?', a:['Insertion','Deletion','Punktmutation','Inversion','Substitution'], c:[2,4]},
      {subject:'Biologie', q:'Plazenta: Welche Aussage ist falsch?', a:['Nabelschnur verbindet Plazenta mit Uteruswand','Sie verbindet die zwei Kreisläufe nicht direkt','Sie bildet eine Barriere zwischen fetalem und mütterlichem Blutkreislauf','Medikamente können übertragen werden'], c:[0]},
      {subject:'Biologie', q:'Wie können Ionen passiv durch die Zellmembran gelangen?', a:['Primär aktiver Transport','Erleichterte Diffusion mittels Kanälen','Sekundär aktiver Symport','Sekundär aktiver Antiport','Direkt durch Phospholipide'], c:[1]},
      {subject:'Biologie', q:'Welches ist die korrekte Reihenfolge der Harnableitung aus dem Nierenbecken nach außen?', a:['Harnleiter - Harnblase - rechte und linke Harnröhre','Rechter und linker Harnleiter - Harnblase - rechte und linke Harnröhre','Rechte und linke Harnröhre - Harnblase - Harnleiter','Rechte und linke Harnblase - Harnleiter - Harnröhre','Rechter und linker Harnleiter - Harnblase - Harnröhre'], c:[4]},
      {subject:'Biologie', q:'Was zum Aufbau der DNA ist korrekt?', a:['Pentose ist allein das Rückgrat der DNA','Rückgrat ist über Phosphodiesterbindungen verknüpft','Basen hängen über kovalente Bindungen zusammen'], c:[1]},
      {subject:'Biologie', q:'Proteinbiosynthese: Was ist wahr?', a:['Translation beginnt beim Poly-A-Schwanz','tRNA transportiert Aminosäuren zu Ribosomen','Transkription beginnt an beliebiger mRNA-Stelle'], c:[1]},
      {subject:'Biologie', q:'Welcher Sinn hat im Innenohr seine Verankerung?', a:['Gleichgewicht','Temperatur','Druck'], c:[0]},
      {subject:'Biologie', q:'Auge: Welche Aussage ist falsch?', a:['Stäbchen sind zum Farbsehen und Zapfen zum Schwarz-Weiß-Sehen','Am blinden Fleck befindet sich die Austrittsstelle des Sehnervs','Rhodopsin kommt in Stäbchen vor','Die Brechkraft der Linse wird durch den Ziliarmuskel angepasst','Die Netzhaut enthält Fotorezeptoren und Nerven'], c:[0]},
      {subject:'Biologie', q:'Wo befindet sich DNA beim Menschen?', a:['Zellkern und Mitochondrien','Ausschließlich im Zellkern','Zellkern und Golgi-Apparat','Endoplasmatisches Retikulum'], c:[0]},
      {subject:'Biologie', q:'Welche Aussage zur Zellteilung bei Eukaryonten trifft zu?', a:['Menschliche Geschlechtszellen haben einen diploiden Chromosomensatz','Zellzyklus: G1 - G2 - S - M','Für die Mitose wird das Kerngenom in Mitochondrien verpackt','Crossing-over macht diploide Zellen direkt haploid','In Meiose I erfolgt die Trennung der replizierten homologen Chromosomen'], c:[4]},

      // CHEMIE
      {subject:'Chemie', q:'Durch welche Maßnahme lässt sich die Lage des chemischen Gleichgewichts NICHT beeinflussen?', a:['Einsatz eines Katalysators','Verminderung von Druck','Entfernung von Produkten','Erhöhung der Temperatur','Zufuhr von Edukten'], c:[0]},
      {subject:'Chemie', q:'Um welche funktionelle Gruppe handelt es sich? (Bildfrage aus Vorlage)', a:['Ether','Carbonsäure','Ester','Amid','Keton'], c:[2]},
      {subject:'Chemie', q:'Triglyzerid / Fette erkennen: Welche Struktur passt grundsätzlich zu einem Fett?', a:['Glycerin mit drei Fettsäureresten über Esterbindungen','Ein einzelner Aminosäurerest','Ein Monosaccharid-Ring','Ein Nukleotid'], c:[0]},
      {subject:'Chemie', q:'Nebengruppen: Was trifft zu?', a:['Bei den meisten Nebengruppenelementen handelt es sich um Edelmetalle','Nebengruppenelemente unterscheiden sich stärker innerhalb einer Periode als Hauptgruppenelemente','Alle Nebengruppenelemente haben höchstens 2 Valenzelektronen','Bor enthält Metalle und Nichtmetalle','Alle Nebengruppenelemente haben Oxidationszahl -II'], c:[1]},
      {subject:'Chemie', q:'Knallgasreaktion: Welche Aussage ist falsch?', a:['Sauerstoff nimmt Elektronen auf','Wasserstoff wird oxidiert','Wasserstoff nimmt Elektronen auf','Wasserstoff ist ein Reduktionsmittel','Sauerstoff wird reduziert'], c:[2]},
      {subject:'Chemie', q:'Normalpotential/Elektrochemie: Was trifft zu?', a:['Halbzellen sind leitend miteinander verbunden','Metallstäbe tauchen in Lösung ihrer Ionen','Unedle Metalle haben negatives Standardpotential','Edle Metalle werden reduziert','Einzelne Potentiale können nicht gemessen werden, nur Potentialdifferenzen'], c:[0,1,2,3,4]},
      {subject:'Chemie', q:'Eine Säure hat pKs = 5,75. Was trifft zu?', a:['Es handelt sich um eine starke Säure','Das Säureanion ist eine schwache Base','Ks = 10^5,75','Sie kann in einem Puffersystem mitwirken','Alle sind richtig'], c:[1,3]},
      {subject:'Chemie', q:'Chemische Bindungen: Was trifft zu?', a:['Salze sind klassische Beispiele für Ionenbindungen','Ionenbindungen bilden sich zwischen Elementen, die im PSE übereinanderstehen','Atombindungen bilden sich zwischen Alkalimetallen und Halogenen','Metallbindungen entstehen durch Anziehung von Anionen und Kationen','Alle sind richtig'], c:[0]},
      {subject:'Chemie', q:'Was passiert, wenn eine Base in Wasser gelöst wird?', a:['OH⁻-Konzentration sinkt unter 10⁻⁷ mol/L','H⁺-Konzentration steigt über 10⁻⁷ mol/L','pH-Wert steigt über 7','pOH-Wert steigt über 7','pH-Wert sinkt unter 7'], c:[2]},
      {subject:'Chemie', q:'2A + B ⇌ C + D, ΔH = -245 kJ/mol: Welche Aussage trifft zu?', a:['MWG: ([C]·[D])/([2A]·[B])','Wenn man B entfernt, verschiebt sich das Gleichgewicht nach rechts','Wenn man C hinzufügt, verschiebt sich das Gleichgewicht nach links','ΔH beschreibt die Entropie','Alle sind richtig'], c:[2]},
      {subject:'Chemie', q:'Welche Aussage zu Gemischen ist richtig?', a:['Eine Emulsion ist ein Gas in einer Flüssigkeit','Eine Suspension ist ein Feststoff in einer Flüssigkeit','Rauch ist eine Form von Nebel','Vermischung von Gasen ist Rauch','Alle sind richtig'], c:[1]},
      {subject:'Chemie', q:'Nach welchem Ordnungsprinzip werden Elemente im PSE angeordnet?', a:['Aufsteigende Massenzahl','Aufsteigende Kernladungszahl','Aufsteigende Elektronenanzahl','Neutronenanzahl','Alle sind richtig'], c:[1]},
      {subject:'Chemie', q:'Welche Aussage zur organischen Chemie ist richtig?', a:['Organische Moleküle bestehen ausschließlich aus C und H','Kohlenwasserstoffe bestehen aus unpolaren Molekülen','Moleküle mit Doppelbindungen heißen Alkine','Alkene haben mindestens eine Dreifachbindung','Ein Molekül mit zwei C-Atomen kann Butan heißen'], c:[1]},
      {subject:'Chemie', q:'Man kennt Massenzahl und Protonenzahl. Was stimmt beim neutralen Atom?', a:['Gleich viele Elektronen wie Nukleonen','Gleich viele Elektronen in der Hülle wie Protonen im Kern','Immer gleich viele Protonen wie Neutronen','Gleich viele Valenzelektronen wie Protonen','Alle sind richtig'], c:[1]},
      {subject:'Chemie', q:'Chemische Reaktionen: Was trifft zu?', a:['Gesamtvolumen aller Reaktionsteilnehmer ist konstant','Masse bleibt konstant','Stoffmenge aller Moleküle bleibt gleich','Stoffmenge aller Atome bleibt gleich','Alle sind richtig'], c:[1,3]},
      {subject:'Chemie', q:'Gesetz von Gay-Lussac bei konstantem Druck: Was trifft zu?', a:['Produkt aus Temperatur und Volumen ist konstant','V/T ist konstant','Volumen und Temperatur sind direkt proportional'], c:[1,2]},
      {subject:'Chemie', q:'Wie lautet die Formel von Eisen(III)-oxid?', a:['Fe₂O₃','Fe₃O₂','FeO₂','FeO','Fe₃O'], c:[0]},
      {subject:'Chemie', q:'Welche Aussage zu Phasenübergängen ist richtig?', a:['Beim Gefrieren wird Wärme abgegeben','Beim Schmelzen wird Wärme abgegeben','Beim Verdampfen wird Wärme abgegeben','Sublimieren ist exotherm','Beim Resublimieren wird der Umgebung Wärme entzogen'], c:[0]},
      {subject:'Chemie', q:'Bei welchem Prozess wird Energie aus dem Atomkern frei?', a:['Spaltung von schweren Kernen','Entfernung von Elektronen aus dem Atomkern','Spaltung von leichten Kernen','Fusion von schweren Kernen','Alle sind richtig'], c:[0]},
      {subject:'Chemie', q:'Welche Bindungsart besteht zwischen gegenüberliegenden Basen in der DNA?', a:['Ionenbindung','Wasserstoffbrückenbindungen','Kovalente Bindung','Koordinative Bindung','Van-der-Waals-Kräfte'], c:[1]},
      {subject:'Chemie', q:'Welche Aussage zur Entropie ist richtig?', a:['Bei 0 °C haben Stoffe geringstmögliche Entropie','Bei 0 K findet keine Teilchenbewegung mehr statt','Beim Verdampfen nimmt Entropie ab','Beim Schmelzen nimmt Entropie ab','Beim Erstarren nimmt Entropie ab'], c:[1,4]},
      {subject:'Chemie', q:'Welche Oxidationszahl hat Sauerstoff in H₂O₂?', a:['+1','-1','0','-2','+2'], c:[1]},
      {subject:'Chemie', q:'Welche Aussagen zu Nukleinsäuren sind richtig?', a:['Einzelstränge sind über Wasserstoffbrücken zu Doppelsträngen verknüpft','Pentosen sind über Phosphodiesterbindungen verknüpft','Basen sind über kovalente Bindungen miteinander verknüpft','DNA hat ein Zucker-Phosphat-Rückgrat','Alle sind richtig'], c:[0,1,3]},
      {subject:'Chemie', q:'Welche Aussagen zu Sauerstoff sind korrekt?', a:['Molekularer Sauerstoff hat die Summenformel O₂','Ozon ist eine besondere Form von Sauerstoff','Sauerstoff ist Hauptbestandteil der Luft','Sauerstoff hat 8 Valenzelektronen','Sauerstoff hat Ordnungszahl 8','Sauerstoff ist ein Produkt der Photosynthese'], c:[0,1,4,5]},
      {subject:'Chemie', q:'Was ist ein Produkt der Photosynthese?', a:['Sauerstoff','Wasser','Kohlendioxid','²³⁵U','Alle sind richtig'], c:[0]},

      // PHYSIK
      {subject:'Physik', q:'Die Auffächerung von weißem Licht beim Regenbogen entsteht durch:', a:['wellenlängenabhängige Absorption','wellenlängenabhängige Totalreflexion','wellenlängenabhängige Beugung','wellenlängenabhängige Streuung','wellenlängenabhängige Brechung'], c:[4]},
      {subject:'Physik', q:'Was besagt die Heisenbergsche Unschärferelation?', a:['Ort und Impuls können nicht gleichzeitig exakt bestimmt werden','Ort und Impuls können gleichzeitig genau bestimmt werden','Energie und Zeit können gleichzeitig beliebig genau bestimmt werden','Masse und Ort können nicht gleichzeitig bestimmt werden','Zeit und Geschwindigkeit können nicht gleichzeitig bestimmt werden'], c:[0]},
      {subject:'Physik', q:'Als was wird Licht in der Physik gewertet?', a:['Fluss von Teilchen','Elektromagnetische Welle','Elektrostatische Interaktion','Phlogiston'], c:[0,1]},
      {subject:'Physik', q:'Wer gilt als Begründer des Quantenmodells / der Quantenhypothese?', a:['Rutherford','Planck','Maxwell'], c:[1]},
      {subject:'Physik', q:'Licht geht von Glas in Luft über. Was passiert beim Durchqueren der Luft?', a:['Die Lichtgeschwindigkeit ändert sich','Die Wellenlänge bleibt konstant','Eintrittswinkel hängt nicht mit Austrittswinkel zusammen','Die Frequenz ändert sich'], c:[0]},
      {subject:'Physik', q:'Wie lautet die Formelidee für Arbeit?', a:['Kraft mal Länge des Hebels','Kraft in Richtung der Kraftwirkung mal Weglänge','Kraft mal Zeit','Masse mal Beschleunigung'], c:[1]},
      {subject:'Physik', q:'Was bezeichnet der Doppler-Effekt?', a:['Frequenzänderung bei Relativbewegung zwischen Sender und Empfänger','Immer niedrigere Frequenz bei Bewegung','Immer höhere Frequenz bei Bewegung','Auslöschung der Wellen'], c:[0]},
      {subject:'Physik', q:'Welche Dimension/Einheit hat das Drehmoment?', a:['m/s','Newton','Joule','Nm'], c:[3]},
      {subject:'Physik', q:'Induktion: Was trifft zu, wenn eine Leiterschleife im Magnetfeld bewegt wird?', a:['Nur bei Translation, nicht bei Rotation, kommt es zur Induktion','Geschwindigkeit hat keinen Einfluss auf induzierte Spannung','Liegt die Schleife vollständig in homogenem Magnetfeld und der Fluss ändert sich nicht, tritt keine Induktion auf','Bei gleichmäßiger Hin- und Herbewegung entsteht keine Induktion'], c:[2]},
      {subject:'Physik', q:'Wo ist die elektrische Feldstärke bei Hochspannungsleitungen bezogen auf den Boden am höchsten?', a:['Direkt beim Holzmast','Direkt beim Metallmast','In der Mitte zwischen den Masten, wo die Leitung dem Boden am nächsten ist','Überall gleich stark'], c:[2]},
      {subject:'Physik', q:'Was ist KEINE SI-Basiseinheit?', a:['Kraft (Newton)','Temperatur (Kelvin)','Stromstärke (Ampere)','Lichtstärke (Candela)','Stoffmenge (Mol)'], c:[0]},
      {subject:'Physik', q:'Welche Aussage zum Atomdurchmesser ist richtig?', a:['Kerndurchmesser im Nanometerbereich','Elektronenhülle nur 10-mal so groß wie Kern','Atomdurchmesser 10 nm','Atomdurchmesser etwa 0,1 nm','Elektronenhülle 100-mal so groß wie Kern'], c:[3]},
      {subject:'Physik', q:'Welche v(t)-Skizze zeigt gleichförmig beschleunigte Bewegung?', a:['Lineare Gerade mit positiver Steigung','Parabel','Lineare Gerade mit stärkerer positiver Steigung','Potenzfunktion dritten Grades','Gerade parallel zur x-Achse'], c:[0,2]},
      {subject:'Physik', q:'Federpendel: Welche Aussage ist richtig?', a:['Zu jedem Zeitpunkt ist potentielle Energie gleich kinetischer Energie','Die Gesamtenergie bleibt konstant'], c:[1]},
      {subject:'Physik', q:'Kirchhoffsche Gesetze: Welche Aussage trifft zu?', a:['Stromstärke ist immer größer','Aus Ladungserhaltung folgt: Im Knoten ist Summe der zu- und abfließenden Ströme null','Die Richtung der Masche ist für das Ergebnis entscheidend'], c:[1]},
      {subject:'Physik', q:'Chloroplasten wandeln Sonnenenergie in Glucose um. Welches Prinzip passt dazu?', a:['1. Newtonsches Axiom','Snellius','Bernoulli','Archimedes-Prinzip','Energieerhaltung','Brechungsindex'], c:[4]},
      {subject:'Physik', q:'Was wird mit der Radiokarbonmethode bestimmt?', a:['Verhältnis von C-12 und C-14','Löslichkeit von C-14','Löslichkeit von X','Verhältnis von K-40 und C-14'], c:[0]},
      {subject:'Physik', q:'Temperaturskalen: Welche Aussage ist falsch?', a:['Die Kelvinskala ist sowohl im negativen als auch positiven Wertebereich definiert','Temperaturunterschiede können in Kelvin und Celsius angegeben werden','Ein Temperaturunterschied von 1 °C entspricht 1 K','Wasser gefriert ungefähr bei 274 K'], c:[0]},

      // MATHE
      {subject:'Mathe', q:'Wie berechnet sich das Volumen V eines Quaders mit Kantenlängen a, b und c?', a:['V = 2abc','V = a + b + c','V = abc','V = 2(ab + ac + bc)','V = (a+b+c)³'], c:[2]},
      {subject:'Mathe', q:'Welche Einheit gehört zur Aktivität?', a:['Bq','Sv','Gy'], c:[0]},
      {subject:'Mathe', q:'1 Terabyte dividiert durch 100 Megabyte ergibt ungefähr:', a:['1 Million','10⁹','10⁶','10000','1×10⁵'], c:[3]},
      {subject:'Mathe', q:'Zu 2 Hektar werden 200 m² dazugekauft. Wie groß ist die Totalfläche?', a:['0,202 km²','22·10³ m²','20200 m²'], c:[2]},
      {subject:'Mathe', q:'Welche Umrechnung ist richtig?', a:['72 km/h = 20 m/s'], c:[0]},
      {subject:'Mathe', q:'V = (a³ · b²) / c². Welche Aussage stimmt?', a:['Wenn a und c verdoppelt werden, vervierfacht sich V','Wenn a verdoppelt und b geviertelt wird, ist V halb so groß','Wenn a und b verdoppelt werden, verachtfacht sich V','Wenn c verdoppelt wird, viertelt sich V'], c:[1,3]},
      {subject:'Mathe', q:'Rechtwinkliges Dreieck: Welche Beziehung ist korrekt?', a:['cos = Gegenkathete / Hypotenuse','sin = Ankathete / Hypotenuse','tan = Gegenkathete / Ankathete'], c:[2]},
      {subject:'Mathe', q:'x₁ + x₂ = 10 und x₁ - x₂ = 0. Was sind x₁ und x₂?', a:['x₁ = x₂ = 5','x₁ = 10, x₂ = 0','x₁ = x₂ = 2'], c:[0]},
      {subject:'Mathe', q:'Wie kann man 3 ppm Kohlenmonoxid in 1 L Luft ausdrücken?', a:['3 µL/L','10⁶/L'], c:[0]},
      {subject:'Mathe', q:'Steigungsdreieck: Welche Aussagen sind richtig?', a:['Mehr als 100 % Steigung sind nicht möglich','Ein Steigungswinkel von 45° entspricht 100 % Steigung','Der Steigungswinkel kann über tan(α)=Δy/Δx berechnet werden'], c:[1,2]},
      {subject:'Mathe', q:'Vektorsubtraktion: V₁ = (-10,1), V₂ = (2,-5). Was ist V₁ - V₂?', a:['(-12,6)','(12,6)','(12,-6)'], c:[0]},
      {subject:'Mathe', q:'Vereinfache den Ausdruck (-5)^4 · 3.', a:['(-15)^8','(-15)^4','Es gibt keine Vereinfachung zu einer einzigen Potenz mit Basis -15'], c:[2]}
    ];

    let answered = new Map();

    function currentQuestions() {
      const subject = document.getElementById('subject').value;
      return subject === 'Alle' ? questions : questions.filter(q => q.subject === subject);
    }

    function renderQuiz() {
      const quiz = document.getElementById('quiz');
      quiz.innerHTML = '';
      const list = currentQuestions();

      list.forEach((item, index) => {
        const globalIndex = questions.indexOf(item);
        const card = document.createElement('div');
        card.className = 'question-card';
        card.innerHTML = `
          <div class="meta">${item.subject} · Frage ${index + 1} von ${list.length}${item.c.length > 1 ? ' · Mehrfachantwort möglich' : ''}</div>
          <div class="question">${escapeHTML(item.q)}</div>
          <div class="answers"></div>
          <div class="feedback" id="feedback-${globalIndex}"></div>
        `;

        const answersDiv = card.querySelector('.answers');
        item.a.forEach((answer, answerIndex) => {
          const btn = document.createElement('button');
          btn.className = 'answer';
          btn.textContent = answer;
          btn.onclick = () => checkAnswer(globalIndex, answerIndex, btn, card);
          answersDiv.appendChild(btn);
        });

        quiz.appendChild(card);

        if (answered.has(globalIndex)) {
          restoreCard(globalIndex, card);
        }
      });

      updateScore();
    }

    function checkAnswer(questionIndex, answerIndex, btn, card) {
      const q = questions[questionIndex];
      if (answered.has(questionIndex)) return;

      const correct = q.c.includes(answerIndex);
      answered.set(questionIndex, correct);

      const buttons = card.querySelectorAll('.answer');
      buttons.forEach((b, i) => {
        b.classList.add('disabled');
        if (q.c.includes(i)) b.classList.add('correct');
      });
      if (!correct) btn.classList.add('wrong');

      const feedback = document.getElementById(`feedback-${questionIndex}`);
      feedback.textContent = correct ? '✅ Richtig!' : '❌ Falsch. Die grün markierte Antwort ist richtig.';
      feedback.style.color = correct ? 'var(--right)' : 'var(--wrong)';

      updateScore();
    }

    function restoreCard(questionIndex, card) {
      const q = questions[questionIndex];
      const buttons = card.querySelectorAll('.answer');
      buttons.forEach((b, i) => {
        b.classList.add('disabled');
        if (q.c.includes(i)) b.classList.add('correct');
      });
      const correct = answered.get(questionIndex);
      const feedback = document.getElementById(`feedback-${questionIndex}`);
      feedback.textContent = correct ? '✅ Richtig!' : '❌ Falsch. Die grün markierte Antwort ist richtig.';
      feedback.style.color = correct ? 'var(--right)' : 'var(--wrong)';
    }

    function updateScore() {
      const list = currentQuestions();
      let done = 0;
      let right = 0;
      list.forEach(q => {
        const idx = questions.indexOf(q);
        if (answered.has(idx)) {
          done++;
          if (answered.get(idx)) right++;
        }
      });
      document.getElementById('scoreBox').textContent = `Punkte: ${right} / ${done} beantwortet · Insgesamt ${list.length} Fragen`;
    }

    function resetQuiz() {
      answered.clear();
      renderQuiz();
    }

    function escapeHTML(str) {
      return str.replace(/[&<>'"]/g, char => ({'&':'&amp;','<':'&lt;','>':'&gt;',"'":'&#39;','"':'&quot;'}[char]));
    }

    renderQuiz();
  </script>
</body>
</html>
