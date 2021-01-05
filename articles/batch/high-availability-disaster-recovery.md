---
title: Vysoká dostupnost a zotavení po havárii
description: Naučte se navrhovat aplikace Batch pro regionální výpadky.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831002"
---
# <a name="design-your-batch-application-for-high-availability"></a>Návrh aplikace Batch pro vysokou dostupnost

Azure Batch je k dispozici ve všech oblastech Azure, ale při vytvoření účtu Batch musí být přidružený k jedné konkrétní oblasti. Všechny operace pro účet Batch se pak vztahují na tuto oblast. Například fondy a přidružené virtuální počítače se vytvoří ve stejné oblasti jako účet Batch.

Při navrhování aplikace, která používá dávku, je nutné vzít v úvahu možnost, že dávka nebude v oblasti dostupná. Je možné narazit na vzácnou situaci, kdy došlo k potížím s oblastí jako s celou, celou službou Batch v oblasti nebo vaším konkrétním účtem Batch.

Pokud je aplikace nebo řešení využívající dávku vždycky k dispozici, musí být navržená tak, aby převzetí služeb při selhání do jiné oblasti nebo aby trvalo rozdělení úloh mezi dvě nebo více oblastí. Oba přístupy vyžadují aspoň dva účty Batch, přičemž každý účet se nachází v jiné oblasti.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Několik účtů Batch ve více oblastech

Použití několika účtů Batch v různých oblastech umožňuje, aby vaše aplikace pokračovala i v případě, že účet Batch v jedné oblasti nebude k dispozici. Pokud vaše aplikace musí být vysoce dostupná, je potřeba mít několik účtů obzvláště důležité.

V některých případech mohou být aplikace navrženy tak, aby záměrně používaly dvě nebo více oblastí. Pokud například potřebujete značnou kapacitu, může být potřeba použít více oblastí pro zpracování rozsáhlých aplikací nebo zařízení pro budoucí nárůst. Tyto aplikace budou také vyžadovat několik účtů Batch (jeden pro každou oblast).

## <a name="design-considerations-for-providing-failover"></a>Faktory návrhu pro poskytování převzetí služeb při selhání

Když zadáváte možnost převzetí služeb při selhání v alternativní oblasti, je potřeba vzít v úvahu všechny komponenty v řešení; Stačí jenom druhý účet Batch. Ve většině aplikací služby Batch se například vyžaduje účet Azure Storage, přičemž účet úložiště a účet Batch musí být ve stejné oblasti, aby se přijatelný výkon.

Při navrhování řešení, které je možné převzetí služeb při selhání, vezměte v úvahu následující body:

- Předem vytvořte všechny požadované účty v každé oblasti, například účet Batch a účet úložiště. Neúčtují se vám často žádné poplatky za vytváření účtů a účtují se poplatky jenom při použití účtu nebo při uložení dat.
- Ujistěte se, že jsou na všech účtech předem nastavené odpovídající [kvóty](batch-quota-limit.md) , takže můžete přidělit požadovaný počet jader pomocí účtu Batch.
- K automatizaci nasazení aplikace v oblasti použijte šablony nebo skripty.
- Udržujte binární soubory aplikace a referenční data ve všech oblastech. Když budete mít aktuální čas, zajistíte tak, aby se oblast online rychle nemusela čekat na nahrávání a nasazování souborů. Pokud je například vlastní aplikace k instalaci na uzlech fondu uložena a odkazována pomocí balíčků aplikací služby Batch, pak při vytváření nové verze aplikace by se měla odeslat na každý účet Batch a na který odkazuje konfigurace fondu (nebo se nová verze nastaví jako výchozí).
- V aplikaci, která volá službu Batch, úložiště a jakékoli další služby, usnadňuje přepínání klientů nebo zatížení do různých oblastí.
- V rámci normálního fungování zvažte možnost často přepínat do alternativní oblasti. Například u dvou nasazení v samostatných oblastech můžete každý měsíc přepnout do alternativní oblasti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření účtů Batch pomocí [Azure Portal](batch-account-create-portal.md), [Azure CLI](./scripts/batch-cli-sample-create-account.md), [PowerShellu](batch-powershell-cmdlets-get-started.md)nebo [rozhraní API pro správu služby Batch](batch-management-dotnet.md).
- Přečtěte si o [výchozích kvótách přidružených k účtu Batch](batch-quota-limit.md) a o tom, jak se kvóty dají zvýšit.
