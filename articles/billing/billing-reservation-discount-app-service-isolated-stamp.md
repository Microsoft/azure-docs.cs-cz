---
title: Způsob uplatňování slev za rezervaci na kolky služby App Service v izolovaném prostředí
description: Zjistěte, jak se na kolky služby App Service v izolovaném prostředí uplatňují slevy za rezervaci.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "68298253"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Způsob uplatňování slev za rezervaci na kolky služby App Service v izolovaném prostředí

Když si zakoupíte rezervovanou kapacitu s poplatkem za kolek služby App Service v izolovaném prostředí, na poplatek za kolek v určité oblasti se automaticky uplatní sleva za rezervaci. Sleva za rezervaci se uplatňuje na využití naměřené měřičem pro poplatky za izolovaný kolek. Pracovní procesy, další front-endy a veškeré další prostředky přidružené ke kolku se dál fakturují pomocí běžné sazby.

## <a name="reservation-discount-application"></a>Uplatnění slevy za rezervaci

Sleva za kolek služby App Service v izolovaném prostředí se na spuštěné izolované kolky uplatňuje po hodinách. Pokud nemáte během některé hodiny nasazený žádný kolek, zůstane rezervovaná kapacita pro danou hodinu nevyužitá. Nepřevádí se.

Po nákupu se zakoupená rezervace přiřadí k izolovanému kolku běžícímu v určité oblasti. Pokud tento kolek vypnete, začnou se slevy za rezervaci automaticky uplatňovat na jiné kolky spuštěné v této oblasti. Pokud žádné takové kolky neexistují, použije se rezervace na následující kolek vytvořený v dané oblasti.

Pokud kolky neběží celou hodinu, rezervace se po zbytek hodiny automaticky použije na jiné vyhovující kolky ve stejné oblasti.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Výběr typu kolku – Windows nebo Linux

Prázdný izolovaný kolek standardně vytváří měřič kolku pro Windows. To platí i v případě, že nejsou nasazené žádné pracovní procesy. Když dojde k nasazení pracovních procesů pro Windows, vytváří tento měřič dál. Pokud nasadíte pracovní proces pro Linux, změní se měřič na měřič kolku pro Linux. Když jsou nasazené pracovní procesy pro Linux i Windows, vytváří kolek měřič pro Windows.

V důsledku toho se může měřič kolku v průběhu existence kolku změnit z měřiče pro Windows na měřič pro Linux. Oproti tomu rezervace platí jen na konkrétní operační systém. Budete si muset koupit rezervaci podporující pracovní procesy, které se chystáte v kolku nasadit. Kolky jenom pro Windows a smíšené kolky využívají rezervaci pro Windows. Kolky obsahující jenom pracovní procesy pro Linux využívají rezervaci pro Linux.

Rezervaci pro Linux si kupte jenom v případě, že se chystáte používat v kolku _výhradně_ pracovní procesy pro Linux.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervované instance poplatků za izolované kolky v závislosti na druhu nasazení.

- **Příklad 1:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde nejsou žádné kolky služby App Service v izolovaném prostředí. Do této oblasti nasadíte nový kolek a budete za tento kolek platit sazbu za rezervaci.
- **Příklad 2:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde už je nasazený jeden kolek služby App Service v izolovaném prostředí. Na nasazený kolek se začne uplatňovat sazba za rezervaci.
- **Příklad 3:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde už máte nasazený jeden kolek služby App Service v izolovaném prostředí. Na nasazený kolek se začne uplatňovat sazba za rezervaci. Později kolek odstraníte a nasadíte nový. Pro tento nový kolek získáte sazbu za rezervaci. Za období, kdy nejsou nasazené žádné kolky, se slevy nepřevádějí.
- **Příklad 4:** V určité oblasti si zakoupíte jednu instanci rezervované kapacity izolovaného kolku pro Linux a potom do této oblasti nasadíte nový kolek. Při počátečním nasazení kolku bez pracovních procesů se vytvoří měřič kolku pro Windows. Nezískáte žádnou slevu. Při nasazení prvního pracovního procesu pro Linux do tohoto kolku se vytvoří měřič kolku pro Linux a začne se uplatňovat sleva za rezervaci. Pokud se do kolku později nasadí pracovní proces pro Windows, nahradí se měřič kolku měřičem kolku pro Windows. Už se vám nebude uplatňovat sleva za rezervaci rezervované kapacity izolovaného kolku pro Linux.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervaci, najdete v tématu [Správa rezervací Azure](billing-manage-reserved-vm-instance.md).
- Další informace o tom, jak nakoupit rezervovanou kapacitu kolku služby App Service v izolovaném prostředí předem a ušetřit peníze, najdete v tématu věnovaném [předplacení poplatků za kolky služby App Service v izolovaném prostředí s rezervovanou kapacitou](billing-prepay-app-service-isolated-stamp.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
