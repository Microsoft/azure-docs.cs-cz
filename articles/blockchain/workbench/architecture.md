---
title: Architektura Azure Blockchain Workbench
description: Přehled architektury Azure Blockchain Workbench Preview a jejích komponent.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324900"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura Azure Blockchain Workbench

Azure Blockchain Workbench Preview zjednodušuje vývoj blockchainových aplikací tím, že poskytuje řešení pomocí několika komponent Azure. Blockchain Workbench se dá nasadit pomocí šablony řešení na Azure Marketplace. Šablona umožňuje vybrat moduly a komponenty pro nasazení, včetně zásobníku blockchainu, typu klientské aplikace a podpory integrace IoT. Po nasazení poskytuje Blockchain Workbench přístup k webové aplikaci, aplikaci pro iOS a aplikaci pro Android.

![Architektura Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identita a ověřování

Pomocí Blockchain Workbench, konsorcium můžete federate jejich podnikové identity pomocí Azure Active Directory (Azure AD). Workbench generuje nové uživatelské účty pro identity v řetězci s podnikovými identitami uloženými ve službě Azure AD. Mapování identit usnadňuje ověřené přihlášení do klientských api a aplikací a používá zásady ověřování organizací. Workbench také poskytuje možnost přidružit podnikové identity ke konkrétním rolím v rámci dané inteligentní smlouvy. Kromě toho Workbench také poskytuje mechanismus pro identifikaci akcí, které tyto role mohou provést a v jakou dobu.

Po nasazení Blockchain Workbench uživatelé interagují s Blockchain Workbench buď prostřednictvím klientských aplikací, rozhraní API pro klienty založené na REST nebo rozhraní API pro zasílání zpráv. Ve všech případech musí být interakce ověřeny, a to buď prostřednictvím služby Azure Active Directory (Azure AD) nebo pověření specifická pro zařízení.

Uživatelé federate své identity konsorciu Azure AD odesláním e-mailové pozvánky účastníkům na jejich e-mailovou adresu. Při přihlášení jsou tito uživatelé ověřováni pomocí jména, hesla a zásad. Například dvoufaktorové ověřování jejich organizace.

Azure AD se používá ke správě všech uživatelů, kteří mají přístup k Blockchain Workbench. Každé zařízení, které se připojuje k inteligentní smlouvě, je také přidruženo k Azure AD.

Azure AD se také používá k přiřazení uživatelů ke zvláštní skupině správců. Uživatelům přidruženým ke skupině správců je udělen přístup k právům a akcím v rámci blockchainworkbench, včetně nasazení smluv a udělení oprávnění uživateli k přístupu ke smlouvě. Uživatelé mimo tuto skupinu nemají přístup k akcím správce.

## <a name="client-applications"></a>Klientské aplikace

Workbench poskytuje automaticky generované klientské aplikace pro webové a mobilní (iOS, Android), které lze použít k ověření, testování a zobrazení blockchainových aplikací. Rozhraní aplikace je dynamicky generováno na základě metadat inteligentní smlouvy a může pojmout jakýkoli případ použití. Klientské aplikace dodávají uživatelsky orientovaný front-end kompletním blockchainovým aplikacím generovaným Blockchain Workbench. Klientské aplikace ověřují uživatele prostřednictvím služby Azure Active Directory (Azure AD) a pak představují uživatelské prostředí přizpůsobené obchodnímu kontextu inteligentní smlouvy. Uživatelské prostředí umožňuje vytváření nových inteligentních instancí smlouvy oprávněnými osobami a pak představuje možnost provádět určité typy transakcí ve vhodných bodech obchodního procesu, který inteligentní smlouva představuje.

Ve webové aplikaci mají oprávnění uživatelé přístup ke konzole administrator Console. Konzola je k dispozici uživatelům ve skupině Administrator ve službě Azure AD a poskytuje přístup k následujícím funkcím:

* Nasazení Microsoft u poskytovalinteligentní kontrakty pro oblíbené scénáře. Například scénář převodu majetku.
* Nahrajte a nasaďte své vlastní inteligentní kontrakty.
* Přiřaďte uživateli přístup k inteligentní mu smlouvě v kontextu konkrétní role.

Další informace najdete v [tématu Azure Blockchain Workbench ukázkové klientské aplikace na GitHubu](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Rozhraní API služby brány

Blockchain Workbench obsahuje rozhraní API služby brány založené na REST. Při zápisu do blockchainu rozhraní API generuje a doručuje zprávy zprostředkovateli událostí. Pokud je data požadována rozhraní MA), dotazy jsou odesílány do databáze SQL mimo řetězec. Databáze SQL obsahuje repliku dat v řetězci a metadat, která poskytuje kontext ové a konfigurační informace pro podporované inteligentní kontrakty. Dotazy vrátí požadovaná data z repliky mimo řetězec ve formátu informované metadata pro smlouvu.

Vývojáři mohou přistupovat k rozhraní API služby brány a vytvářet nebo integrovat blockchainová řešení, aniž by se spoléhali na klientské aplikace Blockchain Workbench.

> [!NOTE]
> Chcete-li povolit ověřený přístup k rozhraní API, dvě klientské aplikace jsou registrovány ve službě Azure Active Directory. Azure Active Directory vyžaduje odlišné registrace aplikací každý typ aplikace (nativní a webové). 

## <a name="message-broker-for-incoming-messages"></a>Zprostředkovatel zpráv pro příchozí zprávy

Vývojáři, kteří chtějí odesílat zprávy přímo do Blockchain Workbench, mohou odesílat zprávy přímo do service busu. Například rozhraní API zpráv lze použít pro integraci mezi systémem nebo zařízení IoT.

## <a name="message-broker-for-downstream-consumers"></a>Zprostředkovatel zpráv pro následné spotřebitele

Během životního cyklu aplikace dojde k událostem. Události mohou být spuštěny rozhraním API brány nebo v hlavní knize. Oznámení událostí můžete zahájit příjem kódu na základě události.

Blockchain Workbench automaticky nasazuje dva typy zákazníků událostí. Jeden spotřebitel je spuštěn událostmi blockchain k naplnění úložiště SQL mimo řetězec. Druhý příjemce je zachytit metadata pro události generované rozhraní API týkající se nahrávání a ukládání dokumentů.

## <a name="message-consumers"></a>Spotřebitelé zpráv

 Spotřebitelé zpráv přebírají zprávy ze služby Service Bus. Základní model událostí pro spotřebitele zpráv umožňuje rozšíření dalšíslužby a systémy. Můžete například přidat podporu k naplnění CosmosDB nebo vyhodnocení zpráv pomocí Azure Streaming Analytics. Následující části popisují zprávy, které jsou zahrnuty v blockchainworkbench.

### <a name="distributed-ledger-consumer"></a>Spotřebitel distribuované hlavní knihy

Zprávy technologie distribuované hlavní knihy (DLT) obsahují metadata pro transakce, které mají být zapsány do blockchainu. Příjemce načte zprávy a odešle data tvůrce transakcí, autora podpisu a směrovači.

### <a name="database-consumer"></a>Příjemce databáze

Příjemce databáze přebírá zprávy z service bus a odešle data do připojené databáze, jako je například databáze SQL.

### <a name="storage-consumer"></a>Spotřebitel úložiště

Příjemce úložiště přebírá zprávy z service bus a odesílá data do připojeného úložiště. Například ukládání zahashed dokumentů ve službě Azure Storage.

## <a name="transaction-builder-and-signer"></a>Tvůrce transakcí a autor podpisu

Pokud zpráva na zprostředkovatele příchozí zprávy musí být zapsána do blockchainu, bude zpracována příjemcem DLT. Příjemce DLT je služba, která načte zprávu obsahující metadata pro požadovanou transakci, která má být provedena, a poté odešle informace *tvůrce a podepisujícímu.* *Tvůrce transakcí a podepisující autor* sestavuje transakci blockchain na základě dat a požadovaného cíle blockchainu. Po sestavení je transakce podepsána. Privátní klíče jsou uloženy v trezoru klíčů Azure.

 Blockchain Workbench načte příslušný soukromý klíč z trezoru klíčů a podepíše transakci mimo Key Vault. Po podepsání je transakce odeslána do transakčních směrovačů a účetních knih.

## <a name="transaction-routers-and-ledgers"></a>Transakční směrovače a hlavní knihy

Transakční směrovače a účetní knihy berou podepsané transakce a směrují je na příslušný blockchain. V současné době Blockchain Workbench podporuje Ethereum jako svůj cílový blockchain.

## <a name="dlt-watcher"></a>Proces DLT

Sledovací modul technologie distribuované účetní knihy (DLT) monitoruje události, ke kterým dochází v řetězcích bloků připojených k blockchainworkbench.
Události odrážejí informace týkající se jednotlivců a systémů. Například vytváření nových instancí smlouvy, provádění transakcí a změny stavu. Události jsou zachyceny a odeslány zprostředkovateli odchozích zpráv, takže je mohou spotřebovávat příjemci pro příjem dat.

Například příjemce SQL monitoruje události, spotřebovává je a naplní databázi SQL s zahrnutými hodnotami. Kopie umožňuje obnovení repliky dat v řetězci v obchodě mimo řetězec.

## <a name="azure-sql-database"></a>Databáze Azure SQL

Databáze Azure SQL připojená k Blockchain Workbench ukládá definice smluv, metadata konfigurace a repliku dat uložených v blockchainu, která je přístupná SQL. Tato data lze snadno dotazovat, vizualizovat nebo analyzovat přímým přístupem k databázi. Vývojáři a další uživatelé mohou databázi používat pro vytváření sestav, analýzy nebo jiné integrace zaměřené na data. Uživatelé mohou například vizualizovat data transakcí pomocí Power BI.

Toto úložiště mimo řetězec poskytuje podnikovým organizacím možnost dotazovat data v SQL, nikoli v hlavní knize blockchainu. Také standardizací na standardní schéma, které je agnostik blockchain technologie zásobníky, off-chain úložiště umožňuje opakované použití sestav a dalších artefaktů napříč projekty, scénáře a organizace.

## <a name="azure-storage"></a>Azure Storage

Azure Storage se používá k ukládání smluv a metadat spojených se smlouvami.

Od nákupních objednávek a konosamentů, přes obrázky používané ve zprávách a lékařských snímcích až po video pocházející z kontinua, včetně policejních kamer a hlavních filmů, dokumenty hrají roli v mnoha scénářích zaměřených na blockchain. Dokumenty nejsou vhodné umístit přímo na blockchain.

Blockchain Workbench podporuje možnost přidávat dokumenty nebo jiný mediální obsah s blockchainovou obchodní logikou. Hash dokumentu nebo mediálního obsahu se ukládá v blockchainu a skutečný dokument nebo mediální obsah se ukládá ve službě Azure Storage. Přidružené informace o transakcích jsou doručovány zprostředkovateli příchozích zpráv, zabaleny, podepsány a směrovány do blockchainu. Tento proces aktivuje události, které jsou sdíleny prostřednictvím zprostředkovatele odchozích zpráv. SQL DB spotřebovává tyto informace a odešle je do DB pro pozdější dotazování. Navazující systémy by také mohly tyto události využívat, aby mohly fungovat podle potřeby.

## <a name="monitoring"></a>Monitorování

Workbench poskytuje protokolování aplikací pomocí Application Insights a Azure Monitor. Application Insights se používá k ukládání všech protokolovaných informací z Blockchain Workbench a obsahuje chyby, varování a úspěšné operace. Application Insights mohou vývojáři používat k ladění problémů s Blockchain Workbench. 

Azure Monitor poskytuje informace o stavu blockchainové sítě. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení služby Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)