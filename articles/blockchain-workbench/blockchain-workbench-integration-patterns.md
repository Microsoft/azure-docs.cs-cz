---
title: Inteligentní vzorců kontrakt integrace Azure Blockchain Workbench
description: Přehled vzorů integrace inteligentní kontrakt v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: a6a44e30fe58617b43c5491a72fc882015bc9591
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886059"
---
# <a name="smart-contract-integration-patterns"></a>Vzory integrace inteligentní kontraktu

Inteligentní kontrakty se často představují obchodní pracovní postup, který potřebuje k integraci s externími systémy a zařízení.

Požadavky na tyto pracovní postupy zahrnují potřeba zahájení transakce v distribuované hlavní knihy, které obsahují data z externího systému, služby nebo zařízení. Také potřebují mít reagování na události pocházející z inteligentní kontrakty na distribuované hlavní knihy externími systémy.

Rozhraní REST API a zasílání zpráv integrace poskytují možnost Odeslat transakce z externích systémů do inteligentní kontrakty zahrnuté v aplikaci Azure Blockchain Workbench, jak poslat externími systémy na základě změn, které provést oznamování událostí Umístěte v rámci aplikace.

Scénáře integrace dat Azure Blockchain Workbench obsahuje sadu zobrazení databáze, která kombinace transakční data z blockchain a metadata o aplikacích a inteligentní kontrakty sloučení.

Některé scénáře, jako jsou ty související s zadejte řetězec nebo médium, kromě toho může vyžadovat integrace dokumenty. Zatímco Azure Blockchain Workbench neposkytne, že volání rozhraní API pro zpracování dokumentů přímo, dokumenty lze začlenit do Blockchain aplikace Azure. Tato část také obsahuje tento vzor.

Tato část obsahuje vzory identifikovat pro každý z těchto typů integrace implementace v koncová řešení.

## <a name="rest-api-based-integration"></a>Integrace založené na rozhraní API REST

Možnosti v rámci webové aplikace Azure Blockchain Workbench, vygeneruje se zveřejňují přes rozhraní REST API. To zahrnuje odesílání Azure Blockchain Workbench, konfiguraci a správu aplikací, odeslání transakce distribuované hlavní knihy a dotazování metadata a hlavní knihy data aplikací.

Rozhraní REST API slouží především pro interaktivní klientů, jako jsou webové, mobilní a robota aplikace.

Tato část zjistí vzory zaměřuje na aspekty rozhraní REST API, které odesílají transakce do distribuované hlavní knihy a ty, které dotaz na data o transakce z Azure Blockchain Workbench *vypnout řetězu* databáze SQL.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Odesílání transakce distribuované hlavní knihy z externího systému

Rozhraní REST API Azure Blockchain Workbench poskytuje schopnost posílání ověřené požadavky na spouštění transakce v distribuované hlavní knihy.

![Odesílání transakce do distribuované hlavní knihy](media/blockchain-workbench-integration-patterns/send-transactions-ledger.png)

Tato situace nastane pomocí procesu, které popsané výše, kde:

-   Externí aplikace se ověřuje na Azure Active Directory, které jsou zřízené v rámci nasazení Azure Blockchain Workbench.
-   Autorizovaní uživatelé obdrží token nosiče, který lze odesílat s požadavky na rozhraní API.
-   Externí aplikace volání rozhraní REST API pomocí tokenu nosiče.
-   Rozhraní REST API balíčky požadavek zprávu a odešle ji do služby Service Bus. Zde bude načíst, podepsané a odeslaných do příslušné distribuované knihy.
-   Rozhraní API REST odešle požadavek do databáze SQL Azure Blockchain Workbench zaznamenávat žádosti a vytvoření aktuálního stavu zřizování.
-   Databáze SQL vrátí stav zřizování a volání rozhraní API vrátí ID pro externí aplikaci, která je volána.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Dotazování Blockchain Workbench metadata a hlavní knihy distribuované transakce

Rozhraní REST API Azure Blockchain Workbench poskytuje schopnost posílání ověřené požadavky související s inteligentní kontrakt provádění na distribuované hlavní knihy podrobnosti dotazu.

![Dotazování metadat](media/blockchain-workbench-integration-patterns/querying-metadata.png)

Tato situace nastane pomocí procesu, které popsané výše, kde:

1. Externí aplikace se ověřuje na Azure Active Directory, které jsou zřízené v rámci nasazení Azure Blockchain Workbench.
2. Autorizovaní uživatelé obdrží token nosiče, který lze odesílat s požadavky na rozhraní API.
3. Externí aplikace volání rozhraní REST API pomocí tokenu nosiče.
4. Rozhraní REST API dotazuje data žádosti z databáze SQL a vrátí ji do klienta.

## <a name="messaging-integration"></a>Integrace pro zasílání zpráv

Zasílání zpráv integrace usnadňuje interakce se systémy, služeb a zařízení, kde interaktivní přihlášení není možná nebo žádoucí. Zasílání zpráv integrace se zaměřuje na dva typy zpráv, ty, které požadují, aby transakce provést v distribuované hlavní knihy a události, které jsou vystavené tento hlavní knihy, když transakce došlo.

Zasílání zpráv integrace se zaměřuje na spuštění a monitorování transakcí týkající se vytvoření uživatele, vytvoření kontraktu a provádění transakcí v kontraktech a slouží především *bezobslužných* back-end systémy.

Tato část zjistí vzory zaměřuje na aspekty rozhraní API na základě zpráv, které odesílají do distribuované hlavní knihy a ty, které představují zprávy o událostech vystavené základní hlavní knihy distribuované transakce.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Doručování jednosměrný událostí z kontraktu inteligentní k příjemce událostí 

V tomto scénáři dojde k události v rámci inteligentní kontraktu, například změna stavu nebo spuštění určitého typu transakce. Tato událost je vysílání přes mřížce událostí k příjemce pro příjem dat a tyto příjemce potom proveďte příslušné akce.

Příklad tohoto scénáře je, že když dojde k transakci, příjemce by upozorněni a může trvat akce, například záznam informace v databázi SQL nebo běžných dat služby. Toto je stejný vzor, který následuje Workbench k naplnění jeho *vypnout řetězu* databázi SQL.

Jiné by pokud kontraktu inteligentní přejde na konkrétní stav, například při kontraktu přejde do *OutOfCompliance*. Když se stane tuto změnu stavu, se může spustit výstrahu k odeslání do Správce mobilního telefonu.

![Jedním ze způsobů události doručení](media/blockchain-workbench-integration-patterns/one-way-event-delivery.png)

Tato situace nastane pomocí procesu, které popsané výše, kde:

-   Inteligentní kontrakt přejde do nového stavu a odešle událost do hlavní knihy.
-   Hlavní knihy přijímá a doručí události do Azure Blockchain Workbench.
-   Azure Blockchain Workbench je přihlášen k události z hlavní knihy a přijímá události.
-   Azure Blockchain Workbench publikuje události odběratelům v mřížce událostí.
-   Externími systémy přihlášeni k mřížce událostí, využívat zprávu a proveďte příslušné akce.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednosměrné události doručení zprávy z externího systému pro inteligentní kontraktu

Je také scénář, který pokračuje v opačném směru. V takovém případě událost je generován senzoru nebo do externího systému a data z této události by měly být odeslány na inteligentní kontrakt.

Běžným příkladem jsou doručování dat z finančních trhů, například ceny komodit, stock nebo vazeb, inteligentní kontrakt.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Blockchain Workbench Azure v očekávaném formátu

Některé aplikace jsou postaveny na integraci se službou Azure Blockchain Workbench a přímo generuje a odesílání zpráv v očekávané formátech.

![Přímé doručení](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Tato situace nastane pomocí procesu, které popsané výše, kde:

-   V externím systému, která aktivuje vytváření zpráv pro Azure Blockchain Workbench dojde k události.
-   Externí systém má kód napsaný pro vytvoření této zprávy ve formátu, známé a odešle to přímo na Service Bus.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání hlavní knihy, odesílání dat z externího systému do konkrétní smlouvě.
-   Po obdržení zprávy kontrakt přejde do nového stavu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručování zpráv ve formátu neznámé Azure Blockchain Workbench

Některé systémy nemůže být upraven pro doručování zpráv ve standardní formáty používá Azure Blockchain Workbench. V těchto případech existující mechanismy a zpráv můžete často použít formáty z těchto systémů. Konkrétně typy nativní zpráv z těchto systémů lze je transformovat pomocí Logic Apps, Azure Functions nebo další vlastní kód pro mapování na jedno standardní formáty očekávání pro zasílání zpráv.

![Neznámá zpráva formátu](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Tato situace nastane pomocí procesu, které popsané výše, kde:

-   Dojde k události v externím systému, která aktivuje vytváření zpráv.
-   Aplikace logiky nebo vlastní kód slouží k přijímat zprávy a transformují je standardní formátovaná zpráva Azure Blockchain Workbench.
-   Aplikace logiky odešle zprávu transformovaných přímo k Service Bus.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání hlavní knihy, odesílání dat z externího systému do konkrétní funkce na kontrakt.
-   Funkce spustí a obvykle změní stav. Změna stavu přesune dál pracovní postup společnosti promítnuta inteligentní kontrakt, povolení dalších funkcí, mají nyní provést podle potřeby.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Přechod řízení externí zpracovat a operátoru await dokončení

Existují scénáře, kde musí kontraktu inteligentní zastavit vnitřní spuštění a můžete předat do externího procesu. Že by pak dokončete externího procesu, pošlete e-mail na inteligentní kontrakt a provádění by poté pokračujte v rámci inteligentní kontrakt.

#### <a name="transition-to-the-external-process"></a>Přechod do externího procesu

Tento vzor je obvykle implementovaná pomocí následující postup:

-   Inteligentní kontrakt přechody na určitém stavu. V tomto stavu, buď ne nebo omezený počet funkcí lze provést, dokud nedojde k externím systémem požadované akce.
-   Změna stavu je prezentované jako událost pro příjem dat příjemce.
-   Podřízené příjemce obdrží události a aktivuje spuštění externího kódu.

![Ovládací prvek přechod do externího procesu](media/blockchain-workbench-integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Vrátí ovládací prvek z inteligentní kontraktu

V závislosti na přizpůsobit externí systém může nebo nemusí být možné doručování zpráv v jednom z standardní formáty, které Azure Blockchain Workbench očekává. Podle možnost externími systémy generovat jednu z těchto zpráv se určit, který návratový následující dvě cesty.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Blockchain Workbench Azure v očekávaném formátu

![](media/blockchain-workbench-integration-patterns/direct-delivery.png)

V tomto modelu probíhá komunikace kontraktu a následné stavu změnu následující výše zpracovat where -

-   Při dosažení dokončení nebo konkrétní verze při provádění externích kódu, událost je odeslána Service Bus, připojení k Azure Blockchain Workbench.

-   Pro systémy, které nelze přizpůsobit přímo k zápisu zprávu, která odpovídá očekávání rozhraní API bude možné transformovat.

-   Obsah zprávy se zabalí a odešle na konkrétní funkci na inteligentní kontrakt. Děje se jménem uživatele přidruženého k externím systému.

-   Funkce spustí a obvykle slouží k úpravě stavu. Změna stavu přesune dál pracovní postup společnosti promítnuta inteligentní kontrakt, povolení dalších funkcí, mají nyní provést podle potřeby.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručování zpráv ve formátu neznámé Azure Blockchain Workbench

![Neznámá zpráva formátu](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

V tomto modelu, kde zprávu ve standardním formátu nelze odeslat přímo, komunikaci se smlouvou a následující výše dojde ke změně stavu následné zpracování kde:

1.  Při dosažení dokončení nebo konkrétní verze při provádění externích kódu, událost je odeslána Service Bus, připojení k Azure Blockchain Workbench.
2.  Aplikace logiky nebo vlastní kód slouží k přijímat zprávy a transformují je standardní formátovaná zpráva Azure Blockchain Workbench.
3.  Aplikace logiky odešle zprávu transformovaných přímo k Service Bus.
4.  Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
5.  Azure Blockchain Workbench zahájí volání hlavní knihy, odesílání dat z externího systému do konkrétní smlouvě.
6. Obsah zprávy se zabalí a odešle na konkrétní funkci na inteligentní kontrakt. Děje se jménem uživatele přidruženého k externím systému.
7.  Funkce spustí a obvykle slouží k úpravě stavu. Změna stavu přesune dál pracovní postup společnosti promítnuta inteligentní kontrakt, povolení dalších funkcí, mají nyní provést podle potřeby.

## <a name="iot-integration"></a>Integrace IoT

Běžné scénáře integrace je zahrnutí telemetrická data načtená ze senzorů v inteligentní kontrakt. Na základě dat doručil senzorů, může inteligentní kontrakty provést informované akce a změnit stav kontrakt.

Například pokud vůz, doručování lékařství měli jeho teploty soar do 110 stupňů, ho může mít vliv efektivitu léku a veřejné bezpečnosti problém mohou způsobovat není-li zjistil a odebrána z zásobovací řetězec. Pokud ovladač accelerated jeho car na 100 miles za hodinu, může spustit výsledné informace senzor zrušení pojištění jeho pojištění zprostředkovatelem. Pokud Auto pronájem car, může GPS data signalizují, že ovladač se mimo geography, předmětem jeho pronájem smlouvy a účtují snížení.

Na výzvu je, že podobných senzorů můžete doručování dat na základě konstantní a není vhodné k odeslání všech těchto dat do inteligentní kontrakt. Typickým přístupem je omezit počet zpráv odeslaných do blockchain při dodávání všechny zprávy do sekundárního úložiště. Například doručování zpráv přijme jenom pevně zadaném intervalu, například jednou za hodinu a omezením hodnota spadá mimo dohodnuté na rozsah pro inteligentní kontrakt. Kontrola hodnoty, které spadají mimo tolerance, zajistí, že je relevantní pro obchodní logiky kontraktů dat přijatých a provést. Kontrolou hodnoty v intervalu potvrdí pořád hlásí senzor. Všechna data posílá sekundární vytváření sestav úložiště umožňující širší generování sestav, analýzy a strojové učení. Například při získávání odečty snímačů pro GPS nelze vyžadovat každou minutu pro inteligentní kontrakt, může poskytují zajímavé údaje, které mají být použity v sestavách nebo mapování tras.

Na platformě Azure je integrace s zařízení obvykle provádí službou IoT Hub. IoT Hub umožňuje směrovat zprávy na základě obsahu a umožňuje typ funkce popsané výše.

![Zprávy IoT](media/blockchain-workbench-integration-patterns/iot.png)

Proces výše znázorňuje vzor pro tato možnost je implementovaná:

-   Zařízení komunikuje přímo nebo prostřednictvím brána pole do služby IoT Hub.
-   IoT Hub přijímá zprávy a vyhodnocuje zprávy proti trasy navázat, prohlédněte si obsah zprávy, třeba. *Zjistí senzoru teplotu vyšší než 50 stupňů?*
-   IoT Hub odešle zprávy, které splňují kritéria definovaná sběrnice služby pro trasu.
-   Aplikace logiky nebo jiný kód, naslouchá na Service Bus, který je vytvořen IoT Hub pro trasu.
-   Aplikace logiky nebo jiný kód načte a transformace zprávu, která se známé formátu.
-   Transformovaných zprávy, nyní ve standardním formátu, je odeslán sběrnice služby pro Azure Blockchain Workbench.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání hlavní knihy, odesílání dat z externího systému do konkrétní smlouvě.
-   Po obdržení zprávy, kontrakt vyhodnotí data a může ke změně stavu na základě výsledku této vyhodnocení, například pro vysoké teploty, změňte stav, který má *mimo dodržování předpisů*.

## <a name="data-integration"></a>Integrace dat

Kromě a na základě zpráv rozhraní API REST Azure Blockchain Workbench také poskytuje přístup k databázi SQL, naplní meta-data aplikací a kontrakt, jakož i transakční data z hlavní knihy distribuované.

![Integrace dat](media/blockchain-workbench-integration-patterns/data-integration.png)

Integrace dat je dobře známé:

-   Azure Blockchain Workbench ukládají metadata o aplikace, pracovní postupy, smlouvy a transakce v rámci své normální operační chování.
-   Externími systémy nebo nástroje zadejte jeden nebo více dialogová okna pro usnadnění shromažďování informací o databázi, například název databázového serveru, název databáze, typ ověřování, přihlašovací údaje, které databáze zobrazení využívat.
-   Dotazy jsou napsaných pro zobrazení databáze SQL pro usnadnění podřízené spotřeba externími systémy, služby, vytváření sestav, nástroje pro vývojáře a enterprise kancelářské nástroje.

## <a name="storage-integration"></a>Integrace úložiště

Mnoho scénářů může vyžadovat potřeba začlenit attestable soubory. Několik důvodů bude nevhodných umístěte soubory na blockchain. Místo toho je běžný postup k provedení kryptografická hodnota hash (například SHA-256) pro soubor a sdílet tuto hodnotu hash na distribuované hlavní knihy. Provádění hash kdykoli znovu budoucí by měl vrátit stejný výsledek. Pokud je upraven soubor, i v případě, že pouze jeden bod. je upravit v bitovou kopii, vrátí hodnotu hash na jinou hodnotu.

![Integrace úložiště](media/blockchain-workbench-integration-patterns/storage-integration.png)

Vzor může být implementováno kde:

-   Externí systém trvá souboru v úložišti mechanismu, jako je Azure Storage.
-   Hodnota hash je generována soubor nebo soubor a přidružená metadata například identifikátor pro vlastníka, adresu URL, kde se soubor nachází, atd.
-   Hodnota hash a veškerá metadata, posílá funkce na inteligentní kontraktu, například *FileAdded*
-   V budoucnosti souborů a metadat může být použita hodnota hash znovu a porovná s hodnot uložených na hlavní knihy.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Požadavky pro implementaci integrace vzory pomocí REST a zpráva rozhraní API

Pro usnadnění možnost pro zařízení s externím systému nebo interakci s inteligentní kontrakt pomocí REST nebo zprávy rozhraní API, potřeba splnit následující podmínky-

1. Ve službě Azure Active Directory pro konsorcium je vytvořen účet, který představuje externí systém nebo zařízení.
2. Odpovídající inteligentní smluv pro vaši aplikaci Azure Blockchain Workbench mít funkcí definovaných přijímat události z externího systému nebo zařízení.
3. Konfigurační soubor aplikace pro inteligentní smlouva obsahuje roli, která systém nebo zařízení bude přiřazen.
4. Konfigurační soubor aplikace pro inteligentní smlouva uvedeno v, které s oznámením, že tato funkce je možné volat v roli definované.
5. Konfigurační soubor aplikace a její inteligentní kontrakty odeslány do Azure Blockchain Workbench.

Po nahrání aplikace účtu Azure Active Directory pro externí systém je přiřazený k kontrakt a přidružené role.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testování externího systému integrace toky před psaní kódu integrace 

Poskytuje možnost integrovat s externími systémy je klíčovým požadavkem mnoho scénářů. Je třeba být schopný ověřit návrhu inteligentní kontrakt předchozí nebo paralelně s vývojem kód pro integraci s externími systémy.

Použití služby Azure Active Directory (Azure AD) můžou výrazně urychlit produktivita vývojářů a času na hodnotu. Konkrétně kód integraci s externím systému, může trvat netriviální množství času. Pomocí Azure AD a automatické generování UX pomocí Azure Blockchain Workbench, to umožňují vývojářům Přihlaste se k Workbench jako daný externí systém a naplní hodnoty očekává z tohoto externího systému pomocí UX To umožňuje rychlý vývoj a ověření nápadů v testování konceptu prostředí buď před, nebo paralelně integrace kód zapisovaný pro externími systémy.
