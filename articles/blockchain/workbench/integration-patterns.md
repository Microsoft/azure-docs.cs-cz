---
title: Vzory integrace inteligentních kontraktů – Azure blockchain Workbench
description: Přehled způsobů integrace inteligentních kontraktů ve službě Azure blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538316"
---
# <a name="smart-contract-integration-patterns"></a>Vzory integrace inteligentních kontraktů

Inteligentní kontrakty často reprezentují obchodní pracovní postup, který je potřeba integrovat s externími systémy a zařízeními.

Požadavky těchto pracovních postupů zahrnují nutnost iniciovat transakce v distribuované hlavní knize, které obsahují data z externího systému, služby nebo zařízení. Také potřebují, aby externí systémy reagovaly na události pocházející z inteligentních smluv v distribuované účetní knize.

Integrace REST API a zasílání zpráv odesílá transakce z externích systémů do inteligentních kontraktů, které jsou součástí aplikace Azure blockchain Workbench. Odesílá také oznámení o událostech externím systémům na základě změn, které probíhají v rámci aplikace.

V případě scénářů integrace dat zahrnuje Azure blockchain Workbench sadu databázových zobrazení, která sloučí kombinaci transakčních dat z blockchain a meta data o aplikacích a inteligentních kontraktech.

Kromě toho můžou některé scénáře, jako jsou třeba ty, které souvisejí s dodavatelským řetězcem nebo médiem, vyžadovat i integraci dokumentů. I když Azure blockchain Workbench neposkytuje volání rozhraní API pro zpracování dokumentů přímo, můžou se dokumenty začlenit do aplikace blockchain. Tato část také obsahuje tento model.

Tato část obsahuje vzory identifikované pro implementaci každého z těchto typů integrací v rámci kompletních řešení.

## <a name="rest-api-based-integration"></a>Integrace na základě REST API

Možnosti v rámci webové aplikace vygenerované službou Azure blockchain Workbench jsou zpřístupněny prostřednictvím REST API. Mezi možnosti patří nahrávání, konfigurace a Správa aplikací, odesílání transakcí do distribuované knihy a dotazování na metadata aplikace a data hlavní knihy.

REST API se primárně používá pro interaktivní klienty, jako jsou webové, mobilní a robotské aplikace.

V této části se dozvíte, jaké jsou vzory zaměřené na aspekty REST API, které odesílají transakce do distribuované účetní knihy a vzory, které dotazují data o transakcích z databáze Azure blockchain Workbench z neaktivního *řetězu* .

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Posílání transakcí do distribuované hlavní knihy z externího systému

REST API Azure blockchain Workbench odesílá ověřené požadavky na provádění transakcí v distribuované účetní knize.

![Odesílání transakcí do distribuované knihy](./media/integration-patterns/send-transactions-ledger.png)

K provádění transakcí dojde pomocí dříve popsaného procesu, kde:

-   Externí aplikace se ověří Azure Active Directory zřízené jako součást nasazení Azure blockchain Workbench.
-   Autorizovaní uživatelé obdrží nosný token, který se dá odeslat s požadavky do rozhraní API.
-   Externí aplikace volají REST API pomocí nosných tokenů.
-   REST API zabalí požadavek jako zprávu a odešle ji do Service Bus. Odtud se načte, podepíše a odešle do příslušné distribuované účetní knihy.
-   REST API vytvoří požadavek na databázi SQL Azure blockchain Workbench pro záznam požadavku a určení aktuálního stavu zřizování.
-   SQL DB vrátí stav zřizování a volání rozhraní API vrátí ID k externí aplikaci, která ji volala.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Dotazování na metadata blockchain Workbench a transakce distribuované hlavní knihy

REST API Azure blockchain Workbench odesílá ověřené požadavky na dotazy týkající se provádění inteligentních kontraktů v distribuované účetní knize.

![Dotazování na metadata](./media/integration-patterns/querying-metadata.png)

Dotazování proběhne pomocí dříve popsaného procesu, kde:

1. Externí aplikace se ověří Azure Active Directory zřízené jako součást nasazení Azure blockchain Workbench.
2. Autorizovaní uživatelé obdrží nosný token, který se dá odeslat s požadavky do rozhraní API.
3. Externí aplikace volají REST API pomocí nosných tokenů.
4. REST API se dotazuje na data žádosti z databáze SQL a vrátí je klientovi.

## <a name="messaging-integration"></a>Integrace zasílání zpráv

Integrace zasílání zpráv usnadňuje interakci se systémy, službami a zařízeními, kde interaktivní přihlášení není možné nebo žádoucí. Integrace zasílání zpráv se zaměřuje na dva typy zpráv: zprávy požadující transakce spouštěné v distribuované účetní knize a události vystavené touto hlavní knihou v době, kdy byly provedeny transakce.

Integrace zasílání zpráv se zaměřuje na provádění a monitorování transakcí souvisejících s vytvořením uživatele, vytvořením smlouvy a prováděním transakcí ve smlouvách a primárně se používá *v bezsystémovém back-* Endu.

Tato část se zabývá vzory, které se zaměřují na aspekty rozhraní API založeného na zprávách, které odesílají transakce do distribuované hlavní knihy a vzory, které reprezentují zprávy událostí vystavené základní distribuovanou knihou.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Jednosměrné doručování událostí z inteligentní smlouvy na příjemce události 

V tomto scénáři dojde k události v rámci inteligentní smlouvy, například změny stavu nebo provedení konkrétního typu transakce. Tato událost je vysílána prostřednictvím Event Grid příjemcům pro příjem dat a uživatelé pak přijmou příslušné akce.

Příkladem tohoto scénáře je, že když dojde k transakci, příjemce se upozorní a může provést akci, jako je například záznam informací v databázi SQL nebo Common Data Service. Tento scénář je stejný jako ten, který slouží k naplnění svého *řetězce* SQL DB v aplikaci Workbench.

Další by byl v případě, že se inteligentní kontrakt změní na určitý stav, například když kontrakt přejde do *OutOfCompliance*. Když dojde ke změně stavu, může se spustit výstraha, která se pošle na mobilní telefon správce.

![Jednosměrné doručování událostí](./media/integration-patterns/one-way-event-delivery.png)

K tomuto scénáři dochází pomocí dříve popsaného procesu, kde:

-   Inteligentní kontrakt přejde do nového stavu a odešle událost do hlavní knihy.
-   Tato kniha obdrží a doručuje událost do Azure blockchain Workbench.
-   Azure blockchain Workbench se přihlásí k odběru událostí z hlavní knihy a přijme událost.
-   Azure blockchain Workbench publikuje událost pro předplatitele na Event Grid.
-   Externím systémům se přihlásí k odběru Event Grid, vybírají zprávu a provede příslušné akce.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednosměrné doručování zpráv z externího systému do inteligentního kontraktu

Existuje také scénář, který se bude natékat od opačného směru. V takovém případě se událost generuje pomocí senzoru nebo externího systému a data z této události by se měla odeslat do inteligentní smlouvy.

Běžným příkladem je doručování dat z finančních trhů, například ceny komodit, akcií nebo dluhopisů do inteligentní smlouvy.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Azure blockchain Workbench v očekávaném formátu

Některé aplikace jsou sestavené pro integraci s Azure blockchain Workbench a přímo generují a odesílají zprávy v očekávaných formátech.

![Přímé doručování](./media/integration-patterns/direct-delivery.png)

K tomuto doručení dochází pomocí dříve popsaného procesu, kde:

-   K události dochází v externím systému, který aktivuje vytvoření zprávy pro Azure blockchain Workbench.
-   Externí systém obsahuje kód napsaný pro vytvoření této zprávy ve známém formátu a pošle je přímo do Service Bus.
-   Azure blockchain Workbench se přihlašuje k odběru událostí z Service Bus a zprávu načte.
-   Azure blockchain Workbench inicializuje volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
-   Po přijetí zprávy se kontrakt přejde do nového stavu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručení zprávy ve formátu, který není známý pro Azure blockchain Workbench

Některé systémy není možné upravovat, aby bylo možné doručovat zprávy ve standardních formátech používaných službou Azure blockchain Workbench. V těchto případech je možné často použít stávající mechanismy a formáty zpráv z těchto systémů. Konkrétně typy nativních zpráv těchto systémů lze transformovat pomocí Logic Apps, Azure Functions nebo jiného vlastního kódu pro mapování na jeden ze standardních formátů pro zasílání zpráv, které jsou očekávány.

![Neznámý formát zprávy](./media/integration-patterns/unknown-message-format.png)

K tomu dochází v dříve použitém procesu, kde:

-   K události dojde v externím systému, který spustí vytvoření zprávy.
-   Aplikace logiky nebo vlastní kód slouží k přijetí této zprávy a jejich transformaci na standardní formátovanou zprávu ve formátu Azure blockchain Workbench.
-   Aplikace logiky pošle transformované zprávy přímo do Service Bus.
-   Azure blockchain Workbench se přihlašuje k odběru událostí z Service Bus a zprávu načte.
-   Azure blockchain Workbench inicializuje volání do hlavní knihy a odesílá data z externího systému do konkrétní funkce v kontraktu.
-   Funkce se spustí a obvykle upraví stav. Změna stavu přesune směrem nahoru pracovní postup, který se projeví v rámci inteligentního kontraktu, takže se teď v případě potřeby spustí další funkce.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Přechod řízení na externí proces a čekání na dokončení

Existují situace, kdy je nutné, aby inteligentní kontrakt zastavil interní spuštění a předání externímu procesu. Tento externí proces se pak dokončí, pošle zprávu na inteligentní kontrakt a provádění by pak pokračovalo v rámci inteligentní smlouvy.

#### <a name="transition-to-the-external-process"></a>Přechod k externímu procesu

Tento model je obvykle implementován pomocí následujícího přístupu:

-   Inteligentní kontrakt přejde do určitého stavu. V tomto stavu je možné provést buď žádné, nebo omezené množství funkcí, dokud externí systém neprovede požadovanou akci.
-   Změna stavu je proplochá jako událost příjemci pro příjem dat.
-   Klient pro příjem dat obdrží událost a aktivuje provádění externího kódu.

![Diagram znázorňuje změnu stavu v rámci smlouvy, která způsobila, že událost přejde do distribuované hlavní knihy. Blockchain Workbench pak vybere událost a publikuje ji.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Vrácení ovládacího prvku z inteligentního kontraktu

V závislosti na možnosti přizpůsobení externího systému může nebo nemusí být možné doručovat zprávy v jednom ze standardních formátů, které očekává Azure blockchain Workbench. Na základě možnosti externích systémů, jak vygenerovat jednu z těchto zpráv, určete, která z následujících dvou návratových cest bude provedena.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Azure blockchain Workbench v očekávaném formátu

![V diagramu se zobrazí zpráva o tom, že externí systém vybral blockchain Workbench prostřednictvím Service Bus. Blockchain Workbench pak pošle zprávu jako transakci do distribuované účetní knihy jménem agenta. Je předána kontraktu, kde způsobuje změnu stavu.](./media/integration-patterns/direct-delivery.png)

V tomto modelu se komunikace se smlouvou a následnou stavovou změnou probíhá po předchozím procesu –

-   Po dosažení dokončení nebo konkrétního milníku v rámci externího spuštění kódu se do Service Bus připojeného k Azure blockchain Workbench pošle událost.

-   Pro systémy, které není možné přímo přizpůsobovat pro zápis zprávy, která odpovídá očekávání rozhraní API, se transformuje.

-   Obsah zprávy se zabalí a odešle do konkrétní funkce v rámci inteligentního kontraktu. Toto doručení se provádí jménem uživatele, který je přidružený k externímu systému.

-   Funkce se spustí a obvykle upraví stav. Změna stavu přesune směrem nahoru pracovní postup, který se projeví v rámci inteligentního kontraktu, takže se teď v případě potřeby spustí další funkce.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručení zprávy ve formátu, který není známý pro Azure blockchain Workbench

![Neznámý formát zprávy](./media/integration-patterns/unknown-message-format.png)

V tomto modelu, kde nelze odeslat zprávu ve standardním formátu, se komunikace se smlouvou a následnou stavovou změnou probíhá po předchozím procesu:

1.  Po dosažení dokončení nebo konkrétního milníku v rámci externího spuštění kódu se do Service Bus připojeného k Azure blockchain Workbench pošle událost.
2.  Aplikace logiky nebo vlastní kód slouží k přijetí této zprávy a jejich transformaci na standardní formátovanou zprávu ve formátu Azure blockchain Workbench.
3.  Aplikace logiky pošle transformované zprávy přímo do Service Bus.
4.  Azure blockchain Workbench se přihlašuje k odběru událostí z Service Bus a zprávu načte.
5.  Azure blockchain Workbench inicializuje volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
6. Obsah zprávy se zabalí a odešle do konkrétní funkce v rámci inteligentního kontraktu. Toto doručení se provádí jménem uživatele, který je přidružený k externímu systému.
7.  Funkce se spustí a obvykle upraví stav. Změna stavu přesune směrem nahoru pracovní postup, který se projeví v rámci inteligentního kontraktu, takže se teď v případě potřeby spustí další funkce.

## <a name="iot-integration"></a>Integrace IoT

Běžný scénář integrace je zahrnutí dat telemetrie získaných ze senzorů v rámci inteligentních kontraktů. V závislosti na datech dodaných senzory mohou chytré smlouvy přijmout informovaná opatření a měnit stav smlouvy.

Pokud například nákladní automobil, který dodává lékařství, dosáhl teploty společnosti do 110 stupňů, může to mít dopad na účinnost lékařství a může způsobit potíže s veřejným zabezpečením, pokud se nezjistí a odebere z dodavatelského řetězce. Pokud ovladač zrychluje své auto na 100 mil za hodinu, mohou výsledné informace snímače aktivovat zrušení pojištění jejich poskytovatelem pojištění. Pokud byl automobil půjčovnou auta, data GPS by mohla indikovat, že řidič přešel mimo zeměpisnou smlouvu, na kterou se vztahuje Smlouva o pronájmu, a účtuje pokutu.

Důvodem je, že tyto senzory můžou data doručovat na konstantu a není vhodné posílat všechna tato data do inteligentní smlouvy. Typickým přístupem je omezení počtu zpráv odeslaných do blockchain při doručování všech zpráv do sekundárního úložiště. Například doručení zpráv, které jsou přijímány pouze v pevně stanoveném intervalu, například jednou za hodinu, a v případě, že obsažená hodnota spadá mimo dohodnutý rozsah pro inteligentní kontrakt. Kontrola hodnot, které spadají mimo toleranci, zajišťuje, že budou přijata a provedena data relevantní pro obchodní logiku. Kontrola hodnoty v intervalu potvrdí, že senzor stále hlásí. Všechna data se odesílají do sekundárního úložiště pro vytváření sestav, která umožňují širší tvorbu sestav, analýz a strojového učení. Například při získávání čtecího senzoru pro GPS nemusí být každá minuta pro inteligentní kontrakt vyžadována, může poskytnout zajímavá data, která se mají použít v sestavách nebo mapování tras.

Integrace se zařízeními na platformě Azure se obvykle provádí s IoT Hub. IoT Hub poskytuje směrování zpráv na základě obsahu a umožňuje typ dříve popsaných funkcí.

![Zprávy IoT](./media/integration-patterns/iot.png)

Tento proces znázorňuje vzor:

-   Zařízení komunikuje přímo nebo přes bránu pole IoT Hub.
-   IoT Hub přijímá zprávy a vyhodnocuje zprávy proti vytvořeným trasám, které kontrolují obsah zprávy, například. *Hlásí senzor teplotu větší než 50 stupňů?*
-   IoT Hub odesílá zprávy, které splňují kritéria, pro definovanou Service Bus trasy.
-   Aplikace logiky nebo jiný kód naslouchá Service Bus, který IoT Hub pro trasu vytvořil.
-   Aplikace logiky nebo jiný kód načte a transformuje zprávu do známého formátu.
-   Transformovaná zpráva se teď ve standardním formátu pošle do Service Bus pro Azure blockchain Workbench.
-   Azure blockchain Workbench se přihlašuje k odběru událostí z Service Bus a zprávu načte.
-   Azure blockchain Workbench inicializuje volání do hlavní knihy a odesílá data z externího systému do konkrétní smlouvy.
-   Po přijetí zprávy kontrakt vyhodnotí data a může změnit stav na základě výsledku tohoto vyhodnocení, například pro vysokou teplotu, změnit stav na *nedodržení předpisů*.

## <a name="data-integration"></a>Integrace dat

Kromě REST a rozhraní API založeného na zprávách poskytuje Azure blockchain Workbench také přístup k SQL DB naplněný metadaty aplikace a kontraktu a také transakční data z distribuovaných knih.

![Integrace dat](./media/integration-patterns/data-integration.png)

Integrace dat je dobře známá:

-   Azure blockchain Workbench ukládá v rámci normálního provozního chování metadata o aplikacích, pracovních postupech, kontraktech a transakcích.
-   Externí systémy nebo nástroje poskytují jednu nebo více dialogových oken, které usnadňují shromažďování informací o databázi, jako je název databázového serveru, název databáze, typ ověřování, přihlašovací údaje a informace o tom, která zobrazení databáze se mají využít.
-   Dotazy se zapisují mezi zobrazeními databáze a usnadňují tak příjem dat z externích systémů, služeb, sestav, vývojářských nástrojů a podnikových nástrojů pro produktivitu.

## <a name="storage-integration"></a>Integrace úložiště

Řada scénářů může vyžadovat, abyste museli začlenit ověřené soubory. Z několika důvodů je nevhodné vkládat soubory do blockchain. Běžným přístupem je místo toho provést kryptografickou hodnotu hash (například SHA-256) proti souboru a sdílet tuto hodnotu hash v distribuované účetní knize. Opětovné provádění algoritmu hash v jakékoli budoucí době by mělo vracet stejný výsledek. Pokud je soubor upravený, i když se v obrázku změní jenom jeden pixel, hodnota hash vrátí jinou hodnotu.

![Integrace úložiště](./media/integration-patterns/storage-integration.png)

Vzor lze implementovat tam, kde:

-   Externí systém uchovává soubor v úložném mechanismu, například Azure Storage.
-   Hodnota hash se vygeneruje se souborem nebo souborem a přidruženými metadaty, jako je například identifikátor vlastníka, adresa URL, kde je soubor umístěný atd.
-   Hodnota hash a jakákoli metadata se odešlou do funkce v rámci inteligentních kontraktů, jako je *Přidání* .
-   V budoucnu může být soubor a meta data znovu hash a porovnána s hodnotami uloženými v hlavní knize.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Předpoklady pro implementaci integračních vzorů pomocí rozhraní REST a zpráv API

Aby bylo možné zajistit, aby externí systém nebo zařízení mohl komunikovat s inteligentními kontrakty pomocí rozhraní REST API nebo služby Message API, musí dojít k následujícímu:

1. V Azure Active Directory pro konsorcium je vytvořen účet, který představuje externí systém nebo zařízení.
2. Jedna nebo víc odpovídajících inteligentních kontraktů pro vaši aplikaci Azure blockchain Workbench má funkce definované tak, aby přijímala události z externího systému nebo zařízení.
3. Konfigurační soubor aplikace pro Vaši čipovou smlouvu obsahuje roli, ke které je systém nebo zařízení přiřazeno.
4. Konfigurační soubor aplikace pro vaši inteligentní kontrakt identifikuje, v jakém stavu je tato funkce volána definovanou rolí.
5. Konfigurační soubor aplikace a jeho inteligentní kontrakty se nahrají do Azure blockchain Workbench.

Po nahrání aplikace se k kontraktu a přidružené roli přiřadí účet Azure Active Directory pro externí systém.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testování toků integrace externích systémů před zápisem kódu pro integraci 

Integrace s externími systémy je klíčovým požadavkem mnoha scénářů. Před vývojem kódu pro integraci s externími systémy je žádoucí, aby bylo možné ověřit návrh inteligentních kontraktů předem nebo paralelně.

Použití Azure Active Directory (Azure AD) může výrazně zrychlit produktivitu vývojářů a dobu jejich využití. Konkrétně integrace kódu s externím systémem může trvat netriviální množství času. Pomocí Azure AD a automatické generace uživatelského prostředí pomocí Azure blockchain Workbench můžete vývojářům dovolit, aby se přihlásili do aplikace blockchain Workbench jako externí systém a naplnili hodnoty z externího systému prostřednictvím uživatelského rozhraní. Před zápisem kódu Integration Code pro externí systémy můžete nápady rychle vyvíjet a ověřovat v prostředí pro kontrolu konceptu.
