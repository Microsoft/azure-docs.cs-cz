---
title: Přehled služby relačních databází Azure Database for MariaDB
description: Přehled služby relačních databází Azure Database for MariaDB
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 696ba68894b4ac5f73c234ea49883fd0aa3db4d5
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320913"
---
# <a name="what-is-azure-database-for-mariadb"></a>Co je Azure Database for MariaDB?

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu. Služba Azure Database for MariaDB je založená na databázovém stroji [MariaDB Community Edition](https://mariadb.org/download/). Tato služba je ve verzi Public Preview. 

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

Ve verzi Preview je služba Azure Database for MariaDB k dispozici v několika úrovních: Basic, Obecné účely a Optimalizovaný pro paměť. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost pomáhá databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [Cenové úrovně](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jak se rozhodnout, kdy vertikálně navýšit nebo snížit kapacitu? Můžete využít integrované funkce monitorování výkonu a upozorňování v Azure Database for MariaDB v kombinaci s hodnocením výkonu na základě virtuálních jader. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity virtuálních jader v závislosti na stávajících nebo předpokládaných požadavcích. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu

Dostupnost služby Azure se smlouvou SLA (nenabízí se ve fázi Public Preview) dosahuje špičkové hodnoty 99,99 %, protože staví na globální síti Microsoftem spravovaných datových center. Tato síť pomáhá udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. S Azure Database for MariaDB získáte výhody integrovaného zabezpečení, odolnosti proti chybám a ochrany dat. S Azure Database for MariaDB můžete využít obnovení serveru do určitého bodu v čase, a to až 35 dnů zpět.

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

## <a name="next-steps"></a>Další kroky

Přečetli jste si úvod ke službě Azure Database for MariaDB a teď můžete pokračovat následovně:
- Na stránce s [cenami](https://azure.microsoft.com/pricing/details/mariadb/) najdete porovnání nákladů a kalkulačky. 
- Začněte [vytvořením prvního serveru](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
