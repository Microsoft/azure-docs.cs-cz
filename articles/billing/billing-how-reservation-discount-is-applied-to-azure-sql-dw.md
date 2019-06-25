---
title: Jak použít slevy na rezervaci do služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Přečtěte si, jak použít slevy na rezervaci pro Azure SQL Data Warehouse a šetřit peníze je tak.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918358"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Jak použít slevy na rezervaci do služby Azure SQL Data Warehouse

Po nákupu Azure SQL Data Warehouse vyhrazené kapacity sleva za rezervaci se automaticky využije na datových skladů, které existují v dané oblasti. Sleva za rezervaci se vztahuje na využití, protože ho vygeneroval měřiče cDWU SQL Data Warehouse. Úložiště a sítě se účtují sazbami průběžných plateb.

## <a name="reservation-discount-application"></a>Slev za rezervaci

Sleva za rezervované kapacity SQL Data Warehouse se využije ke spuštěnému sklady po hodinách. Pokud nemáte skladu nasazeny hodinu pro určitou hodinu nevyužité záložní kapacitu. To se nepřenáší.

Po nákupu rezervace, které můžete zakoupit odpovídá SQL Data Warehouse využití generované s spuštěním sklady v libovolném bodě v čase. Vypnout některých skladů pak slevy na rezervaci automaticky platit pro všechny odpovídající sklady.

Pro skladů, které nelze spustit pro celou hodinu rezervaci se automaticky využije na odpovídající jindy během této hodiny.

## <a name="discount-examples"></a>Slevy příklady

Následující příklady ukazují, jak SQL Data Warehouse záložní kapacitu sleva, v závislosti na nasazení.

- **Příklad 1**: Můžete si koupit 5 jednotky 100 cDWU vyhrazené kapacity. Spustit instanci DW1500c SQL Data Warehouse je jedna hodina. V takovém případě je vygenerován využití za 15 jednotky 100 cDWU využití. Sleva za rezervaci se vztahuje na 5 jednotek, které jste použili. Bude se vám účtovat podle sazeb s průběžnými platbami pro zbývající 10 jednotek 100 cDWU využití, který jste použili.

- **Příklad 2**: Můžete si koupit 5 jednotky 100 cDWU vyhrazené kapacity. Spusťte dvě instance DW100c SQL Data Warehouse je jedna hodina. V tomto případě jsou emitovány dvě události využití pro 1 jednotku 100 cDWU využití. Jak událostí využití získat slevy záložní kapacitu. Zbývající 3 jednotky 100 cDWU vyhrazené kapacity jsou ztraceny a není přenesou pro budoucí použití.

- **Příklad 3**: 1 jednotka 100 cDWU vyhrazené kapacity nákupu. Spustíte dvě instance DW100c SQL Data Warehouse. Každá běží po dobu 30 minut. V takovém případě obou událostí použití získat slevy záložní kapacitu. Bez použití se účtuje podle sazeb s průběžnými platbami.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

- Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Zobrazit transakce rezervace](billing-view-reservations.md)
- [Získejte rezervace transakce a využití prostřednictvím rozhraní API](billing-reservation-apis.md)
- [Správa rezervací](billing-manage-reserved-vm-instance.md)
