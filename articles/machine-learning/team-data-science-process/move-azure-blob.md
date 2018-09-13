---
title: Přesun dat do a z úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: Přesun dat z a do služby Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 854c671d4db6cdca2b019ed9adb0475e588281b1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35622547"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Přesun dat do a z úložiště objektů Blob v Azure
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Metoda je pro vás nejvhodnější, závisí na váš scénář. [Scénáře pro pokročilé analýzy ve službě Azure Machine Learning](plan-sample-scenarios.md) článek vám pomůže určit prostředky, které potřebujete pro různé datové vědy pracovních postupech, používat v procesu pokročilých analýz.

> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Jako alternativu můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) na: 

* Vytvoření a naplánování kanálu, který se stáhne data z úložiště objektů blob v Azure 
* Předejte do publikované webové služby Azure Machine Learning, 
* Zobrazit výsledky prediktivní analýzy a 
* Nahrajte výsledky do úložiště. 

Další informace najdete v tématu [vytváření prediktivních kanálů pomocí služby Azure Data Factory a Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že máte předplatné Azure, účet úložiště a odpovídající klíč úložiště pro tento účet. Před nahrávání a stahování dat, musíte znát vaše služby Azure storage účtu název a klíč účtu.

* Nastavení předplatného služby Azure, najdete v článku [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření účtu úložiště a účet a informace o klíči najdete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

