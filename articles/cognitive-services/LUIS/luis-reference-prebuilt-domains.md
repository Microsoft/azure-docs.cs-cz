---
title: Informace o předem domény - Azure | Microsoft Docs
titleSuffix: Azure
description: Referenční dokumentace pro předkompilované domény, které jsou předem sestavené kolekce tříd Intent a entity z jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 8e04853e0044e045158642fea51c225378eb3ad6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319050"
---
# <a name="prebuilt-domain-reference"></a>Informace o předem domény
Tento odkaz obsahuje informace o předem domény, které jsou předem sestavené kolekce tříd Intent a entitami, které nabízí LEOŠ.

## <a name="list-of-prebuilt-domains"></a>Seznam přednastavených domén
LEOŠ nabízí 20 předem domén. 

| Předkompilované domény | Popis | Podporované jazyky |
| ---------------- |-----------------------|:------:|
| Kalendář | Domény kalendáře poskytuje záměr a entity pro přidávání, odstraňování, nebo úpravy události, kontrola účastníci dostupnosti a hledání informací o události v kalendáři.| cs-CZ<br/> zh-CN |
| Fotoaparát | Domény fotoaparát poskytuje tříd Intent a entity odběru obrázky, záznam videa a video všesměrové vysílání na aplikaci.| cs-CZ |
| Komunikace | Zasílání zpráv a telefonní hovor.| cs-CZ <br/> zh-CN |
| Zábava  | Zpracování dotazů souvisejících s hudba, filmy a televize.| cs-CZ |
| Události | Rezervace lístků pro koncerty, festivalech, sportu hry a komedie zobrazuje.| cs-CZ |
| Vhodnosti | Zpracovává požadavky týkající se sledování vhodnosti aktivity.| cs-CZ |
| Hraní her | Zpracovává požadavky související s herní strany v hry s víc hráčů.| cs-CZ |
| HomeAutomation | Řízení inteligentní domácí zařízeními, jako je indikátory a zařízení.| cs-CZ<br/> zh-CN |
| MovieTickets | Rezervace lístků na filmy na film kina.| cs-CZ |
| Hudba | Přehrávání hudby na hudební přehrávač.| cs-CZ<br/> zh-CN |
| Poznámka | Poznámka: domény poskytuje záměry a entity související vytváření, úpravy a hledání poznámky.| cs-CZ<br/> zh-CN |
| OnDevice | Domény OnDevice poskytuje záměry a entity související řízení zařízení.| cs-CZ<br/> zh-CN |
| Míst  | Zpracování dotazů souvisejících s jako podnikům, instituce, restaurace, veřejné prostory a adresy.| cs-CZ<br/> zh-CN |
| Připomenutí | Zpracovává požadavky týkající se vytváření, úpravy a hledání připomenutí.| cs-CZ<br/> zh-CN |
| RestaurantReservation | Zpracování požadavků pro správu restaurace rezervace.| cs-CZ<br/> zh-CN |
| Taxíkem | Rezervace pro taxíkem zpracování.| cs-CZ<br/> zh-CN |
| Převede | Text pro daný jazyk překladu.| cs-CZ<br/> zh-CN |
| TV | Řízení televizní přijímače.| cs-CZ |
| Veřejné služby  | Zpracování požadavků, které jsou obvyklé v mnoha oblastech, jako je "Nápověda", "opakovat", "začněte znovu."| cs-CZ |
| Počasí | Získávání zprávy o počasí a prognózy.| cs-CZ<br/> zh-CN |
| Web | Navigace na webu.| cs-CZ<br/> zh-CN |

Další podrobnosti v každé doméně najdete následujících částech.

## <a name="calendar"></a>Kalendář 

Domény kalendáře poskytuje záměry a entity související záznamů v kalendáři. Záměry kalendáře zahrnují přidání, odstranění nebo úpravy události, kontrola dostupnosti a hledání informací o schůzku nebo položka kalendáře.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Přidat | Přidáte novou položku jednorázové do kalendáře.| Vytvořit událost s Lisa na 14: 00 v neděli <br/><br/>Chcete naplánovat schůzku<br/><br/>Potřebuji nastavení schůzky|
| CheckAvailability | Najít dostupnosti pro události nebo schůzky na uživatele nebo jiná osoba kalendáře.| Pokud je k dispozici ke splnění Jima? <br/><br/>Zobrazit, když je Karolína je k dispozici zítra<br/><br/>Je Jan volné na sobotu?|
| Odstranění | Žádost o odstranění položky kalendáře.| Zrušte Moje schůzku s Karolínu. <br/><br/>Odstranit mé schůzku 9: 00<br/>|
| Upravit | Požadavek na změnu existující schůzku nebo položka kalendáře.| Přesuňte Moje schůzku 9: 00 10 am.<br/><br/>Chci aktualizovat mé plán.<br/><br/>Reschdule Moje schůzku s Ryan.|
| Vyhledávání | Zobrazí týdenní kalendáře.| Najde k zubaři Zkontrolujte událost. <br/><br/>Zobrazit kalendář<br/>|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Umístění | Umístění položky kalendáře, schůzka nebo událost. Adresy, města a oblasti jsou dobrými příklady umístění.| 209 Nashville sebou <br/><br/>897 úklidové masopustní<br/><br/>Garážový|
| Předmět | Název nebo schůzku.| Zubní lékař na schůzku <br/><br/>Oběd s Dita<br/><br/>Lékařské schůzku|

## <a name="camera"></a>Fotoaparát 
Domény fotoaparát poskytuje tříd Intent a entity související s použitím fotoaparátu. Záměry zahrnují zaznamenání fotografií, selfie, snímek obrazovky nebo video a vysílání videa k aplikaci.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| CapturePhoto| Zaznamenejte fotografie.| Vytvoří fotografii<br/><br/>zachycení|
| CaptureScreenshot | Vytvoření snímku obrazovky.| Snímek obrazovky trvat.<br/><br/>zachycení obrazovky.|
| CaptureSelfie | Zaznamenání selfie.| Trvat selfie <br/><br/>pořídit snímek mi |
| CaptureVideo | Spusťte záznam videa.| Spustit nahrávání <br/><br/>Begin – záznam|
| StartBroadcasting| Spusťte video všesměrové vysílání.| Počáteční všesměrové vysílání pro Facebook|
| StopBroadcasting| Zastavte video všesměrové vysílání.| Zastavit všesměrové vysílání|
| StopVideoRecording| Záznam videa zastavte.| Které je dostatek<br/><br/>Zastavit záznam|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace k vysílání video, které.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Komunikace 
Komunikace domény poskytuje tříd Intent a entity související s e-mailu, zpráv a telefonních hovorů.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddContact| Přidejte nový kontakt do seznamu kontaktů uživatele.|Přidat nový kontakt <br/><br/>Uložte tento počet a uveďte název jako Karolínu|
| AddMore| Přidejte informace e-mailu nebo textu, jako součást kompozice step-wise e-mailu nebo text.|Přidat další na text <br/><br/>Přidat další tělo e-mailu|
| Odpověď| Přijetí příchozího telefonního hovoru.|odpověď volání <br/><br/>Vyberte|
| AssignContactNickname| Přiřaďte přezdívku kontaktu.|Změňte Isaac dad <br/>Upravit Jima Přezdívka<br/>Přidání Přezdívka do Patti Owens|
| CallVoiceMail| Připojte k hlasová pošta uživatele.|Připojit se k schránce hlasová pošta <br/>Hlasová pošta<br/>volání hlasová pošta|
| CheckIMStatus| Zkontrolujte stav kontaktu, Skype.|Online stav Jim je nastavena na počítače? <br/>Karolína je k dispozici pro chat s?|
| Potvrdit| Potvrďte akci.|Ano<br/>Dobře<br/>Dobře<br/>Potvrzuji, že chcete odeslat tuto e-mailu.<br/>|
| telefonické připojení| Telefonní hovor.|Volání Jima<br/>Prosím vytočit 311<br/>|
| FindContact| Najít kontaktní údaje podle názvu.|Najít číslo Karolínu<br/>Zobrazit mi číslo Karolínu<br/>|
| FindSpeedDial| Vyhledejte rychlé vytáčení číslo telefonní číslo je nastavena na a naopak.|Co je můj dial číslo 5?<br/>Jsou rychlost vytočit sadu?<br/>Co je číslo dial pro 941-5555-333?|
| GetForwardingsStatus| Získáte aktuální stav předávání volání.|Je zapnutý Moje předávání volání?<br/>Chci vědět, pokud je můj stav volání zapnutí nebo vypnutí<br/>|
| GoBack| Přejděte zpět na předchozí krok.|Přejděte zpět na twitteru<br/>Vraťte se krok<br/>Přejít zpět|
| Ignorovat| Ignorujte příchozího hovoru.|Neobsahují odpověď<br/>Ignorovat volání|
| IgnoreWithMessage| Ignorovat příchozího hovoru a místo toho odpovědět s textem.|Nechcete odpovězte toto volání, ale místo toho odeslat zprávu.<br/>Ignorovat a odešlete textový zpět.|
| PressKey| Na klávesnici stiskněte tlačítko nebo číslo.|Vytočit hvězdičky.<br/>Stisknutím tlačítka 1 2 3.|
| ReadAloud| Čtení zprávy nebo e-mailu pro uživatele.|Čtení textu.<br/>Co uživatel říká ve zprávě?|
| TurnForwardingOff| Telefonní hovor.|<br/><br/>|
| Opakovat vytáčení| Opakovat vytáčení nebo volejte číslo znovu.|Vytočte znovu.<br/>Vytočte znovu Moje posledním volání.|
| Odmítnout| Odmítnutí příchozí volání.|Odmítnout volání<br/>Nelze nyní odpovědět<br/>V tuto chvíli není k dispozici a bude zpětné volání, později.|
| Connectoru| Odešlete e-mail. Tento pokus se vztahuje na e-mailu, ale ne textové zprávy.|E-mailu Karel vod: Michal obsaženému, aby byl splendid večeři minulého týdne.<br/>Odeslat e-mail Bob<br/>|
| SendMessage| Odešlete textovou zprávu nebo rychlou zprávu.|Text poslat Jan a Karolínu|
| SetSpeedDial| Nastavte rychlost vytočit zástupce pro telefonní číslo kontaktu.|Nastavte telefonní paměť, jednu pro Karolínu.<br/>Nastavení telefonní paměť pro mom.|
| ShowNext| Najdete v další položky, například v seznamu textové zprávy nebo e-mailů.|Zobrazit dalšímu.<br/>Přejděte na další stránku.|
| ShowPrevious| Najdete v předchozí položce, například v seznamu textové zprávy nebo e-mailů.|Zobrazit předchozí.<br/>Předchozí<br/>Přejděte na předchozí.|
| StartOver| Spusťte systém přes nebo zahájit novou relaci.|Začít znovu<br/>Novou relaci<br/>restart|
| TurnForwardingOff| Vypněte přesměrování volání.|Zastaví předávání Moje volání<br/>vypnout přesměrování volání|
| TurnForwardingOn| Vypněte mluvčího telefonu.|Předávání Moje volání 3333<br/>Přepnout na volání předávání 3333|
| TurnSpeakerOff| Vypněte mluvčího telefonu.|Přejít na požadované vypnout mluvčího.<br/>Vypněte sluchátek s mikrofonem.<br/>|
| TurnSpeakerOn| Zapněte mluvčího telefonu.|Sluchátek s mikrofonem režimu.<br/>Umístí sluchátek s mikrofonem.<br/>|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ zvukového zařízení (mluvčího, sluchátky, mikrofon atd.).| Přednášející<br/>Bezobslužná<br/>Bluetooth|
| Kategorie | Kategorie zpráv nebo e-mailu.| Důležité<br/>S vysokou prioritou|
| ContactAttribute | O dotazy zákazníků atribut kontaktu uživatele.| Datum narození<br/>Adresa<br/>Telefonní číslo|
| Kontakt | Jméno kontaktu nebo zprávy příjemce.| Karolína<br/>Jima<br/>Jan|
| EmailSubject | Text, který slouží jako řádek předmětu e-mailu.| RE: zajímavé scénáře|
| Perokresba | Řádku uživatel chce použít se volat nebo posílat textové nebo e-maily z.| Pracovní řádku<br/>Britské buňky<br/>Skype|
| Zpráva | Zpráva k odeslání jako e-mailu nebo text.| Bylo skvělé splňuje můžete ještě dnes. Můžete vidět brzo!|
| MessageType | Jméno kontaktu nebo zprávy příjemce.| Text<br/>Email|
| OrderReference | Pořadí nebo relativní pozici v seznamu identifikace položku, kterou chcete načíst. Například "posledního" nebo "poslední" v "Co se poslední zprávu poslaný?"| Poslední<br/>Poslední|
| SenderName | Název odesílatele.| Patti Owens|

## <a name="entertainment"></a>Zábava  
Zobrazuje Zábava, které poskytuje domény záměry a entity související hledání filmy, hudbu, hry a televize.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Search| Zobrazuje vyhledávání filmy, Hudba, aplikace, hry a televize.|Vyhledejte v obchodě bylo.<br/>Vyhledejte miniatury.|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ContentRating | Hodnocení jako G nebo R pro filmy obsahu média.|Video dětí.<br/>PG hodnocení.|
| Genre | Genre film, hry, aplikace nebo skladbu.|Comedies<br/>Hrané filmy<br/>Legrační|
| – Klíčové slovo| Určení atribut – klíčové slovo obecné vyhledávání neexistuje v konkrétnější sloty média.|Nahrávky především<br/>Měsíc oblasti<br/>Amelia Earhart|
| Jazyk | Hodnocení jako G nebo R pro filmy obsahu média.|Francouzština<br/>Angličtina<br/>Korejština|
| MediaFormat | Další technické speciální typ ve kterém je naformátován média.|HD videa<br/>3D filmy<br/>Ke stažení|
| MediaSource | V úložišti nebo marketplace pro získávání média.|Netflix<br/>Prime|
| MediaSubTypes| Typy médií menší než filmy a hry.|Ukázky<br/>DLC<br/>Přípojných|
| Příslušnosti| Zemi, kde byl vytvořen film, zobrazit nebo skladbu.|Francouzština<br/>Němčina<br/>Korejština|
| Person (Osoba)| Objektu actor, ředitel, producent, hudebníkem nebo umělcem přidružené videa, aplikace, herní nebo televizního pořadu.|Madonna<br/>Stanley Kubrick|
| Role| Úloze, kterou uživatel k vytvoření média.|Sings<br/>Podle<br/>Od společnosti|
| Titul| Název film, aplikace, hry, televizního pořadu nebo skladbu.|Friends<br/>Minecraft|
| Typ| Typ nebo médium formát film, aplikace, hry, televizního pořadu nebo skladbu.|Hudba<br/>MovieTV <br/>Zobrazuje|
| UserRating| Uživatel hvězdičky nebo palec hodnocení.|5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičkami|

## <a name="events"></a>Události 
Události domény poskytuje tříd Intent a související entity na celém světě pro událostmi, jako je koncerty, festivalech, sportu hry a komedie ukazuje.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresáře| Zakoupit lístky pro událost.|Nastavit jako koupit lístek pro symphony tento víkendu.|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa | Umístění události nebo adresu. |Palo Alto<br/>300 112th průměr SE <br/> Seattle |
| Název | Název události.|Shakespeare v do parku|
| PlaceName| Název umístění události.|Louvre<br/>Opera úklidové<br/>Broadway|
| PlaceType | Typ umístění události bude uchovávat v.|Cafe<br/>THEATRE<br/>Knihovna|
| Typ | Typ události.|Vzájemné součinnosti<br/>Herní sportu|

## <a name="fitness"></a>Vhodnosti 
Domény vhodnosti poskytuje záměry a entity související sledování vhodnosti aktivity. Záměry zahrnují ukládání poznámky, zbývající čas nebo vzdálenost nebo uložení výsledků aktivity.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddNote| Přidá další poznámky sledovaných aktivity.|Potíže s tohoto spuštění byl 6/10<br/>Geologické struktury, které se počítač nachází v systémem je asfaltu<br/>Používám kolo 3 rychlosti|
|GetRemaining| Získá zbývající čas nebo vzdálenost pro aktivitu.|Jak dlouho do další okruhu?<br/>Kolik miles zůstávají ve Moje spustit? Kolik času pro rozdělení?|
| LogActivity| Uložení nebo protokolu výsledky dokončené aktivity.|Uložit Moje poslední spuštění<br/>Přihlaste se Moje procházení soboty<br/>uložení Moje předchozí swim|
| LogWeight| Uložení nebo protokolu váhy aktuálního uživatele.|Uložit Moje aktuální váhy<br/>Nyní protokolu Moje váhy<br/>uložení Moje aktuální váhy textu|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ActivityType | Typ aktivitu ke sledování. |Spusťte<br/>Procházení<br/>Swim<br/>Cyklus |
| Potravinářství | Typ jídlo sledovat v aplikaci vhodnosti. |Banánů<br/>Losos<br/>Bílkovin zatřesením|
| MealType| Typ jídlem ke sledování stavu nebo vhodnosti aplikace.|Snídani<br/>Večeři<br/>Oběd<br/>Supper|
| Měření| Typ měření času, vzdálenost či váha pro použití v aplikaci vhodnosti nebo stavu.|Kilometrech<br/>Miles<br/>Minut<br/>Kg|
| Číslo | Číselné množství pro použití v aplikaci vhodnosti nebo stavu.|19<br/>tři<br/>200<br/>jeden|
| StatType | Typ statistiky na agregovaná data pro použití v aplikaci vhodnosti nebo stavu.|Součet<br/>Průměr<br/>Maximum<br/>Minimální|

## <a name="gaming"></a>Hraní her 
Herní domény poskytuje záměry a související se správou herní strany ve hře hru s více entit.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| InviteParty| Pozvěte a obraťte se na připojení herní strany.|Pozvěte tento přehrávač Moje strany<br/>Dřívější Moje strany<br/>Připojení k mé klan|
|LeaveParty| Získá zbývající čas nebo vzdálenost pro aktivitu.|Jsem si<br/>I mě opuštění této straně pro jinou<br/>Mě ukončení programu|
| StartParty| Spuštění herní strany v hry s víc hráčů.|Kamaráde umožňuje spustit a stranou<br/>spuštění a stranou<br/>Měli jsme spustit klan dnešní večer|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Kontakt| Jméno kontaktní osoby pro použití v hry s víc hráčů.|Karolína<br/>Jima|


## <a name="homeautomation"></a>HomeAutomation 
Domény HomeAutomation poskytuje záměry a entity související řízení inteligentní domácí zařízeními, jako je indikátory a zařízení.

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Vypnutí| Vypnout, zavřít nebo odemknutí zařízení.|Vypnout světla<br/>Zastavit kávy maker<br/>Dvířka zavřít Garážový|
|TurnOn| Zapnout zařízení nebo nastavte zařízení na určité nastavení nebo režim.|zapnout Moje kávy maker<br/>můžete zapnout Moje kávy maker?<br/>Nastavte termostatu 72 stupňů.|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Zařízení | Typ zařízení, které můžete zapnout nebo vypnout.|kávy maker<br/>Termostat<br/>indikátory|
| Operace | Stav nastavení zařízení.|Zámek<br/>otevřít<br/>zapnuté<br/>vypnuto|
| Místnosti | Umístění nebo místnosti zařízení je v.|Obývací pokoj<br/>ložnici<br/>kuchyně|

## <a name="movietickets"></a>MovieTickets 
Domény MovieTickets poskytuje záměry a související na celém světě na filmy na film kina entity.

### <a name="examples"></a>Příklady
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresáře | Nákupu video lístků.|Sešit dva lístky pro vedoucího Omar a dvě musketeers<br/>Chcete koupit lístek pro budoucí filmu<br/>Další středa I má lístek Captian Omar část 2|
|GetShowTime| Získáte showtime přehrávání videa.|Když se zobrazuje Pilot Omar?|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa | Adresa kina film.|Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| MovieTitle | Název film.|Dobu životnosti platformy<br/>Hladu hry<br/>Zahájení|
| PlaceName | Název film kina nebo filmu.|Amir filmu<br/>Alexandrii Theatre<br/>New Yorku kina|
| PlaceType | Typ umístění film se zobrazuje v.|filmu<br/>kina<br/>Imax – filmu|

## <a name="music"></a>Hudba 
Hudba domény poskytuje záměry a entity související přehrávání hudby na hudební přehrávač.

### <a name="examples"></a>Příklady
```
play Beethoven
Increase track volume
Skip to the next song
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| DecreaseVolume | Zmenšení svazku zařízení.|snížit sledovat svazku<br/>svazek dolů|
| IncreaseVolume | Zvětšete svazek zařízení.|Zvětšete svazek sledování<br/>svazek nahoru|
| Ztlumení |Ztlumení přehrávání Hudba.|Ztlumení skladbu<br/>Uveďte sledovat na ztlumení<br/>Ztlumení Hudba |
| Pozastavení | Pozastavení přehrávání Hudba.|Pozastavení<br/>Pozastavení Hudba<br/>Pozastavení sledování|
| PlayMusic | Hudba hrát na zařízení.|přehrání kevina Durant<br/>přehrání Paradise podle Coldplay<br/>přehrání Hello podle Adele|
| Opakovat |Opakujte přehrávané Hudba.|Opakujte skladbu<br/>Přehrání nárůst sledování<br/>Opakujte Hudba|
| Obnovit | Obnoví přehrávání Hudba.|Obnovit skladbu<br/>znovu spustit Hudba<br/>Zrušení pozastavení|
| SkipBack | Přeskočte zpětné sledovat.|Přejděte na další skladbu<br/>Přehrát další skladbu|
| SkipForward |Přeskočte dopředu sledovat.|Přehrát předchozí skladbu<br/>Přejděte zpět na předchozí sledování |
| Zastavit | Zastavte akci týkající se hudba přehrávání. |Zastavte přehrávání tohoto alba.|
| Zapnout | Zrušit ztlumení Hudba přehrávání zařízení.| Zapnout.|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ArtistName | Objektu actor, ředitel, producenta, zapisovač, hudebníkem nebo umělcem přidružené média hrát na zařízení.|Elvis Presley<br/>Kód Swift Taylora<br/>Adele<br/>Mozart|
| Genre | Genre Hudba požadováno.|Hudba země<br/>Klasické Broadway<br/>Přehrání Hudba klasického z Baroque období.|

## <a name="note"></a>Poznámka 
Poznámka: domény poskytuje záměry a entity související vytváření, úpravy a hledání poznámky.

### <a name="examples"></a>Příklady
```
Add to my groceries note lettuce tomato bread coffee
Check off bananas from my grocery list
Remove all items from my vacation list
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddToNote | Informace o přidání poznámky.|Přidejte do mé nákup Poznámka salát rajčete chléb kávy<br/>Přidat do seznamu úkolů<br/>Přidání cupcakes do mé Wunderlist|
| CheckOffItem | Zkontrolujte vypnout položek z existující poznámky.|Zkontrolujte vypnout banánů z mé seznam na nákup<br/>Označit sýr dort na můj svátek shopping seznamu, jak provést|
| Vymazat | Vymazání všech položek z existující poznámky.|Odstranit všechny položky ze seznamu dovolenou<br/>Vymazat všechna Moje čtení seznamu|
| Potvrdit | Potvrďte akci týkající se poznámky.|Je to v pořádku mnou<br/>ano<br/>I mě potvrzení udržuje všechny položky na seznamu|
| Vytvořit | Vytvořte novou poznámku. | Vytvořit seznam<br/>Všimněte si, že Jason připomenout je ve městě první týden může|
| Odstranění | Odstraníte celou poznámku. |Odstranit mé dovolenou seznamu <br/>Odstranit mé nákup Poznámka|
| DeleteNoteItem | Odstranění položek z existující poznámky.| Odstranit čipy z mé seznam na nákup<br/>Odebrání Moje školní shopping seznamu pera|
| ReadAloud | Číst seznam nahlas.|Soubor Readme první<br/>Soubor Readme podrobnosti|
| ShowNext | V tématu na další položku v seznamu poznámky.|Zobrazit následující<br/>Další stránka<br/>Další|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace poznámek.|Wunderlist<br/>OneNote|
| Kontakt | Název a obraťte se na v poznámce.|Karolína<br/>Jima<br/>Jan|
| Zdroj dat | Umístění poznámky.|OneDrive<br/>Dokumentace Google<br/>počítač|
| DataType | Typ souboru nebo s dokumentem, obvykle spojené s konkrétní softwarové programy.|Snímky<br/>Tabulka<br/>List|
| Text | Text poznámky nebo připomenutí.|Funkce Stretch před proti<br/>Spustit dlouho zítra|
| Titul | Název poznámky.|Nákup<br/>uživatele, kteří mají volání<br/>úkolů|

## <a name="ondevice"></a>OnDevice 
Domény OnDevice poskytuje záměry a entity související řízení zařízení.

### <a name="examples"></a>Příklady
```
Close video player
Cancel playback
Can you make the screen brighter?
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| AreYouListening | Požádejte, pokud zařízení naslouchá.|je to na?<br/>jsou vám naslouchání?|
|CloseApplication|Zavřete aplikaci zařízení.|Zavřete přehrávání videa|
|FileBug|Založení záznamu o chybě v zařízení.|založení záznamu o chybě prosím<br/>Můžete založení záznamu o chybě pro mě nejlepší?<br/>Chci ohlásit této chyby|
|GoBack|Zeptejte se můžete vrátit jeden krok nebo vrátit na předchozí krok.|Vraťte se prosím<br/>Přejít na předchozí obrazovce<br/>Přejděte zpět zastavení naslouchání|
|Nápověda| Požádat o pomoc.|Pomůže prosím<br/>Dobrý den,<br/>Co můžete dělat?<br/>Potřebuji pomoc s| 
|LocateDevice|Vyhledejte zařízení.|Můžete najít Můj telefon<br/>Najít iphone na tní<br/>Najít Můj telefon|
|Přihlášení|Přihlaste se k službě pomocí zařízení.|Přihlášení prosím<br/>Přihlašovací službě Facebook.<br/>Přihlaste se k LinkedIn|
|Odhlášení|Odhlaste se ze služby pomocí zařízení.|Odhlásit Můj telefon<br/>Přihlaste se k služby twitter<br/>Odhlásit se|
|MainMenu|Zobrazení v hlavní nabídce zařízení.|Zobrazení nabídky.|
|OpenApplication|Otevřete aplikaci na zařízení.|Otevřete na upozornění prosím<br/>Zapnout fotoaparát<br/>Spusťte kalendáře|
|OpenSetting|Otevřete nastavení v zařízení.|Otevřete nastavení sítě.|
|PairDevice|Pár zařízení.|Můžete mi pomoci v párování signál Bluetooth na telefon<br/>Zapnout bluetooth a spárujte ho s přenosných počítačů<br/>Spárujte signál Bluetooth na svém přenosném počítači|
|PowerOff | Vypněte zařízení.|Můžete vypnout počítač<br/>Shutdown<br/>Vypnout mobilní telefon|
|QueryBattery|Získání informací o výdrž baterie.|Zobrazit výdrž baterie.<br/>Co je můj stav baterie<br/>Kolik baterie nyní?<br/>Zobrazit stav baterie.|
|QueryWifi|Získání informací o Wi-Fi.|Získání informací o Wi-Fi.|
|Restartování|Zařízení restartujte.|Restartujte.|
|RingDevice| Požádejte zařízení prstenec, aby bylo možné ji najít, když se ztratí.|Prstence telefon.| 
|SetBrightness|Nastavte také průraznost zařízení.|Sada také průraznost střední<br/>Sada také průraznost na vysoká<br/>Sada také průraznost na nízkou prioritu|
|SetupDevice|Zahájit instalaci zařízení.|Chcete nainstalovat Instalační program operačního systému<br/>Instalační program prosím<br/>Provést nastavení pro mě nejlepší|
|ShowAppBar|Zobrazte panel aplikaci.|Zobrazit mi na panelu aplikace<br/>Prosím panelu aplikací<br/>Chci panel aplikací|
|ShowContextMenu|Zobrazení místní nabídky.|Chci najdete v části místní nabídky<br/>Kontextové nabídky prosím<br/>Můžete je zobrazit v místní nabídce|
|Přejít do režimu spánku|Umístěte zařízení do režimu spánku.|Jdi spát<br/>Přejít do režimu spánku<br/>Můj počítač v režimu spánku|
|SwitchApplication|Přepínač aplikace pro použití v zařízení.|Přepnout na můj přehrávač médií.|
|TurnDownBrightness|Snižte také průraznost zařízení.|Dim na obrazovce.|
|TurnOffSetting|Vypněte nastavení zařízení.|Deaktivovat Bluetooth<br/>Zakázat dat<br/>Odpojit bluetooth|
|TurnOnSetting|Zapněte nastavení zařízení.|Zapnuto <br/> Vypnuto|
|TurnUpBrightness|Zapněte si také průraznost zařízení.|Můžete provádět na obrazovce světlejší?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace v zařízení.|SoundCloud<br/>YouTube|
| BrightnessLevel | Nastaví úroveň také průraznost na zařízení.|100 %<br/>Padesát<br/>40%|
| Kontakt | Název obraťte se na zařízení.|Paul<br/>Maximální počet Marlen|
| DeviceType | Typ zařízení. |Telefon<br/>Kindle<br/>Přenosný počítač|
| Typ média | Typ média provádí zařízení.|Hudba<br/>Film<br/>Televizní pořady|
| Typem SettingType | Typ nastavení nebo panel nastavení, která chce uživatel upravit.|Wi-Fi<br/>Bezdrátové sítě<br/>Barevné schéma<br/>Centra oznámení|

## <a name="places"></a>Míst  
Domény místech poskytuje záměry pro zpracování dotazů souvisejících s jako podnikům, instituce, restaurace, veřejné prostory a adresy.

### <a name="examples"></a>Příklady
```
Save this location to my favorites
How far away is Holiday Inn?
At what time does Safeway close?
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Přidejte umístění, které má seznamu oblíbených položek uživatele.|Toto umístění pro uložení do složky Oblíbené položky<br/>Přidat tuto adresu do složky Oblíbené položky|
|CheckAccident|Dotaz, zda je v zadané cesty havárie.|Je na 880 nehody?<br/>Zobrazit informace o havárii|
|CheckAreaTraffic|Zkontrolujte provoz pro obecné oblasti nebo dálnice, nikoli na zadanou trasu.|Přenosy dat v Praze<br/>Co je provoz jako v Praze?|
|CheckIntoPlace|Zkontrolujte místo používání sociálních médií.|Poslat mi změnami na Foursquare<br/>Zde se změnami|
|CheckRouteTraffic| Zkontrolujte síťový provoz na konkrétní trasy specifikovaných uživatelem.|Jak je provoz do Mashiko?<br/>Zobrazit mi traffice k Kirkland<br/>Jak je provoz k webu Seattle?| 
|Potvrdit|Potvrďte akci týkající se místo.|Potvrďte Moje restaurace rezervace.|
|Konec|Akce pro ukončení úlohy týkající se místo.|Ukončete prosím<br/>Ukončení mi poskytnutí pokynů|
|FindPlace|Vyhledejte na místě (business, instituce, restaurace, prostor veřejných adres).|Kde je nejbližší knihovny?<br/>Najít dobrou Italská restaurace v Horská zobrazení|
|Getaddress –| Požádejte o adresu na místě.|Zobrazit adresu Guu v ulici Robson<br/>Co je adresa nejbližší Starbucks?| 
|GetDistance|Požádejte o vzdálenost na určité místo.|Jak daleko je svátek DIČ?<br/>jak daleko je k Bellevue odmocnina z tohoto umístění<br/>Co je vzdálenost k Tahoe|
|Gethours –|Požádejte o provozní dobu na místě.|Jaké během Safeway zavřít?<br/>Jaké jsou dobu Domů skladu?<br/>Je stále otevřen Starbucks?|
|GetMenu|Požádejte položek nabídky pro restaurace.|Nemá Zucca sloužit nic vegan?<br/>Co je v nabídce v Sizzler<br/>Zobrazit mi na Applebee nabídky|
|GetPhoneNumber| Požádejte o telefonní číslo na místě.|Co je telefonní číslo nejbližší Starbucks?<br/>Zadejte číslo pro Domů skladu| 
|GetPriceRange| Požádá o rozsahu cena na místě.|Je Zucca levných?<br/>Je polovinu ceny Cineplex ve středu?<br/>Kolik stojí celý severského večeři v Sizzler?|
|GetReviews|Požádejte o recenze na místě.|Zobrazit recenze objektu pro vytváření Cheesecase<br/>Cineplex recenze v Yelp|
|GetRoute|Požádejte o pokynů na místo.|Tom, jak procházet k Bellevue odmocnina<br/>Zobrazit mi nejkratší způsob, jak 8. a 59th z tohoto umístění<br/>Získat mi pokynů pro certifikační Autoritu, horská zobrazení|
|GetStarRating|Požádejte o hvězdiček na místě.|Jak Zucca hodnocením podle Yelp?<br/>Kolik hvězdiček má prací francouzština?<br/>Je vhodné akvárium v Monterrey?|
|GetTransportationSchedule|Získáte sběrnice plán pro místo.|Jaké čas je další bus downtown?<br/>Zobrazit sběrnicích v král okres|
|GetTravelTime|Požádejte dobu cesta do určeného cíle.|Jak dlouho bude trvat zobrazíte Brno z tohoto umístění<br/>Co je doba řízení k Ostravy z SF|
|Funkce MakeCall|Telefonní hovor na místo.|Volání mom<br/>Chcete umístit Skype volání Anna<br/>Volání Jima|
|MakeReservation|Rezervace pro restaurace nebo jiné obchodní požadavek.|Vyhradit v Zucca pro dva jsou pro tonight<br/>Seznam na tabulku pro zítra<br/>Tabulka pro 3 v Palo Alto na 8|
|MapQuestions|Požádat o informace o pokynů nebo jestli se zadaný silniční přejde do cílového umístění.|13 předávat downtown?<br/>Můžete provést 880 při Oakland?|
|hodnocení|Získá Popis hodnocení restaurace nebo místní.|Kolik hvězdiček DIČ Contoso má?|
|ReadAloud|Číst seznam místech nahlas.|Soubor Readme první<br/>Soubor Readme podrobnosti|
|Selectitem –|Vyberte položku ze seznamu voleb týkající se místo nebo místech.|Vyberte druhý<br/>Vyberte první|
|ShowMap|Zobrazit mapu oblast.|Zobrazit mapu pro druhý<br/>Zobrazit mapu<br/>Najít Brno na mapě|
|ShowNext|Zobrazte další položku v řadě.|Zobrazit následující<br/>přejděte na další stránku|
|ShowPrevious|Zobrazte předchozí položce v řadě.|Zobrazit předchozí<br/>předchozí<br/>Přejít na předchozí|
|StartOver|Restartujte aplikaci nebo zahájit novou relaci.|Začít znovu<br/>Novou relaci<br/>
restart|
|TakesReservations|Dotaz, zda místo přijímá rezervace.|galerii obrázky přijímat rezervace<br/>Je možné provádět rezervace v zahrada olivového

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Umístění nebo adresu na místě.|Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| Veřejných zařízení | Cíle charakteristiky nebo výhod na místě.|bezpečnost dětí eat free<br/>waterfront<br/>volné parkovací|
| Prostředí | Prostředí na místě.|dětský friendly<br/>běžné restaurace<br/>Sporty|
| Kuchyni | Kuchyni na místě. |Středozemního<br/>italština<br/>Indickém|
| DestinationAddress| Cílové umístění nebo adresu.|Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| DestinationPlaceName| Název cílového umístění, které je obchodní, restaurace, veřejné přitažlivosti nebo instituce.|parku – střed<br/>safeway<br/>walmart|
| DestinationPlaceType | Typ cílového umístění, které je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Restaurace<br/>Opera<br/>filmu|
| Vzdálenost | Vzdálenost na místo.|15 miles<br/>5 miles<br/>rychle 10 miles|
| MealType | Typ jídlem jako snídani nebo oběd. |Snídani<br/>Večeři<br/>Oběd<br/>Supper|
| OpenStatus | Určuje, zda je na místě otevřené nebo uzavřený.|Otevřenost<br/>uzavřený<br/>otevření|
| PlaceName | Název na místě.|Objekt pro vytváření cheesecake|
| PlaceType | Typ na místě.|Cafe<br/>THEATRE<br/>Knihovna|
| PreferredRoute | Upřednostňované trasa specifikovaných uživatelem. | 101 <br/>202 <br/>Trasy 401|
| Produkt | Produktu, které nabízí na místě. | Oblečení<br/>Automatické obnovení systému digitální fotoaparáty<br/>Nový rybího | 
| PublicTransportationRoute | Název veřejné Transport trasy, která hledá uživatele. | Train severovýchodním ose<br/>Sběrnice trasy 3 X |
| hodnocení | Hodnocení na místě. | 5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičkami|
| RouteAvoidanceCriteria | Kritéria pro vyloučení konkrétní trasy jako zabraňující havárií, konstrukce nebo mýtné | Mýtné <br/>Konstrukce<br/>Trasy 11|
| ServiceProvided | Toto je služba poskytovaná firmy nebo místní například kadeřnictví, sněhové plowing, terénní úpravy. | kadeřnictví<br/>Mechanic<br/>instalatér|
| TransportationCompany | Název zprostředkovatele přenosu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ Transport.|Sběrnice<br/>Trénování<br/>Autem|

## <a name="reminder"></a>Připomenutí 
Připomenutí domény poskytuje záměry a entity pro vytváření, úpravy a hledání připomenutí.

### <a name="examples"></a>Příklady
```
Change my interview to 9 am tomorrow
Remind me to buy milk on my way back home
Can you check if I have a reminder about Christine's birthday?
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Změnit| Změňte připomenutí.|Změňte Moje rozhovoru 9: 00 zítra<br/>Přesunout Moje připomenutí přiřazení do zítra|
| Vytvořit| Vytvořte nový připomenutí.|Vytvoření připomenutí<br/>Připomenout koupit mléka<br/>Chci nezapomeňte volat Milena při jsem doma|
| Odstranění | Odstraňte připomenutí.|Odstranit připomenutí Moje obrázek<br/>odstranit toto připomenutí|
| Vyhledávání | Zjistí, zobrazí se připomenutí.|O Moje výročí jsou připomenutí?<br/>Můžete je zkontrolovat, pokud připomenutí o narozeninách Jana na?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Text | Textový popis připomenutí.|vyzvednutí čistírna<br/>vyřazení automobil v centru služby|

## <a name="restaurantreservation"></a>RestaurantReservation 
Domény RestaurantReservation poskytuje tříd Intent a entity související se správou restaurace rezervace.

### <a name="examples"></a>Příklady
```
Reserve at Zucca for two for tonight
Book a table at BJ's for tomorrow
Table for 3 in Palo Alto at 7
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Rezerva | Rezervace pro restaurace žádost. |Vyhradit v Zucca pro dva jsou pro tonight<br/>Seznam na tabulku pro zítra<br/>Tabulka pro 3 v Palo Alto na 7|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa| Umístění událostí nebo adresu rezervace.|Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| Veřejných zařízení | Atribut popisující je vybavení na místě.|zobrazení oceánu<br/>bez kouření|
| AppName | Název aplikace pro provádění rezervací.|OtevřítTabulku<br/>Yelp<br/>TripAdvisor|
| Prostředí | Popis prostředí restaurace nebo jiném místě.|Romantický<br/>běžné<br/>vhodné pro skupiny|
| Kuchyni | Typ jídlo, kuchyni nebo kuchyni příslušnosti. |Čínština<br/>italština<br/>Mexických|
| MealType | Typ jídlem přidružené rezervace.|Snídani<br/>Večeři<br/>Oběd<br/>Supper|
| PlaceName | Název místního firmy, restaurace, veřejné přitažlivosti nebo instituce.|IHOP<br/>Objekt pro vytváření cheesecake<br/>Louvre|
| PlaceType | Typ místní firmy, restaurace, veřejné přitažlivosti nebo instituce.|restaurace<br/>Opera<br/>filmu|
| hodnocení | Hodnocení místní nebo restaurace.|5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičkami|

## <a name="taxi"></a>Taxíkem 
 
Domény taxíkem poskytuje tříd Intent a entity pro vytváření a správu taxíkem rezervace.

### <a name="examples"></a>Příklady
```
Get me a cab at 3 pm
How much longer do I have to wait for my taxi?
Cancel my Uber
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresáře | Volání taxíkem. |Získat mi soubor cab<br/>Najít taxíkem<br/>Sešit mi uber x|
| Zrušit | Zrušte akci týkajících se rezervace taxíkem.|Zrušit Moje taxíkem<br/>Zrušit Moje Uber|
| sledování | Sledovat taxíkem trasy.|Jak dlouho je nutné čekat na můj taxíkem?<br/>Kde je můj Uber?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa| Adresa přidružená rezervace taxíkem. |Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| DestinationAddress| Cílové umístění nebo adresu. |Palo Alto<br/>300 112th průměr SE<br/>Seattle|
| DestinationPlaceName | Název cílového umístění, které je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Parku – střed<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Typ cílového umístění, které je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Restaurace<br/>Opera<br/>filmu|
| PlaceName | Název místního firmy, restaurace, veřejné přitažlivosti nebo instituce, která. |Parku – střed<br/>Safeway<br/>Walmart|
| PlaceType| Typ místní v požadavku na sešit taxíkem.|Restaurace<br/>Opera<br/>filmu|
| TransportationCompany | Název zprostředkovatele přenosu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ Transport.|Sběrnice<br/>Trénování<br/>Autem|

## <a name="translate"></a>Převede 
Domény přeložit poskytuje záměry a entity související text pro daný jazyk překladu.

### <a name="examples"></a>Příklady
```
Translate to French
Translate hello to German
Translate this sentence to English
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Převede| Převede text na jiný jazyk.|Převede na francouzštinu<br/>Převede hello němčina|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| TargetLanguage | Cílový jazyk překladu.|Francouzština<br/>Němčina<br/>Korejština|
| Text | Text na překlad|Hello World<br/>Dobré ráno<br/>Dobrý večer|

## <a name="tv"></a>TV 
 
Televizního domény poskytuje pro řízení televizní přijímače tříd Intent a entity.

### <a name="examples"></a>Příklady
```
Switch channel to BBC
Show TV guide
Watch National Geographic
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| ChangeChannel| Změňte kanál na Televizi.|Změna kanálu pro CNN<br/>Kanál přepínače do BBC<br/>Přejděte do kanálu 4|
| Showguide –| Zobrazit v Průvodci TV.|Zobrazit televize<br/>Co je na film kanálu nyní?<br/>Zobrazit seznam programu|
| WatchTV| Požádejte o sledovat televizního kanálu.|Chcete sledovat Disney kanál<br/>přejděte prosím na TV<br/>Podívejte se na National geografické|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ChannelName | Název televizního kanálu.|CNN<br/>BBC<br/>Film kanálu|

## <a name="utilities"></a>Veřejné služby  
Domény nástrojů poskytuje záměry pro úlohy, které jsou společné pro celou řadu úloh, jako je například pozdrav, zrušení, potvrzení, Nápověda, opakování, navigace, spuštění a zastavení.

### <a name="examples"></a>Příklady
```
Go back to Twitter
Please help
Repeat last question please
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Zrušit | Zrušte akci.|Zrušit zprávu<br/>Nechci už odesílat e-mailu|
| Potvrdit | Potvrďte akci.|Ano jejda potvrzuji<br/>Vhodné I mě potvrzení<br/>V pořádku I mě potvrzení|
| FinishTask | Dokončete úlohu spustit uživatel.|Mám Hotovo<br/>Dokončeno.<br/>Je Hotovo|
| GoBack | Přejděte zpět jeden krok nebo vrátit do předchozího kroku.|Přejděte zpět na Twitteru<br/>Vraťte se krok<br/>Přejít zpět|
| Nápověda | Žádost o pomoc.|Pomozte<br/>Otevřít nápovědu<br/>Nápověda|
| Opakovat | Opakujte akci.|Opakujte poslední otázku prosím<br/>Opakujte poslední skladbu|
| ShowNext | Zobrazte další položku v řadě. |Zobrazit následující<br/>přejděte na další stránku|
| ShowPrevious | Zobrazte předchozí položce v řadě.|Zobrazit předchozí|
| StartOver | Restartujte aplikaci nebo zahájit novou relaci.|Začít znovu<br/>Novou relaci<br/>restart|
| Zastavit | Zastavte akci.| Zastavit oznámením této<br/>Zmlkni<br/>Zastavit prosím|

## <a name="weather"></a>Počasí 
Domény počasí poskytuje tříd Intent a entity pro zprávy o počasí a prognózy.

### <a name="examples"></a>Příklady
```
weather in London in september
What?s the 10 day forecast?
What's the average temperature in India in september?
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| GetCondition | Získáte historické fakty související s počasí. |počasí v Londýně v září<br/>Co je průměrnou teplotu v Indii v září?|
| GetForecast | Získejte aktuální počasí a prognózy další několik dní. |Jak je počasí dnes?<br/>Co je 10 den prognózy?<br/>Jak bude počasí tento víkendu?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Umístění| Absolutní umístění pro žádost o počasí.|Seattle<br/>Paříž<br/>Palo Alto|

## <a name="web"></a>Web 
Doména webové poskytuje záměrem pro navigaci k webu.

### <a name="examples"></a>Příklady
```
Navigate to facebook.com
Go to www.twitter.com
Navigate to www.bing.com
```

### <a name="intents"></a>Záměry
| Záměrné název | Popis | Příklady |
| ---------------- |-----------------------|----|
| Navigace | Žádost o navigaci na zadané webové stránky. |Přejděte na facebook.com<br/>Přejděte na www.twitter.com|

