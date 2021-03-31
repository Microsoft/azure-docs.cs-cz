---
title: Prostředky a pracovní postup služby Batch
description: Přečtěte si o funkcích služby Batch a její pracovní postup na vysoké úrovni z hlediska vývoje.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965208"
---
# <a name="batch-service-workflow-and-resources"></a>Prostředky a pracovní postup služby Batch

V tomto přehledu základních součástí služby Azure Batch probereme pracovní postup vysoké úrovně, který mohou vývojáři služby Batch použít k tvorbě rozsáhlých paralelních výpočetních řešení společně s primárními prostředky služby, které se používají.

Bez ohledu na to, jestli vyvíjíte distribuovanou výpočetní aplikaci nebo službu, která vystavuje přímá [REST API](/rest/api/batchservice/) volání nebo používáte jinou sadu [SDK služby Batch](batch-apis-tools.md#batch-service-apis), použijete spoustu prostředků a funkcí, které jsou zde popsané.

> [!TIP]
> Obecný úvod do služby Batch najdete v části [Základy služby Azure Batch](batch-technical-overview.md). Prohlédněte si také nejnovější [aktualizace služby Batch](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Základní pracovní postup

Následující obecný pracovní postup je typický pro téměř všechny aplikace a služby, které používají službu Batch pro zpracování paralelních úloh:

1. Nahrajte **datové soubory**, které chcete zpracovat, do účtu [Azure Storage](../storage/index.yml). Služba Batch obsahuje integrovanou podporu pro přístup ke službě Azure Blob Storage a vaše úkoly si při spuštění mohou stáhnout tyto soubory do [výpočetních uzlů](nodes-and-pools.md#nodes).
2. Nahrajte **soubory aplikace**, které plánujete svými úkoly spouštět. Tyto soubory mohou být binární soubory nebo skripty a jejich závislosti a jsou spouštěny úkoly v úlohách. Vaše úkoly si mohou stahovat tyto soubory z vašeho účtu úložiště, nebo můžete použít funkci [balíčků aplikací](nodes-and-pools.md#application-packages) služby Batch pro správu aplikací a nasazení.
3. Vytvořte [fond](nodes-and-pools.md#pools) výpočetních uzlů. Při vytváření fondu zadejte počet výpočetní uzlů pro fond, jejich velikost a operační systém. Při spuštění každého úkolu v úloze je tento úkol přiřazen ke spouštění na jednom z uzlů ve vašem fondu.
4. Vytvořte [úlohu](jobs-and-tasks.md#jobs). Úloha spravuje kolekci úkolů. Každou úlohu přidružíte ke konkrétnímu fondu, kde budou spouštěny úkoly této úlohy.
5. Přidejte do úlohy [úkoly](jobs-and-tasks.md#tasks). Každý úkol spustí aplikaci nebo skript, které jste nahráli, pro zpracování datových souborů, které si úkol stahuje z vašeho účtu úložiště. Při dokončení může úkol nahrát svůj výstup do Azure Storage.
6. Monitorujte průběh úlohy a načtěte výstup úkolu z Azure Storage.

> [!NOTE]
> K používání služby Batch potřebujete [účet Batch](accounts.md). Většina řešení Batch také používá pro ukládání a načítání souborů přidružený účet [Azure Storage](../storage/index.yml).

## <a name="batch-service-resources"></a>Prostředky služby Batch

Následující témata popisují prostředky služby Batch, které umožňují vaše distribuované výpočetní scénáře.

- [Účty Batch a účty úložiště](accounts.md)
- [Uzly a fondy](nodes-and-pools.md)
- [Úlohy a úkoly](jobs-and-tasks.md)
- [Soubory a adresáře](files-and-directories.md)

## <a name="next-steps"></a>Další kroky

- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
- Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tyto rychlé starty vás provedou ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a představí vám použití služby Azure Storage k přípravě a načítání souborů úloh.
- Stáhněte a nainstalujte nástroj [Batch Explorer](https://azure.github.io/BatchExplorer/), který můžete používat při vývoji řešení Batch. Nástroj Batch Explorer vám pomůže vytvářet, ladit a monitorovat aplikace Azure Batch.
- Prohlédněte si komunitní materiály, včetně [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [úložiště komunity Batch](https://github.com/Azure/Batch)a [Azure Batch fóra](/answers/topics/azure-batch.html).
