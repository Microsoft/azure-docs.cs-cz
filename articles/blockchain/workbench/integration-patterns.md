---
title: Vzory integrace inteligentních smluv – Azure Blockchain Workbench
description: Přehled vzorců integrace inteligentních smluv v Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325967"
---
# <a name="smart-contract-integration-patterns"></a>Vzory integrace inteligentních kontraktů

Inteligentní smlouvy často představují obchodní pracovní postup, který je třeba integrovat s externími systémy a zařízeními.

Požadavky těchto pracovních postupů zahrnují nutnost iniciovat transakce v distribuované hlavní knize, které obsahují data z externího systému, služby nebo zařízení. Musí také mít externí systémy reagovat na události pocházející z inteligentních smluv na distribuované hlavní knize.

Rozhraní REST API a integrace zasílání zpráv odesílá transakce z externích systémů do inteligentních kontraktů zahrnutých v aplikaci Azure Blockchain Workbench. Také odesílá oznámení událostí do externích systémů na základě změn, ke kterým dochází v rámci aplikace.

Pro scénáře integrace dat Azure Blockchain Workbench obsahuje sadu zobrazení databáze, které slučují kombinaci transakčních dat z blockchainu a metadat o aplikacích a inteligentních kontraktech.

Kromě toho mohou některé scénáře, například scénáře týkající se dodavatelského řetězce nebo médií, vyžadovat také integraci dokumentů. Zatímco Azure Blockchain Workbench neposkytuje volání rozhraní API pro zpracování dokumentů přímo, dokumenty lze začlenit do blockchainové aplikace. Tato část také obsahuje tento vzor.

Tato část obsahuje vzory určené pro implementaci každého z těchto typů integrací v řešení koncové na konec.

## <a name="rest-api-based-integration"></a>Integrace založená na REST API

Možnosti v rámci webové aplikace generované blockchainem Azure Workbench jsou zpřístupňovány prostřednictvím rozhraní REST API. Mezi funkce patří nahrávání, konfigurace a správa aplikací Azure Blockchain Workbench, odesílání transakcí do distribuované hlavní knihy a dotazování metadat aplikací a dat hlavní knihy.

Rozhraní REST API se používá především pro interaktivní klienty, jako jsou webové, mobilní a botové aplikace.

Tato část se zabývá vzory zaměřenými na aspekty rozhraní REST API, které odesílají transakce do distribuované hlavní knihy, a vzory, které se dotazují na data o transakcích z *mimořetězové* sql databáze Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Odesílání transakcí do distribuované hlavní knihy z externího systému

Rozhraní AZURE Blockchain Workbench REST API odesílá ověřené požadavky na provádění transakcí v distribuované hlavní knize.

![Odesílání transakcí do distribuované hlavní knihy](./media/integration-patterns/send-transactions-ledger.png)

Provádění transakcí dochází pomocí procesu znázorněné dříve, kde:

-   Externí aplikace se ověřuje ve službě Azure Active Directory zřízená jako součást nasazení Azure Blockchain Workbench.
-   Oprávnění uživatelé obdrží nosný token, který lze odeslat s požadavky na rozhraní API.
-   Externí aplikace volat rozhraní REST API pomocí tokenu nosiče.
-   Rozhraní REST API zabalí požadavek jako zprávu a odešle jej do sběrnice. Odtud je načten, podepsán a odeslán do příslušné distribuované hlavní knihy.
-   Rozhraní REST API provede požadavek na Azure Blockchain Workbench SQL DB pro záznam požadavku a stanovení aktuálního stavu zřizování.
-   SQL DB vrátí stav zřizování a volání rozhraní API vrátí ID externí aplikaci, která ho volala.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Dotazování metadat blockchainworkbench a transakcí distribuované hlavní knihy

Rozhraní AZURE Blockchain Workbench REST API odesílá ověřené požadavky na podrobnosti dotazu související s prováděním inteligentních smluv v distribuované hlavní knize.

![Dotazování metadat](./media/integration-patterns/querying-metadata.png)

Dotazování probíhá pomocí dříve znázorněného procesu, kde:

1. Externí aplikace se ověřuje ve službě Azure Active Directory zřízená jako součást nasazení Azure Blockchain Workbench.
2. Oprávnění uživatelé obdrží nosný token, který lze odeslat s požadavky na rozhraní API.
3. Externí aplikace volat rozhraní REST API pomocí tokenu nosiče.
4. Rozhraní REST API dotazuje data pro požadavek z DATABÁZE SQL a vrátí je klientovi.

## <a name="messaging-integration"></a>Integrace zasílání zpráv

Integrace zasílání zpráv usnadňuje interakci se systémy, službami a zařízeními, kde interaktivní přihlášení není možné nebo žádoucí. Integrace zasílání zpráv se zaměřuje na dva typy zpráv: zprávy požadující transakce budou provedeny v distribuované hlavní knize a události vystavené v této hlavní knize, když došlo k transakcím.

Integrace zasílání zpráv se zaměřuje na provádění a monitorování transakcí souvisejících s vytvářením uživatelů, vytvářením smluv a prováděním transakcí na smlouvách a je primárně využívána *bezhlavými* back-endovými systémy.

Tato část se zabývá vzory zaměřenými na aspekty rozhraní API založené na zprávách, které odesílají transakce do distribuované hlavní knihy, a vzory, které představují zprávy událostí vystavené základní distribuovanou hlavní knihou.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Jednosměrné doručení události z chytré smlouvy spotřebiteli události 

V tomto scénáři dojde k události v rámci inteligentní smlouvy, například změny stavu nebo provádění určitého typu transakce. Tato událost je vysílána prostřednictvím služby Event Grid pro následné spotřebitele a tito spotřebitelé pak přijmou vhodná opatření.

Příkladem tohoto scénáře je, že když dojde k transakci, spotřebitel by být upozorněna a může provést akci, jako je například záznam informací v DATABÁZI SQL nebo common data service. Tento scénář je stejný vzor, který Workbench následuje naplnit jeho *mimo řetězec* SQL DB.

Dalším by bylo, pokud inteligentní smlouvy přechody do určitého stavu, například když smlouva přejde do *outOfCompliance*. Dojde-li k této změně stavu, může se aktivovat výstraha, která má být odeslána na mobilní telefon správce.

![Jednosměrné doručování událostí](./media/integration-patterns/one-way-event-delivery.png)

K tomuto scénáři dochází pomocí dříve znázorněného procesu, kde:

-   Inteligentní smlouva přejde do nového stavu a odešle událost do hlavní knihy.
-   Hlavní kniha přijímá a doručuje událost do Azure Blockchain Workbench.
-   Azure Blockchain Workbench se přihlásí k odběru událostí z hlavní knihy a obdrží událost.
-   Azure Blockchain Workbench publikuje událost předplatitelům na Event Grid.
-   Externí systémy jsou přihlášeni k odběru mřížky událostí, spotřebovávají zprávu a provádějí příslušné akce.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednosměrné doručení zprávy z externího systému do inteligentní smlouvy

Existuje také scénář, který proudí z opačného směru. V tomto případě je událost generována senzorem nebo externím systémem a data z této události by měla být odeslána do inteligentní smlouvy.

Běžným příkladem je dodání dat z finančních trhů, například cen komodit, akcií nebo dluhopisů, do inteligentního kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručení pracovní plochy Azure Blockchain v očekávaném formátu

Některé aplikace jsou vytvořené pro integraci s Azure Blockchain Workbench a přímo generuje a odesílá zprávy v očekávaných formátech.

![Přímá dodávka](./media/integration-patterns/direct-delivery.png)

K tomuto doručení dochází pomocí dříve znázorněného procesu, kde:

-   Dojde k události v externím systému, který aktivuje vytvoření zprávy pro Azure Blockchain Workbench.
-   Externí systém má kód napsaný k vytvoření této zprávy ve známém formátu a odešle ji přímo do sběrnice.
-   Azure Blockchain Workbench se přihlásí k odběru událostí ze služby Service Bus a načte zprávu.
-   Azure Blockchain Workbench zahájí volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
-   Po obdržení zprávy se smlouva přejde do nového stavu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručení zprávy ve formátu neznámém Azure Blockchain Workbench

Některé systémy nelze upravit tak, aby doručování zpráv ve standardních formátech používaných Azure Blockchain Workbench. V těchto případech lze často použít existující mechanismy a formáty zpráv z těchto systémů. Konkrétně nativní typy zpráv těchto systémů lze transformovat pomocí Logic Apps, Azure Functions nebo jiný vlastní kód mapovat na jeden ze standardních formátů zasílání zpráv očekává.

![Neznámý formát zprávy](./media/integration-patterns/unknown-message-format.png)

K tomu dochází pomocí dříve znázorněného procesu, kde:

-   Dojde k události v externím systému, který aktivuje vytvoření zprávy.
-   Aplikace logiky nebo vlastní kód se používá k přijetí této zprávy a transformovat ji na standardní Azure Blockchain Workbench formátované zprávy.
-   Aplikace logiky odešle transformované zprávy přímo do sběrnice.
-   Azure Blockchain Workbench se přihlásí k odběru událostí ze služby Service Bus a načte zprávu.
-   Azure Blockchain Workbench zahájí volání do hlavní knihy, odesílání dat z externího systému na konkrétní funkci na smlouvě.
-   Funkce provede a obvykle upraví stav. Změna stavu posune vpřed obchodní pracovní postup odráží v inteligentní smlouvy, povolení další funkce, které mají být nyní provedeny podle potřeby.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Přechod řízení na externí proces a čekání na dokončení

Existují scénáře, kde inteligentní kontrakt musí zastavit interní provádění a předat externí proces. Tento externí proces by pak dokončit, poslat zprávu inteligentní smlouvy a provádění by pak pokračovat v rámci inteligentní smlouvy.

#### <a name="transition-to-the-external-process"></a>Přechod na externí proces

Tento vzor je obvykle implementována pomocí následujícího přístupu:

-   Inteligentní smlouvy přechází do určitého stavu. V tomto stavu lze provést žádný nebo omezený počet funkcí, dokud externí systém neprovede požadovanou akci.
-   Změna stavu se objeví jako událost pro příjemce navazujícím trhu.
-   Příjemce příjem obdrží událost a aktivuje spuštění externího kódu.

![Řízení přechodu na externí proces](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Vrácení kontroly z inteligentní smlouvy

V závislosti na možnosti přizpůsobit externí systém může nebo nemusí být schopen doručit zprávy v jednom ze standardních formátů, které Azure Blockchain Workbench očekává. Na základě schopnosti externích systémů generovat jednu z těchto zpráv určit, která z následujících dvou návratových cest je přijata.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručení pracovní plochy Azure Blockchain v očekávaném formátu

![](./media/integration-patterns/direct-delivery.png)

V tomto modelu dojde ke komunikaci se smlouvou a následné změně stavu po předchozím procesu, kdy

-   Po dosažení dokončení nebo konkrétní milník v provádění externího kódu, událost se odešle do service bus připojené k Azure Blockchain Workbench.

-   Pro systémy, které nelze přímo přizpůsobit k napsání zprávy, která odpovídá očekávání rozhraní API, je transformována.

-   Obsah zprávy je zabalen a odeslán do určité funkce na inteligentní smlouvy. Tato dodávka se provádí jménem uživatele přidruženého k externímu systému.

-   Funkce provede a obvykle upraví stav. Změna stavu posune vpřed obchodní pracovní postup odráží v inteligentní smlouvy, povolení další funkce, které mají být nyní provedeny podle potřeby.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručení zprávy ve formátu neznámém Azure Blockchain Workbench

![Neznámý formát zprávy](./media/integration-patterns/unknown-message-format.png)

V tomto modelu, kde zprávu ve standardním formátu nelze odeslat přímo, dojde ke komunikaci se smlouvou a následné změně stavu po předchozím procesu, kdy:

1.  Po dosažení dokončení nebo konkrétní milník v provádění externího kódu, událost se odešle do service bus připojené k Azure Blockchain Workbench.
2.  Aplikace logiky nebo vlastní kód se používá k přijetí této zprávy a transformovat ji na standardní Azure Blockchain Workbench formátované zprávy.
3.  Aplikace logiky odešle transformované zprávy přímo do sběrnice.
4.  Azure Blockchain Workbench se přihlásí k odběru událostí ze služby Service Bus a načte zprávu.
5.  Azure Blockchain Workbench zahájí volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
6. Obsah zprávy je zabalen a odeslán do určité funkce na inteligentní smlouvy. Tato dodávka se provádí jménem uživatele přidruženého k externímu systému.
7.  Funkce provede a obvykle upraví stav. Změna stavu posune vpřed obchodní pracovní postup odráží v inteligentní smlouvy, povolení další funkce, které mají být nyní provedeny podle potřeby.

## <a name="iot-integration"></a>Integrace IoT

Běžným scénářem integrace je zahrnutí telemetrických dat načtených ze senzorů do inteligentní smlouvy. Na základě údajů dodaných senzory by inteligentní smlouvy mohly přijímat informovaná opatření a měnit stav smlouvy.

Pokud například nákladní vozidlo dodávající léčivý přípravek mělo teplotu 110 stupňů, může to mít vliv na účinnost tohoto léčivého přípravku a může způsobit problém veřejné bezpečnosti, pokud nebude zjištěn a odstraněn z dodavatelského řetězce. Pokud řidič zrychlil své auto na 100 mil za hodinu, výsledné informace o senzorech by mohly vyvolat zrušení pojištění jejich pojišťovnou. V případě, že auto bylo auto z půjčovny, GPS data by mohla naznačovat, kdy řidič šel mimo geografii, na kterou se vztahuje jejich nájemní smlouvy a účtovat pokutu.

Výzvou je, že tyto senzory mohou poskytovat data na konstantní bázi a není vhodné odesílat všechna tato data do inteligentní smlouvy. Typickým přístupem je omezit počet zpráv odeslaných do blockchainu při doručování všech zpráv do sekundárního úložiště. Například doručování zpráv přijatých pouze v pevném intervalu, například jednou za hodinu a když obsažená hodnota spadá mimo dohodnutý rozsah pro inteligentní smlouvy. Kontrola hodnot, které nespadají do tolerance, zajišťuje, že data relevantní pro obchodní logiku smluv jsou přijata a provedena. Kontrola hodnoty v intervalu potvrzuje, že senzor stále hlásí. Všechna data se odesílají do sekundárního úložiště sestav, aby bylo možné širší vytváření sestav, analýzy a strojové učení. Například, zatímco získávání údajů ze senzorů pro GPS nemusí být vyžadováno každou minutu pro inteligentní smlouvu, mohou poskytnout zajímavá data, která mají být použita v přehledech nebo mapových trasách.

Na platformě Azure se integrace se zařízeními obvykle provádí pomocí Služby IoT Hub. Služba IoT Hub poskytuje směrování zpráv na základě obsahu a umožňuje typ dříve popsaných funkcí.

![Zprávy IoT](./media/integration-patterns/iot.png)

Proces zobrazuje vzor:

-   Zařízení komunikuje přímo nebo prostřednictvím brány pole do služby IoT Hub.
-   Služba IoT Hub přijímá zprávy a vyhodnocuje zprávy proti vytvořeným trasám, které například kontrolují obsah zprávy. *Hlásí senzor teplotu vyšší než 50 stupňů?*
-   Služba IoT Hub odesílá zprávy, které splňují kritéria, definované sběrnici Service Bus pro trasu.
-   Aplikace logiky nebo jiný kód naslouchá service bus, který služba IoT Hub vytvořilpro trasu.
-   Aplikace logiky nebo jiný kód načte a transformovat zprávu do známého formátu.
-   Transformovaná zpráva, která je teď ve standardním formátu, se odesílá do service busu pro Azure Blockchain Workbench.
-   Azure Blockchain Workbench se přihlásí k odběru událostí ze služby Service Bus a načte zprávu.
-   Azure Blockchain Workbench zahájí volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
-   Po obdržení zprávy smlouva vyhodnotí data a může změnit stav na základě výsledku tohoto hodnocení, například pro vysokou teplotu, změnit stav na *Mimo shodu*.

## <a name="data-integration"></a>Integrace dat

Kromě REST a rozhraní API založeného na textech poskytuje Azure Blockchain Workbench také přístup k SQL DB, který je naplněn metadaty aplikací a kontraktů a transakčními daty z distribuovaných účetních knih.

![Integrace dat](./media/integration-patterns/data-integration.png)

Integrace dat je dobře známa:

-   Azure Blockchain Workbench ukládá metadata o aplikacích, pracovních postupech, smlouvách a transakcích jako součást svého běžného provozního chování.
-   Externí systémy nebo nástroje poskytují jeden nebo více dialogových oken pro usnadnění shromažďování informací o databázi, jako je název databázového serveru, název databáze, typ ověřování, přihlašovací pověření a zobrazení databáze, která mají být využita.
-   Dotazy jsou zapisovány do zobrazení databáze SQL, aby se usnadnila následná spotřeba pomocí externích systémů, služeb, sestav, nástrojů pro vývojáře a nástrojů pro podnikovou produktivitu.

## <a name="storage-integration"></a>Integrace úložiště

Mnoho scénářů může vyžadovat nutnost začlenit atestovatelné soubory. Z několika důvodů není vhodné umístit soubory na blockchain. Místo toho běžným přístupem je provedení kryptografické hodu hash (například SHA-256) proti souboru a sdílené složce, která hash v distribuované hlavní knize. Provedení hash znovu v budoucnu by měl vrátit stejný výsledek. Pokud je soubor změněn, i když je v obraze změněn pouze jeden pixel, vrátí hodnota hash jinou hodnotu.

![Integrace úložiště](./media/integration-patterns/storage-integration.png)

Vzor lze implementovat tam, kde:

-   Externí systém zachová soubor v mechanismu úložiště, jako je například Azure Storage.
-   S souborem nebo souborem a přidruženými metadaty, jako je identifikátor vlastníka, adresa URL, kde je soubor umístěn atd.
-   Hodnota hash a všechna metadata jsou odeslána do funkce inteligentní smlouvy, například *FileAdded*
-   V budoucnu lze soubor a meta-data znovu zařadit a porovnat s hodnotami uloženými v hlavní knize.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Požadavky pro implementaci vzorů integrace pomocí REST a message API

Aby se usnadnila možnost externího systému nebo zařízení pracovat s inteligentnísmlouvou pomocí rozhraní REST nebo api pro zprávy, musí dojít k následujícímu

1. Ve službě Azure Active Directory pro konsorcium se vytvoří účet, který představuje externí systém nebo zařízení.
2. Jeden nebo více vhodných inteligentních kontraktů pro vaši aplikaci Azure Blockchain Workbench má definované funkce pro přijímání událostí z vašeho externího systému nebo zařízení.
3. Konfigurační soubor aplikace pro inteligentní kontrakt obsahuje roli, která je přiřazena systému nebo zařízení.
4. Konfigurační soubor aplikace pro inteligentní kontrakt určuje, ve kterém stavu je tato funkce volána definovanou rolí.
5. Konfigurační soubor aplikace a jeho inteligentní kontrakty se nahrávají do Azure Blockchain Workbench.

Po nahrání aplikace je účet Služby Azure Active Directory pro externí systém přiřazen ke smlouvě a přidružené roli.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testování toků integrace externího systému před zápisem integračního kódu 

Integrace s externími systémy je klíčovým požadavkem mnoha scénářů. Je žádoucí, aby bylo možné ověřit inteligentní návrh smlouvy před nebo paralelně s vývojem kódu pro integraci s externími systémy.

Použití Služby Azure Active Directory (Azure AD) může výrazně urychlit produktivitu vývojářů a čas na zhodnocení. Konkrétně integrace kódu s externím systémem může trvat netriviální množství času. Pomocí Azure AD a automatické generování UX azure blockchain workbench, můžete povolit vývojářům přihlásit se k Blockchain Workbench jako externí systém a naplnit hodnoty z externího systému prostřednictvím uživatelského rozhraní. Můžete rychle rozvíjet a ověřovat nápady v prostředí ověření koncepce před vytvořením integračního kódu pro externí systémy.
