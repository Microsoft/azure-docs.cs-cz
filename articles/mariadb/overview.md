---
title: Přehled služby Azure Database for MariaDB relačních databází
description: Přečtěte si o službě Azure Database for MariaDB, službě relačních databází v cloudu Microsoftu na základě verze MySQL Community Edition.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 08/13/2019
ms.openlocfilehash: 46ba972c489326157cbdea19a1363a30e18f72b8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177978"
---
# <a name="what-is-azure-database-for-mariadb"></a>Co je Azure Database for MariaDB?

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu. Azure Database for MariaDB vychází z [MariaDB Community Edition](https://mariadb.org/download/) (k dispozici v rámci licence GPLv2) databázového stroje verze 10,2 a 10,3.

Azure Database for MariaDB poskytuje:

- Integrovaná vysoká dostupnost bez dalších nákladů.
- Předvídatelný výkon s využitím celkových cen průběžných plateb.
- Škálování podle potřeby během několika sekund.
- Zabezpečená ochrana citlivých dat v klidovém a pohybovém provozu.
- Automatické zálohování a obnovení k bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti vyžadují skoro žádnou správu. Jsou k dispozici bez dalších nákladů. Azure Database for MariaDB vám může přispět k rychlému vývoji vaší aplikace a urychlení vaší doby uvedení na trh. Pro správu virtuálních počítačů a infrastruktury nemusíte přidělovat úžasné čas a prostředky. Můžete také pokračovat v vývoji aplikace pomocí Open source nástrojů a platformy podle vašeho výběru. Zajistěte rychlost a efektivitu vašich obchodních požadavků, a to vše bez učení nových dovedností.

Další informace o základních konceptech a funkcích v Azure Database for MariaDB, včetně výkonu, škálovatelnosti a možností správy, najdete v těchto rychlých startech:

- [Vytvoření serveru Azure Database for MariaDB pomocí Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund

Služba Azure Database for MariaDB nabízí několik úrovní služeb: Basic, Pro obecné účely a paměťově optimalizované. Každá úroveň nabízí jiný výkon a možnosti pro podporu odlehčených a těžkých databázových úloh. Svou první aplikaci můžete vytvořit na malé databázi po dobu několika dolarů měsíčně a pak upravit měřítko tak, aby splňovalo požadavky vašeho řešení. Díky dynamické škálovatelnosti může vaše databáze transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jenom když je potřebujete. Podrobnosti najdete v tématu [cenové úrovně](concepts-pricing-tiers.md) .

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Jak se rozhodnout, kdy navýšení nebo snížení kapacity navýšení? V kombinaci s hodnocením výkonu na základě virtuální jádra můžete využít integrované funkce monitorování výkonu a upozorňování na Azure Database for MariaDB. Pomocí těchto nástrojů můžete rychle vyhodnotit účinek škálování virtuální jádra nahoru nebo dolů na základě aktuálních nebo plánovaných potřeb výkonu. Podrobnosti najdete v tématu [výstrahy](howto-alert-metric.md) .

## <a name="keep-your-app-and-business-running"></a>Zajištění chodu aplikace a firmy

Smlouva SLA o špičkové 99,99% dostupnosti v oboru Azure využívá globální síť datacenter spravovaných Microsoftem. Síť pomáhá udržet aplikaci spuštěnou 24/7. Můžete využít integrované zabezpečení, odolnost proti chybám a ochranu dat v Azure Database for MariaDB. V Azure Database for MariaDB můžete použít obnovení k určitému bodu v čase k obnovení serveru do dřívějšího stavu, a to až 35 dní.

## <a name="secure-your-data"></a>Zabezpečení dat

Služba Azure Database Services má tradici zabezpečení dat, které Azure Database for MariaDB uchovávání. Azure Database for MariaDB nabízí funkce, které omezují přístup, chrání data v klidovém provozu a pohybují a vám může pomáhat sledovat aktivity. Informace o zabezpečení platformy Azure najdete na [Centrum zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/security) .

Služba Azure Database for MariaDB používá šifrovací modul ověřený 140-2 Standard FIPS pro šifrování úložiště neaktivních dat. Data včetně záloh se šifrují na disku s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá AES 256-bit šifry, která je součástí Azure Storage šifrování. Klíče jsou spravované systémem. Šifrování úložiště je vždycky zapnuté a nedá se zakázat.

Ve výchozím nastavení je služba Azure Database for MariaDB nakonfigurovaná tak, aby vyžadovala [zabezpečení připojení SSL](./concepts-ssl-connection-security.md) pro data při pohybu přes síť. Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž v prostředním", protože šifruje datový proud mezi serverem a vaší aplikací. Volitelně můžete zakázat požadavek SSL pro připojení k vaší databázové službě, pokud vaše klientská aplikace nepodporuje připojení SSL.

## <a name="contacts"></a>Složce

Můžete odeslat jakékoli dotazy nebo návrhy, které máte o práci s Azure Database for MariaDB [týmu Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (ne s aliasem technické podpory).

Můžete také použít následující kontaktní body:
- Pokud chcete kontaktovat podporu Azure, [otevřete žádost o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) v Azure Portal.
- Pokud chcete vyřešit problém s vaším účtem, [otevřete žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) v Azure Portal.
- Pokud chcete poskytnout zpětnou vazbu nebo požádat o nové funkce, vytvořte položku ve [fórech Azure Feedback](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Další kroky

Teď, když jste si přečetli Úvod do Azure Database for MariaDB, jste připraveni:
- Porovnání nákladů a kalkulačky najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/mariadb/) . 
- Začněte [vytvořením prvního serveru](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
