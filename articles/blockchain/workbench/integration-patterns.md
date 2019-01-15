---
title: Inteligentní smlouvy vzorů integrace v Azure Blockchain Workbench
description: Přehled inteligentní smlouvy vzorů integrace v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e1bc16d32167d62d5f66f64bb383fcceeb79eb5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267143"
---
# <a name="smart-contract-integration-patterns"></a>Vzory integrace inteligentní kontraktu

Chytrých kontraktů se často představují pracovní postup společnosti, kterou je potřeba integrovat s externími systémy a zařízení.

Požadavky na tyto pracovní postupy zahrnují věcí a potřebovali si zahájení transakce na distribuované účetní knihy, které obsahují data z externího systému, služby nebo zařízení. Také musí být externím systémům reagovat na události pocházející z kontraktů uzpůsobených na distribuované účetní knihy.

Rozhraní REST API a integrace zasílání zpráv poskytují možnost Odeslat transakce z externích systémů chytrých kontraktů zahrnuté v aplikaci Azure Blockchain Workbench, jak odeslat oznámení události k externím systémům na základě změn, které trvat umístit v rámci aplikace.

Azure Blockchain Workbench pro scénáře integrace dat, obsahuje sadu zobrazení databáze, které sloučit kombinaci transakční data z blockchain a metadata o aplikacích a chytrých kontraktů.

Kromě toho některé scénáře, jako je ta, která souvisí slouží k poskytování řetězce nebo médií, může také vyžadují integraci dokumentů. Zatímco Azure Blockchain Workbench neposkytuje, že volání rozhraní API pro zpracování dokumentů přímo, dokumenty lze začlenit do aplikace Azure Blockchain. Tato část také obsahuje tento vzor.

Tato část obsahuje tyto vzory se dají identifikovat pro každý z těchto typů integrace ve vašich řešeních pro komplexní implementace.

## <a name="rest-api-based-integration"></a>Integrace založené na rozhraní API REST

Možnosti ve webové aplikaci Azure Blockchain Workbench umožňuje vygenerovat jsou zveřejňovány prostřednictvím rozhraní REST API. Možnosti patří Azure Blockchain Workbench umožňuje odeslání, konfigurace a správy aplikací, Odeslat transakce na distribuované účetní knihy a dotazování dat aplikací metadata a hlavní knihy.

Rozhraní REST API se používá především pro interaktivní klientů, jako je web, mobilní zařízení a aplikace robota.

Tato část zjistí zaměřených na aspekty v rozhraní REST API, které odesílají transakce na distribuované účetní knihy a ty, které dotazy na data o transakcích z Azure Blockchain Workbench *vypnuto řetězu* SQL database.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Odeslání transakce na distribuované účetní knihy z externího systému

Rozhraní REST API Azure Blockchain Workbench umožňuje odeslat ověřených požadavků k provedení transakcí na distribuované účetní knihy.

![Odesílání transakce na distribuované účetní knihy](./media/integration-patterns/send-transactions-ledger.png)

Tato situace nastane pomocí procesu uvedené výše, kde:

-   Ověří externí aplikace do Azure Active Directory, které jsou zřízené jako součást nasazení Azure Blockchain Workbench.
-   Autorizovaní uživatelé obdrží token nosiče, který je možné odeslat s požadavky na rozhraní API.
-   Externí aplikace provést volání do rozhraní REST API využitím nosný token.
-   Rozhraní REST API balíčky žádosti jako zpráva a odesílá je do služby Service Bus. Tady bude načten, podepsané a odesílat odpovídající distribuované účetní knihy.
-   Rozhraní REST API odešle požadavek do databáze SQL Azure Blockchain Workbench zaznamenávat žádosti a navázání aktuální stav zřizování.
-   SQL DB vrátí stav zřizování a volání API vrátí ID pro externí aplikaci, která ji zavolala.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Dotazování metadat Blockchain Workbench a transakce distribuované účetní knihy

Rozhraní REST API Azure Blockchain Workbench umožňuje poslat dotaz podrobnosti související s provedením inteligentní smlouvy na distribuované účetní knihy ověřených požadavků.

![Dotazování metadat](./media/integration-patterns/querying-metadata.png)

Tato situace nastane pomocí procesu uvedené výše, kde:

1. Ověří externí aplikace do Azure Active Directory, které jsou zřízené jako součást nasazení Azure Blockchain Workbench.
2. Autorizovaní uživatelé obdrží token nosiče, který je možné odeslat s požadavky na rozhraní API.
3. Externí aplikace provést volání do rozhraní REST API využitím nosný token.
4. Rozhraní REST API zadávání dotazů na data žádosti z databáze SQL a vrátí ji do klienta.

## <a name="messaging-integration"></a>Integrace zasílání zpráv

Zasílání zpráv integrace usnadňuje interakci s systémů, služeb a zařízení, ve kterém interaktivního přihlášení není možná nebo žádoucí. Integrace zasílání zpráv se zaměřuje na dva typy zpráv, ty, které požadují, že transakce na distribuované účetní knihy a události, které jsou vystavené této knihy, když transakce neproběhly provést.

Integrace zasílání zpráv se zaměřuje na spuštění a monitorování transakcí týkajících se vytvoření uživatele, vytváření a provádění transakcí v kontraktech a je převážně používána *bezobslužného* back endových systémů.

Tato část zjistí zaměřených na aspekty rozhraní API založená na zprávách, které odesílají transakce na distribuované účetní knihy a ty, které představují zprávy vystavené základní distribuované účetní knihy.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Doručování jednosměrné událostí z inteligentního smlouvy k příjemci události 

V tomto scénáři dojde k události v rámci inteligentní smlouvy, například změny stavu nebo spuštění konkrétního typu transakce. Tato událost je vysílán prostřednictvím Event Grid pro příjem dat uživatele a tito příjemci poté přijmout vhodná opatření.

Příkladem tohoto scénáře je, že pokud dojde k transakci, příjemce by dostat oznámení a může provést akci, jako je například nahrávání informací v databázi SQL nebo Common Data Service. Toto je stejný vzor, který následuje Workbench k naplnění jeho *vypnuto řetězu* SQL DB.

Jiné by, pokud kontrakt inteligentní přejde do určitého stavu, například když začnou kontrakt *OutOfCompliance*. Pokud tuto změnu stavu dojde, může aktivovat upozornění k odeslání do Správce mobilního telefonu.

![Doručování jednosměrné událostí](./media/integration-patterns/one-way-event-delivery.png)

Tato situace nastane pomocí procesu uvedené výše, kde:

-   Inteligentní kontrakt přejde do nového stavu a odesílá události do hlavní knihy.
-   Hlavní knihy přijímá a doručí události do Azure Blockchain Workbench.
-   Azure Blockchain Workbench je přihlášen k události z hlavní knihy a přijímá události.
-   Azure Blockchain Workbench publikuje události odběratelům na služby Event Grid.
-   Externí systémy přihlášeni do služby Event Grid, využívat zprávy a provést příslušné akce.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednosměrná doručování událostí zprávy z externího systému do inteligentní kontraktu

Je také scénář, který toky z opačným směrem. V takovém případě je generována událost senzor nebo externího systému a dat z této události odesílaných do inteligentní kontraktu.

Běžným příkladem je doručování dat z finanční trhy, například ceny komodit, akcie nebo papírů, inteligentní kontraktu.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Azure Blockchain Workbench v očekávaném formátu

Některé aplikace jsou sestaveny pro integraci s Azure Blockchain Workbench a přímo generuje a odesílání zpráv v očekávané formáty.

![Přímé doručování](./media/integration-patterns/direct-delivery.png)

Tato situace nastane pomocí procesu uvedené výše, kde:

-   V externím systému, která aktivuje vytváření zprávy pro Azure Blockchain Workbench dojde k události.
-   Externí systém kódu napsaného pro vytvoření této zprávy ve formátu známé a odešle tato přímo ke službě Service Bus.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání na účetní knize, odesílání dat z externího systému do konkrétní kontraktu.
-   Po obdržení zprávy kontrakt přejde do nového stavu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručování zpráv ve formátu Neznámý Azure Blockchain Workbench

Některé systémy, nelze jej změnit pro doručování zpráv ve standardních formátů používaných sadou Azure Blockchain Workbench. V těchto případech mechanismy a zprávy můžete často použít formáty z těchto systémů. Konkrétně typy nativní zpráv z těchto systémů je možné transformovat pomocí Logic Apps, Azure Functions nebo jiný vlastní kód pro mapování na jeden ze standardních formátech očekávání pro zasílání zpráv.

![Neznámá zpráva formátu](./media/integration-patterns/unknown-message-format.png)

Tato situace nastane pomocí procesu uvedené výše, kde:

-   Dojde k události v externím systému, který se aktivuje vytvořením zprávy.
-   Aplikaci logiky nebo vlastní kód se používá pro příjem této zprávy a transformovat ho do standardní Azure Blockchain Workbench formátovaných zpráv.
-   Aplikace logiky odešle transformovanou zprávu přímo ke službě Service Bus.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání na účetní knize, odesílání dat z externího systému do konkrétní funkce kontraktu.
-   Funkce spustí a obvykle změní stav. Změna stavu pokračuje pracovní postup společnosti projeví v inteligentní kontraktu povolení dalších funkcí teď provádět podle potřeby.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Přechod od ovládacího prvku k externímu zpracování a operátoru await dokončení

Existují scénáře, ve kterém musí kontrakt inteligentní zastavit vnitřní spuštění a předání do externího procesu. Že by pak dokončete externího procesu, pošlete e-mail na inteligentní smlouvy a provedení by pokračovat v rámci inteligentní smlouvy.

#### <a name="transition-to-the-external-process"></a>Přechod do externího procesu

Tento model se zpravidla implementuje používá následující postup:

-   Inteligentní smlouvy přechody do konkrétního stavu. V tomto stavu, buď žádný nebo omezený počet funkcí, které lze spustit, dokud nedojde k externím systémem požadovanou akci.
-   Změna stavu je prezentované jako událost pro příjem dat příjemce.
-   Příjem dat příjemce obdrží událost a aktivuje spuštění externího kódu.

![Ovládací prvek přechod do externího procesu](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Návrat ovládacího prvku z inteligentního kontraktu

V závislosti na možnost přizpůsobit si externího systému může nebo nemusí být schopen doručování zpráv v jednom ze standardní formáty, které se očekává, že Azure Blockchain Workbench. Založené na externích systémů možnost Generovat jeden z následujících zpráv určí, které následující dvě návratový cesty se provedou.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Přímé doručování Azure Blockchain Workbench v očekávaném formátu

![](./media/integration-patterns/direct-delivery.png)

V tomto modelu probíhá komunikace smlouvy a změnu stavu následující po výše uvedeného procesu where -

-   Při dosažení dokončení nebo konkrétní verze při provádění externí kód, odesílat události služby Service Bus, připojení k Azure Blockchain Workbench.

-   Pro systémy, které nelze přizpůsobit přímo k zápisu zprávy, který odpovídá očekávání rozhraní API se bude transformovat.

-   Obsah zprávy se zabalí a odešle specifické funkce Inteligentní kontraktu. To se provádí jménem uživatele přidruženého k externím systému.

-   Funkce spustí a obvykle slouží k úpravě stavu. Změna stavu pokračuje pracovní postup společnosti projeví v inteligentní kontraktu povolení dalších funkcí teď provádět podle potřeby.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Doručování zpráv ve formátu Neznámý Azure Blockchain Workbench

![Neznámá zpráva formátu](./media/integration-patterns/unknown-message-format.png)

V tomto modelu, kde zprávu ve standardním formátu nelze odeslat přímo, komunikace s kontrakt a po výše uvedené dojde ke změně stavu následné zpracování where:

1.  Při dosažení dokončení nebo konkrétní verze při provádění externí kód, odesílat události služby Service Bus, připojení k Azure Blockchain Workbench.
2.  Aplikaci logiky nebo vlastní kód se používá pro příjem této zprávy a transformovat ho do standardní Azure Blockchain Workbench formátovaných zpráv.
3.  Aplikace logiky odešle transformovanou zprávu přímo ke službě Service Bus.
4.  Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
5.  Azure Blockchain Workbench zahájí volání na účetní knize, odesílání dat z externího systému do konkrétní kontraktu.
6. Obsah zprávy se zabalí a odešle specifické funkce Inteligentní kontraktu. To se provádí jménem uživatele přidruženého k externím systému.
7.  Funkce spustí a obvykle slouží k úpravě stavu. Změna stavu pokračuje pracovní postup společnosti projeví v inteligentní kontraktu povolení dalších funkcí teď provádět podle potřeby.

## <a name="iot-integration"></a>Integrace IoT

Běžné scénáře integrace je zahrnutí telemetrická data načtená ze senzorů v inteligentní kontraktu. Na základě dat od snímačů, může chytrých kontraktů informovaně akcí a mění stav kontraktu.

Například pokud truck doručování lékařství jeho teploty soar 110 stupňů, ho může mít vliv efektivitu léku a může způsobit problém bezpečnost veřejnosti není-li zjištěna a odebírat dodavatelský řetězec. Pokud ovladač accelerated jeho automobilu na 100 mil za hodinu, výsledné informace senzor způsobovat zrušení pojištění jeho pojištění zprostředkovatelem. Pokud auta automobilu pronájmů, GPS data může znamenat když ovladač mimo daným územím předmětem souhlas pronájmů a účtovat snížení.

Výzvou je, že tyto senzory můžete poskytování dat na základě konstantní a není vhodné pro všechna tato data posílat inteligentní kontraktu. Typickým přístupem je omezit počet zpráv odeslaných do blockchainu při současném dodávání všechny zprávy do sekundárního úložiště. Například doručení zpráv obdržela pouze pevně zadaném intervalu, třeba jednou za hodinu a omezením hodnota spadá mimo dohodnuté na rozsah pro inteligentní kontrakt. Kontrola hodnoty, které spadají mimo tolerance, zajistí, že k datům souvisejícím s kontrakty obchodní logiky přijme a spustit. Kontrola hodnotu v intervalu potvrdí, že senzor je stále hlásí. Všechna data se odesílají do sekundární vytváření sestav úložiště umožňující širší sestavy, analýzy a machine learningu. Například při získávání údajů snímačů přes pro GPS nemusí být požadovaný každou minutu pro inteligentní smlouvy, poskytuje zajímavá data, který se má použít v sestavách nebo mapování tras.

Na platformě Azure integrace s zařízení obvykle provádí pomocí služby IoT Hub. IoT Hub umožňuje směrovat zprávy na základě obsahu a umožňuje, aby typ funkce je popsáno výše.

![Zprávy IoT](./media/integration-patterns/iot.png)

Výše uvedený postup znázorňuje vzor pro tato možnost je implementovaná:

-   Zařízení s komunikuje přímo nebo přes bránu pole do služby IoT Hub.
-   IoT Hub přijme zprávy a vyhodnocuje zprávy proti trasy navázat, prohlédněte si obsah zprávy, třeba. *Zjistí senzoru teploty vyšší než 50 stupňů?*
-   IoT Hub odesílá zprávy, které splňují kritéria definovaná Service Bus pro trasu.
-   Aplikace logiky nebo jiný kód naslouchá na Service Bus, která se má vytvořit služby IoT Hub pro trasu.
-   Aplikace logiky nebo jiný kód načte a transformujte zprávy na známý formát.
-   Transformovanou zprávu, nyní ve standardním formátu, je odeslána ke službě Service Bus pro Azure Blockchain Workbench.
-   Azure Blockchain Workbench je přihlášen k události ze služby Service Bus a načítá zprávy.
-   Azure Blockchain Workbench zahájí volání na účetní knize, odesílání dat z externího systému do konkrétní kontraktu.
-   Po obdržení zprávy, kontrakt vyhodnotí data a může změnit stav podle výsledku vyhodnocení, například pro nejvyšší teplota, změňte stav, který má *dodržování předpisů z*.

## <a name="data-integration"></a>Integrace dat

Kromě REST a rozhraní API založená na zprávách Azure Blockchain Workbench umožňuje také poskytuje přístup k SQL DB vyplní meta-data aplikací a smlouvu, jakož i transakční data z distribuované účetní knihy.

![Integrace dat](./media/integration-patterns/data-integration.png)

Integrace dat je dobře známé:

-   Azure Blockchain Workbench ukládá metadata o aplikacích, pracovních postupů, kontrakty a transakce jako součást své normální provozní chování.
-   Externí systémy nebo nástroje zadejte jeden nebo více dialogů usnadnit shromažďování informací o databázi, jako je například název databázového serveru, název databáze, typ ověřování, přihlašovací údaje a může si zobrazit databázi, kterou chcete-li využívají.
-   Dotazy se zapisují na zobrazení databáze SQL pro usnadnění příjem dat využití pomocí externích systémů, služeb, vytváření sestav, vývojářské nástroje a nástroje pro produktivitu organizace.

## <a name="storage-integration"></a>Integrace úložiště

Mnoho scénářů mohou vyžadovat potřeba začlenit attestable soubory. Z několika důvodů bude vhodné blokovat soubory z blockchain. Běžným přístupem je místo toho k provedení kryptografické hodnoty hash (například SHA-256) pro soubor a sdílet tuto hodnotu hash na distribuované účetní knihy. Provádění-the-hash kdykoli znovu budoucí by měl vrátit stejný výsledek. Pokud se změní soubor, i v případě, že v obrázku se upraví právě jeden pixel,-the-hash vrátí jinou hodnotu.

![Integrace úložiště](./media/integration-patterns/storage-integration.png)

Je možné implementovat vzor kde:

-   Externí systém uchovává soubor v mechanismus pro ukládání, jako je Azure Storage.
-   Hodnota hash generuje s použitím souboru nebo souboru a související metadata například identifikátor pro vlastníka, adresu URL, kam se soubor nachází, atd.
-   -The-hash a veškerá metadata posílá funkce na inteligentní smlouvy, jako například *FileAdded*
-   V budoucích verzích souborů a metadat může být mají hodnotu hash znovu a porovná hodnot uložených na hlavní knihy.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Požadavky pro implementaci vzorců integrace pomocí REST a zpráva rozhraní API

Pro usnadnění schopnosti pro externí systém nebo zařízení komunikovat se inteligentní kontraktu pomocí REST nebo zpráva rozhraní API, potřeba splnit následující podmínky-

1. Ve službě Azure Active Directory pro konsorcia se vytvoří účet, který představuje externí systém nebo zařízení.
2. Odpovídající inteligentní kontrakty pro vaši aplikaci Azure Blockchain Workbench umožňuje mít funkce definovat pro přijímání událostí z externího systému nebo zařízení.
3. Konfigurační soubor aplikace pro inteligentní smlouvu obsahuje roli, která bude systém nebo zařízení přiřadit.
4. Konfigurační soubor aplikace pro inteligentní kontrakt identifikuje v kterém s oznámením, že tato funkce je možné vyvolat v definované role.
5. Azure Blockchain Workbench se nahraje do konfiguračního souboru aplikace a její chytrých kontraktů.

Po nahrání aplikace se účet služby Azure Active Directory pro externí systém přiřadí kontrakt a přidružených rolí.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testování toků integrace externí systém před psaním kódu integrace 

Možnost integrovat s externími systémy, že je klíčovým požadavkem mnoha scénářů. Je třeba být schopný ověřit návrh inteligentní smlouvy předchozí nebo paralelní vývoj kódu pro integraci s externími systémy.

Použití služby Azure Active Directory (Azure AD) výrazně urychlit produktivitu vývojářů a času na hodnotu. Konkrétně integrace kódu s externím systémem může trvat netriviální množství času. Pomocí služby Azure AD a automatické – generování uživatelského rozhraní aplikace Azure Blockchain Workbench, to umožňuje vývojářům přihlášení do aplikace Workbench jako externího systému a vyplněné hodnoty očekává z tohoto externího systému prostřednictvím uživatelské prostředí To umožňuje rychle vyvíjet a ověření nápadů v testování konceptu prostředí buď před, nebo souběžně s integrační kód zapisovaný externím systémům.
