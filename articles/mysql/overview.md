---
title: Overview of Azure Database for MySQL relational database service
description: Learn about the Azure Database for MySQL service, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 680b5e9ef8e7e8ed59d3b502b49fc1b45d016e80
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483513"
---
# <a name="what-is-azure-database-for-mysql"></a>Co je Azure Database for MySQL?

Azure Database for MySQL is a relational database service in the Microsoft cloud based on the [MySQL Community Edition](https://www.mysql.com/products/community/) (available under the GPLv2 license) database engine, versions 5.6, 5.7, and 8.0. Azure Database for MySQL delivers:

- Integrovanou vysokou dostupnost bez dalších poplatků.
- Předvídatelný výkon a všeobecné ceny při průběžných platbách.
- Škálování podle potřeby během několika sekund.
- Zabezpečení pro ochranu neaktivních uložených dat i přenášených dat.
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikací pomocí libovolných opensourcových nástrojů a platforem a dodávat je tak rychle a efektivně, jak váš podnik vyžaduje – a to vše, aniž byste se museli učit nové dovednosti.

![Azure Database for MySQL conceptual diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

This article is an introduction to Azure Database for MySQL core concepts and features related to performance, scalability, and manageability, with links to explore details. Tyto rychlé starty vám pomůžou začít:

- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Řadu ukázek v Azure CLI najdete tady:

- [Azure CLI samples for Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund
The Azure Database for MySQL service offers several service tiers: Basic, General Purpose, and Memory Optimized. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. See [Pricing tiers](concepts-service-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Jak se rozhodnout, kdy přidat a kdy odebrat výkon? Můžete využít integrované funkce monitorování výkonu a upozorňování v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. Podrobnosti najdete v tématu [Upozornění](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu
Dostupnost služby Azure se smlouvou o úrovní služeb (SLA) dosahuje špičkové hodnoty 99,99 %, protože staví na globální síti Microsoftem spravovaných datových center. Může tedy udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. With every Azure Database for MySQL server, you take advantage of built-in security, fault tolerance, and data protection that you would otherwise have to buy or design, build, and manage. With Azure Database for MySQL, you can use point-in-time restore to recover a server to an earlier state, as far back as 35 days.

## <a name="secure-your-data"></a>Zabezpečte svoje data
Azure database services have a tradition of data security that Azure Database for MySQL upholds, with features that limit access, protect data at-rest and in-motion, and help you monitor activity. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/trustcenter/security). For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Kontakty
For any questions or suggestions you might have about working with Azure Database for MySQL, send an email to the Azure Database for MySQL Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). This email address is not a technical support alias.

Můžete také využít tyto komunikační kanály:

- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Další kroky
Now that you've read an introduction to Azure Database for MySQL and answered the question "What is Azure Database for MySQL?" you're ready to:

- Na stránce s cenami najdete porovnání nákladů a kalkulačky. [Ceny](https://azure.microsoft.com/pricing/details/mysql/)
- Začněte vytvářet první server. [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
