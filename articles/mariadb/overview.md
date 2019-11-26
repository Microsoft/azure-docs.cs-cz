---
title: Přehled služby relačních databází Azure Database for MariaDB
description: Learn about the Azure Database for MariaDB service, a relational database service in the Microsoft cloud based on the MySQL community edition.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: aed2e090a1e3a6a4c80f70ba25da9a903a6192c1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484153"
---
# <a name="what-is-azure-database-for-mariadb"></a>Co je Azure Database for MariaDB?

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu. Azure Database for MariaDB is based on the [MariaDB community edition](https://mariadb.org/download/) (available under the GPLv2 license) database engine, version 10.2 and 10.3.

Azure Database for MariaDB nabízí:

- Integrovanou vysokou dostupnost bez dalších poplatků.
- Předvídatelný výkon a všeobecné ceny při průběžných platbách.
- Škálování podle potřeby během několika sekund.
- Zabezpečenou ochranu důvěrných neaktivních uložených dat i přenášených dat.
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti nevyžadují téměř žádnou správu. Jsou k dispozici bez dalších poplatků. Azure Database for MariaDB vám může pomoct rychle vyvíjet aplikace a zkrátit dobu uvedení na trh. Nemusíte vynakládat drahocenný čas a prostředky na správu virtuálních počítačů a infrastruktury. Zároveň můžete i nadále vyvíjet aplikace s využitím libovolných open source nástrojů a platforem. Zajistěte rychlost a efektivitu dodávání, kterou váš podnik vyžaduje, aniž byste se museli učit nové dovednosti.

Informace o základních konceptech a funkcích v Azure Database for MariaDB, včetně výkonu, škálovatelnosti a možnostech správy, najdete v těchto rychlých startech:

- [Vytvoření serveru Azure Database for MariaDB pomocí webu Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MariaDB pomocí Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund

The Azure Database for MariaDB service offers several service tiers: Basic, General Purpose, and Memory Optimized. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost pomáhá databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. See [Pricing tiers](concepts-pricing-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jak se rozhodnout, kdy vertikálně navýšit nebo snížit kapacitu? Můžete využít integrované funkce monitorování výkonu a upozorňování v Azure Database for MariaDB v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. Podrobnosti najdete v tématu [Upozornění](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu

Azure's industry-leading 99.99% availability SLA is powered by a global network of Microsoft-managed datacenters. Tato síť pomáhá udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S Azure Database for MariaDB získáte výhody integrovaného zabezpečení, odolnosti proti chybám a ochrany dat. S Azure Database for MariaDB můžete využít obnovení serveru do určitého bodu v čase, a to až 35 dnů zpět.

## <a name="secure-your-data"></a>Zabezpečte svoje data

Databázové služby Azure mají tradici zabezpečení dat, kterou služba Azure Database for MariaDB zachovává. Azure Database for MariaDB nabízí funkce, které omezují přístup, chrání přenášená i neaktivní uložená data a pomáhají s monitorováním aktivity. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/trustcenter/security). For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Kontakty

Jakékoli dotazy nebo návrhy k používání Azure Database for MariaDB můžete zasílat [týmu Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (tento alias neslouží k poskytování technické podpory).

Můžete použít také následující kontaktní bod:
- Pokud chcete kontaktovat podporu Azure, [vytvořte žádost o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) na webu Azure Portal.
- Pokud řešíte problém s účtem, vytvořte [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na webu Azure Portal.
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte [fóra Azure pro názory](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Další kroky

Přečetli jste si úvod ke službě Azure Database for MariaDB a teď můžete pokračovat následovně:
- Na stránce s [cenami](https://azure.microsoft.com/pricing/details/mariadb/) najdete porovnání nákladů a kalkulačky. 
- Začněte [vytvořením prvního serveru](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
