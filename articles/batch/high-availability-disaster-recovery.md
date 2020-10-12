---
title: Vysoká dostupnost a zotavení po havárii
description: Naučte se navrhovat aplikace Batch pro regionální výpadky.
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 1e22cb19aba1dcedc4ece7ddc2d1de0ab3233238
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83725733"
---
# <a name="design-your-application-for-high-availability"></a>Návrh aplikace pro zajištění vysoké dostupnosti

Azure Batch je místní služba. Batch je k dispozici ve všech oblastech Azure, ale při vytvoření účtu Batch musí být přidružený k oblasti. Všechny operace pro účet Batch se pak vztahují na tuto oblast. Například fondy a přidružené virtuální počítače se vytvoří ve stejné oblasti jako účet Batch.

Při navrhování aplikace, která používá dávku, je nutné vzít v úvahu možnost, že dávka nebude v oblasti dostupná. Je možné narazit na vzácnou situaci, kdy došlo k potížím s oblastí jako s celou, celou službou Batch v této oblasti nebo problémem s konkrétním účtem Batch.

Pokud je aplikace nebo řešení využívající dávku vždycky k dispozici, musí být navržená tak, aby převzetí služeb při selhání do jiné oblasti nebo aby trvalo rozdělení úloh mezi dvě nebo více oblastí. Oba přístupy vyžadují aspoň dva účty Batch, přičemž každý účet se nachází v jiné oblasti.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Několik účtů Batch ve více oblastech

Použití více účtů Batch v různých oblastech umožňuje, aby vaše aplikace pokračovala i v případě, že účet Batch v jiné oblasti nebude k dispozici. Používání více účtů je obzvláště důležité, pokud vaše aplikace musí být vysoce dostupná.

V některých případech může být aplikace navržena tak, aby vždy používala dvě nebo více oblastí. Pokud například potřebujete značnou kapacitu, může být potřeba použít více oblastí pro zpracování rozsáhlých aplikací nebo zařízení pro budoucí nárůst.

## <a name="design-considerations-for-providing-failover"></a>Faktory návrhu pro poskytování převzetí služeb při selhání

Klíčovým bodem, který je třeba vzít v úvahu při převzetí služeb při selhání v alternativní oblasti, je, že je nutné vzít v úvahu všechny komponenty v řešení; Stačí jenom druhý účet Batch. Ve většině aplikací služby Batch se například vyžaduje účet Azure Storage, přičemž účet úložiště a účet Batch musí být ve stejné oblasti, aby se přijatelný výkon.

Při navrhování řešení, které je možné převzetí služeb při selhání, vezměte v úvahu následující body:

- Předem vytvořte všechny požadované účty v každé oblasti, například účet Batch a účet úložiště. Často se neúčtují žádné poplatky za vytváření účtů, a to jenom v případě, že jsou uložená nějaká data nebo se účet používá.
- Zajistěte, aby byly u účtů předem nastavené kvóty, takže můžete přidělit požadovaný počet jader pomocí účtu Batch.
- K automatizaci nasazení aplikace v oblasti použijte šablony nebo skripty.
- Udržujte binární soubory aplikace a referenční data ve všech oblastech. Když budete mít aktuální čas, zajistíte tak, aby se oblast online rychle nemusela čekat na nahrávání a nasazování souborů. Pokud je například vlastní aplikace k instalaci na uzlech fondu uložena a odkazována pomocí balíčků aplikací služby Batch, pak při vytváření nové verze aplikace by se měla odeslat na každý účet Batch a na který odkazuje konfigurace fondu (nebo se nová verze nastaví jako výchozí).
- V aplikaci, která volá službu Batch, úložiště a jakékoli další služby, můžete snadno přesměrovat klienty nebo zatížení do jiné oblasti.
- Osvědčeným postupem, jak zajistit úspěšné převzetí služeb při selhání, je často přepnutí do alternativní oblasti v rámci běžné operace. Například u dvou nasazení v samostatných oblastech se každý měsíc přepínáním na alternativní oblast.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření účtů Batch pomocí [Azure Portal](batch-account-create-portal.md), [Azure CLI](cli-samples.md), [PowerShellu](batch-powershell-cmdlets-get-started.md)nebo [rozhraní API pro správu služby Batch](batch-management-dotnet.md).
- Výchozí kvóty jsou přidruženy k účtu Batch. [Tento článek](batch-quota-limit.md) obsahuje podrobnosti o výchozích hodnotách kvót a popisuje, jak lze kvóty zvýšit.
