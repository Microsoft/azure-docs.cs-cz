---
title: Vysvětlení, jak sleva za rezervaci se použije ke službě Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak se sleva za rezervaci použije k zřízená propustnost (RU/s) ve službě Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: cwatson
ms.reviewer: sngun
ms.openlocfilehash: adcd91a8f1b3368d03f4b634e7aef40104d953e3
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393634"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Vysvětlení, jak sleva za rezervaci se použije ke službě Azure Cosmos DB

Po nákupu Azure Cosmos DB vyhrazené kapacity sleva za rezervaci se automaticky využije na prostředky Azure Cosmos DB, které odpovídají atributy a množství rezervace. Rezervace obsahuje zajištěné propustnosti pro prostředky Azure Cosmos DB a nepopisuje softwaru, sítě, úložiště, nebo předem definovanou kontejneru poplatky.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Sleva za rezervaci použitý k účtům Azure Cosmos DB

Sleva za rezervaci se použije k [zřízená propustnost](../cosmos-db/request-units.md) z hlediska jednotek žádostí za second(RU/s) po hodinu hodinách. Sleva za rezervaci se pro prostředky Azure Cosmos DB, která nepoužívají celou hodinu, automaticky využije na další služby Cosmos DB prostředky, které odpovídají atributy rezervace. Sleva můžete použít pro prostředky Azure Cosmos DB, které jsou spuštěny souběžně. Pokud nemáte k dispozici prostředky Cosmos DB, na kterých běží celou hodinu a které se shodují s atributy rezervace, neobdržíte všech výhod sleva za rezervaci pro určitou hodinu.

* Slevy proběhne to znamená, že rezervace s vyšší jednotkách požadavků poskytují vyšší slevy.  
* Nákup rezervace se uplatní slevy ke všem oblastem v poměru ekvivalentní místní ceny na vyžádání. Rezervace poměry slevy v jednotlivých oblastech najdete v části [sleva za rezervaci v jedné oblasti](#reservation-discount-per-region) části tohoto článku.

## <a name="reservation-discount-per-region"></a>Sleva za rezervaci na oblast
Sleva za rezervaci se použije na náklady na služby Azure Cosmos DB propustnosti na základě hodinu hodinu na jedno předplatné nebo rozsahu zaregistrované nebo účtu. Sleva za rezervaci se vztahují na měření využití v různých oblastech v poměru následující:

|Popis měřiče  |Oblast |Poměr  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – Asie a Tichomoří – jihovýchod  |  Asie a Tichomoří – jihovýchod    |   1      |
|Azure Cosmos DB – 100 RU/s/hodina – Asie a Tichomoří – východ |   Asie a Tichomoří – východ   |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Evropa – sever|  Evropa – sever       |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Korea – jih|    Korea – jih     |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Evropa – západ|    Evropa – západ     |      1   |
|Azure Cosmos DB – 100 RU/s/hodina – Korea – střed|   Korea – střed    |       1  |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – jih|   Velká Británie – jih      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – západ|   Spojené království – západ      |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – sever |   Velká Británie – sever    |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – Velká Británie – jih 2|   Velká Británie – jih 2      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – východ 2|  USA – východ 2     |     1    |
|Azure Cosmos DB – 100 RU/s/hodina - střed USA – sever|   USA (střed) – sever      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ|   USA – západ      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – střed| USA – střed        |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ 2|   USA – západ 2      |      1   |
|Azure Cosmos DB – 100 RU/s/hodina – USA – Středozápad|   USA – středozápad      |       1  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – východ|   USA – východ      |  1       |
|Azure Cosmos DB – 100 RU/s/hodina - Jižní Afrika – sever|     Jižní Afrika – sever    |   1      |
|Azure Cosmos DB – 100 RU/s/hodina - Jižní Afrika – západ |    Jižní Afrika – západ      |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – jih|    Indie – jih     |    1.0375    |
|Azure Cosmos DB – 100 RU/s/hodina - CA – východ|   Kanada – východ      |    1.1      |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – východ|   Japonsko – východ      |    1,125     |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – západ|     Japonsko – západ    |   1,125       |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – západ|     Indie – západ    |    1.1375     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – střed|    Indie – střed     |  1.1375       |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – východ|     Austrálie – východ    |   1,15       |
|Azure Cosmos DB CA 100 RU/s/hodina – střed|  Kanada – střed       |   1.2       |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – střed|   Francie – střed      |    1,25      |
|Azure Cosmos DB – 100 RU/s/hodina – Brazílie – jih|  Brazílie – jih       |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed|   Austrálie – střed      |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2| Austrálie – střed 2        |    1,5     |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih|    Francie – jih     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scénáře, které ukazují, jak se sleva za rezervaci použije

Vezměte v úvahu následující požadavky na rezervaci:

* Požadované propustnosti: 50 000 RU/s  
* Použít oblastí: 2. 

V tomto případě jsou vaše celkové náklady na vyžádání pro 500 množství měřiče 100 RU/s v těchto dvou oblastí pro celkový počet RU/s spotřebě 100 000 za hodinu. 

**Scénář 1**

Například pokud potřebujete nasazení služby Azure Cosmos DB v oblastech "USA – Sever střední" a "Oblasti USA – západ", a pokud každá oblast má spotřeby propustnost 50 000 RU/s. Nákup rezervace 100 000 RU/s by zcela zajistit rovnováhu mezi poplatky na vyžádání.

Sleva předmětem rezervace je vypočítán jako (propustnost spotřeby * reservation_discount_ratio_for_that_region). Poměr slevu na rezervaci je pro oblasti "USA – sever – střed" a "Oblasti USA – západ", "1". Proto zlevněné celkem RU/s se 100 000 RU/s (Tato hodnota se vypočítává jako: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s), a nemusíte platit žádné další poplatky za běžné sazby průběžných plateb. 

|Popis měřiče | Oblast |Využití propustnosti (RU/s) |Sleva za rezervaci u RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina - střed USA – sever  |   USA (střed) – sever  | 50,000  | 50,000  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ  |  USA – západ   |  50,000  |  50,000 |

**Scénář 2**

Například pokud potřebujete nasazení služby Azure Cosmos DB v oblastech "Francie – jih" a "Austrálie – střed 2", a pokud každá oblast má spotřeby propustnost 50 000 RU/s. Nákup rezervace 100 000 RU/s by byly použitelné následujícím způsobem (za předpokladu, že využití Austrálie – střed 2 se slevou nejprve):

|Popis měřiče | Oblast |Využití propustnosti (RU/s) |Sleva za rezervaci u RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2  |  Austrálie – střed 2   |  50,000  |  50,000   |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih  |  Francie – jih   |  50,000 |  15,384  |

50 000 jednotek využití v oblasti "Austrálie – střed 2" odpovídá 75 000 RU/s fakturovatelné využití (nebo normalizované využití). Tato hodnota je vypočítán jako (využití propustnosti * reservation_discount_ratio_for_that_region) který se rovná 75 000 RU/s (Tato hodnota se vypočítává jako: 50 000 * 1.5 = 75 000 RU/s) fakturovatelné nebo normalizované využití. 

100 000 RU/s nákup rezervace by posun 75 000 RU/s v "Austrálie – střed 2" a "Francie – jih" oblast opustí 25 000 RU/s. Z zbývajících 25 000 RU/s, sleva za rezervaci 15,384 RU/s (Tato hodnota se vypočítává jako: 25 000 / 1.625 = 15,384 RU/s) se použije pro oblasti "Francie – jih". Zbývající 34,616 RU/s v oblasti "Francie – jih" se účtuje za běžné sazby průběžných plateb. 

Azure fakturační systém přiřadí sníženou sazbou za rezervaci pro první instanci, která je zpracována který by odpovídal rezervace konfigurace (například Austrálie – střed 2 v tomto případě).

K pochopení a zobrazení aplikace Azure rezervace na fakturaci využití sestav, naleznete v tématu [pochopit Azure rezervace využití](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

* [Co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md)  
* [Předplatíte prostředky Azure Cosmos DB pomocí služby Azure Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity](../sql-database/sql-database-reserved-capacity.md)  
* [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)  
* [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

