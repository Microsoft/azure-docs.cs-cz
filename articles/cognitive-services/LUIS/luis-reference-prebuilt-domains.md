---
title: Předem sestavený odkaz na doménu – LUIS
titleSuffix: Azure Cognitive Services
description: Referenční informace pro předem připravených domén, které jsou předem sestavené kolekce záměry a entity z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: b840f1ce42c9d7e4af8854a2c6bd7fd26f5b88e9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307432"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Předem sestavený odkaz na doménu pro aplikaci LUIS
V tomto materiálu najdete informace o [předem připravených domén](luis-how-to-use-prebuilt-domains.md), které jsou předem sestavené kolekce záměry a entity, které nabízí služba LUIS.

[Vlastní domény](luis-how-to-start-new-app.md), naopak spustit bez záměry a modely. Všechny předem připravených domény záměry a entity můžete přidat do vlastního modelu.

# <a name="supported-domains-across-cultures"></a>Podporované domény napříč kulturami

Následující tabulka shrnuje aktuálně podporované domény. Podpora angličtiny je obvykle více dokončena než jiné. 

| Typ entity       | EN-US      | ZH-CN   | DE    | FR     | ES    | it      | PT – BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Kalendář](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Telecommunication](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [E-mailu](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Poznámky](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Zadá](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [ToDo](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Nástroje](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Před](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Předem připravené domény nejsou v nástroji **podporované** :

* Francouzština (Kanada)
* Hindština
* Španělština – Mexiko

# <a name="description-for-luis-prebuilt-domains"></a>Popis pro předem sestavené domény LUIS
## <a name="calendar"></a>**Kalendář**
Kalendář je cokoli o osobních schůzkách a událostech, ne o veřejných událostech (například na světových plánech, kalendáři událostí v Seattlu) nebo v obecném kalendáři (například v dnešním dni).
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------------
 AcceptEventEntry | Přijme v kalendáři (n) událost/schůzku/událost/událost. | Přijmout schůzku <br> Přijmout událost <br> Přijměte dnešní schůzku.
 Zrušit | Zruší probíhající akci od virtuálního pomocníka, například zrušení procesu vytvoření schůzky. <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci zrušit ve scénáři kalendáře. Pokud pro příkaz Cancel potřebujete obecný výraz, použijte možnost zrušit záměr v doméně **nástroje** . * | Je to v pořádku, stačí událost zrušit. <br> Ne, pouze zrušit schůzku.
 ChangeCalendarEntry | Změňte nebo znovu Naplánujte záznam v kalendáři. | Změnit plán na 6 dop. zítřejší událost zítra do 2 ODP. <br> Změnit plán události lékaře na 5 odp. <br> Naplánujte si oběd s tomash Olson do pátku. <br> Změna času události
 CheckAvailability | Zjistěte dostupnost pro událost nebo schůzku na uživatele nebo jinou osobu kalendáře. | Pokud je k dispozici podle Jima? <br> Zobrazit, kdy je Karolínu k dispozici zítra <br> Je Chris zdarma v sobotu?
 Potvrdit | Potvrďte, jestli se má provést operace nebo akce na základě předchozího záměru. <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci potvrdit pro scénář kalendáře. Pokud potřebujete obecnější výrazy o "Confirm", využijte možnost potvrdit záměr v doméně **nástrojů** . *| To je správné, vytvořte prosím schůzku. <br> Ano, děkuji, připojovat se k schůzce.
 ConnectToMeeting | Připojte se k schůzce. | Připojte mě k 11:00 konferenčnímu hovoru s Andy. <br> Přijměte volání rozpočtové schůzky.
 ContactMeetingAttendees | Kontaktujte účastníky schůzky. | Sdělte schůzi, kterou používám v souvislosti s 3:00 schůzkou. <br> Informování kolegů na 8 se doplněním, že musí začít od 8:30.
 CreateCalendarEntry | Přidáte novou položku jednorázové do kalendáře. | Vytvoří schůzku o diskuzi o problémech. <br> vytvořit schůzku sabc@microsoft.com
 DeleteCalendarEntry | Požadavek na odstranění položku kalendáře.| Zrušte Moje s Karolínu. <br> Odstranit mou 9 dop. žádost. <br> Odstraní moji událost.
  FindCalendarEntry | Otevřete aplikaci kalendáře nebo vyhledejte záznam v kalendáři. | Hledání k zubaři Zkontrolujte událost. <br> Zobrazit můj kalendář <br> Co je v kalendáři ve čtvrtek?
 FindCalendarWhen | Ověřte čas, kdy plán proběhne. | Kdy se mám doplňovat se žlutou a Zuzana? <br> Kdy mám naplánované brunch? 
 FindCalendarWhere | Ověřte místo, kde se plán provádí. | Kde jsou moje schůzky zítra? <br>Kde mám na večeři schůzku s Stacy a Michael zítra?
  FindCalendarWho | Ověřte účastníka (účastníky), kteří se budou přizúčastnit k cílovému plánu. | Chci potvrdit na dnešních setkáních na 2. <br> Bude jim na příští schůzi zdravotní sestry?
 FindCalendarDetail | Zkontroluje a zobrazí podrobnosti o plánu. | Potřebuji, abyste si zobrazili podrobnosti o schůzce, kterou jsem naplánovali na moji Pavla.
 FindDuration | Ověřte dobu trvání. | Kolik času musím vyzvednutím nákupů? <br> Jak dlouho mám na oběd?
 FindMeetingRoom | Najděte dostupné místnosti pro schůzky. | Co mám v místnostech splňovat? <br> Nové umístění schůzky, najděte ho.
 GoBack | Vraťte se k poslednímu kroku nebo položce.  <br> ***Upozornění**: Další program GoBack General projevy najdete v tématu doména **nástroje** . * | Předchozí <br> Zpět k poslednímu e-mailu.
 Odmítnout | Uživatel odmítne, jaký je virtuální asistent navržený. <br> ***Upozornění**: Další informace o odmítnutí obecných projevy najdete v tématu věnovaném doméně **nástroje** . * | Není nutné nastavovat událost. <br> V tuto chvíli mám další věci.
ShowNext | Podívejte se na další událost. <br> ***Upozornění**: Další ShowNext General projevy najdete v tématu doména **nástroje** . * | Dát mi další událost <br> Co je dál v kalendáři?
 ShowPrevious | Ověřte předchozí událost. <br> ***Upozornění**: Další ShowPrevious General projevy najdete v tématu doména **nástroje** . * | Co je plán před tím?
 TimeRemaining | Ověřte zbývající čas do další události. | Zobrazit, kolik času mám k dispozici před schůzkami <br> Zobrazuje dobu, po kterou mám čas od zahájení příští schůzky.
 
### <a name="entities"></a>**Podnikům**
Název entity | Typ entity | Popis | Příklady | Přihrádky
-------|-----------------------|-------------|---------|--------
Jméno kontaktu | personName | Jméno účastníka kontaktu nebo schůzky. | Doplněním s **Betsy**. <br>  Splnění s **Aubrey** dne 3. července v 7. odp. | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | pouh | Název cílového kalendáře | oběd s MOM úterý 12 – úterý – **osobní** <br> Použijte můj kalendář **Google** jako výchozí kalendář. <br> Aktualizovat třídu Yoga na Mon St ve 3 hodiny seznam v **osobním** kalendáři | Google <br> individuální <br> nejdůležitější. <br> hlavní
Trvání | datetime | Doba trvání schůzky, schůzky nebo zbývající čas. | Přidejte k pracovnímu kalendářnímu zasedání s Gary a prodiskutujte Scholarship podrobnosti zítra na **20 minut**. <br> Přidejte do kalendáře událost v Subway v pátek, kterou bude jídlo od Tomáše **po dobu 9** hodin. | hodinu <br> 2 dny <br> 20 minut 
EndDate | datetime | Koncové datum schůzky nebo schůzky. | Kalendář – přidat vše v hloubce Marie 3 až **Marie 5** | Marie 5  
EndTime | datetime | Čas ukončení schůzky nebo schůzky | můžete nastavit 2 30 na **tři** | tři 
Location | pouh | Umístění položky kalendáře, schůzce nebo události.  Adresy, města a oblasti jsou dobrým příkladem umístění. | vložení schůzky do **Fremont** pro uvedení tabletu v Barmě <br> schůzka pro bono v **Edina** | 209 americkém Nashvillu Posilovně <br> 897 masopustní house <br> Garáži 
MeetingRoom | pouh | Prostor pro schůzku nebo schůzku. | Přidat k pracovnímu kalendáři s Jake ve 2. odp. v **kanceláři** tento pátek | jeho kanceláře <br> konferenční místnost <br> Místnost 2
MoveEarlierTimeSpan | datetime | Čas, kdy uživatel chce přesunout schůzku nebo schůzku dříve | Přesuňte svoje datum obědu předem o **30 minut**. | 30 minut <br> dvě hodiny 
MoveLaterTimeSpan |  datetime | Čas, kdy uživatel chce přesunout schůzku nebo schůzku později. | Přejít na moji schůzku s Orchidejová box a **4 hodinami** | 4 hodiny <br> 15 minut 
OrderReference | pouh | Ordinální nebo relativní umístění v seznamu, určení položek k načtení. | Co má příští schůzka pro budoucnost? | další
OriginalEndDate | datetime | Původní koncové datum schůzky nebo schůzky. | Změna moje dovolené od konce **pátek** do pondělí | Pátek 
OriginalEndTime | datetime | Původní čas ukončení schůzky nebo schůzky. | Zajistěte, aby končil na **3** až 4 | 3
OriginalStartDate | datetime | Původní počáteční datum schůzky nebo schůzky. | Změna události **zítřka**od 10 DOP. do středy – 9 dop.  | zítra 
OriginalStartTime | datetime | Původní čas zahájení schůzky nebo schůzky. | Změna události zítřka od **10 DOP** . do středy – 9 dop. | 10 DOP.
PositionReference | ordinal | Absolutní pozice v seznamu, která identifikuje položku, která se má načíst. | **Druhý** z nich | sekunda <br> Ne. 3 <br> číslo 5
RelationshipName konstruktoru | list | Název vztahu kontaktu | Přidat oběd na 1:00. odp. s mým **manželem** | manželka <br> manžel <br> sesterské 
SlotAttribute | pouh | Atribut uživatel chce dotazovat nebo upravit. | změnit **umístění** události <br> změnit **čas** na sedm hodin | location <br> time 
StartDate | datetime | Počáteční datum schůzky nebo schůzky. | Vytvořit schůzku ve **středu** ve 4 hodiny | Středa 
StartTime | datetime | Čas zahájení schůzky nebo schůzky. | vytvořit schůzku ve středu ve **4 hodiny** | 4 hodiny
Subject | jednoduchý vzor. Jakýmikoli | Předmět, například název schůzky nebo schůzky. | Jaká je doba **přípravy** schůzky? | Dentist <br> Oběd s Julia 
Message | jednoduchý vzor. Jakýmikoli | Zpráva, která se odešle účastníkům | Účastníci schůzky na slavnostním setkání s oznámením, že **budou pozdě** | Budeme pozdě

## <a name="communication"></a>**Telecommunication**
Žádosti o provedení volání, posílání textů a IMs, hledání/přidávání kontaktů a různé požadavky související s komunikací (obecně odchozí). _Název kontaktu_ projevy nepatří do domény komunikace.

### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
AddContact | Přidáte nový kontakt seznamu kontaktů uživatele. | Přidat nový kontakt  <br>   Uložte toto číslo a název zadejte jako Jana.
AddFlag | Přidání příznaku k e-mailu | Označit tento e-mail <br> Přidejte k tomuto e-mailu příznak.
AddMore | Přidáte informace do e-mailu nebo textu, jako součást kompozice podle jednotlivých kroků e-mailu nebo text. | Přidejte k textu další text.  <br>   Přidejte další text k e-mailu.
Odpověď | Přijetí příchozího telefonního hovoru. | Odpovězte na volání.  <br>   Vyberte ji.
AssignContactNickname | Pojmenovat kontakt. | Změňte Petr na Dád.  <br>   Upravit přezdívku jim. <br>   Přidejte přezdívku do Patti Owens.
OnCuePoint | Vrátí telefonní hovor. | Zpětné volání.
CallVoiceMail | Připojte se k uživatele hlasová pošta. | Umožňuje připojit mě k poli hlasové pošty. <br>Hlasová pošta. <br>   Zavolejte na hlasovou poštu.
Zrušit | Zruší operaci. | Operaci. <br>   Ukončete.
CheckIMStatus | Zkontroluje stav kontaktu ve IM. | Je Jim jeho online stav nastaven na hned? 
CheckMessages | Kontrolovat nové zprávy nebo e-maily. | Kontrolovat moji doručenou poštu <br>  Mám nějaké nové e-maily?
Potvrdit | Potvrďte akci. | Ano, odeslat. <br> Napravo, potvrzuji, že chci odeslat tento e-mail.
EndCall | Ukončení telefonního hovoru. | Zavěste volání. <br> Ukončení.
FindContact | Najdete kontaktní údaje podle názvu. | Najít číslo objektu MUM. <br>   Zobrazit číslo Karolínu
FinishTask | Dokončete aktuální úlohu. | Hotovo <br> To vše.
TurnForwardingOff | Vypněte přesměrování volání. | Zastavit předávání volání <br> Přepnout přesměrování volání.
TurnForwardingOn | Zapněte předávání volání. | Předávání Moje volání 3333 <br>  Přepněte na přesměrování volání do 3333.
GetForwardingsStatus | Získejte aktuální stav předávání volání. | Moje předávání volání zapnutý? <br>   Řekněte mi, jestli je můj stav volání zapnutý nebo vypnutý.
Getnotifications | Získejte oznámení. | otevřít moje oznámení <br>   můžete kontrolovat moje oznámení o telefonu na Facebooku.
GoBack | Přejděte zpět na předchozí krok. | Přejděte zpět na twitteru <br>   Přejděte zpět krok <br>   Zpět
Ignorovat | Ignorujte příchozí volání. | Není odpovědět <br>   Ignorovat volání
IgnoreWithMessage | Ignorovat příchozí volání a místo toho odpovědět s textem. | Toto volání není odpovědět, ale místo odeslání zprávy. <br>   Ignorovat a odeslala textová zpráva zpět.
Telefonické připojení | Telefonní hovor. | Jan volání <br>   Vytočte prosím 311.
FindSpeedDial | Najdete číslo rychlé vytáčení, telefonní číslo je nastavena na a naopak. | Co je volání čísla 5? <br>   Je nutné rychlost vytáčení sady? <br>   Co je počet volání pro 941-5555-333?
Předat dál | Přepošle e-mail | Tento e-mail pošlete do Greg.
ReadAloud | Přečíst zprávu nebo e-mailu pro uživatele. | Přečtěte si text. <br>   Co uživatel Řekněme, že ve zprávě?
PressKey | Na klávesnici stiskněte tlačítko nebo číslo. | Volání na hvězdičku. <br>   Stiskněte 1 2 3.
QueryLastText | Dotaz na poslední text nebo zprávu | Koho jsem mne? <br>   Kdo mě v poslední době zobrazil?
Vytočit | Vytáčení nebo znovu volat na číslo. | Vytočit. <br>   Vytáčení Moje poslední volání.
Odmítnout | Odmítne příchozí volání. | Odmítnutí volání <br>   Nelze přijmout <br>   V tuto chvíli není k dispozici a bude zpětné volání, později.
Odpovědět | Odpovězte na zprávu. | reakce na Lore Hound <br>   odpovědět zadáním mého způsobu
SearchMessages | Vyhledávejte zprávy za určitých podmínek. | Můžete hledat e-maily odesílané Jana?
Connectoru | Odeslání e-mailu. Cílem tohoto je se vztahuje na e-mailu, ale ne textové zprávy. | E-mail na Jan vodách: Mike, tato večeře minulý týden byla Splendid. <br>   Poslat e-mail Bobovi
SendMessage | Odešlete textovou zprávu nebo rychlou zprávu. | Poslat text Chris a Karolínu <br>   Greg zpráv Facebooku <br>   
SetSpeedDial | Nastavte místní volání rychlost pro telefonní číslo kontaktu. | Nastavení telefonního seznamu, jeden pro Karolínu. <br>   Nastavení telefonní pro mom.
ShowCurrentNotification | Zobrazit aktuální oznámení. | Existují nějaká nová oznámení? <br> Zobrazit oznámení
ShowNext | Zobrazit další položky, například na seznam textových zpráv nebo e-mailů. | Zobrazit další příkaz. <br>   Přejdete na další stránku.
ShowPrevious | Viz předchozí položka, třeba na seznam textových zpráv nebo e-mailů. | Zobrazit předchozí. <br>   Předchozí. <br>   Přejdete na předchozí.
TurnSpeakerOff | Vypněte phone mluvčího. | Přeneste do složky vypnout mluvčího. <br>   Vypněte sluchátek s mikrofonem.
TurnSpeakerOn | Zapněte telefon mluvčího. | Režim sluchátek s mikrofonem. <br>   Umístí sluchátek s mikrofonem.

### <a name="entities"></a>**Podnikům**
Název entity | Typ entity | Popis | Příklady | Přihrádky
------|-------|----------|--------------|---------------
Attachment | pouh | Příloha, kterou chce uživatel odeslat pomocí textu nebo e-mailu | Odešlete **soubor** e-mailem z OneNotu. <br> Odeslat údržbu **doc** do Katie | soubor <br> přípon
AudioDeviceType | pouh | Typ zvukového zařízení (mluvčí, sluchátka, mikrofon atd.) | Odpovězte **na používání bez praktických rukou**. <br> Vytočit znovu na **telefonu mluvčího**. | mluvčího <br> bez praktických rukou <br> protokolu
Kategorie | pouh | Kategorie zprávy nebo e-mailu musí mít v e-mailovém systému jasně definici, jako je například "Nepřečteno", "příznak". Popis bez jasné definice, například "nové" a "poslední", není kategorie. | Označit všechny e-maily jako **přečtené**  <br> Nový e-mail s **vysokou prioritou** pro Paul | Důležité <br> Vysoká priorita <br> číst
ContactAttribute | pouh | Atribut kontaktu s dotazem na uživatele| O každý **narozeniny** příští měsíc mám vědět? | narozeniny <br> adresa <br> telefonní číslo
Jméno kontaktu | personName  | Jméno kontaktu nebo zprávy příjemce. | Poslat e-mail na **Stevens** | Stevens
Date/Time | datetime | Datum a čas přijatého e-mailu | Čtení **dnešního**e-mailu <br> Komu se e-mail **dnes**? <br> Komu telefon je **7 hodin**? | dnes <br> zítra
DestinationPhone | pouh | Cílový uživatel chce zavolat nebo odeslat text. | telefonování <br> odeslat textovou zprávu **Domů** | doma <br> Domovská složka
EmailAddress | email | Uživatel e-mailové adresy chce odeslat nebo dotazovat. | Odeslat e-mail naMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | jednoduchý vzor. Jakýmikoli | Text použitý jako řádek předmětu e-mailu. | Vytváření e-mailů davidch s předmětem předmětu **Hey**  | RE: zajímavé scénář
Klíč | pouh | Klíč uživatele chce stisknout. | Stiskněte klávesu **MEZERNÍK** . <br> stiskněte **9** | dokončíte <br> hvězd <br> 8
Perokresba | pouh | Uživatel řádku chce použít k odeslání e-mailu nebo textu. | Přečtěte si poslední e-mail ze **služby Hotmail** . <br> Zavolejte Petra od **mobilního telefonu**. <br> Zavolejte Dád pomocí **pracovní** linky.| Služba <br> Skype <br> Britské buňky
SenderName | personName | Jméno odesílatele. | Přečíst e-mail od **David** <br> E-maily z Chanda | David <br> Chanda
FromRelationshipName | pouh | Název vztahu odesílatele. | Přečíst zprávu od **Dád**. <br> Můžete si přečíst všechny textové zprávy z **MOM**? | Dád <br> MOM 
Message | jednoduchý vzor. Jakýmikoli |  Zpráva k odeslání e-mailu nebo text.  | Poslat e-mail s oznámením, že jsem**zaneprázdněn** | Zaneprázdněn
OrderReference | pouh | Ordinální nebo relativní umístění v seznamu, určení položek k načtení. | Jaká byla **Poslední** odeslaná zpráva? <br> Přečtěte si **nejnovější** e-mail pro Nokia. <br> Přečtěte si **nové** textové zprávy. | posledního <br> latest <br> používané <br> nejnovějších
PositionReference | jednoduché – pořadové číslo | Ordinální nebo relativní umístění v seznamu, určení položek k načtení.| Jaká byla **první** odeslaná zpráva? <br> Druhý **třetí** .| První <br> jiného
phoneNumber | phoneNumber | Telefonní číslo, na které chce uživatel zavolat nebo odeslat text. | Odeslat text do **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName konstruktoru | pouh | Název vztahu kontaktu nebo příjemce zprávy. | Poslat e-mail mému **manželovi** | manželka
SearchTexts | jednoduchý vzor. any | Texty používané pro filtrování e-mailů nebo zpráv | Vyhledat všechny e-maily, které obsahují "**Surface pro**" | Plocha pro
Telefonního | pouh | Rychlost vytáčení. | Zavolejte **3 4 5**. <br> Nastavte rychlost vytáčení **One**. | 345 <br> 5

## <a name="email"></a>**E-mailu**
E-mail je subdoménou domény *komunikace* . Hlavně obsahuje žádosti o posílání a přijímání zpráv prostřednictvím e-mailů.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
AddMore | Přidáte informace do e-mailu nebo textu, jako součást kompozice podle jednotlivých kroků e-mailu nebo text. | Přidejte další text k e-mailu.
Zrušit | Zruší operaci. <br> ***Upozornění**: Další informace o zrušení obecných projevy najdete v tématu doména **nástroje** . * | Operaci. Neodešlete ho. <br> Ukončete.
CheckMessages | Kontrolovat nové zprávy a e-maily bez podmíněného dotazu. Pokud existuje nějaká podmínka, projevy patří do záměru *SearchMessage* . | Podívejte se do složky Doručená pošta. <br> Mám nové e-maily? 
Potvrdit | Potvrďte probíhající akci související se zpracováním e-mailů. <br> ***Upozornění**: Další informace najdete v tématu doména **nástroje** , kde najdete další informace o obecné projevy. * | Ano, odeslat. <br> Potvrzuji, že chcete odeslat tento e-mail.
Odstranění | Odstraňte e-mail nebo e-maily filtrované podle určitých podmínek. | Vložit e-mail do koše <br> Vyprázdnit moji doručenou poštu <br> Odebrat e-maily Marie
ReadAloud | Přečíst zprávu nebo e-mailu pro uživatele. <br> ***Upozornění**: Další ReadAloud General projevy najdete v tématu doména **nástroje** . *  | Přečtěte si e-maily odeslané Marie.
Odpovědět | Odpovědět na aktuální e-mail s oznámením. | Vytvoří odpověď na e-mail. <br> Odpovězte zadáním "Děkujeme, že se vám hodně blíží, na Jan".
SearchMessages | Vyhledávejte zprávy za určitých podmínek, včetně jména odesílatele, času a předmětu. | Zobrazit zprávy z Nisheen <br> Můžete hledat e-maily s názvem "ABC"?
Connectoru | Odeslání e-mailu. | E-mail na Jan: Mike, tato večeře minulý týden byla Splendid. <br> Poslat e-mail Bobovi
ShowNext | Podívejte se na další položky v seznamu textových zpráv nebo e-mailů. <br> ***Upozornění**: Další ShowNext General projevy najdete v tématu doména **nástroje** . * | Zobrazit další příkaz. <br> Přejdete na další stránku.
ShowPrevious | Zobrazí předchozí položky v seznamu textových zpráv nebo e-mailů. <br> ***Upozornění**: Další ShowPrevious General projevy najdete v tématu doména **nástroje** . * | Zobrazit předchozí. <br> Předchozí. <br> Přejdete na předchozí.
Předat dál | Předejte e-mail. | Tento e-mail pošlete do Greg.
AddFlag | Přidejte příznak do e-mailu. | Označit tento e-mail <br> Přidejte k tomuto e-mailu příznak.
QueryLastText | Dotaz na poslední e-mail | Komu se mi pošle e-mail? <br> Kdo mi nedávno e-mail?


### <a name="entities"></a>**Podnikům**
Název entity | Typ entity | Popis | Příklady | Přihrádky
------|-------|----------|--------------|---------------
Attachment | pouh | Příloha, kterou chce uživatel odeslat pomocí textu nebo e-mailu | Odešlete **soubor** e-mailem z OneNotu. <br> Odeslat údržbu **doc** do Katie | soubor <br> přípon
Jméno kontaktu | personName  | Jméno kontaktu nebo zprávy příjemce. | Poslat e-mail na **Stevens** | Stevens
Date | datetime | Datum přijetí e-mailu | Čtení **dnešního**e-mailu <br> Komu se e-mail **dnes**? | dnes
EmailAddress | email | Uživatel e-mailové adresy chce odeslat nebo dotazovat. | Odeslat e-mail naMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | jednoduchý vzor. Jakýmikoli | Text použitý jako řádek předmětu e-mailu. | Vytváření e-mailů davidch s předmětem předmětu **Hey**  | RE: zajímavé scénář
SenderName | personName | Jméno odesílatele. | Přečíst e-mail od **David** <br> E-maily z Chanda | David <br> Chanda
FromRelationshipName | pouh | Název vztahu odesílatele. | Přečíst zprávu od **Dád**. <br> Můžete si přečíst všechny textové zprávy z **MOM**? | Dád <br> MOM 
Message | jednoduchý vzor. Jakýmikoli |  Zpráva k odeslání e-mailu nebo text.  | Poslat e-mail s oznámením, že jsem**zaneprázdněn** | Zaneprázdněn
Kategorie | pouh | Kategorie zprávy nebo e-mailu musí mít v e-mailovém systému jasně definici, jako je například "Nepřečteno", "příznak". Popis bez jasné definice, například "nové" a "poslední", není kategorie. | Označit všechny e-maily jako **přečtené**  <br> Nový e-mail s **vysokou prioritou** pro Paul | Důležité <br> Vysoká priorita <br> číst
OrderReference | pouh | Ordinální nebo relativní umístění v seznamu, určení položek k načtení. | Jaká byla **Poslední** odeslaná zpráva? <br> Přečtěte si **nejnovější** e-mail pro Nokia. <br> Přečtěte si **nové** textové zprávy. | posledního <br> latest <br> používané <br> nejnovějších
PositionReference | jednoduché – pořadové číslo | Ordinální nebo relativní umístění v seznamu, určení položek k načtení.| Jaká byla **první** odeslaná zpráva? <br> Druhý **třetí** .| První <br> jiného
RelationshipName konstruktoru | pouh | Název vztahu kontaktu nebo příjemce zprávy. | Poslat e-mail mému **manželovi** | manželka
Time | datetime | Time | odeslání e-mailu **Tonight**. | tonight
SearchTexts | jednoduchý vzor. any | Texty používané pro filtrování e-mailů nebo zpráv | Vyhledat všechny e-maily, které obsahují "**Surface pro**" | Plocha pro 
Perokresba | pouh | Uživatel řádku chce použít k odeslání e-mailu z. | Přečtěte si poslední e-mail ze **služby Hotmail** . <br> Odeslat e-mail ze svého **pracovního účtu**| Služba <br> pracovní účet 

## <a name="homeautomation"></a>**HomeAutomation**
Doména HomeAutomation poskytuje záměry a entity týkající se řízení inteligentních domácích zařízení. Podporujeme hlavně Řídicí příkazy související s kvadranty a klimatizačním modulem. Ale má některé možnosti generalizace na jiných zařízeních elektrického zařízení.
### <a name="supported-devices-and-properties"></a>**Podporovaná zařízení a vlastnosti**
Zařízení | Vlastnosti
-------|---------
Senzor teploty | Teplota
Lampa světla | Zapnuto, jas, barva
TV, Radio | Zapnuto, svazek
Klimatizace (A/C), termostat | Zapnuto, teplota, napájení
Kryt | Zapnuto, napájení
Výstupy, přehrávač DVD, Ice maker atd. | Zapnuto

### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
 Vypnutí | Uživatel chce vypnout zařízení nebo nastavení.  | Vypněte světla. <br> Vypínejte něco. <br> Něco mimo.
 TurnOn | Zapněte zařízení nebo zapněte a nastavte zařízení na konkrétní nastavení nebo režim. | Zapněte světlo až 50%. <br> Zapnout moji kavárnový program <br> Můžete zapnout moje kavárnový program?
 SetDevice | Uživatel chce nastavit zařízení na konkrétní nastavení nebo typ.  | Nastaví letecký klimatizace na 26 stupňů. <br> Zapněte světle modrou. <br> Zavolejte toto světlo jako nightlight.
 QueryState | Uživatel požádá o stav zařízení nebo nastavení.  | Jaký je termostat nastavený na? <br> Je A/C na? <br> Jaká je teplota Bedroom?
 TurnUp | Zapne nastavení nebo jas zařízení. | Zesvětlit světla o 75 procent. <br> Jas tady získáte o 10 procent.  <br> Zajistěte si, aby se do obývacího místnosti zahřívá.
 TurnDown | Vypnutí a vypnutí zařízení, a to buď pomocí ztlumení, teploty nebo jasu světla. | Rozveďte knihovnu dolů o 44%. <br> Tmavé světla. <br> Udělejte si chladicí místnost.
### <a name="entities"></a>**Podnikům**
Název entity | Typ entity | Popis | Příklady
-------|----------|--------------|---------------
Název zařízení | List | Uživatelem definovaný text pro svá zařízení. | Modrá<br> Upřímn <br> Helpdesku
DeviceType | List | Podporovaná zařízení. | Světlomet <br> Klimatizace <br> nightlight
Location | pouh | Umístění nebo místnost, ve které je zařízení. | Uspořádání<br> Downstairs <br> Bedroom
NumericalChange | pouh | Hodnota, o kterou se nastavení zvýší nebo sníží. <br> <br> _Slot se zobrazí pouze s turn_up a turn_down záměry._ | 3<br> 50%<br>
OrderReference | ordinal | Účelem tohoto slotu je zachytit výběr položek. Označuje pozici položky v seznamu. | První<br> 2\.
Kvantifikátor | List | Kvantifikátor určuje, kolik instancí konkrétní entity je odkazováno. Například "All", "každé" atd. | Vše<br> Vždy po<br> Vše
Nastavení | Jednoduchý | Nastavení, na které si uživatel přeje nastavit své zařízení, včetně scény, úrovně, intenzity, barvy, režimu, teploty a stavu zařízení. | Modrá<br> 72 <br> Hork 
Typem SettingType | List | Nastavení zařízení, které uživatel zajímá. | Teplota<br> 
Datum a čas |  datetime | Doba a doba provozu zařízení| 5 minut <br> 3 hodiny
Jednotka | List | Chcete-li označit slova jako ' Degrees ', ' Percent ', "Fahrenheita", "Celsia", musí být každý termín jednotky označen samostatně. | Určitý<br> Percent


## <a name="notes"></a>**Poznámky**
Poznámka: doména usnadňuje vytváření poznámek a zápis položek pro uživatele.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
AddToNote | Přidání informací do otevřené poznámky | Přidejte do poznámky k plánování, abyste se obrátili na svého nadřízeného o projektu.
Vymazat | Vymažte všechny položky z již existujících poznámky. | Odebere všechny položky z poznámky o dovolené. <br>Vymazat vše z poznámky ke čtení.
Potvrdit | Potvrďte akci vztahující se na poznámku. <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci potvrdit scénář pro poznámení. Pokud potřebujete obecnější výrazy o "Confirm", využijte možnost potvrdit záměr v doméně **nástrojů** . * | Pro tuto poznámku je to v pořádku. <br>Potvrzuji, že se všechny položky v seznamech zachovají.
Create | Vytvoří novou poznámku. | Vytvořte poznámku. <br>Všimněte si, že je možné připomenout, že Jason je ve městě první týden z května. 
Odstranění | Odstraníte celý poznámku. | Odstranit mou poznámku o dovolené <br>Odstranit poznámky k nákupním položkám
ReadAloud | Přečtěte si poznamenejte si nahlas. | Přečtěte si první poznámku. <br>Přečtěte si podrobnosti.
Zavřít | Zavřít aktuální poznámku | Zavřete poznámku. <br>Zavřít aktuální poznámku
Otevřenost | Otevřete již existující poznámku. | Otevřete mou poznámku o volání. <br>Otevřete poznámku "plánování".
RemoveSentence | Odebere větu poznámky. | Odebere poslední větu. <br>Odstraňte čipy z nákupní poznámky. <br>Odebrání per z nákupní poznámky ze školy
ChangeTitle | Změňte název poznámky. | Tato poznámka se nazývá "plánování".

### <a name="entities"></a>**Podnikům**
Název entity | Typ entity | Popis | Příklady 
------- | ------- | ------- | -------
Text | jednoduchý vzor. Jakýmikoli | Text poznámky nebo připomenutí. | roztažení před procházení <br> dlouho běžící zítra
Název | jednoduchý vzor. Jakýmikoli | Název poznámky. | Nákup <br> uživatelům volání <br> úkol
CreationDate | datetimeV2 | Tato pozice je určena v případě, kdy uživatel žádá o poznámky vytvořené v určitém časovém intervalu. | 
Kvantifikátor | List | Když se uživatel zeptá, že provede akci na všech položkách ' All ', ' všechny ' nebo ' Any ' nebo na veškerý text v poznámce. | vše <br> libovolné <br> každé
OrderReference | ordinal | Uživatel chce provádět akce s položkami First, Last, Next atd. | první <br> posledního


## <a name="places"></a>**Zadá**
Mezi ně patří firmy, instituce, restaurace, veřejné prostory a adresy. Doména podporuje místo hledání a dotazování informací na veřejném místě, jako je například umístění, provozní doba a vzdálenost. 
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
Funkce MakeCall | Telefonní hovor na místo. | Applebees na 1000/200 Rojas St a zavolejte.
FindPlace | Hledat na místě (business, instituce, restaurace, prostor veřejných adres). Ale ne: <li> Pouze název firmy: Starbucks <li> Pouze název umístění: Praha/Norsko <li> Cuisine, potravina nebo produkt: Pizza/guacamole/italština | Kde je nejbližší knihovny? <br> Starbucks v Seattlu. <br> Kde je nejbližší knihovny? <br> 
GetAddress | Požádejte o adresu místo. | Zobrazit adresu GUU na Robson ulici <br> Jaká je adresa nejbližší Starbucks?
GetDistance | Zeptejte se na vzdálenost z aktuálního umístění na konkrétní místo. | Jak daleko je sváteční DIČ?<br> Jak daleko má Bellevue Square z tohoto místa? <br> Jaká je vzdálenost k Tahoe?
GetPhoneNumber | Požádejte o telefonní číslo na místě. | Co je telefonní číslo nejbližší Starbucks?<br> Uveďte číslo domovského skladu. <br> Telefonní číslo pro Disneyland.
GetPriceRange | Požádejte o rozsah spotřeby na hlavu na místo. | Průměrná cena J. sushi v Seattlu <br> Ve středu je Cineplex poloviční cenu? <br> Kolik stojí celý lobster večeře v Sizzler?
GetStarRating | Požádejte o hodnocení hvězdičkami místa. | Jak je Zucca ohodnocená?<br> Kolik hvězdiček prací francouzština máte?<br> Je dobré aquarium v Monterrey?
Gethours – | Požádejte o provozní dobu na místě. | Zároveň Safeway zavřít?<br> Co jsou hodiny pro Domů Depot?<br> Je stále otevřen Starbucks?
GetReviews | Požádejte o kontroly na místě. | Zobrazit recenze pro cheesecake Factory <br> Čtení Cineplex revizí. <br> Existují nějaké nedávné recenze pro Humperdinks?
GetMenu | Požádejte o položky nabídky pro restauraci. | Nemá Zucca sloužit nic veganská? <br> Co je v nabídce na Sizzler? <br> Zobrazit nabídku Applebee
RatePlace | Ohodnoťte místo. | Hodnocení 4 hvězdičkami pro Pizza Maxi v Kimberly. <br> Dejte 4 hvězdičkám, které se Bonefish na TripAdvisor. <br> Vytvořte recenzi 4 hvězdičkami pro La Hacienda.
AddFavoritePlace | Uživatel chce přidat umístění k oblíbeným položkám nebo k seznamu MVP. | Uložit toto umístění do oblíbených. <br> Přidejte si co nejlépe k oblíbeným položkám.

### <a name="entities"></a>**Podnikům**
LUIS entity | Typ entity | Popis | Příklady | Příklady utterance
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | pouh | Umístění nebo adresu místo. | Palo Alto <br> 300 112th uložit SE <br> Seattle | **1020 Middlefield VP.** v **Palo Alto** <br> úložiště pro sadbu ptáků v **Seattlu** <br> Získejte vzdálenost od sem až **300 112Th Ave**.
Vymoženosti | pouh | Objektivní charakteristiky a výhody na veřejném místě. | waterfront <br> parkovací zdarma | Kirkland **Waterfront** ryby v restauracích. <br> Existuje **zdarma zaparkování** v blízkosti mě?
Kuchyni | pouh | Typ food, kuchyni nebo kuchyni příslušnosti. | Čínština <br> italština <br> Sushi <br> Noodle <br> | Potřebuji pomoc s vyhledáním **čínské** restaurace. <br> Jaké jsou otevírací hodiny **sushi** restaurace? <br> Kde je nejbližší **steak** House?
Date | datetime | Datum a čas nebo doba trvání cílového umístění. | zítra <br> dnes <br> 6 hodin | Jak dlouho Aquarium zavřít **zítra**? <br> Nejbližší obchod s bicykly, který je otevřený po **6 hodin**
Vzdálenost | Dimenze | Vzdálenost k veřejnému místu z currenct pozice uživatele. | 15 mil. <br> 10 mil | oděvní obchod do **15 mil** . <br> Restaurace na dětská, která je jenom **10 mil** .
MealType | List | Typ jídla jako snídani nebo oběd. | Snídani <br> Společnost dinner | Hledat **snídani** Greenwood Seattle <br> Najděte si místo, kde máte svůj pracovní **oběd**.
Novou | List | Popište okolní místo bez absolutního umístění nebo adresy. | místa <br> v této oblasti <br> od sebe | Najděte **nejbližší** indickou restauraci. <br> Kde je **místní** Wetherspoon? <br> Jakékoli **dobré restaurace?**
OpenStatus | List | Určuje, zda místo, kde je otevřeno nebo Uzavřeno. | open (otevírá) <br> Uzavřeno | Jakou dobu Yogurt půda **uzavřela** ještě dnes? <br> Jaká jsou **otevírací** hodiny pro Costco?
PlaceName | pouh | Název cíl, který je firmy, restaurace, veřejné přitažlivosti nebo instituce. Název místa může obsahovat placetype, pokud se běžně používá. | Centrální Park <br> Safeway <br> Walmart| Jak dlouho je **Safeway** lékárna otevřená? <br> Je **Walmart** otevřené?
PlaceType | pouh | Zadejte cíl, který je místní firmy, restaurace, veřejné přitažlivosti nebo instituce. | Restaurace <br> Opera <br> kina | **kino** v Cambridge <br> Je k dispozici **restaurace** v mém okolí?
PriceRange | pouh | Cenový rozsah produktů nebo služeb na místě. | levný <br> cenově výhodné <br> nákladný | Najít **cenově dostupné** opravy zařízení <br> Co je **levné** pizza místo, které je teď otevřené?
Produkt | pouh | Produkt nabízí na místě. | civilní <br> televizí | Kde je to nejlepší místo pro získání **jídla**? <br> Najděte mi východní Kilbride hledání **televizních pořadů**.
Hodnocení | pouh | Hodnocení na místě. | 5 hvězdiček <br> vrchol <br> Dobré | Existují nějaké **dobré** místo, kde mě mám vyzkoušet a jíst zítra <br> **nejlepší** Amsterdam restaurací <br> **Seznamte** se třemi Pizza obchody.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Rezervační doména pro restaurace podporuje záměry při zpracování rezervace v restauracích.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
Rezervovat | Požádat o rezervaci pro restauraci. | Vyhradit na Zucca pro dvě pro Tonight. <br> Seznamte se s tabulkou pro budoucnost. <br> Tabulka pro 3 v Palo Alto 7. <br> Vytvořte rezervaci pro 3 v Red severském.
DeleteReservation | Zruší nebo odstraní rezervaci pro restauraci. | Zruší rezervaci na Zucca zítra v noci. <br> Zapomenout svou rezervaci na červenou severský čas v 7:00. odp. příští pátek. <br> Nepotřebuji rezervaci pro Zucca, zrušte ji.
ChangeReservation | Změňte čas, umístění nebo počet osob pro existující rezervaci restaurace. | Změnit moji rezervaci na 9 hodin <br> Změňte moji rezervaci z Zucca na červenou sever. <br> 7 lidí místo 6 pro rezervaci na adrese Zucca.
Potvrdit | Potvrďte akci související s rezervací. <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci potvrdit pro scénář rezervace restaurace. Pokud potřebujete obecnější výrazy o "Confirm", využijte možnost potvrdit záměr v doméně **nástrojů** . * | Ano, provedli jsme rezervace pro Tonight při 8 hodin od tvůrce těstovin. <br> Ano, stačí ho zarezervovat. <br> Potvrďte rezervaci na sushi řádku.
FindReservationDetail | Zobrazí podrobné informace o existující rezervaci. | Najít rezervaci v návratu San Diegu <br> Zobrazit rezervaci zítra <br> Zobrazí podrobnosti o mé rezervaci pro Zucca.
FindReservationWhere | Ověřte absolutní umístění rezervace. | Kde je umístění Zucca v mé rezervaci? <br> Zobrazí národní prostředí pro moji rezervaci zítra.
FindReservationWhen | Ověřte přesně dobu rezervace. | Kdy je rezervace Zucca pro zítra? <br> Čas pro moji rezervaci v Red severského.
Odmítnout | Uživatel odmítne, jaký je virtuální asistent navržený ke správě rezervace. <br> ***Oznámení**:P zapůjčení najdete v tématu další **informace o odmítnutí** obecných projevy. * | Není nutné nastavovat událost. | Ne, nechci změnit rezervaci. <br> Ne, nemusíte ho zarezervovat, udělali jsme chybu.

### <a name="entities"></a>**Podnikům**
LUIS – entita | Typ entity | Popis | Příklady
-------|------|---------|-------------------
Adresa | pouh | Umístění události nebo adresy pro rezervaci. | Palo Alto<br>300 112th uložit SE<br>Seattle
Prostředí | List | Popis atmosféry restaurace. | Romantický<br>příležitostné<br>vhodné pro skupiny<br>Hezké
Kuchyni | pouh | Typ food, kuchyni nebo kuchyni příslušnosti. | Čínština<br>italština<br>Mexické<br>Sushi<br>Noodle<br>steak
MealType | List | Typ jídla přidružené k rezervaci. | Snídani<br>Společnost dinner<br>Oběd<br>Supper
PlaceName | pouh | Název restaurace | Zucca<br>Objekt pro vytváření cheesecake<br>červená severské
Hodnocení | pouh | Hodnocení místa nebo restauraci. | 5 hvězdiček<br>3 hvězdičky<br>4 hvězdičky
NumberPeople | pouh | Počet lidí pro rezervaci | 3<br>šest
Time | datetime| Čas rezervace pro restaurace | zítra<br>tonight<br>7:00 hodin<br>Předvečer vánoční


## <a name="todo"></a>**ToDo**
Doména _TODO_ poskytuje typy seznamů úkolů pro uživatele, kteří budou přidávat, označovat a odstraňovat položky ToDo-Items.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
AddToDo | Uživatel chce přidat položky úkolu pro kterýkoli z typů seznamů. |  Připomenout mi k nákupu mléka <br> Přidat položku s názvem koupit mléko do seznamu úkolů
Potvrdit | Uživatel chce potvrdit probíhající akci. Utterance obsahuje explicitní signál, například "Ano" pro potvrzení operace. <br> <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci potvrdit pro scénář ToDo. Pokud potřebujete obecnější výrazy o "Confirm", využijte možnost potvrdit záměr v doméně **nástrojů** . * | Odstraňte prosím úkol. <br> Opravdu chci přidat tuto úlohu. <br> Ano, chci to udělat.
Zrušit | Uživatel chce zrušit probíhající akci.  <br> <br> ***Upozornění**: Tento záměr zahrnuje hlavně akci potvrdit pro scénář rezervace restaurace. Pokud potřebujete obecnější výrazy o "Confirm", využijte možnost potvrdit záměr v doméně **nástrojů** . * | Ne, zapomenout ho. <br> Stačí úlohu zrušit. <br> Ne, nepřidávat
DeleteToDo | Umožňuje odstranit položku v seznamu úkolů odkazem na její pořadí nebo odstranit všechny položky úkolů. | Odebere všechny úlohy. <br> Potřebuji pomoc, aby se odstranil druhý z nich.
MarkToDo | Označte úkol jako dokončený nebo hotový odkazem na jeho pořadí nebo obsah úkolu. | Označte úkol koupit mléko jako dokončený. <br> Dokončete čtení úkolu. <br> Vše dokončete.
ShowNextPage | Seznam se rozdělí na několik stránek, které se zobrazí. Zobrazit položky seznamu na další stránce pro uživatele | Můžete zobrazit další stránku v seznamu nákupů? <br> Co je k dalšímu? <br> Co dále?
ShowPreviousPage | Zobrazit položky seznamu na předchozí stránce pro uživatele | Zobrazit předchozí <br> Potřebuji, abyste zkontrolovali předchozí úkoly.
ShowToDo | Zobrazí všechny položky v seznamu úkolů. | Zobrazit můj nákupní seznam <br> Zobrazit můj seznam pro nákup

### <a name="entities"></a>**Podnikům**
LUIS – entita | Typ entity | Popis | Příklady
-------|------|---------|-------------------
ContainsAll | pouh | Reprezentuje všechny položky v seznamu úkolů. | vám může pomáhat odebrat **všechny** úlohy. <br> Dokončete **vše**.
ordinal | ordinal | Pořadové číslo nebo číselná reference na položku. | Označte **třetí** ho jako dokončený. <br> Odstraňte **první** úlohu.
ListType | pouh | Typ seznamu úkolů  | Přidejte obuv do seznamu **nakupování** .
FoodOfGrocery | List | Zjištění seznamu položek stravy | Připomenout mi k nákupu **mléka** <br> Přidat **hovězí maso** do seznamu nenákupů
TaskContent | jednoduchý vzor. any | Zjištění obsahu úlohy. | Připomenout mi, aby **volal moji matka** . <br> Přidat **narozeniny oslavy Jan** do seznamu úkolů


## <a name="utilities"></a>**Nástroje**
Doména _nástrojů_ je obecná doména mezi všemi _Luis_ předem sestavenými modely, které obsahují běžné záměry a projevy v rozdílných scénářích.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
 Zrušit | Zruší akci, žádost, úlohu nebo službu. | Zrušte ho. <br> Nezapomeňte, že ho zapomenete.
 Potvrdit | Potvrďte akci. | Jistě <br> Ano, prosím <br> Potvrdit.
 Odmítnout | Uživatel odmítne, jaký je virtuální asistent navržený. | Ne
 FinishTask | Dokončení úkolu uživatel začal. | Hotovo. <br> To je všechno. <br> Skončit.
 GoBack | Vrátit zpět o jeden krok nebo návrat k předchozímu kroku. | Vraťte se ke kroku. <br> Vrať se.
 Help | Žádost o pomoc. | Požádejte o podporu. <br> Otevřete help.
 Opakovat | Opakujte akci. | Řekněte to znovu.
 ShowNext | Zobrazit nebo oznámit další položky | Zobrazit další příkaz.
 ShowPrevious | Zobrazte předchozí položky v řadě. | Zobrazit předchozí.
 StartOver | Restartování aplikace nebo zahájit novou relaci. | Službu.
 Zastavit | Řekněte virtuálnímu asistentovi, aby ukončil hovor.  | Zastavte si tuto činnost.
 SelectAny | Uživatel žádá o výběr libovolné položky nebo výsledků zobrazené na obrazovce. | Jakékoli z nich. <br> Vyberte jednu z nich.
 SelectNone | Uživatel vybere žádnou z existujících položek a požádá o další možnosti. | Dejte mi další návrhy. <br> Žádná z nich.
  Selectitem – | Uživatel žádá o výběr položky nebo výsledků zobrazených na obrazovce. | Vyberte třetí z nich. <br> Vyberte vpravo nahoře.
 Eskalovat | Zeptejte se na služby zákazníkům, abychom mohli tyto problémy zpracovat. | Můžu se obrátit na někoho?
 ReadAloud | Přečíst si text nahlasu uživateli | Přečtěte si tuto stránku. <br> Přečtěte si je nahlas.

### <a name="entities"></a>**Podnikům**
LUIS – entita | Typ entity | Popis | Příklady
------------|-------------|-------------|---------
ordinal | ordinal | Pořadové číslo nebo číselná reference na položku. | **Druhý** . <br> **Druhý** .
number | number | Počet položek, které uživatel potřebuje | Další **3** položky
DirectionalReference | pouh | Referenční bod, kde na obrazovce se nachází položka. | Ten vpravo <br> umístit

## <a name="weather"></a>**Před**
V případě povětrnostních podmínek se zaměřuje na kontrolu povětrnostní podmínky a Advisory s využitím místa a času nebo při kontrole podle počasí.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
 CheckWeatherValue | Zeptejte se na počasí nebo faktor týkající se počasí pro místo na základě předpovědi nebo minulých informací. <br> Mezi faktory související s počasí patří: <li> teplota </li> <li> deště/sněhu/srážky </li> <li> suchá/mokrá/vlhkost </li> <li> mlhy </li> <li> Index UV </li> <li> palce na deště a sněhu </li> | Co je index kvality ovzduší v Peking? <br> Kolik se očekává v Seattlu v březnu? <br> Zaznamenejte nejvyšší teplotu Havajské ostrovy.
 CheckWeatherTime | Zeptejte se na čas předpokládaných nebo historických faktorů souvisejících s počasí pro určité místo. | Kdy se očekává, že se má vzdeště? <br> Dobrý čas na přechod na Kanadu <br> Kdy je nejžhavějších měsíc v Minnesota?
 QueryWeather | Zeptejte se na povětrnostní podmínky nebo okolnosti související s počasí pro konkrétní umístění, pro které se očekává odpověď "Ano, ne nebo možná", nebo požádat o doporučení ohledně aktivity, která závisí na počasí. | Bude to v budoucnosti? <br> Je dnes Slunečné? <br> Je možné, že už brzy nebudete moct přejít na Aljaška?
 ChangeTemperatureUnit | Změňte jednotku počasí, včetně Celsia, kelvinů a Fahrenheita. | Převod ve stupních Celsia. <br> Můžu v kelvinech získat?
 GetWeatherAdvisory | Získat z konkrétního umístění Poradce pro počasí nebo upozornění | Jsou v Memphis nějaké informační Poradce pro počasí? <br> Existují u své oblasti nějaká upozornění počasí?

### <a name="entities"></a>**Podnikům**
LUIS – entita | Typ entity | Popis | Příklady
------------|-------------|-------------|---------
Location | geografické | Absolutní nebo implicitní umístění žádosti o počasí. | Palo Alto<br>Šanghaj<br>Seattle<br>Delvina<br>
Date/Time   | datetime | Datum a čas nebo doba trvání dotazu na počasí | Listopad<br>po hodinách<br>označení<br>Tento víkend<br>10 dní<br>
AdditionalWeatherCondition | list | Další popis slova pro počasí, jako je například rychlost nebo směr větru. | direction<br>Rychlá<br>takt
Historické | pouh | Popis slov historických povětrnostních podmínek, včetně průměrných vyberte joomlaých případů v minulém časovém období. | již<br>Historická/historická<br>sezón<br>Nejlepší čas<br>někdy zaznamenané
PrecipitationUnit | Dimenze | Srážení pro sněhu nebo deště. | 5 palců<br>6 cm
SuitableFor | pouh | Popis lidské aktivity pod povětrnostní podmínkou, která je společná, když uživatelé dotazují na doporučení aktivity, které závisí na povětrnostní situaci. | člena<br>sluneční<br>Swimming
TemperatureUnit |teplota | teplota | 18 Celsia<br>7 stupňů kelvinů
WeatherRange | pouh | Konkrétní podmínka teploty, větru a dalších povětrnostních podmínek mezi časovým obdobím | maximum<br>maximální<br>nízká<br>Průměrná vysoká<br>Nejvyšší
WeatherCondition | pouh | Popis podmínky počasí | Slunečné<br>zástěr<br>srážky<br>teplota<br>pohádk<br>za běhu
WindDirectionUnit | list | Směrová slova větru | severu<br>odtud<br>Atlantiku<br>– Západ<br>severovýchod


## <a name="web"></a>**Web**
Webová doména poskytuje záměr a entity pro hledání webu.
### <a name="intents"></a>**Záměry**
Název záměru | Popis | Příklady
---------|----------|---------
 Hledání na bázi | Požadavek na přechod na zadaný web nebo hledání ve vyhledávacím webu. | Hledat Surface v google.com. <br> Najít šťastný narozeniny na webu <br> Přejít na www.twitter.com.

### <a name="entities"></a>**Podnikům**
LUIS – entita | Typ entity | Popis | Příklady
------------|-------------|-------------|---------
SearchEngine | List | Uživatel vyhledávacího stroje chce použít. | Bing <br> Google
Prohledávanýtext | jednoduchý vzor. Jakýmikoli | Uživatel s textem chce Hledat. <br> _Označení "přátelé v Facebooku" jako Prohledávanýtext, pokud web po "in" není vyhledávacím modulem. Adresa URL by měla také označovat jako Prohledávanýtext._ | Video <br> Hloubkové učení <br> Přepravní
Odkaz | url | Odkaz na web. | www.twitter.com

