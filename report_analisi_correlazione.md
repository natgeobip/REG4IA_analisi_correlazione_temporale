# Analisi di correlazione tra inquinanti atmosferici e variabili satellitari, meteorologiche e statiche

## Obiettivo dell'analisi

L'analisi di correlazione è stata condotta con l'obiettivo di valutare le relazioni statistiche tra le concentrazioni degli inquinanti atmosferici NO₂, O₃, PM10 e PM2.5 e l'insieme delle variabili esplicative disponibili nel dataset. Tali variabili comprendono indicatori satellitari Sentinel-5P/TROPOMI, variabili meteorologiche legate alla ventilazione, e variabili statiche di posizione, rappresentate dalle coordinate geografiche dei punti di osservazione.

La correlazione è utilizzata in questa fase come strumento esplorativo. Essa consente di identificare associazioni lineari tra le variabili, di riconoscere pattern coerenti con i processi atmosferici noti e di supportare la selezione delle feature più informative per le successive fasi di regressione statistica e machine learning. L'analisi non ha quindi finalità causale diretta, ma permette di stabilire quali predittori risultano maggiormente associati ai diversi inquinanti e quali variabili, al contrario, presentano contributi lineari marginali o trascurabili.

Le relazioni sono state analizzate sia sui dati grezzi giornalieri, sia mediante matrici temporali costruite variando il ritardo temporale tra le serie e la finestra di media mobile. Questo approccio permette di distinguere le relazioni istantanee da quelle che emergono su scale temporali più lunghe, dove il rumore meteorologico giornaliero tende a ridursi e il segnale sinottico o stagionale risulta più evidente.

## Metodologia

### Coefficiente di correlazione di Pearson

Il coefficiente di correlazione di Pearson misura l'intensità e la direzione della relazione lineare tra due variabili quantitative. Date due variabili $X$ e $Y$, osservate su $n$ coppie di valori $(x_i, y_i)$, il coefficiente di Pearson è definito come:

```math
r_{XY} =
\frac{\sum_{i=1}^{n}(x_i-\bar{x})(y_i-\bar{y})}
{\sqrt{\sum_{i=1}^{n}(x_i-\bar{x})^2}\sqrt{\sum_{i=1}^{n}(y_i-\bar{y})^2}}
=
\frac{\mathrm{cov}(X,Y)}{\sigma_X\sigma_Y}
```

dove:

- $r_{XY}$ è il coefficiente di correlazione lineare tra $X$ e $Y$;
- $x_i$ e $y_i$ sono i valori osservati della coppia di variabili per l'osservazione $i$;
- $\bar{x}$ e $\bar{y}$ sono le medie campionarie delle due variabili;
- $n$ è il numero di osservazioni valide considerate;
- $\mathrm{cov}(X,Y)$ è la covarianza tra le due variabili;
- $\sigma_X$ e $\sigma_Y$ sono le deviazioni standard di $X$ e $Y$.

Il coefficiente assume valori compresi tra $-1$ e $+1$. Valori positivi indicano che le due variabili tendono ad aumentare congiuntamente; valori negativi indicano che all'aumentare di una variabile l'altra tende a diminuire; valori prossimi a zero indicano assenza di una relazione lineare apprezzabile.

Nel presente report si utilizza la seguente classificazione qualitativa dell'intensità della correlazione:

| Intervallo di $\lvert r \rvert$ | Interpretazione qualitativa |
|---:|---|
| 0,00 - 0,19 | correlazione molto debole o trascurabile |
| 0,20 - 0,39 | correlazione debole |
| 0,40 - 0,59 | correlazione moderata |
| 0,60 - 0,79 | correlazione forte |
| 0,80 - 1,00 | correlazione molto forte |

Tale classificazione deve essere interpretata come guida descrittiva. In ambito atmosferico, anche correlazioni moderate possono essere rilevanti, poiché le concentrazioni degli inquinanti sono influenzate simultaneamente da emissioni, chimica atmosferica, trasporto, deposizione, condizioni meteorologiche e struttura spaziale del territorio.

### Costruzione delle matrici di correlazione

Per ogni inquinante è stata calcolata la correlazione con tutte le variabili disponibili e con i proxy satellitari ritenuti fisicamente pertinenti. Le variabili satellitari considerate includono la colonna troposferica di NO₂, la colonna di O₃, l'Aerosol Index utilizzato come proxy indiretto del particolato, SO₂ e HCHO. Le variabili meteorologiche comprendono la velocità del vento giornaliera e metriche aggiuntive di ventilazione calcolate nelle ore precedenti il passaggio satellitare. Le coordinate geografiche, longitudine e latitudine, sono interpretate come variabili statiche, utili a rappresentare gradienti spaziali legati a urbanizzazione, quota, aree alpine, pianura e distribuzione delle sorgenti emissive.

È importante precisare che Sentinel-5P non fornisce una misura separata delle concentrazioni di PM10 e PM2.5. Per entrambe le frazioni del particolato è stato quindi utilizzato lo stesso Aerosol Index come indicatore satellitare indiretto della presenza di aerosol atmosferico. Di conseguenza, le differenze tra i coefficienti ottenuti per PM10 e per PM2.5 non derivano da due prodotti satellitari distinti, ma dal confronto dello stesso indice AI con due variabili CAMS diverse.

La prima matrice di correlazione è stata costruita sui dati grezzi giornalieri, senza lag e senza media mobile, al fine di fornire una visione d'insieme delle relazioni lineari tra variabili CAMS, indicatori Sentinel-5P e vento. Successivamente, per ciascuna coppia inquinante-proxy, è stata costruita una matrice bidimensionale in cui le righe rappresentano il lag temporale e le colonne rappresentano la finestra di media mobile. Ogni cella della matrice contiene il valore medio del coefficiente di Pearson calcolato sui punti di osservazione validi.

La scala giornaliera considera lag da 0 a 7 giorni e finestre di media mobile fino a 14 giorni. La scala settimanale considera lag fino a 4 settimane e finestre di media mobile fino a 4 settimane. In entrambi i casi, la cella con il valore massimo in modulo identifica la combinazione temporale più informativa per la specifica coppia inquinante-proxy. Le matrici sono rappresentate tramite heatmap, così da rendere immediatamente riconoscibili le aree di correlazione positiva, negativa o nulla.

![Figura 1 - Matrice generale di correlazione sui dati giornalieri](risultati_correlazione/matrice_correlazione_variabili_giornaliero.png)

*Figura 1.  matrice generale di correlazione tra inquinanti, indicatori satellitari e vento.*

## Interpretazione dei risultati

### Quadro generale delle correlazioni

La matrice generale evidenzia una struttura fisicamente coerente. NO₂, PM10 e PM2.5 risultano tra loro fortemente associati, mentre O₃ mostra una relazione negativa con NO₂ e con il particolato. Questo comportamento è coerente con il ruolo del NO₂ come tracciante di emissioni antropiche primarie e con i processi fotochimici che regolano il bilancio dell'ozono troposferico.

I coefficienti più rilevanti sui dati giornalieri grezzi sono i seguenti:

| Inquinante | Correlazioni positive principali | Correlazioni negative principali | Correlazioni trascurabili |
|---|---|---|---|
| NO₂ | PM2.5 $r = 0,825$, PM10 $r = 0,810$, S5P NO₂ $r = 0,566$, Aerosol Index $r = 0,303$ | O₃ $r = -0,720$, latitudine $r = -0,421$ | S5P O₃ $r = 0,017$, vento $r = 0,036$, HCHO $r = -0,043$, SO₂ $r = 0,052$, direzione vento $r = -0,004$ |
| O₃ | HCHO $r = 0,139$, latitudine $r = 0,146$ | NO₂ $r = -0,720$, S5P NO₂ $r = -0,563$, PM2.5 $r = -0,541$, PM10 $r = -0,466$, Aerosol Index $r = -0,302$ | S5P O₃ $r = 0,059$, vento $r = -0,038$, longitudine $r = 0,036$, direzione vento $r = -0,030$ |
| PM10 | PM2.5 $r = 0,977$, NO₂ $r = 0,810$, S5P NO₂ $r = 0,490$, Aerosol Index $r = 0,241$ | O₃ $r = -0,466$, latitudine $r = -0,430$ | HCHO $r = 0,002$, S5P O₃ $r = 0,030$, SO₂ $r = 0,046$, vento $r = -0,049$ |
| PM2.5 | PM10 $r = 0,977$, NO₂ $r = 0,825$, S5P NO₂ $r = 0,511$, Aerosol Index $r = 0,246$ | O₃ $r = -0,541$, latitudine $r = -0,397$ | HCHO $r = -0,026$, SO₂ $r = 0,051$, S5P O₃ $r = 0,063$, vento $r = -0,052$ |

La correlazione molto forte tra PM10 e PM2.5 $(r = 0,977)$ indica che le due frazioni del particolato condividono gran parte della variabilità spaziale e temporale. Questo risultato è atteso, poiché entrambe sono influenzate da sorgenti comuni, condizioni di stabilità atmosferica, aerosol secondario e processi di accumulo. Tuttavia, proprio questa elevata correlazione suggerisce cautela nella successiva modellazione: l'inclusione simultanea di variabili fortemente ridondanti può generare multicollinearità.

### NO₂

Il NO₂ presenta una correlazione molto forte con PM2.5 $(r = 0,825)$ e PM10 $(r = 0,810)$, coerente con la presenza di sorgenti emissive comuni, in particolare traffico veicolare, combustione civile e attività antropiche concentrate nelle aree urbanizzate. La correlazione positiva con S5P NO₂ $(r = 0,566)$ conferma che la colonna troposferica osservata da satellite è informativa rispetto alla variabilità al suolo rappresentata dal campo CAMS, pur non descrivendola in modo perfetto. La differenza tra colonna satellitare e concentrazione superficiale è attesa, poiché il satellite integra il contenuto verticale dell'atmosfera e risente di nuvolosità, geometria di osservazione, profilo verticale e condizioni di rimescolamento.

L'associazione con l'Aerosol Index è positiva ma più debole $(r = 0,303)$. Tale risultato può essere interpretato come effetto indiretto della co-localizzazione tra aree emissive, aerosol e ossidi di azoto, più che come relazione chimica diretta. Le correlazioni con SO₂ $(r = 0,052)$, HCHO $(r = -0,043)$, S5P O₃ $(r = 0,017)$ e vento $(r = 0,036)$ sono invece trascurabili nella matrice grezza.

La relazione negativa con O₃ $(r = -0,720)$ è particolarmente significativa. Essa riflette processi fotochimici noti, tra cui la titolazione dell'ozono da parte del monossido di azoto nelle aree ad alta emissione di NOₓ e l'anti-correlazione stagionale tra episodi invernali di accumulo di NO₂ e condizioni estive favorevoli alla formazione di O₃. Anche la correlazione negativa con la latitudine $(r = -0,421)$ è interpretabile in termini spaziali: i valori più elevati di NO₂ tendono a concentrarsi nelle aree più meridionali e pianeggianti del dominio, maggiormente urbanizzate e più soggette a ristagno, mentre le aree alpine o meno emissive mostrano concentrazioni inferiori.

L'analisi temporale rafforza il ruolo del proxy satellitare diretto. Alla scala giornaliera, la combinazione ottimale per NO₂ è S5P NO₂ con lag 0 e media mobile a 14 giorni, con $r = 0,819$ e deviazione standard spaziale pari a 0,130. Alla scala settimanale, lo stesso proxy raggiunge $r = 0,878$ con lag 0 e media mobile di 4 settimane. La stabilità del lag nullo indica coerenza temporale tra il campo modellato e l'osservazione satellitare, mentre l'aumento della correlazione con la media mobile indica che il segnale NO₂ emerge con maggiore chiarezza quando il rumore meteorologico giornaliero viene attenuato.

Tra i proxy alternativi, l'Aerosol Index mostra una correlazione giornaliera forte $(r = 0,606)$ e settimanale forte $(r = 0,650)$, mentre HCHO mostra una correlazione negativa moderata-forte $(r = -0,515$ giornaliero; $r = -0,628$ settimanale). Questa anti-correlazione può riflettere differenze stagionali e spaziali tra aree dominate da emissioni primarie di NO₂ e condizioni fotochimiche più favorevoli alla produzione di composti organici ossidati.

![Figura 2 - Matrice giornaliera NO2 vs NO2 TROPOMI](risultati_correlazione/matrice_giornaliero_NO2_NO2_TROPOMI.png)

*Figura 2.  heatmap lag-media mobile per NO₂ e NO₂ TROPOMI.*

### O₃

L'ozono mostra il comportamento più nettamente anti-correlato rispetto agli inquinanti primari. La correlazione con NO₂ è forte e negativa $(r = -0,720)$, e la correlazione con S5P NO₂ è moderata-negativa $(r = -0,563)$. Anche PM2.5 $(r = -0,541)$ e PM10 $(r = -0,466)$ risultano negativamente associati a O₃. Questo quadro è coerente con la letteratura atmosferica: l'O₃ troposferico è un inquinante secondario, la cui concentrazione dipende da radiazione solare, temperatura, disponibilità di NOₓ e composti organici volatili, nonché dai regimi chimici locali. In contesti urbani ad alta emissione di NO, l'ozono può essere consumato per titolazione, generando anti-correlazione con NO₂.

La correlazione positiva con HCHO nella matrice grezza è debole $(r = 0,139)$, ma diventa più rilevante nell'analisi temporale: O₃ e HCHO raggiungono $r = 0,499$ alla scala giornaliera e $r = 0,633$ alla scala settimanale. Questo risultato è fisicamente plausibile, poiché HCHO è un indicatore di attività fotochimica e di ossidazione dei composti organici volatili, processi che partecipano alla formazione di ozono.

La relazione più forte dell'analisi temporale è tuttavia quella negativa tra O₃ e il proxy NO₂. Alla scala giornaliera, il valore ottimale è $r = -0,820$, con lag 7 giorni e media mobile a 14 giorni. Alla scala settimanale, il valore raggiunge $r = -0,877$, con lag 2 settimane e media mobile a 4 settimane. La presenza di lag positivo suggerisce che l'anti-correlazione non è soltanto istantanea, ma riflette anche persistenza meteorologica, stagionalità e tempi di risposta chimico-trasportistica. Nella rappresentazione aggregata, il fit lineare tra O₃ e il proxy NO₂ presenta $r = -0,826$ e $R^2 = 0,682$, mentre un fit polinomiale di secondo grado raggiunge $R^2 = 0,777$, suggerendo una possibile componente non lineare nella relazione.

Il fatto che la colonna S5P O₃ non risulti fortemente correlata con l'O₃ superficiale $(r = 0,059$ nella matrice grezza; $r = 0,155$ nel migliore caso giornaliero) è coerente con la natura verticale della misura satellitare. La colonna totale di ozono è dominata in larga misura dal contributo stratosferico e non rappresenta direttamente la concentrazione troposferica al suolo.

![Figura 3 - Matrice giornaliera O3 vs proxy NO2](risultati_correlazione/matrice_giornaliero_O3_NO2_proxy.png)

*Figura 3.  heatmap lag-media mobile per O₃ e proxy NO₂.*

### PM10

PM10 mostra una correlazione molto forte con PM2.5 $(r = 0,977)$ e con NO₂ $(r = 0,810)$. La prima relazione riflette la sovrapposizione tra frazioni granulometriche e la presenza di processi comuni di accumulo e aerosol secondario. La seconda indica che il particolato è associato a contesti emissivi simili a quelli degli ossidi di azoto, sebbene il PM10 includa anche contributi da risospensione, polveri naturali, trasporto regionale e particelle grossolane.

La correlazione con S5P NO₂ è moderata $(r = 0,490)$, mentre quella con Aerosol Index è debole $(r = 0,241)$ nella matrice grezza. Nell'analisi lag-media mobile, il miglior proxy per PM10 risulta essere S5P NO₂, con $r = 0,509$ alla scala giornaliera, lag 1 giorno e media mobile a 14 giorni. Alla scala settimanale il coefficiente aumenta a $r = 0,544$, con lag 0 e media mobile a 4 settimane. La rappresentazione aggregata produce invece un coefficiente pooled più alto, pari a $r = 0,773$ e $R^2 = 0,598$. Questa differenza indica che una quota importante dell'associazione è spiegata dal gradiente spaziale tra aree più e meno inquinate, oltre che dalla dinamica temporale locale.

L'Aerosol Index raggiunge $r = 0,468$ alla scala giornaliera e $r = 0,498$ alla scala settimanale, valori moderati che ne confermano l'utilità come indicatore indiretto ma non specifico del particolato. Questo indice non rappresenta una misura satellitare diretta del PM10: è lo stesso prodotto AI utilizzato anche nell'analisi del PM2.5. HCHO mostra correlazioni negative più contenute $(r = -0,281$ giornaliero; $r = -0,375$ settimanale), mentre SO₂ rimane debole $(r = 0,143$ giornaliero; $r = 0,187$ settimanale).

La correlazione negativa con O₃ $(r = -0,466)$ può essere letta come effetto della contrapposizione tra condizioni meteorologiche favorevoli all'accumulo di particolato, spesso associate a stabilità e ridotto rimescolamento, e condizioni fotochimiche più favorevoli alla formazione di ozono. Anche la correlazione negativa con la latitudine $(r = -0,430)$ suggerisce una maggiore incidenza del PM10 nelle aree più pianeggianti e antropizzate.

![Figura 4 - Matrice giornaliera PM10 vs proxy NO2](risultati_correlazione/matrice_giornaliero_PM10_NO2_proxy.png)

*Figura 4.  heatmap lag-media mobile per PM10 e proxy NO₂.*

### PM2.5

PM2.5 presenta una struttura di correlazione molto simile a PM10. La correlazione con PM10 è quasi perfetta $(r = 0,977)$, mentre quella con NO₂ è molto forte $(r = 0,825)$. Ciò evidenzia la condivisione di sorgenti e condizioni atmosferiche favorevoli all'accumulo, ma suggerisce anche attenzione alla ridondanza informativa tra predittori nella modellazione successiva.

La correlazione grezza con S5P NO₂ è moderata $(r = 0,511)$, leggermente superiore a quella osservata per PM10. Nell'analisi temporale, il proxy NO₂ è il più informativo anche per PM2.5, con $r = 0,542$ alla scala giornaliera, lag 1 giorno e media mobile a 14 giorni, e $r = 0,596$ alla scala settimanale, lag 0 e media mobile a 4 settimane. Nella rappresentazione aggregata, il coefficiente pooled è $r = 0,788$, con $R^2 = 0,621$, indicando una relazione forte quando si considera congiuntamente il gradiente spaziale e la variabilità temporale.

L'Aerosol Index presenta correlazioni moderate $(r = 0,467$ giornaliero; $r = 0,507$ settimanale), compatibili con il suo ruolo di proxy satellitare dell'aerosol atmosferico. Anche in questo caso, il valore AI è lo stesso utilizzato per PM10, poiché Sentinel-5P non distingue direttamente tra particolato grossolano e particolato fine. La relazione con HCHO è negativa e moderata alla scala settimanale $(r = -0,440)$, mentre SO₂ rimane debole. La correlazione negativa con O₃ $(r = -0,541)$ è più marcata rispetto al PM10 e può riflettere la maggiore sensibilità della frazione fine alle condizioni di stabilità, accumulo e formazione di aerosol secondario in periodi meno favorevoli alla produzione fotochimica di ozono.

![Figura 5 - Matrice giornaliera PM2.5 vs proxy NO2](risultati_correlazione/matrice_giornaliero_PM25_NO2_proxy.png)

*Figura 5.  heatmap lag-media mobile per PM2.5 e proxy NO₂.*

### Effetto della scala temporale e delle medie mobili

Il confronto tra scala giornaliera e settimanale mostra un incremento sistematico dei coefficienti ottimali. NO₂ passa da $r = 0,819$ a $r = 0,878$, O₃ da $r = -0,820$ a $r = -0,877$, PM10 da $r = 0,509$ a $r = 0,544$, e PM2.5 da $r = 0,542$ a $r = 0,596$. Tale incremento è coerente con l'idea che l'aggregazione temporale attenui rumore, disallineamenti giornalieri, nuvolosità, variabilità del vento e incertezze di osservazione satellitare.

| Inquinante | Proxy ottimale giornaliero | Lag | Media mobile | $r$ giornaliero | Proxy ottimale settimanale | Lag | Media mobile | $r$ settimanale |
|---|---|---:|---:|---:|---|---:|---:|---:|
| NO₂ | NO₂ TROPOMI | 0 giorni | 14 giorni | 0,819 | NO₂ TROPOMI | 0 settimane | 4 settimane | 0,878 |
| O₃ | NO₂ proxy | 7 giorni | 14 giorni | -0,820 | NO₂ proxy | 2 settimane | 4 settimane | -0,877 |
| PM10 | NO₂ proxy | 1 giorno | 14 giorni | 0,509 | NO₂ proxy | 0 settimane | 4 settimane | 0,544 |
| PM2.5 | NO₂ proxy | 1 giorno | 14 giorni | 0,542 | NO₂ proxy | 0 settimane | 4 settimane | 0,596 |

L'uso di finestre mobili ampie, in particolare 14 giorni alla scala giornaliera, suggerisce che la relazione tra CAMS e Sentinel-5P è più robusta su scale sinottiche o sub-stagionali rispetto al singolo giorno. Questo è particolarmente plausibile per NO₂, che presenta elevata variabilità spaziale e temporale, e per il particolato, che risente di accumulo, persistenza e condizioni meteorologiche pregresse.

![Figura 6 - Confronto giornaliero e settimanale](risultati_correlazione/confronto_giornaliero_settimanale.png)

*Figura 6. confronto tra coefficienti ottimali giornalieri e settimanali.*

### Interpretazione dei diagrammi di dispersione e best fitting

I diagrammi di dispersione e best fitting rappresentano, per ciascun inquinante, la relazione tra la variabile CAMS e il proxy Sentinel-5P selezionato come ottimale dall'analisi lag-media mobile. A differenza delle heatmap, che sintetizzano il comportamento medio delle correlazioni al variare della finestra temporale, questi grafici mostrano direttamente la forma della relazione tra le coppie ottimali e permettono di valutare dispersione, linearità, presenza di curvature e stabilità del segnale.

![Figura 7 - Dispersione e best fitting giornaliero](risultati_correlazione/dispersione_bestfit_giornaliero.png)

*Figura 7.  diagrammi di dispersione e best fitting per le coppie ottimali alla scala giornaliera.*

| Inquinante | Coppia ottimale | Lag | Media mobile | $r$ pooled | $R^2$ lineare | $R^2$ polinomiale |
|---|---|---:|---:|---:|---:|---:|
| NO₂ | CAMS NO₂ - S5P NO₂ TROPOMI | 0 giorni | 14 giorni | 0,856 | 0,733 | 0,734 |
| O₃ | CAMS O₃ - S5P NO₂ proxy | 7 giorni | 14 giorni | -0,826 | 0,682 | 0,777 |
| PM10 | CAMS PM10 - S5P NO₂ proxy | 1 giorno | 14 giorni | 0,773 | 0,598 | 0,598 |
| PM2.5 | CAMS PM2.5 - S5P NO₂ proxy | 1 giorno | 14 giorni | 0,788 | 0,621 | 0,626 |

Le equazioni dei modelli di best fitting sono riportate di seguito. In ciascun caso, $x$ rappresenta la variabile CAMS alla combinazione temporale ottimale, mentre $y$ rappresenta il proxy Sentinel-5P corrispondente. Per NO₂, PM10 e PM2.5 il modello lineare è sufficiente a descrivere la struttura principale della relazione; per O₃, invece, il modello polinomiale di secondo grado descrive meglio la curvatura osservata nel diagramma di dispersione.

| Inquinante | Modello lineare | Modello polinomiale di secondo grado |
|---|---|---|
| NO₂ | $y = 7,702 \times 10^{-6}x + 4,532 \times 10^{-6}$ | $y = 1,728 \times 10^{-8}x^2 + 7,113 \times 10^{-6}x + 7,723 \times 10^{-6}$ |
| O₃ | $y = -2,563 \times 10^{-6}x + 2,385 \times 10^{-4}$ | $y = 3,697 \times 10^{-8}x^2 - 6,464 \times 10^{-6}x + 3,191 \times 10^{-4}$ |
| PM10 | $y = 5,475 \times 10^{-6}x - 1,516 \times 10^{-6}$ | $y = 1,009 \times 10^{-8}x^2 + 4,972 \times 10^{-6}x + 2,975 \times 10^{-6}$ |
| PM2.5 | $y = 6,030 \times 10^{-6}x + 1,414 \times 10^{-5}$ | $y = -4,478 \times 10^{-8}x^2 + 7,946 \times 10^{-6}x + 1,135 \times 10^{-6}$ |

Per NO₂, il diagramma evidenzia una relazione positiva molto forte tra concentrazione modellata e colonna troposferica S5P. Il coefficiente pooled pari a $r = 0,856$ e il valore $R^2 = 0,733$ indicano che oltre il 73% della variabilità del proxy satellitare, nel dominio aggregato, è spiegata dalla relazione lineare con il NO₂ CAMS alla finestra ottimale. La sovrapposizione quasi perfetta tra fit lineare e fit polinomiale $(R^2 = 0,734)$ suggerisce che, per questa coppia, la componente lineare descrive già in modo adeguato la relazione principale. L'equazione lineare $y = 7,702 \times 10^{-6}x + 4,532 \times 10^{-6}$ ha pendenza positiva, coerente con l'aumento della colonna troposferica S5P all'aumentare della concentrazione modellata. La dispersione residua è comunque attesa, poiché il satellite osserva una colonna atmosferica mentre CAMS rappresenta la concentrazione prossima al suolo.

Per O₃, il grafico mostra una relazione negativa marcata con il proxy NO₂, con $r = -0,826$. Questo risultato conferma in forma visiva l'anti-correlazione tra ozono e ossidi di azoto già osservata nelle matrici. Il valore $R^2$ del fit lineare è pari a 0,682, ma il fit polinomiale di secondo grado raggiunge $R^2 = 0,777$, con un incremento di circa 0,095. Si tratta del miglioramento più evidente tra tutte le coppie analizzate e indica che O₃ è l'unico caso in cui il modello quadratico è sostanzialmente più appropriato del modello lineare. La curvatura del modello $y = 3,697 \times 10^{-8}x^2 - 6,464 \times 10^{-6}x + 3,191 \times 10^{-4}$ è fisicamente coerente con la chimica dell'ozono: a basse e medie concentrazioni di O₃ il legame con NO₂ può variare rapidamente, mentre a concentrazioni più elevate intervengono regimi fotochimici, stagionalità e condizioni meteorologiche che modificano la pendenza della relazione. In altri termini, l'anti-correlazione O₃-NO₂ non è semplicemente una retta decrescente, ma presenta una risposta non lineare compatibile con processi di titolazione, produzione fotochimica e transizione tra regimi atmosferici differenti.

Per PM10 e PM2.5, i diagrammi mostrano relazioni positive con il proxy NO₂, con $r = 0,773$ per PM10 e $r = 0,788$ per PM2.5. In entrambi i casi, il fit lineare e quello polinomiale producono valori di $R^2$ quasi identici: per PM10 il valore resta pari a circa 0,598, mentre per PM2.5 passa solo da 0,621 a 0,626. Questo incremento minimo non giustifica l'introduzione di una componente quadratica come descrizione principale. Le equazioni lineari $y = 5,475 \times 10^{-6}x - 1,516 \times 10^{-6}$ per PM10 e $y = 6,030 \times 10^{-6}x + 1,414 \times 10^{-5}$ per PM2.5 descrivono quindi in modo più parsimonioso la relazione osservata. Ciò suggerisce che la relazione principale è sostanzialmente monotona e ben rappresentata da un andamento lineare a scala aggregata. Il fatto che il miglior proxy risulti NO₂ e non l'Aerosol Index indica che, nel dominio analizzato, il gradiente emissivo e antropico associato agli ossidi di azoto è più informativo del solo carico aerosolico satellitare. Questo non significa che NO₂ misuri il particolato, ma che agisce come indicatore indiretto delle condizioni emissive e territoriali che favoriscono anche PM10 e PM2.5.

La differenza tra i coefficienti pooled dei grafici di dispersione e gli $r$ medi per punto riportati nelle matrici è metodologicamente rilevante. I coefficienti pooled sono più elevati perché combinano variabilità spaziale e temporale: includono quindi il contrasto tra aree molto inquinate e aree meno inquinate. Gli $r$ medi per punto, invece, descrivono meglio la coerenza temporale locale. Questa distinzione è essenziale per la modellazione successiva: un predittore può apparire molto forte nel dominio aggregato perché cattura il gradiente spaziale, ma essere meno efficace nel descrivere le variazioni giorno-per-giorno in un singolo punto.

### Ruolo del vento e del trasporto atmosferico

Le correlazioni lineari dirette tra velocità del vento e indicatori satellitari sono generalmente deboli o trascurabili. Per S5P NO₂, il coefficiente passa da $r = -0,024$ usando il vento giornaliero a $r = -0,033$ usando il vento medio nelle 3 ore precedenti il passaggio satellitare. Per S5P O₃, la relazione è leggermente più evidente e positiva, da $r = 0,117$ a $r = 0,158$ usando la finestra pre-13 di 6 ore. Aerosol Index, SO₂ e HCHO mostrano valori prossimi a zero.

Questi risultati non implicano che il vento sia fisicamente irrilevante. La velocità del vento influenza dispersione, ventilazione, trasporto e diluizione degli inquinanti, ma il suo effetto può essere non lineare, dipendere dalla direzione, dalla posizione delle sorgenti e dalla stabilità atmosferica. Un coefficiente di Pearson calcolato sull'intero dominio può quindi risultare basso anche in presenza di effetti locali importanti.

L'analisi per settore di provenienza del vento mostra differenze più informative. Per NO₂, la correlazione con S5P NO₂ rimane elevata in tutti i settori, da $r = 0,794$ con vento da Nord a $r = 0,831$ con vento da Sud. Per O₃, la correlazione negativa con il proxy NO₂ è più intensa nei settori Nord, Est e Sud $(r \approx -0,80)$, mentre risulta leggermente meno marcata con vento da Ovest $(r = -0,769)$. Per PM10 e PM2.5, i coefficienti aumentano nei settori Sud e Ovest, raggiungendo rispettivamente $r = 0,595$ per PM10 e $r = 0,645$ per PM2.5 con vento da Ovest. Questo comportamento può indicare un contributo del trasporto orizzontale e della localizzazione delle sorgenti sopravento.

![Figura 8 - Correlazione vento e indici S5P](risultati_correlazione/vento_pre13_correlazioni_s5p.png)

*Figura 8. confronto delle correlazioni tra indici S5P e finestre di vento.*

### Interpretazione fisica delle variabili meteorologiche e statiche

Le correlazioni osservate devono essere interpretate nel quadro dei processi fisici che regolano concentrazione, dispersione e trasformazione degli inquinanti.

Per NO₂, le correlazioni positive con indicatori emissivi e antropici, come S5P NO₂ e particolato, sono coerenti con la natura primaria dell'inquinante e con la sua associazione a traffico, combustione e densità urbana. La correlazione negativa attesa tra NO₂ e altezza dello strato limite atmosferico, quando questa variabile è disponibile, deriva dal fatto che uno strato limite più alto aumenta il volume di rimescolamento e diluisce gli inquinanti emessi al suolo. Viceversa, condizioni di inversione termica e basso strato limite favoriscono l'accumulo.

Per O₃, la relazione negativa con NO₂ deriva sia da processi chimici rapidi, come la titolazione da parte del NO, sia da differenze stagionali e spaziali tra contesti urbani e rurali. Temperature elevate e forte radiazione solare tendono a favorire la formazione fotochimica dell'ozono; pertanto, in dataset che includano temperatura o radiazione, ci si attende spesso una correlazione positiva con O₃ e negativa o più complessa con NO₂.

Per PM10 e PM2.5, le relazioni positive con NO₂ e Aerosol Index indicano l'importanza di sorgenti comuni e di aerosol atmosferico. L'Aerosol Index deve però essere interpretato come unico proxy comune per entrambe le frazioni, non come misura separata di PM10 o PM2.5 da parte di Sentinel-5P. Le condizioni meteorologiche favorevoli all'accumulo includono bassa velocità del vento, elevata stabilità, scarso rimescolamento verticale e assenza di precipitazioni. L'umidità relativa può favorire la crescita igroscopica delle particelle e la formazione di aerosol secondario, mentre la precipitazione tende a ridurre le concentrazioni attraverso rimozione umida. La velocità del vento può avere un duplice effetto: favorisce la dispersione, ma può anche contribuire alla risospensione di polveri grossolane, soprattutto per PM10.

Le coordinate geografiche agiscono come proxy statici della struttura territoriale. La correlazione negativa della latitudine con NO₂, PM10 e PM2.5 indica che le aree più settentrionali e alpine del dominio tendono ad avere concentrazioni inferiori rispetto alle zone più meridionali e pianeggianti, dove la densità emissiva e le condizioni di ristagno sono maggiori. La latitudine positiva, seppure debole, con O₃ è coerente con concentrazioni relativamente più alte in aree meno soggette a titolazione da NO.

### Interpretazione degli andamenti temporali nei punti rappresentativi

Gli andamenti temporali dei quattro inquinanti sono stati analizzati su due punti rappresentativi: un punto alpino/rurale localizzato in Alta Valtellina, punto 15, e un punto urbano localizzato a Milano centro, punto 736. Il periodo considerato va dal 1 febbraio al 30 aprile 2025. Per ogni inquinante sono riportate la serie CAMS, la serie S5P corrispondente o il relativo proxy, e le medie mobili del segnale satellitare a 3, 7 e 14 giorni.

Questi grafici hanno una funzione complementare rispetto alle matrici di correlazione. Le matrici sintetizzano il comportamento medio del dominio, mentre gli andamenti temporali permettono di verificare come la relazione CAMS-S5P cambi localmente in funzione del contesto emissivo e geografico. Il confronto tra Milano e Alta Valtellina evidenzia infatti che la qualità dell'associazione non è uniforme nello spazio.

![Figura 9 - Andamento temporale Alta Valtellina](risultati_correlazione/andamento_alpina_alta_valtellina.png)

*Figura 9. andamento temporale dei quattro inquinanti nel punto alpino/rurale dell'Alta Valtellina.*

Nel punto alpino/rurale, i coefficienti giornalieri sono deboli per tutti gli inquinanti: $r = 0,094$ per NO₂, $r = -0,155$ per O₃, $r = 0,106$ per PM10 e $r = 0,133$ per PM2.5. Anche l'applicazione della media mobile a 14 giorni non migliora la coerenza in modo sistematico: NO₂ diventa negativo $(r = -0,283)$, O₃ resta quasi nullo $(r = -0,035)$, PM10 è prossimo a zero $(r = -0,021)$ e PM2.5 rimane trascurabile $(r = -0,026)$.

Questo comportamento indica che, in un contesto alpino a basse concentrazioni e con forte influenza orografica, la relazione tra colonna satellitare e concentrazione superficiale può essere debole o instabile. Le concentrazioni CAMS mostrano episodi locali e variazioni rapide, mentre i proxy S5P, soprattutto dopo media mobile, descrivono un segnale più smussato e integrato verticalmente. La discrepanza può essere dovuta alla complessità del profilo verticale in area montana, alla rappresentatività spaziale del pixel satellitare, alla copertura nuvolosa e alla maggiore distanza dalle sorgenti primarie. Nel caso del particolato, l'uso dello stesso Aerosol Index per PM10 e PM2.5 conferma una dinamica satellitare comune, ma non necessariamente allineata alla variabilità superficiale locale.

![Figura 10 - Andamento temporale Milano centro](risultati_correlazione/andamento_milano_centro.png)

*Figura 10. andamento temporale dei quattro inquinanti nel punto urbano di Milano centro.*

Nel punto urbano di Milano centro, la coerenza tra CAMS e S5P è più evidente. Per NO₂, la correlazione giornaliera è $r = 0,516$ e aumenta a $r = 0,670$ con media mobile a 14 giorni. Questo risultato è coerente con la presenza di sorgenti urbane persistenti e con un segnale satellitare NO₂ più robusto in aree ad alta densità emissiva. Il miglioramento con MA14 indica che il filtraggio temporale riduce il rumore giornaliero e rende più chiara la componente comune tra modello e satellite.

Per PM10 e PM2.5, le correlazioni giornaliere sono deboli ma positive $(r = 0,187$ e $r = 0,204)$, mentre con MA14 aumentano rispettivamente a $r = 0,442$ e $r = 0,464$. Ciò suggerisce che il proxy aerosolico e le condizioni emissive associate al contesto urbano diventano più informative quando si osservano scale temporali plurigiornaliere. Anche qui va ribadito che l'Aerosol Index è lo stesso per PM10 e PM2.5: la differenza tra i due coefficienti dipende dalla diversa serie CAMS confrontata con lo stesso indicatore satellitare.

Per O₃, invece, la correlazione locale a Milano è quasi nulla sia su base giornaliera $(r = 0,038)$ sia con media mobile a 14 giorni $(r = 0,064)$. Questo risultato conferma che la colonna S5P O₃ non rappresenta direttamente l'ozono superficiale urbano. La dinamica locale dell'O₃ è controllata da chimica fotoossidativa, titolazione da NO, radiazione, temperatura e trasporto regionale, mentre la misura satellitare di O₃ risente fortemente della componente verticale e stratosferica.

| Inquinante | Milano $r$ giornaliero | Alta Valtellina $r$ giornaliero | Milano $r$ MA14 | Alta Valtellina $r$ MA14 |
|---|---:|---:|---:|---:|
| NO₂ | 0,516 | 0,094 | 0,670 | -0,283 |
| O₃ | 0,038 | -0,155 | 0,064 | -0,035 |
| PM10 | 0,187 | 0,106 | 0,442 | -0,021 |
| PM2.5 | 0,204 | 0,133 | 0,464 | -0,026 |

Il confronto tra i due punti mostra quindi un risultato chiave: l'accordo CAMS-S5P è più forte nel contesto urbano di Milano, dove il segnale emissivo è intenso e persistente, mentre è debole nel punto alpino/rurale, dove prevalgono bassi livelli di concentrazione, condizioni orografiche e maggiore complessità nella relazione tra colonna atmosferica e superficie. Le serie temporali confermano inoltre l'utilità delle medie mobili: esse migliorano l'interpretabilità del segnale urbano per NO₂ e particolato, ma non risolvono le discrepanze nei contesti complessi o a bassa concentrazione.

La figura relativa al punto 15 con tutti gli inquinanti rappresenta lo stesso caso alpino dell'Alta Valtellina e va letta come approfondimento locale del comportamento rurale. Le ulteriori serie rappresentative, relative a Milano centro, hinterland nord, area rurale della Pianura Padana e Alta Valtellina, consentono di confrontare qualitativamente regimi territoriali differenti. In generale, le aree urbane e periurbane mostrano segnali più strutturati per NO₂ e particolato, mentre le aree rurali e alpine presentano relazioni più deboli e maggiore sensibilità alle condizioni meteorologiche e al trasporto regionale.

## Implicazioni per la selezione delle feature

L'analisi suggerisce che le variabili più informative per NO₂ sono S5P NO₂, Aerosol Index e le coordinate spaziali, con particolare attenzione alla latitudine. Per O₃, il proxy NO₂ risulta il predittore più rilevante in senso negativo, seguito da HCHO come indicatore di attività fotochimica. Per PM10 e PM2.5, i risultati indicano l'utilità di S5P NO₂ e dell'Aerosol Index, tenendo presente che l'AI è un unico proxy comune del carico aerosolico e non una misura distinta delle due frazioni granulometriche, oltre alla forte informazione contenuta nei gradienti spaziali.

La selezione delle feature per i modelli successivi dovrebbe tuttavia considerare la ridondanza tra variabili. PM10 e PM2.5 sono quasi perfettamente correlati, e NO₂ è fortemente associato al particolato. L'inclusione simultanea di predittori molto correlati può migliorare apparentemente l'adattamento, ma rendere instabili i coefficienti nei modelli lineari e complicare l'interpretazione fisica. Per modelli sensibili alla multicollinearità, sarà quindi opportuno valutare tecniche di selezione, regolarizzazione o riduzione dimensionale.

## Limiti dell'analisi di correlazione

L'analisi di correlazione presenta diversi limiti metodologici che devono essere esplicitati.

In primo luogo, la correlazione non implica causalità. Un coefficiente elevato tra due variabili indica co-variazione lineare, ma non dimostra che una variabile determini l'altra. Le associazioni possono essere mediate da fattori comuni, come stagionalità, meteorologia, distribuzione spaziale delle sorgenti o condizioni sinottiche.

In secondo luogo, il coefficiente di Pearson misura esclusivamente relazioni lineari. Relazioni non lineari, soglie, saturazioni o dipendenze condizionate possono non essere catturate. Il caso di O₃ è emblematico: la relazione con NO₂ e HCHO può essere fortemente non lineare, poiché dipende dal regime chimico NOₓ-limitato o VOC-limitato, dalla radiazione e dalla temperatura.

In terzo luogo, la presenza di autocorrelazione temporale e spaziale può influenzare i risultati. Le osservazioni giornaliere consecutive non sono indipendenti, così come punti geograficamente vicini possono condividere condizioni emissive e meteorologiche. Ciò può aumentare la significatività apparente dei coefficienti e richiede cautela nell'interpretazione statistica.

Un ulteriore limite riguarda la differenza fisica tra grandezze confrontate. Le misure satellitari rappresentano colonne atmosferiche o indicatori integrati verticalmente, mentre gli inquinanti CAMS analizzati sono riferiti alla concentrazione prossima al suolo. Disallineamenti verticali, nuvolosità, errori di retrieval e diversa risoluzione spaziale possono ridurre o distorcere la correlazione.

Infine, variabili fortemente correlate tra loro possono introdurre multicollinearità nei modelli di regressione. Questo aspetto è particolarmente rilevante per PM10 e PM2.5, ma anche per NO₂ e particolato. La fase di modellazione dovrà quindi distinguere tra variabili realmente complementari e variabili che descrivono lo stesso gradiente spaziale o temporale.

## Sintesi critica

L'analisi di correlazione ha permesso di identificare un insieme di relazioni statistiche coerenti con i processi atmosferici noti. NO₂ si conferma fortemente associato agli indicatori emissivi e al particolato, e mostra una marcata anti-correlazione con O₃. L'ozono risulta governato da una dinamica opposta rispetto agli inquinanti primari, con una forte relazione negativa con il proxy NO₂ e una relazione positiva più evidente con HCHO su scale temporali aggregate. PM10 e PM2.5 mostrano una struttura comune, fortemente correlata con NO₂ e moderatamente associata allo stesso proxy satellitare dell'aerosol, cioè l'Aerosol Index.

L'uso di lag e medie mobili ha evidenziato che le relazioni più robuste emergono su finestre temporali più ampie, in particolare 14 giorni per il dato giornaliero e 4 settimane per quello settimanale. Questo risultato indica che l'informazione satellitare è maggiormente allineata al segnale medio e persistente degli inquinanti, piuttosto che alle fluttuazioni giornaliere di breve periodo.

Nel complesso, le evidenze ottenute supportano la successiva selezione delle feature per modelli di regressione e machine learning. S5P NO₂ risulta una feature chiave per NO₂, O₃ e particolato; Aerosol Index contribuisce alla descrizione del particolato e dei gradienti emissivi; HCHO fornisce informazione utile per i processi fotochimici; le coordinate geografiche rappresentano proxy statici importanti dei gradienti territoriali. La fase modellistica dovrà però integrare tali risultati con controlli di multicollinearità, validazione spaziale e temporale, ed eventuale inclusione di variabili meteorologiche aggiuntive capaci di rappresentare stabilità atmosferica, temperatura, umidità, precipitazione e altezza dello strato limite.

## Appendice: spazi per le figure

### Figure generali e riepiloghi

![Figura A1 - Panoramica dati](risultati_correlazione/panoramica_dati.png)

*Figura A1. Panoramica della copertura dei dati e delle caratteristiche del vento.*

![Figura A2 - Matrice generale giornaliera](risultati_correlazione/matrice_correlazione_variabili_giornaliero.png)

*Figura A2. Matrice generale di correlazione tra variabili giornaliere.*

![Figura A3 - Tabella riepilogo giornaliero](risultati_correlazione/tabella_riepilogo_giornaliero.png)

*Figura A3. Riepilogo dei proxy ottimali alla scala giornaliera.*

![Figura A4 - Tabella best fit giornaliero](risultati_correlazione/tabella_bestfit_giornaliero.png)

*Figura A4. Parametri dei fit lineari e polinomiali sulle combinazioni ottimali.*

![Figura A5 - Dispersione e best fit giornaliero](risultati_correlazione/dispersione_bestfit_giornaliero.png)

*Figura A5. Diagrammi di dispersione e best fitting per le coppie ottimali.*

![Figura A6 - Confronto giornaliero settimanale](risultati_correlazione/confronto_giornaliero_settimanale.png)

*Figura A6. Confronto tra coefficienti ottimali giornalieri e settimanali.*

![Figura A7 - Confronto NO2 giornaliero settimanale](risultati_correlazione/proxy_confronto_NO2_giornaliero_settimanale.png)

*Figura A7. Confronto specifico dei proxy NO₂ tra scala giornaliera e settimanale.*

### Matrici giornaliere per NO₂

![Figura A8 - NO2 vs NO2 TROPOMI giornaliero](risultati_correlazione/matrice_giornaliero_NO2_NO2_TROPOMI.png)

*Figura A8. Matrice lag-media mobile per NO₂ e NO₂ TROPOMI.*

![Figura A9 - NO2 vs Colonna O3 giornaliero](risultati_correlazione/matrice_giornaliero_NO2_Colonna_O3.png)

*Figura A9. Matrice lag-media mobile per NO₂ e colonna O₃.*

![Figura A10 - NO2 vs Aerosol Index giornaliero](risultati_correlazione/matrice_giornaliero_NO2_Aerosol_Index.png)

*Figura A10. Matrice lag-media mobile per NO₂ e Aerosol Index.*

![Figura A11 - NO2 vs SO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_NO2_SO2_proxy.png)

*Figura A11. Matrice lag-media mobile per NO₂ e SO₂ proxy.*

![Figura A12 - NO2 vs HCHO proxy giornaliero](risultati_correlazione/matrice_giornaliero_NO2_HCHO_proxy.png)

*Figura A12. Matrice lag-media mobile per NO₂ e HCHO proxy.*

### Matrici giornaliere per O₃

![Figura A13 - O3 vs Colonna O3 giornaliero](risultati_correlazione/matrice_giornaliero_O3_Colonna_O3.png)

*Figura A13. Matrice lag-media mobile per O₃ e colonna O₃.*

![Figura A14 - O3 vs NO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_O3_NO2_proxy.png)

*Figura A14. Matrice lag-media mobile per O₃ e proxy NO₂.*

![Figura A15 - O3 vs HCHO proxy giornaliero](risultati_correlazione/matrice_giornaliero_O3_HCHO_proxy.png)

*Figura A15. Matrice lag-media mobile per O₃ e HCHO proxy.*

### Matrici giornaliere per PM10

![Figura A16 - PM10 vs Aerosol Index giornaliero](risultati_correlazione/matrice_giornaliero_PM10_Aerosol_Index.png)

*Figura A16. Matrice lag-media mobile per PM10 e Aerosol Index.*

![Figura A17 - PM10 vs NO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM10_NO2_proxy.png)

*Figura A17. Matrice lag-media mobile per PM10 e proxy NO₂.*

![Figura A18 - PM10 vs SO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM10_SO2_proxy.png)

*Figura A18. Matrice lag-media mobile per PM10 e SO₂ proxy.*

![Figura A19 - PM10 vs HCHO proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM10_HCHO_proxy.png)

*Figura A19. Matrice lag-media mobile per PM10 e HCHO proxy.*

### Matrici giornaliere per PM2.5

![Figura A20 - PM2.5 vs Aerosol Index giornaliero](risultati_correlazione/matrice_giornaliero_PM25_Aerosol_Index.png)

*Figura A20. Matrice lag-media mobile per PM2.5 e Aerosol Index.*

![Figura A21 - PM2.5 vs NO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM25_NO2_proxy.png)

*Figura A21. Matrice lag-media mobile per PM2.5 e proxy NO₂.*

![Figura A22 - PM2.5 vs SO2 proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM25_SO2_proxy.png)

*Figura A22. Matrice lag-media mobile per PM2.5 e SO₂ proxy.*

![Figura A23 - PM2.5 vs HCHO proxy giornaliero](risultati_correlazione/matrice_giornaliero_PM25_HCHO_proxy.png)

*Figura A23. Matrice lag-media mobile per PM2.5 e HCHO proxy.*

### Matrici settimanali

![Figura A24 - NO2 vs NO2 TROPOMI settimanale](risultati_correlazione/matrice_settimanale_NO2_NO2_TROPOMI.png)

*Figura A24. Matrice settimanale per NO₂ e NO₂ TROPOMI.*

![Figura A25 - NO2 vs Colonna O3 settimanale](risultati_correlazione/matrice_settimanale_NO2_Colonna_O3.png)

*Figura A25. Matrice settimanale per NO₂ e colonna O₃.*

![Figura A26 - NO2 vs Aerosol Index settimanale](risultati_correlazione/matrice_settimanale_NO2_Aerosol_Index.png)

*Figura A26. Matrice settimanale per NO₂ e Aerosol Index.*

![Figura A27 - NO2 vs SO2 proxy settimanale](risultati_correlazione/matrice_settimanale_NO2_SO2_proxy.png)

*Figura A27. Matrice settimanale per NO₂ e SO₂ proxy.*

![Figura A28 - NO2 vs HCHO proxy settimanale](risultati_correlazione/matrice_settimanale_NO2_HCHO_proxy.png)

*Figura A28. Matrice settimanale per NO₂ e HCHO proxy.*

![Figura A29 - O3 vs NO2 proxy settimanale](risultati_correlazione/matrice_settimanale_O3_NO2_proxy.png)

*Figura A29. Matrice settimanale per O₃ e proxy NO₂.*

![Figura A30 - PM10 vs NO2 proxy settimanale](risultati_correlazione/matrice_settimanale_PM10_NO2_proxy.png)

*Figura A30. Matrice settimanale per PM10 e proxy NO₂.*

![Figura A31 - PM2.5 vs NO2 proxy settimanale](risultati_correlazione/matrice_settimanale_PM25_NO2_proxy.png)

*Figura A31. Matrice settimanale per PM2.5 e proxy NO₂.*

### Confronti tra proxy e settori di vento

![Figura A32 - Proxy confronto NO2 giornaliero](risultati_correlazione/proxy_confronto_giornaliero_NO2.png)

*Figura A32. Confronto dei proxy giornalieri per NO₂.*

![Figura A33 - Proxy confronto O3 giornaliero](risultati_correlazione/proxy_confronto_giornaliero_O3.png)

*Figura A33. Confronto dei proxy giornalieri per O₃.*

![Figura A34 - Proxy confronto PM10 giornaliero](risultati_correlazione/proxy_confronto_giornaliero_PM10.png)

*Figura A34. Confronto dei proxy giornalieri per PM10.*

![Figura A35 - Proxy confronto PM2.5 giornaliero](risultati_correlazione/proxy_confronto_giornaliero_PM25.png)

*Figura A35. Confronto dei proxy giornalieri per PM2.5.*

![Figura A36 - Proxy confronto NO2 settimanale](risultati_correlazione/proxy_confronto_settimanale_NO2.png)

*Figura A36. Confronto dei proxy settimanali per NO₂.*

![Figura A37 - Settori vento NO2](risultati_correlazione/settori_giornaliero_NO2.png)

*Figura A37. Correlazione NO₂ per settore di provenienza del vento.*

![Figura A38 - Settori vento O3](risultati_correlazione/settori_giornaliero_O3.png)

*Figura A38. Correlazione O₃ per settore di provenienza del vento.*

![Figura A39 - Settori vento PM10](risultati_correlazione/settori_giornaliero_PM10.png)

*Figura A39. Correlazione PM10 per settore di provenienza del vento.*

![Figura A40 - Settori vento PM2.5](risultati_correlazione/settori_giornaliero_PM25.png)

*Figura A40. Correlazione PM2.5 per settore di provenienza del vento.*

### Analisi del vento pre-13

![Figura A41 - Distribuzioni vento pre-13](risultati_correlazione/vento_pre13_distribuzioni.png)

*Figura A41. Distribuzione delle metriche di vento giornaliero e pre-13.*

![Figura A42 - Scatter vento e indici S5P](risultati_correlazione/vento_pre13_scatter_s5p.png)

*Figura A42. Diagrammi di dispersione tra vento e indici S5P.*

![Figura A43 - Correlazioni vento e indici S5P](risultati_correlazione/vento_pre13_correlazioni_s5p.png)

*Figura A43. Confronto dei coefficienti vento-indici S5P per diverse finestre temporali.*

![Figura A44 - Confronto vento pre-13](risultati_correlazione/vento_pre13_confronto.png)

*Figura A44. Confronto tra metriche di vento.*

![Figura A45 - Soglia vento pre-13](risultati_correlazione/vento_pre13_soglia.png)

*Figura A45. Impatto della soglia di vento sulle diverse definizioni di ventilazione.*

### Serie temporali e casi rappresentativi

![Figura A46 - Andamento punto 15 tutti inquinanti](risultati_correlazione/andamento_punto_15_tutti_inquinanti.png)

*Figura A46. Andamento temporale dei quattro inquinanti per il punto rappresentativo 15.*

![Figura A47 - Andamento alpina Alta Valtellina](risultati_correlazione/andamento_alpina_alta_valtellina.png)

*Figura A47. Andamento temporale per l'area alpina dell'Alta Valtellina.*

![Figura A48 - Andamento Milano centro](risultati_correlazione/andamento_milano_centro.png)

*Figura A48. Andamento temporale per Milano centro.*

![Figura A49 - Serie alpina Alta Valtellina](risultati_correlazione/serie_alpina_alta_valtellina.png)

*Figura A49. Serie temporale sintetica per l'area alpina dell'Alta Valtellina.*

![Figura A50 - Serie Milano centro](risultati_correlazione/serie_milano_centro.png)

*Figura A50. Serie temporale sintetica per Milano centro.*

![Figura A51 - Serie Milano hinterland nord](risultati_correlazione/serie_milano_hinterland_nord.png)

*Figura A51. Serie temporale sintetica per Milano hinterland nord.*

![Figura A52 - Serie rurale Pianura Padana](risultati_correlazione/serie_rurale_pianura_padana.png)

*Figura A52. Serie temporale sintetica per area rurale della Pianura Padana.*
