---
title: Vytváření technických prostředků aplikace Azure | Azure Marketplace
description: Vytvořte technické prostředky pro nabídku aplikací Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827606"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Příprava technických prostředků aplikace Azure

Tento článek popisuje prostředky pro přípravu technických prostředků pro nabídku aplikací Azure.

## <a name="before-you-begin"></a>Než začnete

Přečtěte si následující video, [Sestavujte šablony řešení a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), přehled o tom, jak vytvořit šablonu Azure Resource Manager k definování řešení Azure Application a následně jak publikovat nabídka aplikace Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Přečtěte si následující dokumentaci k aplikaci Azure, která poskytuje rychlé starty, kurzy a ukázky.

- [Principy šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Šablon rychlý Start

  - [Šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/)
  - [Šablony Azure pro rychlý Start pro GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publikovat definici aplikace](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Nasadit aplikaci katalogu služeb](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Kurzy:

  - [Vytváření definičních souborů](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publikování aplikace Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Vzory

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Řešení spravovaných aplikací](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto prostředků trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky.

Váš technický tým by měl mít znalosti o následujících technologiích Microsoftu:

- Základní porozumění [službám Azure](https://azure.microsoft.com/services/)
- [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)a [Azure](https://azure.microsoft.com/services/?filter=networking)
- Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktické znalosti formátu [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou při správě aplikace Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Do vývojového prostředí doporučujeme přidat následující nástroje:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) s následujícími příponami:

  - Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Navrhujeme také kontrolu dostupných nástrojů na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Další kroky

[Vytvoření nabídky aplikací Azure](./cpp-create-offer.md)

