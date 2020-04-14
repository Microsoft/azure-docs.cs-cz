---
title: Vytvoření technických datových zdrojů image azure kontejnerů | Azure Marketplace
description: Vytvořte technické prostředky pro kontejner Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 1116bd03cf0ef734b74b387ff8f3cff74b29b215
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270311"
---
# <a name="prepare-your-container-technical-assets"></a>Příprava technických prostředků kontejnerů

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek Azure Container do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Příprava technických prostředků Azure Container](https://aka.ms/CreateContainerTechAssets) ke správě migrovaných nabídek.

Tento článek popisuje kroky a požadavky na konfiguraci nabídky kontejneru Azure Marketplace.

## <a name="before-you-begin"></a>Než začnete

Projděte si dokumentaci [k instancí kontejnerů Azure,](https://docs.microsoft.com/azure/container-instances) která poskytuje rychlé starty, kurzy a ukázky.

## <a name="fundamental-technical-knowledge"></a>Základní technické znalosti

Navrhování, vytváření a testování těchto prostředků nějakou dobu trvá a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky.
 
Kromě domény řešení by váš technický tým měl mít znalosti o následujících technologiích společnosti Microsoft:

-    Základní znalosti [služeb Azure](https://azure.microsoft.com/services/) 
-    Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
-    Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-    Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/)
-    Pracovní znalosti [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Navrhované nástroje

Chcete-li spravovat image kontejneru, zvolte jedno nebo obě následující skriptovací prostředí:

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme přidat do vývojového prostředí následující nástroje:

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také zkontrolovat dostupné nástroje na stránce [Nástroje pro vývojáře Azure](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [tržiště Visual Studia](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvoření bitové kopie kontejneru

Další informace naleznete v následujících informacích:

* [Kurz: Vytvoření image kontejneru pro nasazení do instancí kontejneru Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Kurz: Vytváření a nasazování ibi kontejnerů v cloudu pomocí úloh registru kontejnerů Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Další kroky

[Vytvoření nabídky kontejnerů](./cpp-create-offer.md)
