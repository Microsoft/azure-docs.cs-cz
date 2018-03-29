---
title: Přehled Azure databáze pro služba relační databáze MySQL
description: Přehled databáze Azure pro služba relační databáze MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.custom: mvc
ms.openlocfilehash: 3b7ae08446e672c218e7278cff4f77739286fead
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Co je Azure Database pro databázi MySQL?
Databáze pro databázi MySQL Azure je služba relační databáze v cloudu Microsoftu na základě [MySQL Community Edition](https://www.mysql.com/products/community/) databázového stroje. Azure Database pro databázi MySQL nabízí:

- Integrovanou vysokou dostupnost bez dalších poplatků.
- Předvídatelný výkon a všeobecné ceny při průběžných platbách.
- Průběžné škálování během několika sekund.
- Zabezpečení pro ochranu neaktivních uložených dat i přenášených dat.
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů.
- Zabezpečení a dodržování předpisů na podnikové úrovni.

Tyto možnosti vyžadují téměř žádné správy a všechny jsou k dispozici bez dalších poplatků. Umožňují zaměřit na rychlý vývoj aplikací a urychlení doby uvedení na trh než přidělování drahocenný čas a prostředky se správou virtuálních počítačů a infrastruktury. Kromě toho můžete nadále vyvíjet aplikace s otevřeným zdrojem nástroje a platformy zvoleného zajistit vaše obchodní požadavky, všechny bez nutnosti další nové dovednosti s rychlostí a efektivitu.

![Databáze Azure pro koncepční diagram MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Tento článek je úvodem do databáze Azure pro MySQL základní koncepty a funkcí týkajících se výkonu, škálovatelnosti a spravovatelnosti, s odkazy na podrobnější informace. Tyto rychlé starty vám pomůžou začít:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Řadu ukázek v Azure CLI najdete tady:
- [Ukázek Azure CLI pro databázi Azure pro databázi MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund
Databáze Azure pro službu MySQL nabízí několik úrovní služeb: Basic, obecné účely a paměťově optimalizovaná. Každá úroveň nabízí různý výkon a možnosti pro podporu databázových úloh od zcela nenáročných až po velmi náročné. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [Cenové úrovně](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Jak se rozhodnout, kdy přidat a kdy odebrat výkon? Můžete použít předdefinované výkonu monitorování a výstrah funkce v kombinaci s hodnocení výkonu podle vCores. Používání těchto nástrojů, můžete rychle posoudit dopad vCores škálování nahoru nebo dolů na základě potřeb aktuální nebo předpokládané výkonu. Podrobnosti najdete v tématu [Upozornění](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Udržujte své aplikace a podnikáni v chodu
Azure špičkové 99,99 % dostupnost smlouvu o úrovni služeb (SLA) používá technologii globální sítě datových center spravovaných společností Microsoft, pomáhá udržet vaše aplikace s 24/7. S každou databází Azure pro server databáze MySQL můžete využít výhod integrované zabezpečení, odolnost proti chybám a ochrany dat, která by jinak muset koupit nebo návrh, vytvářet a spravovat. S Azure Database pro databázi MySQL můžete v okamžiku obnovení obnovit server do předchozího stavu, až 35 dnů.

## <a name="secure-your-data"></a>Zabezpečení dat
Služby Azure databáze mít tradici zabezpečení dat, které podporuje Azure Database pro databázi MySQL, s funkcemi, které omezit přístup, ochranu dat na rest a v provozu a vám pomůže sledovat činnost. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://www.microsoft.com/en-us/trustcenter/security).

Databáze Azure pro službu MySQL používá šifrování úložiště pro data na rest. Data, včetně zálohování, jsou zašifrovaná na disku (s výjimkou dočasné soubory, které jsou vytvořené pomocí modulu při spuštění dotazů). Služba používá 256bitové šifrování AES, které je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Ve výchozím nastavení je databáze Azure pro službu MySQL nakonfigurovaná tak, aby vyžadovala [zabezpečení připojení SSL](./concepts-ssl-connection-security.md) pro data za provozu v síti. Vynucení připojení SSL mezi databázový server a klientských aplikací pomáhá chránit před útoky "man uprostřed" šifrování datový proud mezi serverem a aplikace.  Volitelně můžete vyžadování SSL pro připojení k vaší databázové službě zakázat, pokud vaše klientská aplikace nepodporuje připojení SSL.

## <a name="next-steps"></a>Další postup
Teď, když jste si přečetli Úvod do Azure Database pro databázi MySQL a znáte odpověď na otázku "Co je Azure databáze pro MySQL?", jste připraveni:
- Najdete na stránce s cenami pro porovnávání náklady a kalkulačky. [Ceny](https://azure.microsoft.com/pricing/details/mysql/)
- Začněte vytvořením první server. [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Vytvoření první aplikace pomocí preferovaný jazyk: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [přejděte](./connect-go.md)
