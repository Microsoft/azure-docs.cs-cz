---
title: Přehled služby relačních databází Azure Database for MariaDB
description: Přehled služby relačních databází Azure Database for MariaDB
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: b1e458339c2befdb51439cd23ef5f22ba4379c78
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850257"
---
# <a name="what-is-azure-database-for-mariadb"></a>Co je Azure Database for MariaDB?

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu. Azure Database pro MariaDB vychází [edice community MariaDB](https://mariadb.org/download/) databázový stroj, verze 10.2.

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

Azure Database pro MariaDB service nabízí několik úrovní služby: Basic, General Purpose a Memory Optimized. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost pomáhá databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Zobrazit [cenové úrovně](concepts-pricing-tiers.md) podrobnosti.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jak se rozhodnout, kdy vertikálně navýšit nebo snížit kapacitu? Můžete využít integrované funkce monitorování výkonu a upozorňování v Azure Database for MariaDB v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu

Smlouva SLA špičkovou 99,99 % dostupnost Azure využívá k tomu globální síti Datacenter řízených microsoftem. Tato síť pomáhá udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S Azure Database for MariaDB získáte výhody integrovaného zabezpečení, odolnosti proti chybám a ochrany dat. S Azure Database for MariaDB můžete využít obnovení serveru do určitého bodu v čase, a to až 35 dnů zpět.

## <a name="secure-your-data"></a>Zabezpečení dat

Databázové služby Azure mají tradici zabezpečení dat, kterou služba Azure Database for MariaDB zachovává. Azure Database for MariaDB nabízí funkce, které omezují přístup, chrání přenášená i neaktivní uložená data a pomáhají s monitorováním aktivity. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/security).

Služba Azure Database for MariaDB používá pro neaktivní uložená data šifrování úložiště. Data včetně zálohovaných dat se šifrují na disku. (Dočasné soubory vytvořené databázovým strojem při spouštění dotazů se na disku nešifrují.) Služba používá 256bitové šifrování AES, které je součástí šifrování služby Azure Storage. Klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Ve výchozím nastavení je ve službě Azure Database for MariaDB nakonfigurované vyžadování [zabezpečení připojení protokolem SSL](./concepts-ssl-connection-security.md) pro data přenášená přes síť. Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky. Volitelně můžete vyžadování SSL pro připojení k vaší databázové službě zakázat, pokud vaše klientská aplikace nepodporuje připojení SSL.

## <a name="contacts"></a>Kontakty

Jakékoli dotazy nebo návrhy k používání Azure Database for MariaDB můžete zasílat [týmu Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (tento alias neslouží k poskytování technické podpory).

Můžete použít také následující kontaktní bod:
- Pokud chcete kontaktovat podporu Azure, [vytvořte žádost o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) na webu Azure Portal.
- Pokud řešíte problém s účtem, vytvořte [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na webu Azure Portal.
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte [fóra Azure pro názory](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Další postup

Přečetli jste si úvod ke službě Azure Database for MariaDB a teď můžete pokračovat následovně:
- Na stránce s [cenami](https://azure.microsoft.com/pricing/details/mariadb/) najdete porovnání nákladů a kalkulačky. 
- Začněte [vytvořením prvního serveru](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
