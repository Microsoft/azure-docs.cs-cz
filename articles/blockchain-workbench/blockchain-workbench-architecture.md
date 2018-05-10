---
title: Azure Blockchain Workbench architektura
description: Přehled architektury Azure Blockchain Workbench a jeho komponenty.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a6c415123a8e1254e214af69711fc4143a929a58
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench architektura

Azure Blockchain Workbench usnadňuje vývoj aplikací blockchain tím, že poskytuje řešení pomocí Azure několik komponent. Blockchain Workbench se dá nasadit pomocí šablony řešení v Azure Marketplace. Šablona umožňuje uživatelům vybrat moduly a součásti pro nasazení s Blockchain Workbench, jako je například blockchain zásobníku, typ klientské aplikace a podporu integrace IoT. Po nasazení Blockchain Workbench poskytuje přístup k webové aplikace, aplikace pro iOS a Android aplikaci.

![Architektura Blockchain Workbench](media/blockchain-workbench-architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identita a ověřování

Pomocí Blockchain Workbench, konsorcium federaci identit jejich organizace pomocí služby Azure Active Directory (Azure AD). Workbench vygeneruje nové uživatelské účty pro identity ve řetězu s podnikové identity uložené ve službě Azure AD. Mapování identit usnadňuje ověřené přihlášení k rozhraní API klienta a aplikace a využívá zásady ověřování organizací. Workbench také umožňuje přidružit podnikové identity pro konkrétní role v rámci dané inteligentní kontrakt. Kromě toho Workbench také poskytuje mechanismus pro identifikaci akce těchto rolí můžete provést a co okamžiku.

Po nasazení Blockchain Workbench uživatelé komunikovat s Blockchain Workbench prostřednictvím klientské aplikace, klienta na základě REST API nebo zasílání zpráv na úrovni rozhraní API. Ve všech případech musí být ověřeny interakce prostřednictvím Azure Active Directory (Azure AD) nebo přihlašovací údaje pro konkrétní zařízení.

Uživatelé federaci jejich identit do služby Azure AD konsorcium zasláním e-mailové pozvání účastníkům v e-mailové adresy. Při přihlášení, tyto uživatele, se ověřují pomocí jméno, heslo a zásady. Například dvoufaktorové ověřování jejich organizace.

Azure AD se používá ke správě všichni uživatelé, kteří mají přístup k Blockchain Workbench. Každé zařízení, připojení k kontraktu inteligentní je taky přiřazený s Azure AD.

Také umožňuje přiřadit uživatele do skupiny speciální správce Azure AD. Uživatelé přidružených ke skupině správce získat správce práva nebo akce v rámci Blockchain Workbench, například nasazování kontrakty a udělení oprávnění pro uživatele pro přístup k kontraktu. Uživatelé mimo této skupině nebudou mít přístup k akce správce.

## <a name="client-applications"></a>Klientské aplikace

Workbench poskytuje aplikacím automaticky generovaného klienta pro webové a mobilní (iOS, Android), který slouží k ověření, testování a zobrazení blockchain aplikací. Aplikace rozhraní se dynamicky vygeneruje na základě metadat inteligentní kontraktu a zvládne žádné případ použití. Klientské aplikace poskytovat front-end uživatelsky orientovaný na dokončení blockchain aplikace generované Blockchain Workbench. Klientské aplikace ověřovat uživatele přes Azure Active Directory (Azure AD) a potom prezentovat činnost koncového uživatele přizpůsobit obchodní kontext inteligentní kontraktu. Činnost koncového uživatele umožňuje vytvoření nové instance inteligentní kontrakt podle autorizované jednotlivce a potom nabídne možnost provést některé typy transakcí v příslušné body v obchodní proces, který představuje inteligentní kontrakt.

Ve webové aplikaci můžete přístup oprávněným uživatelům konzole pro správu. Konzole je k dispozici pro uživatele do skupiny Administrator ve službě Azure AD a poskytuje přístup k následující funkce:

* Nasazení Microsoft inteligentní kontrakty k dispozici pro oblíbené scénáře. Například o scénář asset přenosu.
* Nahrání a nasazení vlastních inteligentní kontrakty.
* Přiřazení přístupu uživatelů k inteligentní smlouvě v rámci určité role.

## <a name="gateway-service-api"></a>Rozhraní API služby brány

Blockchain Workbench zahrnuje rozhraní API služby brány založené na REST. Při zápisu do blockchain, rozhraní API vygeneruje a přináší zprávy události zprostředkovatele. Vyžádání dat pomocí rozhraní API se k databázi SQL vypnout řetězu odesílat dotazy. Databáze SQL obsahuje repliku dat na řetězce a metadata, která poskytuje informace o podporovaných inteligentní kontrakty kontextu a konfiguraci. Dotazy vrátí požadovaná data z repliky vypnout řetězec ve formátu o tom informováni prostřednictvím metadata pro kontrakt.

Vývojáři mají přístup k rozhraní API služby brány sestavovat nebo integrovat řešení blockchain bez spoléhání na Blockchain Workbench klientské aplikace.

> [!NOTE]
> Povolit ověřený přístup k rozhraní API, jsou dva klientské aplikace registrované v Azure Active Directory. Azure Active Directory vyžaduje odlišné aplikace registrace jednotlivých typů aplikací (nativní a webové). 

## <a name="message-broker-for-incoming-messages"></a>Zprostředkovatele zpráv pro příchozí zprávy

Vývojáři, kteří chtějí k odesílání zpráv přímo do Blockchain Workbench mohou zasílat zprávy přímo do služby Service Bus. Například zprávy rozhraní API může pro integraci na systému nebo zařízení IoT.

## <a name="message-broker-for-downstream-consumers"></a>Zprostředkovatele zpráv pro příjem dat příjemci

Během životního cyklu aplikace dojde k události. Události mohou být aktivovány rozhraním API brány nebo na hlavní knihy. Oznamování událostí můžete zahájit podřízené kódu založené na události.

Blockchain Workbench automaticky nasadí dva typy příjemci událostí. Jeden je aktivovaného událostmi blockchain k naplnění úložiště vypnout řetězec SQL. Druhá je zachytit metadata pro události vygenerované pomocí rozhraní API související s nahrávání a ukládání dokumentů.

## <a name="message-consumers"></a>Příjemce zprávu

 Příjemce zprávu trvat zpráv ze služby Service Bus. Základní model eventing u spotřebitelé zpráv umožňuje rozšíření další služby a systémů. Můžete například přidat podpora naplnit CosmosDB nebo vyhodnotit zprávy pomocí Azure streamování Analytics. Následující části popisují spotřebitelé zpráv součástí Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Distribuované hlavní knihy příjemce

Zprávy technologie (DLT) distribuované hlavní knihy obsahovat metadata pro transakce k zápisu do blockchain. Příjemce načte zprávy a nabízených oznámení data Tvůrce transakce, podepisující osoba a směrovače.

### <a name="database-consumer"></a>Příjemce databáze

Příjemce databáze trvá zpráv ze služby Service Bus a nabízených oznámení data do databáze připojené, například databáze SQL.

### <a name="storage-consumer"></a>Příjemce úložiště

Příjemce úložiště trvá zpráv ze služby Service Bus a by vložil data do připojené úložiště. Například ukládání hash dokumentů ve službě Azure Storage.

## <a name="transaction-builder-and-signer"></a>Tvůrce transakce a podepisující osoba

Pokud zpráva na zprostředkovatele příchozích zpráv potřebuje k zápisu do blockchain, budou zpracovány DLT uživatelem. Příjemce DLT je služby, která načte zprávu obsahující metadata pro požadovanou transakci provést a poté odešle informace *Tvůrce transakce a podepisující osoba*. *Tvůrce transakce a podepisující osoba* sestaví blockchain transakce na základě dat a požadované blockchain cíl. Jakmile sestaví, je podepsaný transakce. Soukromé klíče jsou uložené v Azure Key Vault.

 Blockchain Workbench načte příslušný privátní klíč z Key Vault a podepisuje transakce mimo Key Vault. Po podepsání transakce posílá transakce směrovače a knih.

## <a name="transaction-routers-and-ledgers"></a>Transakce směrovače a hlavní knihy

Transakce směrovače a hlavní knihy trvat podepsaný transakce a směrovat je do příslušné blockchain. V současné době Blockchain Workbench podporuje Ethereum jako jeho blockchain cíl.

## <a name="dlt-watcher"></a>DLT sledovacích procesů

Sledovací proces distribuované hlavní knihy technologie (DLT) sleduje události, které se vyskytují v bloku řetězy připojené k Blockchain Workbench.
Události odráží informace, které jsou relevantní pro jednotlivce a systémy. Například vytvoření nové instance kontrakt, provádění transakcí a změny stavu. Události zachycení a odeslat zprostředkovatele odchozí zpráv, aby mohou být spotřebovávána podřízené příjemci.

Například příjemce SQL sleduje události, je využívá a naplní databázi SQL s zahrnuty hodnoty. Kopie umožňuje rekonstrukce repliky ve řetězu dat v úložišti vypnout řetězu.

## <a name="azure-sql-database"></a>Databáze SQL Azure

Databáze Azure SQL připojené k Blockchain Workbench ukládá definice kontraktu, konfigurace metadat a přístupných SQL repliku dat uložených v blockchain. Tato data lze snadno dotaz, vizualizována nebo analyzovat přímý přístup k databázi. Vývojáři a ostatních uživatelů můžete použít databázi pro vytváření sestav, analýzy nebo jiných datově zaměřená integrace. Například uživatelé můžete vizualizovat data transakcí pomocí Power BI.

Tento vypnout řetězec úložiště poskytuje možnost pro velké organizace k dotazování na data v SQL, ne v hlavní knihy blockchain. Taky protože tak můžete standardizovat na standardní schématu, která nerozlišuje blockchain technologie zásobníky, umožňuje vypnout řetězec úložiště opakovaného použití sestav a artefaktů napříč projekty, scénáře a organizace.

## <a name="azure-storage"></a>Azure Storage

Úložiště Azure slouží k uložení kontraktech a metadata spojená se smlouvami.

Dokumenty z nákupních objednávek a faktur doklad, k obrázků použitých v příspěvky a lékařské dokumentů na video pocházející z poměrně, včetně policie kamery textu a obrázků hlavní pohybu, hrají roli v mnoha scénářích zaměřené na blockchain. Dokumenty nejsou vhodné umístit přímo na blockchain.

Blockchain Workbench podporuje možnost přidávat dokumenty nebo další obsah média s blockchain obchodní logiku. Hodnota hash obsahu dokumentu nebo médium je uložen v blockchain a skutečný dokumentu nebo mediálního obsahu je uložená ve službě Azure Storage. Informace o přidružené transakce je doručen do zprostředkovatele příchozích zpráv, zabalené, podepsané a směruje na blockchain. Tento proces se aktivuje události, které jsou sdíleny prostřednictvím zprostředkovatele odchozí zpráv. Databáze SQL využívá tyto informace a odešle ji do databáze pro dotazování později. Podřízené systémy může také využívat tyto události tak, aby fungoval podle potřeby.

## <a name="monitoring"></a>Monitorování

Workbench poskytuje protokolování aplikací pomocí Application Insights a monitorování Azure. Application Insights se používá k ukládání všechny protokolované informace ze Blockchain Workbench a obsahuje chyby, upozornění a úspěšné operace. Application Insights můžete použít vývojáři k ladění problémů s Blockchain Workbench. 

Azure monitorování obsahuje informace o stavu blockchain sítě. 

## <a name="next-steps"></a>Další postup

[Nasazení Azure Blockchain Workbench](blockchain-workbench-deploy.md)