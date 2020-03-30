---
title: Vysoká dostupnost a zotavení po havárii – Azure Batch
description: Přečtěte si, jak navrhnout dávkovou aplikaci pro regionální výpadek. Úlohy by měly být převzetí služeb při selhání do jiné oblasti nebo rozdělit mezi dvě nebo více oblastí.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026077"
---
# <a name="design-your-application-for-high-availability"></a>Návrh aplikace pro zajištění vysoké dostupnosti

Azure Batch je místní služba. Dávka je dostupná ve všech oblastech Azure, ale když je vytvořen účet Batch, musí být přidružena k oblasti. Všechny operace pro účet Batch pak platí pro tuto oblast. Například fondy a přidružené virtuální počítače (VM) jsou vytvořeny ve stejné oblasti jako účet Batch.

Při navrhování aplikace, která používá Batch, je třeba zvážit možnost Batch není k dispozici v oblasti. Je možné se setkat s vzácnou situací, kdy dojde k potížím s oblastí jako celkem, s celou službou Batch v oblasti nebo s problémem s konkrétním účtem Batch.

Pokud aplikace nebo řešení pomocí Batch vždy musí být k dispozici, pak by měl být navržen tak, aby převzetí služeb při selhání do jiné oblasti nebo vždy mít pracovní vytížení rozdělit mezi dvě nebo více oblastí. Oba přístupy vyžadují alespoň dva účty Batch, přičemž každý účet se nachází v jiné oblasti.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Více dávkových účtů ve více oblastech

Použití více účtů Batch v různých oblastech umožňuje aplikaci pokračovat v běhu, pokud účet Batch v jiné oblasti nebude k dispozici. Použití více účtů je zvláště důležité, pokud vaše aplikace musí být vysoce dostupné.

V některých případech aplikace může být navržen tak, aby vždy používat dvě nebo více oblastí. Například pokud potřebujete značné množství kapacity, může být potřeba použít více oblastí pro zpracování rozsáhlé aplikace nebo pro budoucí růst.

## <a name="design-considerations-for-providing-failover"></a>Aspekty návrhu pro poskytování převzetí služeb při selhání

Klíčovým bodem, který je třeba zvážit při poskytování možnosti převzetí služeb při selhání v alternativní oblasti, je, že je třeba zvážit všechny součásti v řešení; nestačí mít pouze druhý účet Batch. Například ve většině dávkových aplikací je vyžadován účet úložiště Azure, přičemž účet úložiště a účet Batch musí být ve stejné oblasti pro přijatelný výkon.

Při navrhování řešení, které může převzetí služeb při selhání, zvažte následující body:

- Předem vytvořte všechny požadované účty v každé oblasti, jako je například účet Batch a účet úložiště. Často není žádný poplatek za vytvoření účtů, pouze pokud jsou uložena data nebo je použit účet.
- Ujistěte se, že kvóty jsou nastaveny na účtech předem, takže můžete přidělit požadovaný počet jader pomocí účtu Batch.
- Pomocí šablon nebo skriptů automatizujte nasazení aplikace v oblasti.
- Udržujte binární soubory aplikace a referenční data aktuální ve všech oblastech. Udržování aktuálního stavu zajistí, že region bude možné rychle zprovoznit, aniž byste museli čekat na nahrávání a nasazování souborů. Pokud je například vlastní aplikace pro instalaci do uzlů fondu uložena a odkazována pomocí balíčků aplikací Batch, měla by být při vytvoření nové verze aplikace odeslána do každého účtu Batch a odkazována konfigurací fondu (nebo nastavit novou verzi jako výchozí).
- V aplikaci volání Batch, úložiště a další služby, snadno přepnout klienty nebo zatížení do jiné oblasti.
- Osvědčeným postupem k zajištění převzetí služeb při selhání bude úspěšné, je často přepnutí do alternativní oblasti jako součást běžného provozu. Například se dvěma nasazeními v samostatných oblastech přepnete každý měsíc do alternativní oblasti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vytváření dávkových účtů pomocí [portálu Azure](batch-account-create-portal.md), [Rozhraní příkazového příkazu Azure](cli-samples.md), Prostředí [PowerShell](batch-powershell-cmdlets-get-started.md)nebo [rozhraní API pro správu dávek](batch-management-dotnet.md).
- Výchozí kvóty jsou přidruženy k účtu Batch; [Tento článek](batch-quota-limit.md) podrobně popisuje výchozí hodnoty kvót a popisuje, jak lze kvóty zvýšit.
