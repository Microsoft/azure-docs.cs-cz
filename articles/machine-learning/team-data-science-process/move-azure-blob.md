---
title: Přesunutí dat do úložiště objektů blob Azure a z nich – proces vědecké ho zpracování týmových dat
description: Přesuňte data do úložiště objektů Blob Azure a z něj pomocí Azure Storage Explorer, AzCopy, Python a SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717584"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Přesunutí dat do úložiště objektů blob Azure a z nich

Proces vědecké analýzy týmových dat vyžaduje, aby data byla přijata nebo načtena do různých prostředí úložiště, která mají být zpracována nebo analyzována nejvhodnějším způsobem v každé fázi procesu.

## <a name="different-technologies-for-moving-data"></a>Různé technologie pro přesun dat

Následující články popisují, jak přesunout data do a z úložiště objektů blob Azure pomocí různých technologií.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Metoda, která je pro vás nejlepší, závisí na vašem scénáři. [Scénáře pro pokročilou analýzu v](plan-sample-scenarios.md) článku Azure Machine Learning vám pomohou určit prostředky, které potřebujete pro různé pracovní postupy datové vědy používané v procesu pokročilé analýzy.

> [!NOTE]
> Úplný úvod do úložiště objektů blob Azure najdete v části [Základy objektů blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Pomocí služby Azure Data Factory

Alternativně můžete azure [data factory](https://azure.microsoft.com/services/data-factory/) použít k: 

* vytvořit a naplánovat kanál, který stahuje data z úložiště objektů blob Azure, 
* předat ji publikované webové službě Azure Machine Learning, 
* přijímat výsledky prediktivní analýzy a 
* nahrát výsledky do úložiště. 

Další informace najdete [v tématu Vytváření prediktivních kanálů pomocí Azure Data Factory a Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním nebo stahováním dat musíte znát název účtu azure storage a klíč účtu.

* Pokud chcete nastavit předplatné Azure, [přečtěte](https://azure.microsoft.com/pricing/free-trial/)si bezplatné měsíční zkušební verzi .
* Pokyny k vytvoření účtu úložiště a k získání informací o účtu a klíči najdete [v tématu O účtech Azure Storage](../../storage/common/storage-create-storage-account.md).

