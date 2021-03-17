---
title: Přehled – Azure Database for MariaDB
description: Přečtěte si o službě Azure Database for MariaDB, službě relačních databází v cloudu Microsoftu na základě verze MySQL Community Edition.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 7c365dee2b3ac32a62b38d0b3273f2f13e402936
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662013"
---
# <a name="what-is-azure-database-for-mariadb"></a>Co je Azure Database for MariaDB?

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu. Azure Database for MariaDB vychází z [MariaDB Community Edition](https://mariadb.org/download/) (k dispozici v rámci licence GPLv2) databázového stroje verze 10,2 a 10,3.

Azure Database for MariaDB poskytuje tyto výhody:

- Integrovaná vysoká dostupnost bez dalších nákladů.
- Předvídatelný výkon s využitím celkových průběžných plateb.
- Škálování podle potřeby během několika sekund.
- Zabezpečená ochrana citlivých neaktivních uložených dat i dat v pohybu.
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

Služba Azure Database for MariaDB nabízí několik úrovní služeb: Basic, Pro obecné účely a paměťově optimalizované. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost pomáhá databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [Cenové úrovně](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jak se rozhodnout, kdy vertikálně navýšit nebo snížit kapacitu? Můžete využít integrované funkce monitorování výkonu a upozorňování v Azure Database for MariaDB v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. Podrobnosti najdete v tématu [Upozornění](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu

Smlouva SLA o špičkové 99,99% dostupnosti v oboru Azure využívá globální síť datacenter spravovaných Microsoftem. Tato síť pomáhá udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S Azure Database for MariaDB získáte výhody integrovaného zabezpečení, odolnosti proti chybám a ochrany dat. S Azure Database for MariaDB můžete využít obnovení serveru do určitého bodu v čase, a to až 35 dnů zpět.

## <a name="secure-your-data"></a>Zabezpečení dat

Databázové služby Azure mají tradici zabezpečení dat, kterou služba Azure Database for MariaDB zachovává. Azure Database for MariaDB nabízí funkce, které omezují přístup, chrání přenášená i neaktivní uložená data a pomáhají s monitorováním aktivity. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/trustcenter/security). Další informace o funkcích zabezpečení Azure Database for MariaDB najdete v [přehledu zabezpečení](concepts-security.md).

## <a name="contacts"></a>Kontakty

Jakékoli dotazy nebo návrhy k používání Azure Database for MariaDB můžete zasílat [týmu Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (tento alias neslouží k poskytování technické podpory).

Můžete použít také následující kontaktní bod:
- Pokud chcete kontaktovat podporu Azure, [vytvořte žádost o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) na webu Azure Portal.
- Pokud řešíte problém s účtem, vytvořte [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na webu Azure Portal.
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte [fóra Azure pro názory](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Další kroky

Přečetli jste si úvod ke službě Azure Database for MariaDB a teď můžete pokračovat následovně:
- Porovnání nákladů a kalkulačky najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/mariadb/) . 
- Začněte [vytvořením prvního serveru](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
