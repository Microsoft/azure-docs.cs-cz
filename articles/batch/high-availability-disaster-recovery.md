---
title: Vysoká dostupnost a zotavení po havárii – Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak navrhovat aplikace Batch pro k oblastnímu výpadku
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252381"
---
# <a name="design-your-application-for-high-availability"></a>Návrh aplikace pro zajištění vysoké dostupnosti

Služba Azure Batch je místní služba. Služba batch je dostupná ve všech oblastech Azure, ale při vytváření účtu Batch musí být přidružené k oblasti. Všechny operace pro účet Batch se pak použít pro tuto oblast. Fondy a přidružených virtuálních počítačích (VM) se vytvoří ve stejné oblasti jako účet Batch.

Při návrhu aplikace, která používá služby Batch, je nutné zvážit možnost Batch není právě k dispozici v oblasti. Je možné se setkat výjimečné situace tam, kde je nějaký problém s oblastí jako celek, celý Batch služeb v oblasti, nebo o problém s konkrétní účet Batch.

Pokud aplikace nebo řešení pomocí služby Batch vždy musí být k dispozici, pak by se měly navrhovat buď převzetí služeb při selhání do jiné oblasti nebo stálý zatížení rozdělené mezi dva nebo víc oblastech. Oba přístupy vyžadují aspoň dva účty Batch, každý účet nacházet v jiné oblasti.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Více účtů Batch v několika oblastech

Použití více účtů Batch v různých oblastech poskytuje možnost pro vaši aplikaci dál běžet, pokud se účet Batch v jiné oblasti stane nedostupnou. Použití více účtů je obzvláště důležité, pokud vaše aplikace potřebuje k zajištění vysoké dostupnosti.

V některých případech mohou být aplikace navrženy, vždy používali dvou nebo více oblastech. Například když potřebujete značné množství kapacity, použití více oblastí může být potřeba k zpracování rozsáhlých aplikací nebo takový na budoucí růst.

## <a name="design-considerations-for-providing-failover"></a>Aspekty návrhu pro poskytování převzetí služeb při selhání

Zásadním aspektem vzít v úvahu při zadávání schopnost převzetí služeb při selhání do alternativní oblasti je, že všechny součásti v řešení potřeba považovat za; nestačí mít druhého účtu Batch. Ve většině aplikací služby Batch, je třeba, vyžaduje se účet úložiště a účet Batch museli být ve stejné oblasti, pro zvýšení výkonu účtu služby Azure storage.

Při návrhu řešení, která se dá převzetí služeb při selhání, zvažte následující body:

- Předem vytvořte všechny požadované účty v jednotlivých oblastech, jako je například účet Batch a účtu úložiště. Často není k dispozici žádné poplatky za účty vytvořené, jen když máte data uložená nebo účet Spustit jako se používá.
- Ujistěte se, že kvóty jsou nastavené u účtů předem, tak můžete přidělit požadovaný počet jader na účtu Batch pomocí.
- Pomocí šablony a/nebo skriptů k automatizaci nasazení aplikace v oblasti.
- Aktualizovat binární soubory aplikace a referenčních dat ve všech oblastech. Udržování aktuálnosti zajistí, že v oblasti můžete rychle převést online bez nutnosti čekat nahrání a nasazení souborů. Například pokud je vlastní aplikace k instalaci na uzly fondu uložené odkazovat pomocí balíčků aplikací Batch při je nová verze aplikace, ji by měl být odeslán do každého účtu Batch a odkazuje konfigurace fondu (nebo Ujistěte se, nová verze výchozí verze).
- V aplikaci služby Batch, storage a všech ostatních služeb poskytovaných, snadno přepnutí klientů nebo zatížení volání do jiné oblasti.
- Osvědčeným postupem zajistit, že bude úspěšná převzetí služeb při selhání je často přepnutí do alternativní oblasti jako součást normálního provozu. Například s dvěma nasazeními v oblastech, přepnutí do alternativní oblasti každý měsíc.

## <a name="next-steps"></a>Další postup

- Další informace o vytváření účtů služby Batch pomocí [webu Azure portal](batch-account-create-portal.md), [rozhraní příkazového řádku Azure](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md), nebo [Batch rozhraní API pro správu](batch-management-dotnet.md).
- Výchozí kvóty jsou přidružené k účtu Batch; [v tomto článku](batch-quota-limit.md) podrobnosti výchozí kvóta hodnoty a popisuje, jak je možné zvýšit kvóty.
