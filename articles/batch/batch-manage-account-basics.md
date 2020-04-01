---
title: Správa účtu – Azure Batch | Dokumenty společnosti Microsoft
description: Informace o tom, co zahrnuje účet Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397268"
---
# <a name="manage-your-batch-account"></a>Správa dávkového účtu

Účet Batch je jednoznačně identifikovaná entita v rámci služby Batch. Veškeré zpracování je přidruženo k účtu Batch.

Účet Batch můžete vytvořit prostřednictvím webu [Azure Portal](batch-account-create-portal.md) nebo prostřednictvím programu, například s použitím [knihovny Batch Management .NET](batch-management-dotnet.md). Při vytváření účtu můžete přidružit účet úložiště Azure pro ukládání aplikací nebo vstupních a výstupních dat souvisejících s úlohami.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

## <a name="components-of-the-batch-account"></a>Součásti účtu Batch

Účet Batch umožňuje efektivně spouštět rozsáhlé paralelní a vysoce výkonné výpočetní úlohy (HPC) v Azure. V rámci účtu, který spravujete:

- Aplikace, které používáte

- Přidělení fondů a uzlů v rámci fondů

- Počet a typy úkolů 

- Vstup a výstup dat. Ke správě úkolů není nutné instalovat další software.

- Při vytváření účtu Dávka budete vyzváni k přiřazení názvu. Tento název je jeho ID a po přiřazení nelze změnit.

- Chcete-li změnit název účtu, musíte jej odstranit a vytvořit nový účet Batch.

- Účet se vytvoří v rámci předplatného, které chcete použít.

- Pomocí účtu můžete identifikovat a načíst klíče primárního a sekundárního účtu z libovolného účtu Batch v rámci předplatného.

- Účet udržuje informace o přidělení fondu a kvóty jádra.  

- Účet obsahuje informace o poloze.

- Účet identifikuje váš účet úložiště.

## <a name="next-steps"></a>Další kroky

- Vytvořte dávkový účet pomocí [portálu Azure](batch-account-create-portal.md).
- Vytvořte dávkový účet programově, například pomocí [knihovny Správy dávek .NET](batch-management-dotnet.md).
- [Konfigurace nebo zakázání vzdáleného přístupu k výpočetním uzlům ve fondu Azure Batch](pool-endpoint-configuration.md).
- [Spuštění úloh přípravy úloh a úloh y uvolnění úloh y dávkových výpočetních uzlů](batch-job-prep-release.md)

