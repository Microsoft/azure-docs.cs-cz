---
title: Správa vašeho účtu
description: Přečtěte si, co zahrnuje účet Azure Batch.
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83722962"
---
# <a name="manage-your-batch-account"></a>Správa účtu Batch

Účet Batch je jednoznačně identifikovaná entita v rámci služby Batch. Veškeré zpracování je přidruženo k účtu Batch.

Účet Batch můžete vytvořit prostřednictvím webu [Azure Portal](batch-account-create-portal.md) nebo prostřednictvím programu, například s použitím [knihovny Batch Management .NET](batch-management-dotnet.md). Při vytváření účtu můžete přidružit účet úložiště Azure pro ukládání aplikací nebo vstupních a výstupních dat souvisejících s úlohami.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

## <a name="components-of-the-batch-account"></a>Součásti účtu Batch

Účet Batch umožňuje efektivně spouštět rozsáhlé paralelní a vysoce výkonné výpočetní úlohy (HPC) v Azure. V rámci účtu, který spravujete:

- Spuštěné aplikace

- Přidělení fondů a uzlů v rámci fondů

- Počet a typy úloh 

- Vstup a výstup dat. Nemusíte instalovat další software pro správu úloh.

- Při vytváření účtu Batch budete požádáni o přiřazení názvu. Toto jméno je jeho ID a po přiřazení nejde změnit.

- Chcete-li změnit název účtu, je nutné jej odstranit a vytvořit nový účet Batch.

- Účet se vytvoří v rámci předplatného, které chcete použít.

- Pomocí účtu můžete identifikovat a načíst primární a sekundární klíče účtu z libovolného účtu Batch v rámci vašeho předplatného.

- Účet uchovává informace o přidělování fondů a hlavních kvótách.  

- Účet obsahuje informace o poloze.

- Účet identifikuje váš účet úložiště.

## <a name="next-steps"></a>Další kroky

- Vytvořte účet Batch pomocí [Azure Portal](batch-account-create-portal.md).
- Vytvořte účet Batch programově, například pomocí [knihovny Batch Management .NET](batch-management-dotnet.md).
- [Umožňuje nakonfigurovat nebo zakázat vzdálený přístup k výpočetním uzlům ve fondu Azure Batch](pool-endpoint-configuration.md).
- [Spuštění úkolů přípravy úloh a uvolnění úloh na výpočetních uzlech Batch](batch-job-prep-release.md)

