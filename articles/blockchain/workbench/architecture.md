---
title: Architektura služby Azure Blockchain Workbench
description: Přehled architektury Azure Blockchain Workbench a jeho součástí.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330632"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architektura služby Azure Blockchain Workbench

Azure Blockchain Workbench zjednodušuje vývoj aplikací pro blockchain tím, že poskytuje řešení pomocí několika komponentami Azure. Blockchain Workbench se dá nasadit pomocí šablony řešení na webu Azure Marketplace. Šablona umožňuje vybrat moduly a komponenty pro nasazení včetně blockchain zásobníku, typ klientské aplikace a podporu pro integraci IoT. Po nasazení Blockchain Workbench umožňuje přístup k webové aplikace, aplikace pro iOS a aplikace pro Android.

![Architektura Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identita a ověřování

Používající Blockchain Workbench, konsorcium může provést federaci jejich podnikových identit pomocí Azure Active Directory (Azure AD). Aplikace Workbench generuje nové uživatelské účty ve řetězu identit s identitami enterprise uložená ve službě Azure AD. Mapování identit usnadňuje ověřeného přihlášení pro klientské rozhraní API a aplikace a využívá zásady ověřování organizací. Aplikace Workbench také poskytuje možnost k přidružení podnikových identit pro konkrétní role v rámci dané inteligentní kontraktu. Kromě toho aplikace Workbench také poskytuje mechanismus pro identifikaci akce, které tyto role můžete provést a zároveň.

Po nasazení Blockchain Workbench umožňuje uživatelům interakci s Blockchain Workbench přes klientských aplikací, rozhraní API založené na protokolu REST klienta nebo rozhraní API zasílání zpráv. Ve všech případech musí být ověřené interakce přes Azure Active Directory (Azure AD) nebo přihlašovací údaje pro konkrétní zařízení.

Uživatelé Federovat svých identit Azure AD konsorcium zasláním e-mailovou pozvánku účastníků v e-mailová adresa. Při přihlašování, tito uživatelé ověřeni pomocí jméno, heslo a zásady. Například dvojúrovňového ověřování jejich organizace.

Azure AD slouží ke správě všichni uživatelé, kteří mají přístup k Blockchain Workbench. Každé zařízení, připojení k inteligentní smlouvy je také službou Azure AD.

Azure AD také umožňuje přiřadit uživatele do skupiny speciální správce. Uživatelé přidružení k skupiny správců je udělen přístup k oprávnění a akcím v rámci Blockchain Workbench, včetně nasazení smluv a udělení oprávnění pro uživatele pro přístup k kontrakt. Uživatelé mimo této skupiny nemají přístup k akcím správce.

## <a name="client-applications"></a>Klientské aplikace

Aplikace Workbench poskytuje aplikace automaticky generovaného klienta pro webové a mobilní zařízení (iOS, Android), který slouží k ověření, testování a zobrazení blockchainové aplikace. Rozhraní aplikace generuje dynamicky na základě metadat inteligentní smlouvy a může obsahovat libovolný případu použití. Klientské aplikace front-endu přístupných dodat kompletní blockchainové aplikace vygenerované Blockchain Workbench. Klientské aplikace ověřovat uživatele pomocí služby Azure Active Directory (Azure AD) a pak prezentujte uživatelské prostředí přizpůsobené obchodní kontext inteligentní kontraktu. Činnost koncového uživatele umožňuje vytvoření nové instance inteligentní kontraktu podle autorizované jednotlivce a potom zobrazí možnosti provádět některé typy transakcí ve vhodných míst v obchodním procesu, kterou představuje inteligentní kontraktu.

Ve webové aplikaci můžou Autorizovaní uživatelé přístup ke konzole správce. Konzole je k dispozici pro uživatele do skupiny správců ve službě Azure AD a poskytuje přístup k následujícím funkcím:

* Microsoft poskytuje kontraktů uzpůsobených pro oblíbené scénáře nasazení. Například majetku přenos scénáři.
* Nahrání a nasazení vlastních chytrých kontraktů.
* Přiřazení uživatelského přístupu k inteligentní smlouvy v kontextu konkrétní roli.

Další informace najdete v tématu [Azure Blockchain Workbench ukázka klientské aplikace na Githubu](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>Rozhraní API služby brány

Blockchain Workbench zahrnuje rozhraní API služby brány založené na protokolu REST. Při zápisu do blockchain, rozhraní API generuje a předává zprávy ostatním zprostředkovatele událostí. Při vyžádání dat pomocí rozhraní API, dotazy se odesílají do služby SQL database mimo řetězec. SQL database obsahuje repliku dat na řetězce a metadata, která poskytuje kontext a informace o konfiguraci pro podporované chytrých kontraktů. Dotazy vrací požadovaná data z repliky mimo řetězec ve formátu zajistí informovanost metadat pro kontrakt.

Vývojáři mají přístup k rozhraní API služby brány sestavovat nebo integrovat blockchainová řešení bez nutnosti spoléhat se na Blockchain Workbench klientské aplikace.

> [!NOTE]
> Povolit ověřený přístup k rozhraní API, dva klientské aplikace zaregistrovaní ve službě Azure Active Directory. Azure Active Directory vyžaduje registrace různých aplikací všechny typy aplikací (nativní a webové). 

## <a name="message-broker-for-incoming-messages"></a>Zprostředkovatele zpráv pro příchozí zprávy

Vývojáři, kteří se mají odesílat zprávy přímo Blockchain Workbench umožňuje odesílat zprávy přímo do služby Service Bus. Zprávy API může například použít pro integraci na systému nebo zařízení IoT.

## <a name="message-broker-for-downstream-consumers"></a>Zprostředkovatele zpráv pro příjem dat uživatele

Během životního cyklu aplikace dojde k událostem. Události se dá spouštět podle rozhraní API brány nebo na hlavní knihy. Oznamování událostí, může iniciovat podřízený kód založený na události.

Blockchain Workbench umožňuje automaticky nasadí dva druhy příjemci událostí. Jednoho příjemce se aktivuje události blockchain k naplnění úložiště mimo řetězec SQL. Zachytit metadata pro události generované modulem rozhraní API související s nahrávání a úložiště dokumentů je další příjemce.

## <a name="message-consumers"></a>Příjemci zpráv

 Příjemci zpráv přijmout zprávy ze služby Service Bus. Základní model zpracování událostí pro příjemci zpráv umožňuje rozšíření dalších služeb a systémů. Například můžete přidat podporu k naplnění služby cosmos DB nebo vyhodnocení zprávy pomocí Azure Stream Analytics. Následující části popisují příjemci zpráv, které jsou součástí Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Příjemce distribuované účetní knihy

Zprávy (DLT) technologií distribuované účetní knihy obsahovat metadata pro transakce má být proveden zápis blockchainu. Příjemce načte zprávy a odesílá data do Tvůrce transakce, podepsaný a směrovače.

### <a name="database-consumer"></a>Příjemce databáze

Příjemce databáze přijímá zprávy ze služby Service Bus a odesílá data do připojené databázi, jako je SQL database.

### <a name="storage-consumer"></a>Úložiště uživatelů

Úložiště uživatelů přijímá zprávy ze služby Service Bus a odesílá data do připojené úložiště. Například ukládat hodnoty hash dokumenty ve službě Azure Storage.

## <a name="transaction-builder-and-signer"></a>Tvůrce transakce a podepisující osoba

Pokud zprávu na zprostředkovatele příchozích zpráv musí být napsaný pro blockchainu, zpracuje se DLT příjemce. Příjemce DLT je služba, která načte zprávu obsahující metadata pro požadovaný transakci ke spuštění a pak odešle informace, které *Tvůrce transakce a podepisuje*. *Tvůrce transakce a podepisuje* sestaví blockchain transakce na základě dat a cíl požadovaného blockchain. Jakmile sestaví, transakce je podepsán. Soukromé klíče jsou uložené ve službě Azure Key Vault.

 Blockchain Workbench načte příslušný privátní klíč ze služby Key Vault a podepíše transakce mimo službu Key Vault. Po přihlášení, je odeslána transakce transakce směrovače a účetní knihy.

## <a name="transaction-routers-and-ledgers"></a>Transakce směrovače a účetní knihy

Transakce směrovače a účetní knihy trvat podepsaný transakce a přesměrovávala je vhodné po blockchain. Blockchain Workbench v současné době podporuje Etherea jako svůj blockchain cíl.

## <a name="dlt-watcher"></a>DLT sledovacího procesu

Sledování distribuované účetní knihy technologie (DLT) sleduje události, ke kterým dochází v bloku řetězy připojené k Blockchain Workbench.
Události zahrnují informace, které jsou relevantní pro jednotlivce a systémy. Například vytváření nových instancí kontraktu, provádění transakcí a následně se změní stav. Zachycení a odeslat zprostředkovatele odchozích zpráv, aby mohou být spotřebovány podřízené příjemci událostí.

Například SQL příjemce sleduje události, je využívá a naplní databázi SQL s hodnotách zahrnutých. Kopie umožňuje opětovnému vytvoření repliky ve řetězu dat v úložišti mimo řetězec.

## <a name="azure-sql-database"></a>Databáze SQL Azure

Azure SQL database, připojené k Blockchain Workbench ukládá definice kontraktu, konfigurace metadat a přístupná pro SQL repliku dat uložených v blockchainu. Tato data můžete snadno být dotazována, vizualizovat nebo analyzovaným přímý přístup k databázi. Vývojářům a dalším uživatelům můžete použít databázi pro vytváření sestav, analytics nebo jiné datově orientovaných integrace. Uživatelům například můžete vizualizovat data transakcí pomocí Power BI.

Tímto úložištěm mimo řetězec dává organizace provádět dotazy na data v SQL, nikoli v blockchainu knihy. Navíc tak můžete standardizovat na standardní schéma, které se nerozlišují zásobníky technologií blockchain, umožňuje úložiště mimo řetězec opakované použití sestav a další artefakty napříč projekty, scénáře a organizacemi.

## <a name="azure-storage"></a>Azure Storage

Azure Storage se využívá k ukládání smluv a metadata spojená s kontrakty.

Dokumenty z nákupních objednávek a náložní, k imagím videa pocházející z continuum včetně policejní kamery textu a obrázků hlavní pohybu, používané zprávy a lékařských dokumentů hrají roli v mnoha scénářích zaměřené na blockchain. Dokumenty nejsou vhodné umístit přímo v blockchainu.

Blockchain Workbench podporuje možnost přidat dokumenty nebo jiných mediálního obsahu se blockchain obchodní logikou. Hodnota hash obsahu dokumentu nebo média je uložen v blockchainu a skutečné dokumentu nebo mediální obsah uložený ve službě Azure Storage. Informace o přidružené transakce je doručit do zprostředkovatele příchozích zpráv, zabalené, podepsaný a směrovat do blockchainu. Tento proces se aktivuje události, které jsou sdílené úložiště přes zprostředkovatele odchozích zpráv. SQL Database využívá tyto informace a odesílá je do databáze pro pozdější dotazování. Podřízených systémů může také používat tyto události tak, aby fungoval podle potřeby.

## <a name="monitoring"></a>Monitorování

Aplikace Workbench poskytuje protokolování aplikací pomocí Application Insights a Azure Monitor. Application Insights se používá k ukládání všechny protokolované informace ze Blockchain Workbench a obsahuje chyby, varování a úspěšné operace. Application Insights můžete použít vývojáři k ladění problémů s Blockchain Workbench. 

Azure Monitor poskytuje informace o stavu sítě blockchain. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení služby Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)