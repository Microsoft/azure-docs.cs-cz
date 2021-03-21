---
title: Architektura Azure Blockchain Workbench
description: Přehled architektury Azure blockchain Workbench Preview a jejích komponent.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021601"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura Azure Blockchain Workbench

Azure blockchain Workbench Preview zjednodušuje vývoj aplikací blockchain tím, že poskytuje řešení s využitím několika komponent Azure. Blockchain Workbench se dá nasadit pomocí šablony řešení v Azure Marketplace. Šablona umožňuje vybrat moduly a komponenty, které chcete nasadit, včetně blockchain zásobníku, typu klientské aplikace a podpory pro integraci IoT. Po nasazení poskytuje blockchain Workbench přístup k webové aplikaci, aplikaci pro iOS a aplikaci pro Android.

![Architektura blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identita a ověřování

Pomocí blockchain Workbench může konsorcium federovat své podnikové identity pomocí Azure Active Directory (Azure AD). Workbench generuje nové uživatelské účty pro identity v řetězcích s podnikovými identitami uloženými ve službě Azure AD. Mapování identit usnadňuje ověřené přihlášení k klientským rozhraním API a aplikacím a používá zásady ověřování organizací. Workbench také umožňuje přidružit podnikové identity k určitým rolím v rámci dané inteligentní smlouvy. Aplikace Workbench také poskytuje mechanismus pro identifikaci akcí, které mohou tyto role provádět a v jakém čase.

Po nasazení aplikace blockchain Workbench uživatelé komunikují s blockchain Workbench prostřednictvím klientských aplikací, rozhraní API klienta založeného na REST nebo rozhraní API pro zasílání zpráv. Ve všech případech musí být interakce ověřené buď prostřednictvím Azure Active Directory (Azure AD) nebo pomocí přihlašovacích údajů specifických pro zařízení.

Uživatelé federovat své identity do konsorcia Azure AD odesláním e-mailových pozvánek účastníkům na jejich e-mailové adrese. Při přihlášení se tito uživatelé ověřují pomocí jména, hesla a zásad. Například dvojúrovňové ověřování organizace.

Služba Azure AD se používá ke správě všech uživatelů, kteří mají přístup k blockchain Workbench. Všechna zařízení, která se připojují k vaší inteligentní smlouvě, jsou také přidružená k Azure AD.

Služba Azure AD se používá také k přiřazení uživatelů ke speciální skupině správců. Uživatelům přidruženým ke skupině správců se uděluje přístup k právům a akcím v rámci blockchain Workbench, včetně nasazení smluv a udělení oprávnění uživateli k přístupu ke smlouvě. Uživatelé mimo tuto skupinu nemají přístup k akcím správce.

## <a name="client-applications"></a>Klientské aplikace

Workbench poskytuje automaticky generované klientské aplikace pro web a mobilní zařízení (iOS, Android), které se dají použít k ověřování, testování a zobrazování aplikací blockchain. Aplikační rozhraní se dynamicky generuje na základě metadat inteligentních kontraktů a může podkládat případ použití. Klientské aplikace dodávají front-end pro uživatele k kompletním aplikacím blockchain vygenerovaným aplikací blockchain Workbench. Klientské aplikace ověřují uživatele prostřednictvím služby Azure Active Directory (Azure AD) a pak prezentují uživatelské prostředí, které je přizpůsobeno obchodnímu kontextu inteligentních kontraktů. Činnost koncového uživatele umožňuje vytvořit nové instance inteligentních kontraktů autorizovanými osobami a pak zaznamená možnost spouštět určité typy transakcí v příslušných bodech v obchodním procesu, který představuje inteligentní smlouva.

Ve webové aplikaci mají oprávnění uživatelé přístup ke konzole správce. Konzola je k dispozici uživatelům ve skupině správce ve službě Azure AD a poskytuje přístup k následujícím funkcím:

* V případě oblíbených scénářů nasaďte společnosti Microsoft poskytnuté inteligentní smlouvy. Například scénář přenosu assetů.
* Nahrajte a nasaďte vlastní inteligentní kontrakty.
* Přiřaďte uživateli přístup k této inteligentní smlouvě v kontextu konkrétní role.

Další informace najdete v tématu [ukázkové klientské aplikace Azure blockchain Workbench na GitHubu](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Rozhraní API služby brány

Blockchain Workbench obsahuje rozhraní API služby brány založené na REST. Při zápisu do blockchain rozhraní API generuje a doručuje zprávy zprostředkovateli událostí. Když rozhraní API požaduje data, odesílají se dotazy do databáze z nepřipojeného řetězce. Databáze obsahuje repliku dat a metadat v řetězcích, které poskytují informace o kontextu a konfiguraci pro podporované inteligentní kontrakty. Dotazy vrátí požadovaná data z repliky z nepoužívaného řetězce ve formátu informující o metadatech pro kontrakt.

Vývojáři mají přístup k rozhraní API služby brány, aby mohli vytvářet nebo integrovat řešení blockchain, aniž by se museli spoléhat na klientské aplikace blockchain Workbench.

> [!NOTE]
> K povolení ověřeného přístupu k rozhraní API se v Azure Active Directory zaregistrují dvě klientské aplikace. Azure Active Directory vyžaduje odlišnou registraci aplikací pro každý typ aplikace (nativní a webový).

## <a name="message-broker-for-incoming-messages"></a>Zprostředkovatel zpráv pro příchozí zprávy

Vývojáři, kteří chtějí odesílat zprávy přímo do aplikace blockchain Workbench, mohou odesílat zprávy přímo do Service Bus. Například můžete použít rozhraní API zprávy pro integraci systému do systému nebo zařízení IoT.

## <a name="message-broker-for-downstream-consumers"></a>Zprostředkovatel zpráv pro příjemce pro příjem dat

Během životního cyklu aplikace dojde k události. Události může aktivovat rozhraní API brány nebo v hlavní knize. Oznámení událostí mohou spustit podřízený kód na základě události.

Blockchain Workbench automaticky nasadí dva typy uživatelů událostí. Jeden příjemce se spustí blockchain událostmi k naplnění úložiště SQL pro off-Chain. Druhým příjemcem je zaznamenat metadata pro události generované rozhraním API, které souvisí s nahráváním a ukládáním dokumentů.

## <a name="message-consumers"></a>Příjemci zpráv

 Příjemci zpráv přebírají zprávy od Service Bus. Základní model událostí pro příjemce zpráv umožňuje rozšíření dalších služeb a systémů. Můžete například přidat podporu k naplnění CosmosDB nebo vyhodnocení zpráv pomocí Azure Stream Analytics. V následujících částech jsou popsány příjemce zpráv zahrnuté v blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Příjemce distribuované hlavní knihy

Zprávy o službě distribuované hlavní knihy (DLT) obsahují metadata pro transakce, které mají být zapsány do blockchain. Příjemce načte zprávy a vloží data do Tvůrce transakcí, podepisujícího a směrovače.

### <a name="database-consumer"></a>Příjemce databáze

Příjemce databáze přebírá zprávy od Service Bus a vkládá data do připojené databáze, jako je například databáze v Azure SQL Database.

### <a name="storage-consumer"></a>Příjemce úložiště

Uživatel úložiště přebírá zprávy od Service Bus a vkládá data do připojeného úložiště. Například ukládání dokumentů s algoritmem hash do Azure Storage.

## <a name="transaction-builder-and-signer"></a>Tvůrce a podepisující transakce

Pokud je nutné zapsat zprávu v zprostředkovateli příchozích zpráv do blockchain, bude zpracována příjemcem služby DLT. Příjemce DLT je služba, která načte zprávu obsahující metadata pro požadovanou transakci, která se má provést, a pak tyto informace odešle do *Tvůrce a podpisu transakce*. *Tvůrce transakce a podepisující osoba* sestaví transakci blockchain na základě dat a požadovaného cíle blockchain. Po sestavení je transakce podepsána. Privátní klíče jsou uloženy v Azure Key Vault.

 Blockchain Workbench načte příslušný privátní klíč z Key Vault a podepíše transakci mimo Key Vault. Po podepsání se transakce posílá do transakčních směrovačů a do knih.

## <a name="transaction-routers-and-ledgers"></a>Směrovače transakcí a hlavní knihy

Směrovače a účetní deníky transakcí berou podepsané transakce a směrují je na příslušné blockchain. V současné době blockchain Workbench podporuje Ethereem jako cílovou blockchain.

## <a name="dlt-watcher"></a>Sledovací proces DLT

Sledovací proces distribuované hlavní knihy (DLT) monitoruje události, ke kterým dochází v řetězcích bloků připojených k blockchain Workbench.
Události odrážejí informace, které jsou relevantní pro jednotlivce a systémy. Například vytváření nových instancí kontraktů, provádění transakcí a změny stavu. Události jsou zachyceny a odesílány do zprostředkovatele odchozích zpráv, aby je mohli využívat i příjemci s dalšími uživateli.

Například příjemce SQL monitoruje události, spotřebovává je a naplní databázi pomocí zahrnutých hodnot. Kopírování umožňuje rekreaci v úložišti s neplatnými daty v neznámém řetězci.

## <a name="azure-sql-database"></a>Azure SQL Database

Databáze připojená k blockchain Workbench ukládá definice kontraktů, metadata konfigurace a repliky přístupné pro SQL data uložená v blockchain. Tato data lze snadno dotazovat, vizuálně nebo analyzovat přímým přístupem k databázi. Vývojáři a jiní uživatelé mohou používat databázi pro vytváření sestav, analýzy nebo jiné integrace zaměřené na data. Například uživatelé mohou vizualizovat data transakcí pomocí Power BI.

Toto úložiště s neplatnými prostředky umožňuje podnikovým organizacím dotazovat se na data v SQL, a ne na blockchain hlavní knihy. Kromě toho, díky standardizaci na standardním schématu, které je nezávislá z blockchainch technologických zásobníků, umožňuje úložiště s neplatnými možnostmi opětovného použití sestav a dalších artefaktů v projektech, scénářích a organizacích.

## <a name="azure-storage"></a>Azure Storage

Azure Storage slouží k uložení smluv a metadat přidružených ke smlouvám.

Z nákupních objednávek a dodacích dokladů, do imagí používaných v novinkách a lékařských obrazech, na video pocházející ze Continuum, včetně nejdůležitějších a podstatných snímků, dokumenty hrají roli v mnoha scénářích orientovaných na blockchain. Dokumenty není vhodné přímo umístit do blockchain.

Blockchain Workbench podporuje možnost přidávat dokumenty nebo další multimediální obsah pomocí obchodní logiky blockchain. Hodnota hash dokumentu nebo mediálního obsahu je uložená v blockchain a skutečný dokument nebo mediální obsah je uložený v Azure Storage. Související informace o transakci se doručí zprostředkovateli příchozích zpráv, zabalí se, podepíše a směruje do blockchain. Tento proces spustí události, které jsou sdíleny prostřednictvím zprostředkovatele odchozích zpráv. SQL DB tyto informace spotřebuje a pošle je do databáze pro pozdější dotazování. Navazující systémy můžou tyto události využít také k jednání podle potřeby.

## <a name="monitoring"></a>Monitorování

Workbench poskytuje protokolování aplikace pomocí Application Insights a Azure Monitor. Application Insights slouží k ukládání všech protokolovaných informací z aplikace blockchain Workbench a obsahuje chyby, varování a úspěšné operace. Application Insights můžou vývojáři použít k ladění problémů s blockchain Workbench. 

Azure Monitor poskytuje informace o stavu sítě blockchain. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení služby Azure Blockchain Workbench](./deploy.md)
