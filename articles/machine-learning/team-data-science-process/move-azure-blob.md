---
title: Přesun dat do a z úložiště objektů BLOB v Azure – vědecký proces týmového zpracování dat
description: Přesuňte data do a z úložiště objektů BLOB v Azure pomocí Průzkumník služby Azure Storage, AzCopy, Pythonu a SSIS.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d885a7fad6e958507e7d9df34bd2b1fb222c6f86
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053662"
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

## <a name="prerequisites"></a>Předpoklady
V tomto článku se předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahráním/stažením dat musíte znát název účtu úložiště Azure a klíč účtu.

* Pokud chcete nastavit předplatné Azure, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a získání informací o účtu a klíčích najdete v tématu [informace o účtech Azure Storage](../../storage/common/storage-create-storage-account.md).

