---
title: Přesun dat do a z Azure Blob Storage | Microsoft Docs
description: Přesun dat z a do služby Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 424216226ca4b92f17cec78dadbb0643defa8d0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Přesun dat do a z Azure Blob Storage
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Jakou metodu je pro vás nejvhodnější závisí na vašem scénáři. [Scénáře pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md) pomáhá určit prostředky, které potřebujete pro různé pracovní úlohy vědecké účely data, která je použita v procesu pokročilou analýzu.

> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v části [základy objektu Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby objektů Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Jako alternativu, můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) na: 

* Vytvoření a plánování kanálu, který stahuje dat z Azure blob storage 
* předejte ji do publikované webové služby Azure Machine Learning, 
* Zobrazí výsledky prediktivní analýzy a 
* Nahrání výsledky do úložiště. 

Další informace najdete v tématu [vytvořit prediktivní kanály pomocí Azure Data Factory a Azure Machine Learning](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání nebo stahování dat, musíte znát klíč účet a název účtu úložiště Azure.

* Předplatné Azure, naleznete v tématu [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny pro vytvoření účtu úložiště a získávání účet a klíčové informace naleznete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

