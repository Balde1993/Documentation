# Tools 
Per facilitare la gestione e la produzione della documentazione a livello di progetto facciamo uso di due script. 

Lo script ***plantuml_render*** prende come argomento un file con estensione .txt ed esegue il render del file chiamado il ***.jar*** fornito da plantUML. 


Lo script ***plantuml_project_render*** prende come argomento il ***path*** della cartella di progetto della documentazione. 
I passi d'esecuzione impementati dallo script sono i sequenti: 

 1. Cerca ricorsivamente a partire dal ***path*** tutti i file con estensione .txt 
    1. Per ognuno dei file trovati esegue il rendering di plantUML sfruttando lo script ***plantuml_render*** (producendo l'omonimo diagramma con estensione .png)
 2. Cerca ricorsivamente a partire dal ***path*** tutti i file con estensione .png
    1. Scansiona il nome del file cercando keyword che indichino la tipologia di diagramma (class, usecase, sequence, activity etc) 
    2. Sposta il file nella cartella associata al tipo di diagramma 
