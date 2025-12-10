Dama AI: Deep Reinforcement Learning Project

Questo progetto implementa un agente intelligente capace di imparare a giocare a Dama (Checkers) autonomamente utilizzando il Deep Reinforcement Learning (DQN).
Il sistema simula un ambiente di gioco, addestra una rete neurale tramite l'esperienza e permette di sfidare l'IA tramite un'interfaccia grafica interattiva.
📋 Indice
Requisiti e Librerie
Architettura del Progetto
Dettaglio delle Classi e Funzioni
Logica di Apprendimento (DQN)
Action Masking

##Guida all'Uso: Training e Test
1. Requisiti e Librerie
Il progetto è scritto in Python. Di seguito le librerie principali utilizzate e il loro scopo:
gymnasium: Framework standard per creare ambienti di Reinforcement Learning. Gestisce lo stato, le azioni e le regole del gioco.
torch (PyTorch): Libreria di Deep Learning utilizzata per costruire, addestrare ed eseguire la Rete Neurale (DQN).
numpy: Fondamentale per la manipolazione delle matrici (la scacchiera è una matrice 8x8) e calcoli numerici veloci.
pygame: Libreria per la creazione dell'interfaccia grafica (GUI) 2D, utilizzata per visualizzare la scacchiera e gestire gli input del mouse.
sys, time, random: Librerie standard per gestione sistema, tempo e generazione di numeri casuali.
Per installare tutto il necessario:
code
Bash
pip install gymnasium torch numpy pygame

2. Architettura del Progetto
Il codice è modulare e si divide in tre componenti fondamentali:
L'Ambiente (CheckersEnv): Il "mondo" del gioco. Conosce le regole, muove i pezzi e calcola i punteggi.
L'Agente (DQN): Il "cervello". Una rete neurale che osserva la scacchiera e decide quale mossa fare.
L'Interfaccia (Main GUI): Il sistema visuale che permette all'umano di vedere la partita e giocare contro l'Agente.

3. Dettaglio delle Classi e Funzioni
A. Classe CheckersEnv (L'Ambiente)
Questa classe estende gym.Env e gestisce la logica della Dama.
__init__: Inizializza lo spazio delle azioni (4096 mosse possibili) e lo spazio delle osservazioni (matrice 8x8).
reset(): Pulisce la scacchiera, riposiziona le pedine e resetta il turno al NERO.
step(action): Esegue una mossa nel simulatore.
Input: Un intero che rappresenta la mossa (es. indice 1024).
Output: La nuova scacchiera, la ricompensa (+1, +10, etc.), se il gioco è finito (done) e info extra.
_find_all_legal_moves(): [CRITICO] Calcola tutte le mosse valide per il giocatore corrente. Implementa la regola della cattura obbligatoria (se puoi mangiare, devi mangiare).
_get_piece_moves / _get_piece_captures: Funzioni ausiliarie che calcolano rispettivamente i movimenti semplici e i salti (mangiate) per ogni singola pedina o Dama.
_execute_move(): Sposta fisicamente il pezzo nella matrice, rimuove i pezzi mangiati e gestisce la promozione a Dama.
B. Classe DQN (La Rete Neurale)
Una Convolutional Neural Network (CNN) progettata per analizzare la scacchiera.
Input: Immagine della scacchiera (Tensor 1x8x8).
Strati Convoluzionali (self.conv): Due livelli di convoluzione per estrarre pattern spaziali (es. pedine in diagonale, minacce, difese).
Strati Lineari (self.fc): Trasformano le feature estratte in valori decisionali.
Output: Un vettore di 4096 valori (Q-Values). Ogni valore rappresenta quanto è "buona" una specifica mossa (es. da A1 a B2).
C. Funzioni di Supporto GUI
get_row_col_from_mouse(pos): Converte le coordinate pixel del click del mouse (es. x=120, y=200) in coordinate della matrice (riga=2, colonna=1).
main_gui(): Il ciclo principale del gioco grafico. Gestisce gli eventi Pygame, il rendering della scacchiera e l'alternanza dei turni Umano/IA.

4. Logica di Apprendimento (DQN)
L'algoritmo utilizzato è il Deep Q-Learning.
Esplorazione vs Sfruttamento: All'inizio, l'IA muove a caso per scoprire il gioco. Col tempo, inizia a usare la rete neurale per scegliere la mossa migliore.
Experience Replay: Le partite non vengono solo giocate, ma salvate in una memoria (ReplayBuffer). L'IA si allena ripescando situazioni passate a caso, per imparare in modo più stabile.
Ricompense (Rewards):
+1: Per ogni pedina mangiata.
+3: Per ogni promozione a Dama.
+10: Per la vittoria.
-10: Penalità se prova a fare una mossa illegale (durante il training).

5. Action Masking
Una delle parti più sofisticate del codice è l'Action Masking utilizzato durante il test/gioco.
Poiché la rete neurale ha 4096 uscite (tutte le combinazioni possibili di caselle), spesso suggerisce mosse impossibili (es. muovere una casella vuota).
L'Action Masking risolve questo problema:
L'IA calcola i punteggi per tutte le mosse.
L'Ambiente fornisce la lista delle sole mosse legali al momento.
Il codice incrocia i dati: seleziona la mossa col punteggio più alto SOLO tra quelle legali.
Questo garantisce che l'IA non violi mai le regole del gioco durante la partita grafica.

6. Guida all'Uso: Training e Test
Fase A: Addestramento
Prima di poter giocare, l'IA deve imparare.
Apri lo script di training.
Esegui la funzione train_agent(num_episodes=5000) (consigliati almeno 1.000-5.000 episodi).
Attendi il completamento.
Verrà generato un file dama_dqn_final.pth. Questo file contiene i "pesi" del cervello addestrato.
Fase B: Test (Gioco)
Una volta che hai il file .pth:
Esegui lo script contenente main_gui().
Si aprirà una finestra Pygame.
Tu sei il NERO (Rosso): Clicca sulla tua pedina (si illumina di verde) e poi sulla destinazione.
L'IA è il BIANCO: Risponderà automaticamente dopo aver "pensato".
Il gioco termina quando uno dei due giocatori non ha più pedine o mosse disponibili.
Use Arrow Up and Arrow Down to select a turn, Enter to jump to it, and Escape to return to the chat.
