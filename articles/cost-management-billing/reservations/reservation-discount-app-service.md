---
title: Sleva za rezervace pro Azure App Service
description: Přečtěte si, jak se slevy za rezervace vztahují na instance Azure App Service Premium v3 a na izolovaná razítka.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: banders
ms.openlocfilehash: c599c64ce4b22bbf7bece77602b22fef6629d07c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369726"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Jak se použijí slevy za rezervace na instance Azure App Service Premium v3 a izolovaná razítka

Tento článek vám pomůže pochopit, jak se slevy vztahují na instance Azure App Service Premium v3 a izolovaná razítka.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Jak se slevy za rezervace vztahují na instance Premium V3

Po zakoupení rezervované instance Azure App Service Premium V3 se sleva rezervace automaticky použije pro App Service instance, které odpovídají atributům a množstvím rezervace. Rezervace pokrývá náklady na instance Premium v3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Způsob použití slevy na Azure App Service 

Slevou rezervace je *použití-IT-nebo-ztráta*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.
Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

### <a name="reservation-discount-for-premium-v3-instances"></a>Sleva rezervace pro instance Premium V3

Sleva za rezervované instance Azure se používá na každou hodinu na běhu instancí Premium v3. Rezervace, které jste zakoupili, odpovídají využití vygenerovaného běžícími instancemi Premium V3 za účelem uplatnění slevy rezervace. V případě instancí Premium v3, které nemusí běžet celou hodinu, se rezervace vyplní jinými instancemi, které nepoužívají rezervaci, včetně souběžně spuštěných instancí. Na konci hodiny je aplikace rezervace pro instance v této hodině uzamčena. V případě, že instance neběží po hodinu nebo souběžných instancích během hodiny, neplní hodinu rezervace, rezervace se pro tuto hodinu nepoužívá. Uplatňování rezervace na fakturovatelné využití virtuálních počítačů znázorňuje následující graf. Graf počítá s jednou zakoupenou rezervací a dvěma vyhovujícími instancemi virtuálních počítačů.

![Obrázek znázorňující použití rezervace k fakturovatelnámu využití virtuálních počítačů](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Každé využití nad limit rezervace se účtuje pomocí standardních sazeb pro průběžné platby. Za využití do limitu rezervace se vám neúčtují žádné poplatky, protože už jste toto využití zaplatili při zakoupení rezervace.
2.  Za 1. hodinu běží instance 1 po dobu 0,75 hodiny a instance 2 po dobu 0,5 hodiny. Celkové využití za 1. hodinu je 1,25 hodiny. Za 0,25 hodiny nad limit se vám naúčtuje běžná sazba pro průběžné platby.
3.  Za 2. a 3. hodinu běží obě instance po dobu 1 hodiny. Jednu instanci pokryje rezervace a u druhé se vám naúčtuje sazba pro průběžné platby.
4.  Za 4. hodinu běží instance 1 po dobu 0,5 hodiny a instance 2 po dobu 1 hodiny. Instanci 1 plně pokryje rezervace a 0,5 hodiny chodu instance 2 také. Za 0,5 hodiny nad limit se vám naúčtuje běžná sazba pro průběžné platby.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Jak se použijí slevy rezervace na izolovaná razítka

Když si zakoupíte rezervovanou kapacitu s poplatkem za kolek služby App Service v izolovaném prostředí, na poplatek za kolek v určité oblasti se automaticky uplatní sleva za rezervaci. Sleva za rezervaci se uplatňuje na využití naměřené měřičem pro poplatky za izolovaný kolek. Pracovní procesy, další front-endy a veškeré další prostředky přidružené ke kolku se dál fakturují pomocí běžné sazby.

### <a name="reservation-discount-application"></a>Uplatnění slevy za rezervaci

Sleva za kolek služby App Service v izolovaném prostředí se na spuštěné izolované kolky uplatňuje po hodinách. Pokud nemáte během některé hodiny nasazený žádný kolek, zůstane rezervovaná kapacita pro danou hodinu nevyužitá. Nepřevádí se.

Po nákupu se zakoupená rezervace přiřadí k izolovanému kolku běžícímu v určité oblasti. Pokud tento kolek vypnete, začnou se slevy za rezervaci automaticky uplatňovat na jiné kolky spuštěné v této oblasti. Pokud žádné takové kolky neexistují, použije se rezervace na následující kolek vytvořený v dané oblasti.

Pokud kolky neběží celou hodinu, rezervace se po zbytek hodiny automaticky použije na jiné vyhovující kolky ve stejné oblasti.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Výběr typu kolku – Windows nebo Linux

Prázdný izolovaný kolek standardně vytváří měřič kolku pro Windows. To platí i v případě, že nejsou nasazené žádné pracovní procesy. Když dojde k nasazení pracovních procesů pro Windows, vytváří tento měřič dál. Pokud nasadíte pracovní proces pro Linux, změní se měřič na měřič kolku pro Linux. Když jsou nasazené pracovní procesy pro Linux i Windows, vytváří kolek měřič pro Windows.

V důsledku toho se může měřič kolku v průběhu existence kolku změnit z měřiče pro Windows na měřič pro Linux. Oproti tomu rezervace platí jen na konkrétní operační systém. Budete si muset koupit rezervaci podporující pracovní procesy, které se chystáte v kolku nasadit. Kolky jenom pro Windows a smíšené kolky využívají rezervaci pro Windows. Kolky obsahující jenom pracovní procesy pro Linux využívají rezervaci pro Linux.

Rezervaci pro Linux si kupte jenom v případě, že se chystáte používat v kolku _výhradně_ pracovní procesy pro Linux.

### <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervované instance poplatků za izolované kolky v závislosti na druhu nasazení.

- **Příklad 1:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde nejsou žádné kolky služby App Service v izolovaném prostředí. Do této oblasti nasadíte nový kolek a budete za tento kolek platit sazbu za rezervaci.
- **Příklad 2:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde už je nasazený jeden kolek služby App Service v izolovaném prostředí. Na nasazený kolek se začne uplatňovat sazba za rezervaci.
- **Příklad 3:** Zakoupíte si jednu instanci rezervované kapacity izolovaného kolku v oblasti, kde už máte nasazený jeden kolek služby App Service v izolovaném prostředí. Na nasazený kolek se začne uplatňovat sazba za rezervaci. Později kolek odstraníte a nasadíte nový. Pro tento nový kolek získáte sazbu za rezervaci. Za období, kdy nejsou nasazené žádné kolky, se slevy nepřevádějí.
- **Příklad 4:** V určité oblasti si zakoupíte jednu instanci rezervované kapacity izolovaného kolku pro Linux a potom do této oblasti nasadíte nový kolek. Při počátečním nasazení kolku bez pracovních procesů se vytvoří měřič kolku pro Windows. Nezískáte žádnou slevu. Při nasazení prvního pracovního procesu pro Linux do tohoto kolku se vytvoří měřič kolku pro Linux a začne se uplatňovat sleva za rezervaci. Pokud se do kolku později nasadí pracovní proces pro Windows, nahradí se měřič kolku měřičem kolku pro Windows. Už se vám nebude uplatňovat sleva za rezervaci rezervované kapacity izolovaného kolku pro Linux.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](manage-reserved-vm-instance.md).
- Další informace o předprodejní App Service Premium v3 a vyhrazené kapacitě rezervovaných razítek za účelem úspory peněz najdete v tématu [platba za Azure App Service s rezervovanou kapacitou](prepay-app-service.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](manage-reserved-vm-instance.md)
  - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
