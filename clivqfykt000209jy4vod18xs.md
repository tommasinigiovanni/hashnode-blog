---
title: "Come unire un Video e Audio"
seoTitle: "Come Unire Video e Audio con FFmpeg: Guida Passo-Passo"
seoDescription: "Scopri come unire facilmente un file video e un file audio utilizzando FFmpeg. Segui la nostra guida dettagliata passo-passo per risolvere i tuoi problemi m"
datePublished: Wed Jun 14 2023 13:13:14 GMT+0000 (Coordinated Universal Time)
cuid: clivqfykt000209jy4vod18xs
slug: come-unire-un-video-e-audio
tags: merge, video, audio, video-editing-app

---

# Il problema

È una situazione in cui molti di noi si sono ritrovati - hai due file separati, uno con il contenuto video che desideri, e l'altro che contiene l'audio corrispondente. Forse hai scaricato un film muto e una traccia di commento separata, o hai registrato una presentazione e l'audio separatamente. Qualunque sia il caso, ora ti trovi di fronte al compito di unire questi due in un unico file coeso. In questo post, esploreremo come fare esattamente questo utilizzando lo strumento FFmpeg.

# L'analisi

FFmpeg è un software libero e open source che consente di manipolare file multimediali. Può convertire audio e video in quasi qualsiasi formato, ed è molto utilizzato per lo streaming di video e audio.

# La soluzione

Il comando che utilizzeremo oggi è il seguente:

```bash
ffmpeg -i video.mp4 -i audio.mp4 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 output.mp4
```

Ora, facciamo un'analisi dettagliata di questo comando:

1. `ffmpeg`: È il comando che avvia il programma FFmpeg.
    
2. `-i video.mp4 -i audio.mp4`: Questi sono i file di input. Il primo `-i video.mp4` indica a FFmpeg che `video.mp4` è il nostro file video di input, mentre il secondo `-i audio.mp4` indica che `audio.mp4` è il nostro file audio di input.
    
3. `-c:v copy -c:a aac`: Queste opzioni definiscono i codec che vogliamo utilizzare. `-c:v copy` dice a FFmpeg di copiare il codec video originale. `-c:a aac` dice a FFmpeg di usare il codec audio AAC per il file di output.
    
4. `-map 0:v:0 -map 1:a:0`: Questi comandi mappano i flussi di input al file di output. `-map 0:v:0` dice a FFmpeg di prendere il primo flusso video (v:0) dal primo file di input (0) e `-map 1:a:0` dice di prendere il primo flusso audio (a:0) dal secondo file di input (1).
    
5. `output.mp4`: Questo è il nome del file di output. Il file risultante sarà un file .mp4 con il video dal primo file di input e l'audio dal secondo file di input.
    

Ecco fatto! Con un solo comando, potete unire un video con un audio. Spero che questo post vi sia stato utile. Se avete domande o commenti, non esitate a lasciarli qui sotto.