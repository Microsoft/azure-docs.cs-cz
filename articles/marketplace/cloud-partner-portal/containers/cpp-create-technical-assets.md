---
title: Vytváření imagí pro Image Azure Containers Technical Assets | Azure Marketplace
description: Vytvořte technické prostředky pro kontejner Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 68db606c9a01c4b1122f9b0cce620762485ca40a
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148267"
---
# <a name="prepare-your-container-technical-assets"></a>Příprava technických prostředků kontejnerů

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek Azure Container do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v tématu [Příprava vašich technických prostředků Azure Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pro správu migrovaných nabídek.

Tento článek popisuje kroky a požadavky pro konfiguraci kontejneru nabídky Azure Marketplace.

## <a name="before-you-begin"></a>Před zahájením

Přečtěte si dokumentaci k [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) , která poskytuje rychlé starty, kurzy a ukázky.

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto prostředků trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky.
 
Kromě vaší domény řešení by váš technický tým měl mít znalosti o těchto technologiích společnosti Microsoft:

-    Základní porozumění [službám Azure](https://azure.microsoft.com/services/) 
-    [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
-    Praktické znalosti [Virtual Machines Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítí Azure](https://azure.microsoft.com/services/?filter=networking)
-    Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-    Praktické znalosti formátu [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou se správou image kontejneru:

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme do vývojového prostředí přidat následující nástroje:

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Navrhujeme také kontrolu dostupných nástrojů na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvoření image kontejneru

Další informace najdete v následujících tématech:

* [Kurz: vytvoření image kontejneru pro nasazení do Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Kurz: sestavování a nasazování imagí kontejneru v cloudu s využitím Azure Container Registrych úloh](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Další kroky

[Vytvoření nabídky kontejneru](./cpp-create-offer.md)
