Anleitung:
1. ConSenseAutoclicker.exe öffnen 
2. durch Dialogfenster durchklicken
3. ConSense öffnen; Startseite ("Meine Übersicht") muss angezeigt werden



Wie das Programm funktioniert:

Das Programm sucht am Bildschirm ob am Bildschirm etwas angezeigt wird, was 
den Bildern "one.png" und "two.png" vom Ordner "clickthis" ähnlich genug sieht. 

Wenn am Bildschirm etwas einem von den Bildern ähnlich genug ist wird das angeklickt.
Sobald ein Bild angeklickt ist wird nach dem nächsten Bild gesucht. 
Nachdem die Revision bestätigt wurde geht das Programm im Browser wieder auf die 
Startseite zurück.


Wenn das Programm nicht funktioniert werden wahrscheinlich die Bilder nicht 
am Bildschirm gefunden. 
Daher die Bilder im Ordner "clickthis" durch neue ersetzen. 
	- zuerst aber unbedingt die Bilder im Ordner "Bilder-Volagen" anschauen, 
	  die neuen Bilder sollten das gleiche Format haben wie diese!
	- Auch gerne davor versuchen die Bilder mit denen aus diesem Ordner 
	  zu überschreiben. 

Bilder ersetzen geht zB. mit Snipping Tool: 
	1. Screenshot machen mit:  Windows-Taste + Shift + S
	2. Auf die Benachrichtigung von Snipping Tool klicken und den Screenshot im Ordner
	  clickthis als "one.png" oder "two.png" speichern


Programm gerne teilen; Die Bilder im Ordner "Bilder-Vorlagen" bitte nicht löschen oder verändern!






Programm in Python geschrieben. 
Mit Nuitka: 'nuitka --mingw64 <filename>.py' in .exe konvertiert. 
____________________________________________________________________________________
(Kopie vom Code: ConSenseAutoclicker.py)

import pyautogui
import time
import numpy as np
import os

pyautogui.FAILSAFE=True
starttime = time.time()
conf_docs = 0
pyautogui.alert(text=f'Um Programm abzubrechen mit Mauszeiger in die linke obere Ecke fahren', title='Programm abbrechen', button='OK')
pyautogui.alert(text=f"Browser mit ConSense muss im Vordergrund geöffnet sein.\nIn Ordner ./clickthis sind 2 Bilder gespeichert, diese werden vom Programm auf dem Bildschirm gesucht und angeklickt\n\n\tSchritt 1: Programm sucht Link von unbestätigten Dokument und klickt darauf\n\tSchritt 2: Programm sucht grünes Hackerl zum bestätigen und klickt darauf, geht dann zurück zu zuvor geöffneter Seite (Startseite Consense)\n\tSchritte 1 und 2 werden so lange ausgeführt bis Zeit um ist", title='Wie das Programm funktioniert', button='OK')
try:    
    duration = float(pyautogui.prompt(text='gewünschte Laufzeit (in Minuten) eingeben und "OK" drücken', title='gewünschte Laufzeit' , default='0'))*60
    use_pausetime = pyautogui.confirm(text='Pause zwischen Bestätigungen machen damit es scheint dass die Dokumente auch wirklich durchgelesen wurden? Empfohlen weil Zeitpunkte der Bestätigungen eingesehen werden können!', title='Pause?' , buttons=['Ja',"Nein"])
    if use_pausetime == "Nein":
        pausetime = 0
    elif use_pausetime == "Ja":
        entered_pausetime = float(pyautogui.prompt(text='gewünschte Pause (in Sekunden) eingeben und "OK" drücken', title='Pause?' , default='0'))
        pausetime = entered_pausetime
    pyautogui.PAUSE = 1

    link_position = None
    check_position = None
    last_clicked = None
    conf_docs = 0
    starttime = time.time()
    timeisup = False
    while True:
        search_starttime = time.time()
        while link_position is None and check_position is None:
            path_pic1 = os.path.join(os.path.curdir, "clickthis","one.png")
            path_pic2 = os.path.join(os.path.curdir, "clickthis","two.png")

            link_position = pyautogui.locateCenterOnScreen(path_pic1, confidence=0.8)#./clickthis/one
            check_position = pyautogui.locateCenterOnScreen(path_pic2, confidence=0.8)#./clickthis/two
            if (duration < (time.time()-starttime) or (time.time() - search_starttime)>60):
                timeisup = True
                break
        if timeisup == True:
            break
        if link_position is not None and str(last_clicked) != "link":
            pyautogui.click(*link_position)
            last_clicked = "link"
            
        elif check_position is not None and str(last_clicked) != "check":
            time.sleep(pausetime - pausetime/10 + np.random.uniform(0,1)*pausetime/5)
            pyautogui.click(*check_position)
            last_clicked = "check"
            pyautogui.press("browserback")
            conf_docs += 1
        link_position = None    
        check_position = None
            
except pyautogui.FailSafeException:
    pyautogui.alert(text=f'Abbruch durch Maus in der linken oberen Ecke', title='FailSafeException', button='OK')
except TypeError:
    pyautogui.alert(text=f'TypeError. Wahrscheinlich durch ein geklicktes "X"', title='TypeError', button='OK')
#except Exception:
#    pyautogui.alert(text=f'Error. Wahrscheinlich ein Bug', title='Käfer', button='OK')

pyautogui.alert(text=f'Stopp\n Laufzeit: {round((time.time() - starttime)/60,1)} Minuten\nBestätigte Dokumente: {conf_docs}', title='Stopp', button='OK')
