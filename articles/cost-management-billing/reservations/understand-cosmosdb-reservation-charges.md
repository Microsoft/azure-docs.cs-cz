---
title: Vysvětlení slevy za rezervaci v Azure Cosmos DB
description: Přečtěte si, jak se na zřízenou propustnost (RU/s) ve službě Azure Cosmos DB uplatňuje sleva za rezervaci.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023138"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Vysvětlení uplatňování slevy za rezervaci ve službě Azure Cosmos DB

Když si zakoupíte rezervovanou kapacitu služby Azure Cosmos DB, na prostředky Azure Cosmos DB, které vyhovují rezervovaným atributům a množství, se automaticky uplatňuje sleva za rezervaci. Rezervace se vztahuje na propustnost zřízenou pro prostředky Azure Cosmos DB. Nevztahuje se na software, sítě, úložiště ani poplatky za předdefinované kontejnery.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Sleva na rezervaci uplatňovaná v účtech Azure Cosmos DB

Sleva za rezervaci se na [zřízenou propustnost](../../cosmos-db/request-units.md) uplatňuje na základě počtu jednotek žádosti za sekundu (RU/s), a to po hodinách. V případě prostředků Azure Cosmos DB, které neběží celou hodinu, se sleva za rezervaci automaticky uplatní na jiné prostředky Cosmos DB, které vyhovují atributům rezervace. Sleva se může uplatnit i na prostředky Azure Cosmos DB, které jsou spuštěné souběžně. Pokud nemáte prostředky Cosmos DB, které běží celou hodinu a odpovídají atributům rezervace, nezískáte pro danou hodinu celou výhodu slevy za rezervaci.

Slevy mají několik úrovní. Rezervace s vyšším počtem jednotek žádostí poskytují vyšší slevy.

Po zakoupení rezervace se slevy uplatňují na všechny oblasti s koeficientem odpovídajícím cenám na vyžádání v příslušné oblasti. Koeficienty slev za rezervace pro každou oblast najdete v části [Sleva za rezervaci podle oblastí](#reservation-discount-per-region) v tomto článku.

## <a name="reservation-discount-per-region"></a>Sleva za rezervaci podle oblastí
Sleva za rezervaci se uplatňuje na poplatky za propustnost služby Azure Cosmos DB po hodinách. Používá se buď v rámci jednoho předplatného, nebo v rozsahu určité smlouvy/účtu. Sleva za rezervaci se na naměřené využití v různých oblastech uplatňuje s následujícími koeficienty:

|Popis měřiče  |Oblast |Koeficient  |
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
|Azure Cosmos DB – 100 RU/s/hodina – USA (střed) – sever|   USA (střed) – sever      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ|   USA – západ      |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – střed| USA – střed        |     1    |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ 2|   USA – západ 2      |      1   |
|Azure Cosmos DB – 100 RU/s/hodina – USA – středozápad|   USA – středozápad      |       1  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – východ|   USA – východ      |  1       |
|Azure Cosmos DB – 100 RU/s/hodina – JAR – sever|     JAR – sever    |   1      |
|Azure Cosmos DB – 100 RU/s/hodina – JAR – západ |    JAR – západ      |    1     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – jih|    Indie – jih     |    1,0375    |
|Azure Cosmos DB – 100 RU/s/hodina – Kanada – východ|   Kanada – východ      |    1.1      |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – východ|   Japonsko – východ      |    1,125     |
|Azure Cosmos DB – 100 RU/s/hodina – Japonsko – západ|     Japonsko – západ    |   1,125       |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – západ|     Indie – západ    |    1,1375     |
|Azure Cosmos DB – 100 RU/s/hodina – Indie – střed|    Indie – střed     |  1,1375       |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – východ|     Austrálie – východ    |   1,15       |
|Azure Cosmos DB – 100 RU/s/hodina – Kanada – střed|  Kanada – střed       |   1.2       |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – střed|   Francie – střed      |    1,25      |
|Azure Cosmos DB – 100 RU/s/hodina – Brazílie – jih|  Brazílie – jih       |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed|   Austrálie – střed      |   1,5      |
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2| Austrálie – střed 2        |    1,5     |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih|    Francie – jih     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scénáře představující uplatňování slevy za rezervaci

Budeme vycházet z těchto požadavků na rezervaci:

* Požadovaná propustnost: 50 000 RU/s  
* Použité oblasti: 2

V tomto případě se budou účtovat celkové poplatky za vyžádaných 500 měřičů po 100 RU/s v těchto dvou oblastech. Celková spotřeba RU/s je 100 000 za hodinu.

**Scénář 1**

Předpokládejme, že potřebujete nasazení Azure Cosmos DB v oblastech USA (střed) – sever a USA – západ. Každá oblast má spotřebu propustnosti 50 000 RU/s. Když zakoupíte rezervaci 100 000 RU/s, zcela pokryje všechny poplatky za vyžádání.

Sleva, kterou rezervace nabízí, se počítá takto: spotřeba_propustnosti * koeficient_slevy_za_rezervaci_pro_danou_oblast. U oblastí USA (střed) – sever a USA – západ se používá koeficient slevy za rezervaci 1. Celkový počet zlevněných RU/s tedy činí 100 000. K této hodnotě dojdeme takto: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Z toho vyplývá, že nemusíte platit žádné další poplatky s běžnými sazbami pro průběžné platby.

|Popis měřiče | Oblast |Spotřeba propustnosti (RU/s) |Sleva za rezervaci uplatněná na RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – USA (střed) – sever  |   USA (střed) – sever  | 50 000  | 50 000  |
|Azure Cosmos DB – 100 RU/s/hodina – USA – západ  |  USA – západ   |  50 000  |  50 000 |

**Scénář 2**

Předpokládejme, že potřebujete nasazení Azure Cosmos DB v oblastech Austrálie – střed 2 a Francie – jih. Každá oblast má spotřebu propustnosti 50 000 RU/s. Když zakoupíte rezervaci 100 000 RU/s, uplatní se následujícím způsobem (za předpokladu, že se sleva nejdříve použila na využití v oblasti Austrálie – střed 2):

|Popis měřiče | Oblast |Spotřeba propustnosti (RU/s) |Sleva za rezervaci uplatněná na RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hodina – Austrálie – střed 2  |  Austrálie – střed 2   |  50 000  |  50 000   |
|Azure Cosmos DB – 100 RU/s/hodina – Francie – jih  |  Francie – jih   |  50 000 |  15 384  |

Využití 50 000 jednotek v oblasti Austrálie – střed 2 odpovídá 75 000 RU/s fakturovatelného využití (neboli normalizovaného využití). Tato hodnota se vypočítá takto: spotřeba_propustnosti * koeficient_slevy_za_rezervaci_pro_danou_oblast. Výpočtem dojdeme k výsledku 75 000 RU/s fakturovatelného neboli normalizovaného využití. K této hodnotě dojdeme takto: 50 000 * 1,5 = 75 000 RU/s.

Zakoupená rezervace 100 000 RU/s by pokryla celých 75 000 RU/s v oblasti Austrálie – střed 2. Pro oblast Francie – jih tak zbude 25 000 RU/s. Ze zbylých 25 000 RU/s se na oblast Francie – jih uplatní sleva za rezervaci ve výši 15 384 RU/s. K hodnotě slevy dojdeme takto: 25 000 / 1,625 = 15 384 RU/s. Zbývající 34 616 RU/s v oblasti Francie – jih se naúčtuje podle běžných sazeb pro průběžné platby.

Fakturační systém Azure přiřadí fakturační výhodu za rezervaci k první zpracovávané instanci, která vyhovuje konfiguraci rezervace. V tomto případě je to oblast Austrálie – střed 2.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace v Azure](save-compute-costs-reservations.md)  
* [Předplacení prostředků Azure Cosmos DB pomocí rezervované kapacity služby Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)  
* [Správa rezervací v Azure](manage-reserved-vm-instance.md)  
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)  
* [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
* [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)