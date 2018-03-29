---
title: Úvod do Azure Cosmos DB Cassandra rozhraní API | Microsoft Docs
description: Zjistěte, jak používat Azure Cosmos databázi do stávajících aplikací "navýšení a shift" a vytvářet nové aplikace pomocí rozhraní API Cassandra pomocí Cassandra ovladače a CQL jste již obeznámeni s.
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: ''
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: 88364cecc1fa1ad7318cb28c9708a42e6a807347
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-apache-cassandra-api"></a>Úvod do Azure Cosmos DB: Apache Cassandra rozhraní API

Azure Cosmos DB poskytuje rozhraní API Cassandra (preview) pro aplikace, které jsou napsané pro Apache Cassandra, třeba možnosti premium jako:

* [Velikost škálovatelné úložiště a propustnost](partition-data.md).
* [Globální distribuční klíč](distribute-data-globally.md)
* Latence v řádu milisekund na 99. percentilu.
* [Pět dobře definované úrovně konzistence](consistency-levels.md)
* [Automatické indexování dat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez nutnosti zabývat Správa schématu a index. 
* Zaručit vysoká dostupnost, všechny zálohovány pomocí [špičkový SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>Co je Azure Cosmos DB Apache Cassandra API?

Azure Cosmos DB lze použít jako úložiště dat pro aplikace napsané pro [Apache Cassandra](https://cassandra.apache.org/), pomocí rozhraní API Cassandra Apache. To znamená, že pomocí stávající [Apache licenci ovladače, které jsou kompatibilní s CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), vaše aplikace napsané pro Cassandra mohou komunikovat s Azure Cosmos DB Cassandra API. V mnoha případech můžete přepínat pomocí Apache Cassandra k používání Azure Cosmos DB Apache Cassandra rozhraní API, jednoduše změnou připojovací řetězec. Pomocí této funkce lze snadno vytvářet a spouštět aplikace rozhraní API Cassandra databáze ve službě Azure cloud s globální distribuční databázi Cosmos Azure a [komplexní SLA špičkový](https://azure.microsoft.com/support/legal/sla/cosmos-db), nadále používat známé dovedností a nástrojů pro Cassandra API.

![Rozhraní Cassandra API pro Azure Cosmos DB](./media/cassandra-introduction/cosmosdb-cassandra.png)

Rozhraní API Cassandra umožňuje pracovat s daty uloženými v databázi Cosmos Azure pomocí nástrojů Query Language Cassandra na základě (např. CQLSH) a ovladače klienta Cassandra jste již obeznámeni s. Další informace o ho v tomto videu Microsoft Mechanics s Kirill Gavrylyuk hlavní manažer inženýrství.

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Jaké jsou výhody používání Apache Cassandra rozhraní API pro Azure Cosmos DB?

**Žádná operace správy**: jako skutečného plně spravované služby, Azure Cosmos DB zajistí, že správci Cassandra rozhraní API se starat o správu a monitorování řadu nastavení napříč operačního systému, JVM a yaml souborů a jejich vztahu. Azure Cosmos DB poskytuje sledování propustnosti, latenci, úložiště a dostupnosti a konfigurovat výstrahy. 

**Správa výkonu**: Azure Cosmos DB poskytuje SLA zálohovaný s nízkou latencí čte a zapisuje pro 99th percentil. Uživatelé se nemusíte starat o velké množství provozní režie poskytují dobrý číst a zapisovat SLA. Obvykle patří plánování způsobem vzniknou, Správa značek odstraněných položek, nastavení filtrů Standard a pomalou repliky. Azure Cosmos DB trvá ji okamžitě bez obav správy tyto problémy a vám umožní soustředit na výsledek aplikace.

**Automatické indexování**: Azure Cosmos DB automaticky indexuje všechny sloupce tabulky v databázi Cassandra rozhraní API. Azure Cosmos DB nevyžaduje vytváření sekundárních indexů pro urychlení dotazů. Nabízí nízkou latenci pro čtení a zápisu dochází přitom automatické indexování konzistentní. 

**Možnost využívat stávající kód a nástroje**: Azure Cosmos DB poskytuje přenosu protokolu úrovně kompatibility s existující sady SDK a nástroje. Tato kompatibilita zajišťuje, můžete použít stávající kód s Cassandra rozhraní API Cosmos Azure DB s trivial změny.

**Propustnost a úložiště pružnost**: Platforma Azure Cosmos poskytuje pružnost zaručenou propustnost v oblastech prostřednictvím jednoduchého portálu, prostředí PowerShell nebo rozhraní příkazového řádku operace. Je možné Elasticky škálovat Azure Cosmos DB tabulky s předvídatelným výkonem bezproblémově růstem vaší aplikace. Azure Cosmos DB podporuje rozhraní API Cassandra tabulky, které je možné škálovat na prakticky neomezené velikosti úložiště. 

**Globální distribuce a dostupnosti**: Azure Cosmos DB umožňuje distribuci dat napříč oblastech Azure za účelem povolení uživatelé s nízkou latencí a prostředí při zajištění dostupnosti. Azure Cosmos DB poskytuje 99,99 % dostupnost v rámci oblasti a 99.999 % číst dostupnost v oblastech s režií žádné operace. Je k dispozici v 30 + Azure Cosmos DB [oblasti Azure](https://azure.microsoft.com/regions/services/). Další informace v [distribuci dat globálně](distribute-data-globally.md). 

**Volba konzistence**: Azure Cosmos DB poskytuje volba pět dobře definované úrovně konzistence pro dosažení optimálního poměru mezi konzistencí a výkonem. Silné a ohraničenou odolností, založenou relaci konzistentní předpona, jsou tyto úrovně konzistence a případnou. Tyto úrovně konzistence podrobné, dobře definované povolit developer, chcete-li zvolit vhodný poměr mezi konzistencí, dostupností a latencí. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md). 

**Podnikové úrovni**: zabezpečení a dodržování ve výchozím nastavení – Azure cosmos DB poskytuje [dodržování předpisů certifikace](https://www.microsoft.com/trustcenter) uživatelé mohli používat platformu, aniž byste museli řešit problémy s dodržováním předpisů. Azure Cosmos DB také poskytuje šifrování v klidovém stavu a v provozu, IP brány firewall a protokoly auditu a aktivity rovině řízení.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>Zaregistrujte se 

Pokud již máte předplatné Azure, můžete si zaregistrovat připojit se k programu Cassandra rozhraní API (preview) [portál Azure](https://aka.ms/cosmosdb-cassandra-signup).  Pokud jste Azure ještě nepoužívali, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/free) kde získat dobu 12 měsíců volného přístupu k databázi Azure Cosmos. Proveďte následující kroky s požadavkem o přístup k programu Cassandra rozhraní API (preview).

1. V [portál Azure](https://portal.azure.com), klikněte na tlačítko **vytvořit prostředek** > **databáze** > **Azure Cosmos DB**. 

2. Na stránce nový účet, vyberte **Cassandra** v poli rozhraní API. 

3. V **předplatné** vyberte předplatné Azure, kterou chcete použít pro tento účet.

4. Klikněte na tlačítko **zaregistrujte si verzi Preview ještě dnes**.

    ![Rozhraní Cassandra API pro Azure Cosmos DB](./media/cassandra-introduction/cassandra-sign-up.png)

3. V registrace informací o dnešku podokně náhledu, klikněte na **OK**. 

    Po odeslání žádosti se stav změní na **čekající na schválení** v podokně nový účet. 

Po odeslání vaši žádost o počkejte e-mailové oznámení, že vaše žádost byla schválena. Kvůli velkému počtu požadavků měli byste obdržet oznámení do jednoho týdne. Není nutné vytvořit lístek podpory pro dokončení požadavku. V pořadí, ve kterém bylo přijato projde kontrolou požadavků. 

## <a name="how-to-get-started"></a>Jak začít
Po připojení programu preview, postupujte podle rozhraní API Cassandra quickstarts k vytvoření aplikace pomocí rozhraní API Cassandra:

* [Rychlý úvod: Sestavení Cassandra webové aplikace s Node.js a Azure Cosmos DB](create-cassandra-nodejs.md)
* [Rychlý úvod: Sestavení Cassandra webovou aplikaci Java a Azure Cosmos DB](create-cassandra-java.md)
* [Rychlý úvod: Sestavení Cassandra webové aplikace pomocí rozhraní .NET a Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rychlý úvod: Sestavení Cassandra webové aplikace Python a Azure Cosmos DB](create-cassandra-python.md)

## <a name="next-steps"></a>Další postup

Informace o rozhraní API služby Azure DB Cosmos Cassandra je integrována do dokumentace se celkově Azure Cosmos DB, ale tady jsou na několik věcí, které vám pomůžou začít:

* Postupujte podle [– elementy QuickStart](create-cassandra-nodejs.md) k vytvoření účtu a nové aplikace pomocí Git vzorku
* Postupujte podle [kurzu](tutorial-develop-cassandra-java.md) k vytvoření nové aplikace prostřednictvím kódu programu.
* Postupujte podle [kurzu data importovat Cassandra](cassandra-import-data.md) k importu existujících dat do Azure Cosmos DB.
* Pro čtení [– nejčastější dotazy](faq.md#cassandra).
