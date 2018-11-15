---
title: Přehled – Azure Database for MySQL relační databázová služba
description: Přehled Azure Database for MySQL relační databázová služba.
services: mysql
ms.service: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.custom: mvc
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 4d0d78b978f6454170b52a95f538f143b9f24505
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684793"
---
# <a name="what-is-azure-database-for-mysql"></a>Co je Azure Database for MySQL?
Azure Database for MySQL je služba relačních databází v cloudu Microsoftu založená na [MySQL Community Edition](https://www.mysql.com/products/community/) databázový stroj, verze 5.6 a 5.7. Azure Database for MySQL nabízí:

- Integrovanou vysokou dostupnost bez dalších poplatků.
- Předvídatelný výkon a všeobecné ceny při průběžných platbách.
- Škálování podle potřeby během několika sekund.
- Zabezpečení pro ochranu neaktivních uložených dat i přenášených dat.
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikací pomocí libovolných opensourcových nástrojů a platforem a dodávat je tak rychle a efektivně, jak váš podnik vyžaduje – a to vše, aniž byste se museli učit nové dovednosti.

![Azure Database for MySQL koncepční diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Tento článek je úvodem do databáze Azure pro MySQL základních konceptů a funkcí týkajících se výkonu, škálovatelnosti a možností správy s odkazy na podrobnější informace. Tyto rychlé starty vám pomůžou začít:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Řadu ukázek v Azure CLI najdete tady:
- [Ukázky v Azure CLI pro službu Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund
Služba Azure Database for MySQL nabízí několik úrovní služby: Basic, General Purpose a Memory Optimized. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Zobrazit [cenové úrovně](concepts-service-tiers.md) podrobnosti.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Jak se rozhodnout, kdy přidat a kdy odebrat výkon? Můžete využít integrované funkce monitorování výkonu a upozorňování v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. Podrobnosti najdete v tématu [Upozornění](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu
Dostupnost služby Azure se smlouvou o úrovní služeb (SLA) dosahuje špičkové hodnoty 99,99 %, protože staví na globální síti Microsoftem spravovaných datových center. Může tedy udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S každou serveru Azure Database for MySQL využíváte integrované zabezpečení, odolnost proti chybám a ochranu dat, které by jinak museli kupovat nebo navrhovat, sestavovat a spravovat. Azure Database for MySQL můžete použít obnovení k určitému bodu v čase obnovení serveru do předchozího stavu, to až 35 dnů zpět.

## <a name="secure-your-data"></a>Zabezpečení dat
Databázové služby Azure mají tradici zabezpečení dat, které podporuje Azure Database for MySQL, pomocí funkcí, které omezují přístup, chránit data v klidovém stavu a za provozu a pomáhají monitorovat aktivitu. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/security).

Služba Azure Database for MySQL používá šifrování úložiště pro data v klidovém stavu. Data včetně záloh se šifrují na disku (s výjimkou dočasných souborů vytvořených databázovým strojem při spouštění dotazů). Služba používá 256bitové šifrování AES, které je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Ve výchozím nastavení, služba Azure Database for MySQL je nakonfigurovaný tak, aby vyžadovala [zabezpečení připojení protokolem SSL](./concepts-ssl-connection-security.md) pro data přenášená přes síť. Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky. Volitelně můžete vyžadování SSL pro připojení k vaší databázové službě zakázat, pokud vaše klientská aplikace nepodporuje připojení SSL.

## <a name="contacts"></a>Kontakty
Pro jakékoli dotazy nebo návrhy, které byste mohli pracovat se službou Azure Database for MySQL, pošlete e-mail na databázi Azure pro MySQL týmu ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Poznámka: Tento alias neslouží k poskytování technické podpory.

Můžete také využít tyto komunikační kanály:
- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Další postup
Teď, když jste přečtěte si úvod ke službě Azure Database for MySQL a znáte odpověď na otázku "Co je Azure databáze pro MySQL?", jste připraveni:
- Na stránce s cenami najdete porovnání nákladů a kalkulačky. [Ceny](https://azure.microsoft.com/pricing/details/mysql/)
- Začněte vytvářet první server. [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Vytvořte svoji první aplikaci pomocí preferovaného jazyka: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [přejít](./connect-go.md)
