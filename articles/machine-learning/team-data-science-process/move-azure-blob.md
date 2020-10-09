---
title: Přesun dat do a z úložiště objektů BLOB v Azure – vědecký proces týmového zpracování dat
description: Přesuňte data do a z úložiště objektů BLOB v Azure pomocí Průzkumník služby Azure Storage, AzCopy, Pythonu a SSIS.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76717584"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Přesun dat do a z úložiště objektů BLOB v Azure

Vědecké zpracování týmových dat vyžaduje, aby data byla v jednotlivých fázích procesu zpracovávána nebo načtena do různých různých úložných prostředí, aby je bylo možné zpracovat nebo analyzovat.

## <a name="different-technologies-for-moving-data"></a>Různé technologie pro přesun dat

Následující články popisují, jak přesunout data do a z úložiště objektů BLOB v Azure pomocí různých technologií.

* [Azure Storage – Průzkumník](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Které metody jsou pro vás nejvhodnější, závisí na vašem scénáři. [Scénáře pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md) článku vám pomůžou určit prostředky, které potřebujete pro nejrůznější pracovní postupy pro datové vědy používané v procesu pokročilé analýzy.

> [!NOTE]
> Úplný Úvod do úložiště objektů BLOB v Azure najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Pomocí služby Azure Data Factory

Jako alternativu můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k těmto akcím: 

* Vytvoření a naplánování kanálu, který stahuje data z úložiště objektů BLOB v Azure 
* předat do publikované Azure Machine Learning webové služby, 
* dodržení výsledků prediktivní analýzy a 
* Nahrajte výsledky do úložiště. 

Další informace najdete v tématu [vytváření prediktivních kanálů pomocí Azure Data Factory a Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním/stažením dat musíte znát název svého účtu Azure Storage a klíč účtu.

* Pokud chcete nastavit předplatné Azure, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a získání informací o účtu a klíči najdete v tématu [informace o Azure Storagech účtech](../../storage/common/storage-create-storage-account.md).

