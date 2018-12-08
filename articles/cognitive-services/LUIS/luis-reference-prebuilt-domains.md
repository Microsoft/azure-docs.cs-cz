---
title: Referenční informace k předem připraveným doménám
titleSuffix: Azure
description: Referenční informace pro předem připravených domén, které jsou předem sestavené kolekce záměry a entity z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a47fb82b1e6d42bdebff8cc4f3cb4c3c87025831
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106239"
---
# <a name="prebuilt-domain-reference"></a>Referenční informace k předem připraveným doménám
V tomto materiálu najdete informace o [předem připravených domén](luis-how-to-use-prebuilt-domains.md), které jsou předem sestavené kolekce záměry a entity, které nabízí služba LUIS.

[Vlastní domény](luis-how-to-start-new-app.md), naopak spustit bez záměry a modely. Všechny předem připravených domény záměry a entity můžete přidat do vlastního modelu.

## <a name="list-of-prebuilt-domains"></a>Seznam předem připravených domén
Služba LUIS nabízí 20 předem připravených domén. 

| Předem připravená doména | Popis | Podporované jazyky |
| ---------------- |-----------------------|:------:|
| Kalendář | Doména kalendáře poskytuje záměr a entity pro přidávání, odstraňování, nebo úpravy události, kontrola dostupnosti účastníků a hledání informací o události v kalendáři.| en-US<br/> zh-CN |
| Fotoaparát | Doména fotoaparátu poskytuje záměry a entity pro provádění obrázky, nahrávání videa a všesměrového vysílání video k aplikaci.| en-US |
| Komunikace | Zasílání zpráv a telefonní hovor.| en-US <br/> zh-CN |
| Zábava  | Zpracování dotazů souvisejících s hudba, filmů a televizního vysílání.| en-US |
| Události | Rezervace letenek pro koncerty, festivalů, sportovní hry a komedie ukazuje.| en-US |
| Způsobilost | Zpracování žádosti týkající se sledování vhodnosti aktivity.| en-US |
| Hraní her | Zpracování žádostí souvisejících her straně hry pro více hráčů.| en-US |
| HomeAutomation | Řízení inteligentní domácí zařízení, jako jsou indikátory a zařízení.| en-US<br/> zh-CN |
| MovieTickets | Rezervace lístky na filmy v celé obrazovky video.| en-US |
| Hudba | Přehrávání hudby v hudební přehrávač.| en-US<br/> zh-CN |
| Poznámka | Poznámka: domény poskytuje záměry a entity týkající se vytváření, úpravám a hledání poznámky.| en-US<br/> zh-CN |
| OnDevice | Doména OnDevice poskytuje záměry a entity související řízení zařízení.| en-US<br/> zh-CN |
| Místa  | Zpracování dotazů souvisejících s jako firmám, instituce, restaurace, veřejné mezery a adresy.| en-US<br/> zh-CN |
| Připomenutí | Zpracování žádosti týkající se vytváření, úprav a vyhledání připomenutí.| en-US<br/> zh-CN |
| RestaurantReservation | Zpracování požadavků pro správu restaurace rezervace.| en-US<br/> zh-CN |
| Taxislužby města | Zpracování rezervací taxislužby.| en-US<br/> zh-CN |
| Překlad | Překlad textu do cílového jazyka.| en-US<br/> zh-CN |
| TV | Řízení televizí.| en-US |
| Veřejné služby  | Zpracování požadavků, které jsou běžné v mnoha oblastech, jako je "Nápověda", "opakování", "začít znovu."| en-US |
| Počasí | Načtení počasí a předpovědi.| en-US<br/> zh-CN |
| Web | Přejděte na web.| en-US<br/> zh-CN |

Další podrobnosti o každé domény najdete v následující části.

## <a name="calendar"></a>Kalendář 

Doména kalendáře poskytuje záměry a entity související položky v kalendáři. Kalendář záměry zahrnují přidání, odstranění nebo úpravy schůzku, kontroluje se dostupnost a vyhledávání informací o události a položky kalendáře.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Přidat | Přidáte novou položku jednorázové do kalendáře.| Vytvořit událost s Lisa ve 14 v neděli <br/><br/>Chci naplánovat schůzku<br/><br/>Je potřeba nastavit na schůzce|
| CheckAvailability | Zjistěte dostupnost pro událost nebo schůzku na uživatele nebo jinou osobu kalendáře.| Pokud je k dispozici podle Jima? <br/><br/>Zobrazit, když Karolína je k dispozici zítra<br/><br/>Je Chris zdarma v sobotu?|
| Odstranění | Požadavek na odstranění položku kalendáře.| Zrušte Moje s Karolínu. <br/><br/>Odstranit Moje schůzky 9: 00<br/>|
| Upravit | Požadavek na změnu existující schůzce nebo položku kalendáře.| Přesuňte Moje schůzky 9: 00 do 10: 00.<br/><br/>Chci aktualizovat mé plán.<br/><br/>S Ryanem Reschdule Moje schůzky.|
| Vyhledávání | Zobrazí týdenní kalendáře.| Hledání k zubaři Zkontrolujte událost. <br/><br/>Zobrazit kalendář<br/>|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Umístění | Umístění položky kalendáře, schůzce nebo události. Adresy, města a oblasti jsou dobrým příkladem umístění.| 209 americkém Nashvillu Posilovně <br/><br/>897 masopustní house<br/><br/>Garáži|
| Subjekt | Název schůzky nebo události.| Událost zubní lékař. <br/><br/>Oběd s Julia<br/><br/>Lékaři|

## <a name="camera"></a>Fotoaparát 
Doména fotoaparátu poskytuje záměry a entity související s používáním kamery. Příkazů se věnují zachytávání fotky, selfie, snímku obrazovky nebo videa a vysílání videa k aplikaci.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| CapturePhoto| Zachycení fotografii.| Vyfotit<br/><br/>zachycení|
| CaptureScreenshot | Pořízení snímku obrazovky.| Získat snímek obrazovky.<br/><br/>Zachytit obrazovku.|
| CaptureSelfie | Zachycení selfie.| Přijmout selfie <br/><br/>pořídit snímek mě |
| CaptureVideo | Spusťte nahrávání videa.| Spustit nahrávání <br/><br/>Začátek nahrávání|
| StartBroadcasting| Spusťte video všesměrové vysílání.| Počáteční všesměrové vysílání na Facebook|
| StopBroadcasting| Zastavte video všesměrové vysílání.| Zastavit všesměrového vysílání|
| StopVideoRecording| Zastavte záznam videa.| Tím<br/><br/>Zastavit záznam|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace k vysílání video.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Komunikace 
Doména komunikace poskytuje záměry a entity související s e-mailu, zpráv a telefonních hovorů.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddContact| Přidáte nový kontakt seznamu kontaktů uživatele.|Přidat nový kontakt <br/><br/>Uložit toto číslo a uveďte název jako Karolínu|
| AddMore| Přidáte informace do e-mailu nebo textu, jako součást kompozice podle jednotlivých kroků e-mailu nebo text.|Přidat další na text <br/><br/>Přidat další tělo e-mailu|
| Odpověď| Přijetí příchozího telefonního hovoru.|Odpověď volání <br/><br/>Výběr|
| AssignContactNickname| Pojmenovat kontakt.|Změnit Petr táta <br/>Přezdívka Jim úpravy<br/>Přidat Přezdívka Patti Owens|
| CallVoiceMail| Připojte se k uživatele hlasová pošta.|Připojit se k Moje hlasovou poštu políčko <br/>Hlasová pošta<br/>Volání hlasová pošta.|
| CheckIMStatus| Zkontrolujte stav kontakt ve Skypu.|Je Jim jeho online stav nastaven na hned? <br/>Je k dispozici se Karolínu?|
| Potvrdit| Potvrďte akci.|Ano<br/>Dobře<br/>Dobře<br/>Potvrzuji, že chcete odeslat tento e-mail.<br/>|
| Telefonické připojení| Telefonní hovor.|Jan volání<br/>Prosím vytáčení 311<br/>|
| FindContact| Najdete kontaktní údaje podle názvu.|Najít číslo Karolínu<br/>Zobrazit číslo Karolínu<br/>|
| FindSpeedDial| Najdete číslo rychlé vytáčení, telefonní číslo je nastavena na a naopak.|Co je volání čísla 5?<br/>Je nutné rychlost vytáčení sady?<br/>Co je počet volání pro 941-5555-333?|
| GetForwardingsStatus| Získejte aktuální stav předávání volání.|Moje předávání volání zapnutý?<br/>Řekněte mi, pokud je můj stav volání zapnutí nebo vypnutí<br/>|
| GoBack| Přejděte zpět na předchozí krok.|Přejděte zpět na twitteru<br/>Přejděte zpět krok<br/>Zpět|
| Ignorovat| Ignorujte příchozí volání.|Není odpovědět<br/>Ignorovat volání|
| IgnoreWithMessage| Ignorovat příchozí volání a místo toho odpovědět s textem.|Toto volání není odpovědět, ale místo odeslání zprávy.<br/>Ignorovat a odeslala textová zpráva zpět.|
| PressKey| Na klávesnici stiskněte tlačítko nebo číslo.|Volání na hvězdičku.<br/>Stiskněte 1 2 3.|
| ReadAloud| Přečíst zprávu nebo e-mailu pro uživatele.|Přečtěte si text.<br/>Co uživatel Řekněme, že ve zprávě?|
| TurnForwardingOff| Telefonní hovor.|<br/><br/>|
| Vytočit| Vytáčení nebo znovu volat na číslo.|Vytočit.<br/>Vytáčení Moje poslední volání.|
| Odmítnout| Odmítne příchozí volání.|Odmítnutí volání<br/>Nelze přijmout<br/>V tuto chvíli není k dispozici a bude zpětné volání, později.|
| Connectoru| Odeslání e-mailu. Cílem tohoto je se vztahuje na e-mailu, ale ne textové zprávy.|E-mailu do vod Mike: Mike, bylo splendid dinner minulý týden.<br/>Odeslání e-mailu pro Roberta<br/>|
| SendMessage| Odešlete textovou zprávu nebo rychlou zprávu.|Poslat text Chris a Karolínu|
| SetSpeedDial| Nastavte místní volání rychlost pro telefonní číslo kontaktu.|Nastavení telefonního seznamu, jeden pro Karolínu.<br/>Nastavení telefonní pro mom.|
| ShowNext| Zobrazit další položky, například na seznam textových zpráv nebo e-mailů.|Zobrazit další příkaz.<br/>Přejdete na další stránku.|
| ShowPrevious| Viz předchozí položka, třeba na seznam textových zpráv nebo e-mailů.|Zobrazit předchozí.<br/>Předchozí<br/>Přejdete na předchozí.|
| StartOver| Začněte v systému nebo zahájit novou relaci.|Začít znovu<br/>Nová relace<br/>restart|
| TurnForwardingOff| Vypněte přesměrování volání.|Zastaví předávání Moje volání<br/>Vypnout přesměrování volání|
| TurnForwardingOn| Vypněte phone mluvčího.|Předávání Moje volání 3333<br/>Přepnout na předávání volání do 3333|
| TurnSpeakerOff| Vypněte phone mluvčího.|Přeneste do složky vypnout mluvčího.<br/>Vypněte sluchátek s mikrofonem.<br/>|
| TurnSpeakerOn| Zapněte telefon mluvčího.|Režim sluchátek s mikrofonem.<br/>Umístí sluchátek s mikrofonem.<br/>|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AudioDeviceType | Typ zvukové zařízení (mluvčího, sluchátka s mikrofonem, mikrofon atd.).| Přednášející<br/>Bezobslužné<br/>Bluetooth|
| Kategorie | Kategorie zpráv nebo e-mailu.| Důležité<br/>S vysokou prioritou|
| ContactAttribute | Atribut, který uživatel dotazy o kontaktu.| Datum narození<br/>Adresa<br/>Telefonní číslo|
| Jméno kontaktu | Jméno kontaktu nebo zprávy příjemce.| Karolína<br/>Jan<br/>Chris|
| EmailSubject | Text použitý jako řádek předmětu e-mailu.| RE: zajímavé scénář|
| Perokresba | Řádek uživatel chce, aby se k volání nebo odeslání textu nebo e-mailu z.| Řádek práce<br/>Britské buňky<br/>Skype|
| Zpráva | Zpráva k odeslání e-mailu nebo text.| Bylo vynikající schůzku můžete ještě dnes. Podívejte se, můžete to za chvíli znovu.|
| MessageType | Jméno kontaktu nebo zprávy příjemce.| Text<br/>Email|
| OrderReference | Ordinální nebo relativní umístění v seznamu, určení položek k načtení. Například "last" nebo "poslední" v "Jaké byly poslední zprávy odeslané?"| Poslední<br/>Poslední|
| SenderName | Jméno odesílatele.| Patti Owens|

## <a name="entertainment"></a>Zábava  
Ukazuje, zábava, které doména poskytuje záměry a entity související hledání filmy, hudbu, hry a televize.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Search| Hledat videa, Hudba, aplikace, hry a TV ukazuje.|Vyhledejte v obchodě Haló.<br/>Hledání miniatury.|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ContentRating | Hodnocení G nebo R filmů obsahu médií.|Videa pro děti.<br/>PG hodnocení.|
| Genre | Rozšířením podle tematických videa, hry, aplikace nebo skladby.|Comedies<br/>Hrané filmy<br/>Legrační|
| Klíčové slovo| Obecný hledané klíčové slovo zadáním atributu neexistuje mezi sloty konkrétnější média.|Nahrávky především<br/>Řeka měsíce<br/>Amelia Earhart|
| Jazyk | Jazyk použitý v médií, jako jsou mluvený jazyk filmu nebo skladby.|Francouzština<br/>Angličtina<br/>Korejština|
| MediaFormat | Další technické speciální typ ve kterém je formátováno média.|HD, High Density filmy<br/>3D filmy<br/>Ke stažení|
| MediaSource | Úložiště nebo webu marketplace k získání média.|Netflix<br/>Prime|
| MediaSubTypes| Typy médií je menší než filmy a hry.|Ukázky<br/>DLC<br/>Přípojných|
| Národnosti| Země, ve kterém byla vytvořena videa, zobrazit nebo skladby.|Francouzština<br/>Němčina<br/>Korejština|
| Person (Osoba)| Objekt actor, ředitel, výrobce, hudebníkem nebo interpreta přidružené videa, aplikaci, hru nebo televizního pořadu.|Madonna<br/>Stanley Kubrick|
| Role| Roli hrají osoby při vytváření média.|Souboru<br/>Podle<br/>Od společnosti|
| Titul| Název filmu, aplikace, hry, televizního pořadu nebo skladby.|Přátelé<br/>Minecraftu.|
| Typ| Formát typu nebo media Video, aplikace, hry, televizního pořadu nebo skladby.|Hudba<br/>MovieTV <br/>Ukazuje|
| UserRating| Uživatel star nebo thumbs hodnocení.|5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičky|

## <a name="events"></a>Události 
Doména události poskytuje záměry a entity související s rezervace letenek pro události, jako jsou koncerty, festivalů, sportovní hry a komedie ukazuje.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Book| Nákup lístků na událost.|Chci si chcete koupit lístku symphony tento víkendu.|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa | Umístění události nebo adresu. |Palo Alto<br/>300 112th uložit SE <br/> Seattle |
| Název | Název události.|Shakespeare v Park|
| PlaceName| Název události umístění.|Louvre<br/>Opera House<br/>Broadway|
| PlaceType | Typ umístění událost bude uchovávat v datovém typu.|Kavárna<br/>Oblasti divadla<br/>Knihovna|
| Typ | Typ události.|Vzájemné součinnosti<br/>Sportovní hra|

## <a name="fitness"></a>Způsobilost 
Doména vhodnosti poskytuje záměry a entity související vhodnosti aktivity pro sledování. Příkazů zahrnují ukládání poznámky, zbývající čas nebo vzdálenost nebo uložení výsledků aktivity.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddNote| Přidá další poznámky sledované aktivity.|Problémy s dokončením tohoto spuštění nastavený na hodnotu 6/10<br/>Terénu, které využívám systémem je asfaltů<br/>Používám kolo 3 rychlost|
|GetRemaining| Získá zbývající čas nebo vzdálenost pro aktivitu.|Kolik času do další okruh?<br/>Kolik mil zůstávají ve Můj test? Kolik času pro rozdělení?|
| LogActivity| Uložit nebo protokolovat výsledky dokončené aktivity.|Uložit Moje poslední spuštění<br/>Protokolovat Moje funkce walk soboty<br/>Uložit Moje předchozí drah|
| LogWeight| Uložit nebo protokolování hmotnost aktuálního uživatele.|Uložit Moje aktuální hmotnost<br/>Moje váha nyní protokolu<br/>Uložit Moje aktuální váha textu|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ActivityType | Typ aktivity pro sledování. |Spusťte<br/>Chůze<br/>Drah<br/>Cyklus |
| Potravinářství | Typ potravin můžete sledovat v vhodnosti aplikace. |Banánů<br/>Lososová<br/>Bílkovin zatřesením|
| MealType| Typ jídla ke sledování stavu nebo vhodnosti aplikace.|Snídani<br/>Společnost dinner<br/>Oběd<br/>Supper|
| Měření| Typ měření času, vzdálenost či váha pro použití v aplikaci vhodnosti nebo stavu.|Kilometrů<br/>Mil<br/>Minut<br/>Kg|
| Číslo | Číselné množství pro použití v aplikaci vhodnosti nebo stavu.|19<br/>tři<br/>200<br/>jeden|
| StatType | Typ statistiky pro agregovaná data pro použití v aplikaci vhodnosti nebo stavu.|Součet<br/>Průměr<br/>Maximum<br/>Minimální|

## <a name="gaming"></a>Hraní her 
Doména herní poskytuje záměry a entity související se správou herní stran hry pro více hráčů.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| InviteParty| Libovolnému připojit herní stran.|Pozvat tento přehrávač Moje straně<br/>Přijďte na můj stran<br/>Připojte se k Moje klan|
|LeaveParty| Získá zbývající čas nebo vzdálenost pro aktivitu.|Já jsem si<br/>Můžu jsem byste museli opustit tuto stranu dalších<br/>Jsem ukončení programu|
| StartParty| Začněte strana herní hry pro více hráčů.|Kamaráde Pojďme začít strana<br/>Spustit strana<br/>by měl začneme klan dnešní večer|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Kontakt| Jméno kontaktu pro použití v hry pro více hráčů.|Karolína<br/>Jan|


## <a name="homeautomation"></a>HomeAutomation 
Doména HomeAutomation poskytuje záměry a entity související s řízením inteligentní domácí zařízení, jako jsou indikátory a zařízení.

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Vypnutí| Vypnout, zavřít nebo odemknutí zařízení.|Vypnout světla<br/>Zastavit kávy maker<br/>Zavřít garážových|
|TurnOn| Zapněte zařízení nebo nastavte zařízení do režimu a konkrétní nastavení.|zapnout Moje kávy maker<br/>můžete zapnout Moje kávy maker?<br/>Nastavte termostat 72 stupňů.|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Zařízení | Typ zařízení, které je možné zapnout nebo vypnout.|kávy maker<br/>Termostat<br/>indikátory|
| Operace | Stav nastavení zařízení.|Zámek<br/>open (otevírá)<br/>zapnuté<br/>vypnuto|
| Místnosti | Umístění nebo místnosti, ve kterém zařízení nachází.|Obývací pokoj<br/>ložnici<br/>kuchyně|

## <a name="movietickets"></a>MovieTickets 
Doména MovieTickets poskytuje záměry a entity související rezervace letenek na filmy v celé obrazovky video.

### <a name="examples"></a>Příklady
```
Book me two tickets for Captain Omar and the two Musketeers
Cancel tickets
When is Captain Omar showing?
```

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Book | Nákup lístků video.|Rezervuje dvě lístky pro vedoucího Omar a dvě musketeers<br/>Budu chtít koupit lístek filmu dneška<br/>Chci, aby lístek pro 2. část Omar Captian další středa|
|GetShowTime| Získejte showtime videa.|Když se zobrazuje vedoucího Omar?|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa | Adresa celé obrazovky video.|Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| MovieTitle | Název filmu.|Životnosti Pi<br/>Hladu hry<br/>Zahájení|
| PlaceName | Název filmu celé obrazovky nebo kina.|Kina Amir<br/>Oblasti divadla Alexandrii<br/>New York celé obrazovky|
| PlaceType | Typ umístění videa se zobrazuje v.|kina<br/>celé obrazovky<br/>IMAX kina|

## <a name="music"></a>Hudba 
Doména Hudba poskytuje záměry a entity související přehrávání hudby v hudební přehrávač.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Přehrát Beethoven|
|zesílení hlasitosti sledování|
|Přeskočit na další skladbu|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| DecreaseVolume | Zeslabení hlasitosti zařízení.|Zeslabení hlasitosti sledování<br/>svazek dolů|
| IncreaseVolume | Zesílení hlasitosti zařízení.|zesílení hlasitosti sledování<br/>svazek nahoru|
| Ztlumit |Ztlumte přehrávání hudby.|Ztlumit skladby<br/>Vložit jeden směr určený na ztlumení<br/>Ztlumit Hudba |
| Pozastavení | Pozastavte přehrávání hudby.|Pozastavení<br/>Pozastavit Hudba<br/>Pozastavit sledování|
| PlayMusic | Přehrávání hudby v zařízení.|Přehrát Kevin Durant<br/>Přehrát Paradise podle Coldplay<br/>Přehrát Hello podle Adele|
| Opakovat |Přehrávání hudby opakujte.|Při opakovaném skladby<br/>Přehrát takto získané místo sledování<br/>Při opakovaném Hudba|
| Obnovit | Obnoví přehrávání hudby.|Skladby Resume<br/>Znovu spusťte Hudba<br/>Zrušení pozastavení|
| SkipBack | Přeskočte zpět stopy.|Přeskočit na další skladbu<br/>Přehrát další skladbu|
| SkipForward |Přeskočte dopředu sledování.|Přehrát předchozí skladbu<br/>Přejděte zpět na předchozí stopa |
| Zastavit | Zastavte akce týkající se přehrávání hudby. |Zastavte tento obrázek.|
| Zrušit ztlumení | Zrušit ztlumení zařízení pro přehrávání hudby.| Zrušit ztlumení.|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ArtistName | Objekt actor, ředitel, výrobce, zapisovače, hudebníkem nebo interpreta související s médii hrát na zařízení.|Elvis Presley<br/>Kód Swift Taylora<br/>Adele<br/>Mozart|
| Genre | Rozšířením podle tematických Hudba žádá.|Hudba země<br/>Klasické Broadway<br/>Přehrát Hudba klasického z Baroque období|

## <a name="note"></a>Poznámka 
Poznámka: domény poskytuje záměry a entity týkající se vytváření, úpravám a hledání poznámky.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Přidat do mé nákup Poznámka salát rajčete bread kávy|
|Zaškrtávat bananas ze seznamu blízkým|
|Odebrat všechny položky ze seznamu o dovolenou|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddToNote | Informace o přidání poznámky.|Přidat do mé nákup Poznámka salát rajčete bread kávy<br/>Přidat do seznamu todo<br/>Přidat cupcakes Moje Wunderlistu|
| CheckOffItem | Zaškrtněte položky z již existujících poznámek.|Zaškrtávat bananas ze seznamu blízkým<br/>Označit produkci minipivovarů ve wisconsinu dort na můj sváteční nákupní seznam jako hotové|
| Vymazat | Vymažte všechny položky z již existujících poznámky.|Odebrat všechny položky ze seznamu o dovolenou<br/>Vymazat vše ze seznamu čtení|
| Potvrdit | Potvrďte akci vztahující se na poznámku.|Je to v pořádku mnou<br/>ano<br/>Můžu jsem potvrzení zachovat všechny položky v seznamech|
| Vytvořit | Vytvoří novou poznámku. | Vytvořit seznam<br/>Všimněte si připomenout, že Jason je ve městě první týden v květnu|
| Odstranění | Odstraníte celý poznámku. |Odstranění seznamu o dovolenou <br/>Odstranit poznámku Moje nákup|
| DeleteNoteItem | Odstranění položek z existující poznámky.| Odstranit ze seznamu blízkým čipy<br/>Odebrat z mojí školy nákupní seznam pera|
| ReadAloud | Seznam číst nahlas.|Soubor Readme první z nich<br/>Soubor Readme podrobnosti|
| ShowNext | Podívejte se na další položku v seznamu poznámky.|Zobrazit další příkaz<br/>Další stránka<br/>Další|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace pořizování poznámek.|Wunderlist<br/>OneNote|
| Jméno kontaktu | Jméno kontaktu v poznámce.|Karolína<br/>Jan<br/>Chris|
| Zdroj dat | Umístění poznámky.|OneDrive<br/>Dokumenty Google<br/>Tento počítač|
| DataType | Typ souboru nebo dokumentu, obvykle spojené s konkrétní softwarové programy.|Snímky<br/>Tabulka<br/>List|
| Text | Text poznámky nebo připomenutí.|roztažení před procházení<br/>dlouho běžící zítra|
| Titul | Název poznámky.|Nákup<br/>uživatelům volání<br/>úkol|

## <a name="ondevice"></a>OnDevice 
Doména OnDevice poskytuje záměry a entity související řízení zařízení.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Zavřete přehrávače videa|
|Zrušit přehrávání|
|Můžete vytvořit obrazovky světlejší?|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| AreYouListening | Dotaz, pokud zařízení naslouchá.|je to na?<br/>které naslouchají?|
|CloseApplication|Ukončete aplikaci zařízení.|Zavřete přehrávače videa|
|FileBug|Oznámit chybu na zařízení.|oznámit chybu prosím<br/>Můžete oznámit chybu pro mě nejlepší?<br/>Chci ohlásit tuto chybu|
|GoBack|Požádejte o jeden krok zpět nebo vrátit na předchozí krok.|Vraťte se prosím<br/>Přejít na předchozí obrazovku<br/>Přejít zpět stop naslouchání|
|Nápověda| Požádat o pomoc.|Nápověda prosím<br/>Dobrý den,<br/>Co můžete dělat?<br/>Potřebuji pomoc s| 
|LocateDevice|Vyhledání zařízení.|Můžete najít Můj telefon<br/>Najít iphone Tomáš.<br/>Najít Můj telefon|
|Přihlášení|Přihlaste se ke službě pomocí zařízení.|Přihlášení prosím<br/>Přihlášení k Facebooku<br/>Přihlaste se k Linkedinu|
|Odhlášení|Odhlaste se z služby pomocí zařízení.|Odhlášení mi na telefon<br/>Přihlaste se na twitteru<br/>Odhlásit se|
|MainMenu|Zobrazte hlavní nabídky zařízení.|Zobrazení nabídky.|
|OpenApplication|Otevřete aplikaci na zařízení.|Otevřete prosím alarm<br/>Zapnout fotoaparátu<br/>Spusťte kalendáře|
|OpenSetting|Otevřete nastavení na zařízení.|Otevřete nastavení sítě.|
|PairDevice|Pár zařízení.|Můžete mi může pomoci v párování Bluetooth signálu do telefonu<br/>Zapněte bluetooth a spárujte ho s přenosných počítačů<br/>Párování Bluetooth signál na můj notebook|
|Vypnout počítač | Vypněte zařízení.|Můžete vypnout počítač<br/>Shutdown<br/>Vypnout mobilní telefon|
|QueryBattery|Získejte informace o výdrži baterie.|Zobrazit výdrž baterie.<br/>Co je můj stav baterie<br/>Kolik baterie nyní?<br/>Zobrazit stav baterie|
|QueryWifi|Získejte informace o Wi-Fi.|Získání informací o Wi-Fi.|
|Restartování|Restartujte zařízení.|Restartujte prosím.|
|RingDevice| Požádejte zařízení kanál, aby bylo možné ji najít, když bylo ukončeno.|Cyklické mi na telefon.| 
|SetBrightness|Nastavte jas zařízení.|Nastavení jasu na médium<br/>Nastavení jasu na vysoká<br/>Nastavení jasu na nízkou prioritu|
|SetupDevice|Zahajte instalaci zařízení.|Nainstalovat Instalační program operačního systému<br/>Nastavte prosím<br/>Proveďte nastavení pro mě|
|ShowAppBar|Zobrazte indikátor aplikace.|Zobrazit na panelu aplikace<br/>Prosím panel aplikace<br/>Chci zobrazit na panelu aplikace|
|ShowContextMenu|Zobrazení místní nabídky.|Chci zobrazit kontextovou nabídku<br/>Místní nabídka prosím<br/>Můžete je zobrazit v místní nabídce|
|Přejít do režimu spánku|Umístěte zařízení do režimu spánku.|Jdi spát<br/>Přejít do režimu spánku<br/>Můj počítač v režimu spánku|
|SwitchApplication|Přepněte aplikace pro použití na zařízení.|Přepnout na můj media Playeru.|
|TurnDownBrightness|Snižte jasu zařízení.|Dim na obrazovce.|
|TurnOffSetting|Vypněte nastavení zařízení.|Deaktivovat Bluetooth<br/>Zakázat dat<br/>Odpojit bluetooth|
|TurnOnSetting|Zapněte nastavení zařízení.|Zapnuto <br/> Vypnuto|
|TurnUpBrightness|Objevit jas zařízení.|Můžete vytvořit obrazovky světlejší?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AppName | Název aplikace na zařízení.|SoundCloud<br/>YouTube|
| BrightnessLevel | Nastaví úroveň jasu na zařízení.|100 %<br/>Padesát<br/>40%|
| Jméno kontaktu | Jméno kontaktu na zařízení.|Paul<br/>Maximální počet Marlen|
| deviceType | Typ zařízení. |Telefon<br/>Kindle<br/>Přenosný počítač|
| mediaType | Typ média stará zařízení.|Hudba<br/>Video<br/>Televizní pořady|
| Typem SettingType | Typ nastavení nebo nastavení panelu, který chce uživatel upravit.|Wi-Fi<br/>Bezdrátové sítě<br/>Barevné schéma<br/>Centrum oznámení|

## <a name="places"></a>Místa  
Doména míst poskytuje příkazy pro zpracování dotazů souvisejících s jako firmám, instituce, restaurace, veřejné mezery a adresy.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Toto umístění pro uložení k oblíbeným|
|Jak daleko je sváteční DIČ?|
|Zároveň Safeway zavřít?|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Přidáte složku do seznamu oblíbených položek uživatele.|Toto umístění pro uložení k oblíbeným<br/>Přidat tuto adresu k oblíbeným|
|CheckAccident|Zadejte dotaz, zda je v zadané cesty havárie.|Je na 880 nehody?<br/>Zobrazit informace o havárii|
|CheckAreaTraffic|Kontrola provozu pro obecné oblasti nebo highway, ne na zadanou trasu.|Provoz v Praze<br/>Co je provoz například v Praze?|
|CheckIntoPlace|Vrácení se změnami do místo použití sociálních médií.|Vrátit se změnami mě na Foursquare<br/>Tady se změnami|
|CheckRouteTraffic| Kontrola provozu konkrétní trasy specifikovaných uživatelem.|Jak se provoz do Mashiko?<br/>Zobrazit traffice k Kirkland<br/>Jak se provoz do Seattlu?| 
|Potvrdit|Potvrďte akci vztahující se na místo.|Potvrďte restaurace rezervace.|
|Konec|Akce pro ukončení úlohy týkající se na místo.|Ukončete prosím<br/>Ukončit, mi dává pokynů|
|FindPlace|Hledat na místě (business, instituce, restaurace, prostor veřejných adres).|Kde je nejbližší knihovny?<br/>Najít dobré italské restaurace v zobrazení Horská oblast|
|GetAddress| Požádejte o adresu místo.|Zobrazit adresu Guu na Robson ulice<br/>Jaká je adresa nejbližší Starbucks?| 
|GetDistance|Požádejte o vzdálenost na konkrétní místo.|Jak daleko je sváteční DIČ?<br/>jak daleko je Bellevue Čtvereček odsud<br/>Co je vzdálenost k Tahoe|
|Gethours –|Požádejte o provozní dobu na místě.|Zároveň Safeway zavřít?<br/>Co jsou hodiny pro Domů Depot?<br/>Je stále otevřen Starbucks?|
|GetMenu|Požádejte o položky nabídky pro restauraci.|Nemá Zucca sloužit nic veganská?<br/>Co je v nabídce v Sizzler<br/>Zobrazit nabídku společnosti Applebee|
|GetPhoneNumber| Požádejte o telefonní číslo na místě.|Co je telefonní číslo nejbližší Starbucks?<br/>Zadejte číslo pro Domů Depot| 
|GetPriceRange| Vyzve k zadání rozsahu cena na místě.|Je Zucca levné?<br/>Ve středu je Cineplex poloviční cenu?<br/>Kolik stojí celý lobster večeře v Sizzler?|
|GetReviews|Požádejte o kontroly na místě.|Zobrazit objektu pro vytváření Cheesecase revize<br/>Přečtěte si recenze Cineplex v Yelp|
|GetRoute|Požádejte o pokyny na místo.|Jak vás Čtvereček Bellevue<br/>Zobrazit nejkratší způsob, jak 8. a 59th odsud<br/>Získat mě pokynů k certifikační Autoritě Horská oblast zobrazení|
|GetStarRating|Požádejte o hodnocení hvězdičkami místa.|Jak Zucca hodnocením podle Yelp?<br/>Kolik hvězdiček prací francouzština máte?<br/>Je dobré aquarium v Monterrey?|
|GetTransportationSchedule|Získáte plán Service bus pro místo.|Kdy je další Service bus k centru?<br/>Zobrazit sběrnice v okrese King County|
|GetTravelTime|Požádejte o dobu trvání cesty do určeného cíle.|Jak dlouho bude trvat odsud se dostat k San Francisku<br/>Co je doby jízdy okolo něj k Denver z SF|
|Funkce MakeCall|Telefonní hovor na místo.|Volání mom<br/>Chci umístit volání Skype Anna<br/>Jan volání|
|MakeReservation|Požádat o rezervaci restaurace nebo jiné firmě.|Rezervovat na Zucca pro dvě pro tonight<br/>Kniha tabulky na zítřejší den<br/>Tabulka 3 v Palo Alto na 8|
|MapQuestions|Žádost o informace o pokynů nebo určuje, zda zadaný silniční přejde do cíle.|Předat 13 prostřednictvím centru?<br/>Můžete provést 880 při Oakland?|
|Hodnocení|Získáte popis hodnocení restaurace nebo místa.|Kolik hvězdiček DIČ Contoso má?|
|ReadAloud|Seznam míst číst nahlas.|Soubor Readme první z nich<br/>Soubor Readme podrobnosti|
|Selectitem –|Zvolte položku ze seznamu voleb týkajících se místa nebo místa.|Vyberte druhou<br/>Vyberte první|
|ShowMap|Zobrazit mapu oblast.|Zobrazit mapu pro druhý<br/>Zobrazit mapu<br/>Najít kalifornském San Franciscu na mapě|
|ShowNext|Zobrazte další položky v řadě.|Zobrazit další příkaz<br/>Přejít na další stránku|
|ShowPrevious|Zobrazte předchozí položky v řadě.|Zobrazit předchozí<br/>předchozí<br/>Přejít na předchozí|
|StartOver|Restartování aplikace nebo zahájit novou relaci.|Začít znovu<br/>Nová relace<br/>
restart|
|TakesReservations|Dotaz, jestli místo přijímá rezervace.|Nepřijme umělecké galerie rezervace<br/>Je možné provádět rezervace na kachnami Olivově

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Umístění nebo adresu místo.|Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| Vymoženosti | Cíle vlastnosti/výhody na místě.|děti jíst zdarma<br/>waterfront<br/>parkovací zdarma|
| Prostředí | Prostředí na místě.|dětský zařízení<br/>příležitostné restaurace<br/>Sporty|
| Kuchyni | Kuchyni na místě. |Středozemního<br/>italština<br/>Území v Indickém|
| DestinationAddress| Cílové umístění nebo adresu.|Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| DestinationPlaceName| Název cíl, který je firmy, restaurace, veřejné přitažlivosti nebo instituce.|centrální park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Zadejte cíl, který je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Restaurace<br/>Opera<br/>kina|
| Vzdálenost | Vzdálenost na místo.|15 mil.<br/>5 mil.<br/>10 mil okamžitě|
| MealType | Typ jídla jako snídani nebo oběd. |Snídani<br/>Společnost dinner<br/>Oběd<br/>Supper|
| OpenStatus | Určuje, zda místo, kde je otevřeno nebo Uzavřeno.|Otevřenost<br/>Uzavřeno<br/>Otevření|
| PlaceName | Název na místě.|Objekt pro vytváření cheesecake|
| PlaceType | Typ na místě.|Kavárna<br/>Oblasti divadla<br/>Knihovna|
| PreferredRoute | O trasu upřednostňovanou specifikovaných uživatelem. | 101 <br/>202 <br/>Trasa 401|
| Produkt | Produkt nabízí na místě. | Oblečení<br/>Azure Site Recovery digitální fotoaparáty<br/>Čerstvé ryb | 
| PublicTransportationRoute | Název veřejné transportation trasy, který je vyhledat uživatele. | Severovýchodní ose trénování<br/>Route Service bus 3 X |
| Hodnocení | Hodnocení na místě. | 5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičky|
| RouteAvoidanceCriteria | Kritéria pro vyloučení konkrétních trasy, jako jsou zabraňující havárií, konstrukce nebo mýtné | Mýtné <br/>Konstrukce<br/>Trasa 11|
| ServiceProvided | Toto je služba poskytovaná podnikový proces nebo místo například kadeřnictví, sníh plowing, terénní úpravy. | kadeřnictví<br/>Mechanic<br/>instalatér|
| TransportationCompany | Název zprostředkovatele přenosu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ dopravy.|Service Bus<br/>Trénování<br/>Autem|

## <a name="reminder"></a>Připomenutí 
Doména připomenutí poskytuje záměry a entity pro vytváření, úprav a vyhledání připomenutí.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Změnit Moje rozhovor 9: 00 zítra|
|Připomenout si chcete koupit mléka na můj způsob zpět domů|
|Můžete je zkontrolovat, pokud mám připomenutí týkající se vaší Jana datum narození?|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Změnit| Změňte připomenutí.|Změnit Moje rozhovor 9: 00 zítra<br/>Přesunout Moje přiřazení připomenutí na zítřejší den|
| Vytvořit| Vytvořte nové připomenutí.|Vytvoření připomenutí<br/>Připomenout si chcete koupit mléka<br/>Chci nezapomeňte volat Milena při jmenuji se během chvilky|
| Odstranění | Odstraňte připomenutí.|Odstranit tento obrázek připomenutí<br/>Odstranit toto připomenutí|
| Vyhledávání | Najdete připomenutí.|Musím připomenutí o mé výročí?<br/>Můžete je zkontrolovat, pokud mám připomenutí týkající se vaší Jana datum narození?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Text | Textový popis připomenutí.|sbírání suchého čištění<br/>vyřazení mém autě v centru služby|

## <a name="restaurantreservation"></a>RestaurantReservation 
Doména RestaurantReservation poskytuje záměry a entity související se správou restaurace rezervace.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Rezervovat na Zucca pro dvě pro tonight|
|Kniha tabulku BJ společnosti na zítřejší den|
|Tabulka 3 v Palo Alto na 7|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Rezervovat | Požádat o rezervaci pro restauraci. |Rezervovat na Zucca pro dvě pro tonight<br/>Kniha tabulky na zítřejší den<br/>Tabulka 3 v Palo Alto na 7|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa| Umístění události nebo adresy pro rezervaci.|Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| Vymoženosti | Atribut popisující vymoženosti na místě.|zobrazení na oceánských<br/>bez kouření|
| AppName | Název aplikace pro provedení rezervace.|OtevřítTabulku<br/>Yelp<br/>TripAdvisor|
| Prostředí | Popis prostředí restaurace nebo druhém místě.|Romantický<br/>příležitostné<br/>vhodné pro skupiny|
| Kuchyni | Typ food, kuchyni nebo kuchyni příslušnosti. |Čínština<br/>italština<br/>Mexické|
| MealType | Typ jídla přidružené k rezervaci.|Snídani<br/>Společnost dinner<br/>Oběd<br/>Supper|
| PlaceName | Název místní firmy, restaurace, veřejné přitažlivosti nebo instituce.|IHOP<br/>Objekt pro vytváření cheesecake<br/>Louvre|
| PlaceType | Typ místní firmy, restaurace, veřejné přitažlivosti nebo instituce.|Restaurace<br/>Opera<br/>kina|
| Hodnocení | Hodnocení místa nebo restauraci.|5 hvězdiček<br/>3 hvězdičky<br/>4 hvězdičky|

## <a name="taxi"></a>Taxislužby města 
 
Doména taxislužby poskytuje záměry a entity pro vytváření a Správa rezervací taxislužby.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Získání souboru cab v 15: 00|
|Jak dlouho je nutné čekat Moje taxislužby?|
|Zrušit Moje Uber|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Book | Volání taxislužby. |Načítání me souboru cab<br/>Najít taxislužby města<br/>Rezervuje mě uber x|
| Zrušit | Zrušte akci týkajících se rezervace taxislužby.|Zrušit Moje taxislužby města<br/>Zrušit Moje Uber|
| sledování | Sledování postupu taxislužby.|Jak dlouho je nutné čekat Moje taxislužby?<br/>Kde je Moje Uber?|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Adresa| Adresa přidružená k rezervace taxislužby. |Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| DestinationAddress| Cílové umístění nebo adresu. |Palo Alto<br/>300 112th uložit SE<br/>Seattle|
| DestinationPlaceName | Název cíl, který je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Centrální Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Zadejte cíl, který je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. |Restaurace<br/>Opera<br/>kina|
| PlaceName | Název místní pracovní, restaurace, veřejné přitažlivosti nebo instituce. |Centrální Park<br/>Safeway<br/>Walmart|
| PlaceType| Typ místa v žádosti o rezervovat taxislužby.|Restaurace<br/>Opera<br/>kina|
| TransportationCompany | Název zprostředkovatele přenosu.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Typ dopravy.|Service Bus<br/>Trénování<br/>Autem|

## <a name="translate"></a>Překlad 
Doména přeložit poskytuje záměry a entity související překlad textu do cílového jazyka.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Převést na Francouzština|
|Přeložit hello na němčinu|
|Převede tuto větu na angličtinu|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Překlad| Překlad textu do jiného jazyka.|Převést na Francouzština<br/>Přeložit hello na němčinu|


### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| TargetLanguage | Cílový jazyk překlad.|Francouzština<br/>Němčina<br/>Korejština|
| Text | Text na překlad|Hello World<br/>Dobré ráno<br/>Dobrý večer|

## <a name="tv"></a>TV 
 
TV domény poskytuje pro řízení televizorů záměry a entity.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Přepínač kanálu BBC|
|Zobrazit televize|
|Podívejte se na geografické národní|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| ChangeChannel| Kanál na Televizi změňte.|Kanál změn na CNN<br/>Přepínač kanálu BBC<br/>Přejděte do kanálu 4|
| Showguide –| Zobrazit Průvodce TV.|Zobrazit televize<br/>Co je ve filmu kanálu teď?<br/>Zobrazit seznam programu|
| WatchTV| Požádejte o podívejte se kanál.|Budu chtít sledovat kanál Disney<br/>přejděte prosím na TV<br/>Podívejte se na geografické národní|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| ChannelName | Název kanál.|CNN<br/>BBC<br/>Video channel|

## <a name="utilities"></a>Veřejné služby  
Domény nástroje zajišťující úlohy, které jsou společné pro mnoho úloh, jako je například greetings, zrušení, potvrzení, Nápověda, opakování, navigace, spouštění a zastavování záměry.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Přejděte zpět na Twitteru|
|Pomozte|
|Opakujte prosím poslední dotaz|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Zrušit | Zrušte akci.|Zrušit zprávy<br/>Nechci už odeslat e-mailu|
| Potvrdit | Potvrďte akci.|Ano ale potvrzuji, že<br/>Kvalitní můžu jsem potvrzení<br/>Dobře I jsem potvrzení|
| FinishTask | Dokončení úkolu uživatel začal.|Už mám všechno<br/>Dokončena<br/>To se dělá|
| GoBack | Vrátit zpět o jeden krok nebo návrat k předchozímu kroku.|Přejděte zpět na Twitteru<br/>Přejděte zpět krok<br/>Zpět|
| Nápověda | Žádost o pomoc.|Pomozte<br/>Otevření nápovědy<br/>Nápověda|
| Opakovat | Opakujte akci.|Opakujte prosím poslední dotaz<br/>Opakujte poslední skladby|
| ShowNext | Zobrazte další položky v řadě. |Zobrazit další příkaz<br/>Přejít na další stránku|
| ShowPrevious | Zobrazte předchozí položky v řadě.|Zobrazit předchozí|
| StartOver | Restartování aplikace nebo zahájit novou relaci.|Začít znovu<br/>Nová relace<br/>restart|
| Zastavit | Zastavte akci.| Zastavit oznámením této<br/>Zmlkni<br/>Zastavte prosím|

## <a name="weather"></a>Počasí 
Doména počasí poskytuje záměry a entity pro zprávy o počasí a předpovědi.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|o počasí v Londýně v září|
|Co? s 10 dní prognózy?|
|Co je průměrná teplota v Indii v září?|


### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| GetCondition | Získejte historické údaje týkající se počasí. |o počasí v Londýně v září<br/>Co je průměrná teplota v Indii v září?|
| GetForecast | Získá aktuální počasí a předpovědi na během několika dalších dnů. |Jak se počasí dnes?<br/>Co je 10 dní prognózy?<br/>Jak se tento víkendu počasí|

### <a name="entities"></a>Entity
| Název entity | Popis | Příklady |
| ---------------- |-----------------------|----|
| Umístění| Absolutní umístění pro žádost o počasí.|Seattle<br/>Paříž<br/>Palo Alto|

## <a name="web"></a>Web 
Doména webové poskytuje záměru pro přechod na web.

### <a name="examples"></a>Příklady

|Příklady|
|--|
|Přejděte na facebook.com|
|Přejít na www.twitter.com|
|Přejděte na www.bing.com|

### <a name="intents"></a>Záměry
| Název záměru | Popis | Příklady |
| ---------------- |-----------------------|----|
| Navigace | Požadavek na přejděte do zadaného webu. |Přejděte na facebook.com<br/>Přejít na www.twitter.com|

