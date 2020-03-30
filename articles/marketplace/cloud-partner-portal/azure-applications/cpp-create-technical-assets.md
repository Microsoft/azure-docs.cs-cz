---
title: Vytvoření technických prostředků aplikace Azure | Azure Marketplace
description: Vytvořte technické prostředky pro nabídku aplikace Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285261"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Příprava technických prostředků aplikace Azure

Tento článek popisuje prostředky pro přípravu technických prostředků pro vaši nabídku aplikace Azure.

## <a name="before-you-begin"></a>Než začnete

Prohlédněte si následující video, [vytváření šablon řešení a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), přehled o tom, jak vytvořit šablonu Azure Resource Manageru, definovat aplikační řešení Azure a pak, jak následně publikovat nabídku aplikací na Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Projděte si následující dokumentaci k aplikacím Azure, která poskytuje rychlé starty, kurzy a ukázky.

- [Principy šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Rychlá zprovoznění:

  - [Šablony Azure pro rychlé zprovoznění](https://azure.microsoft.com/documentation/templates/)
  - [Šablony githubazure rychlých startů](https://github.com/azure/azure-quickstart-templates)
  - [Publikování definice aplikace](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Nasazení aplikace katalogu služeb](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Kurzy:

  - [Vytvoření definičních souborů](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publikování aplikace Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Vzorky:

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Řešení spravovaných aplikací](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Základní technické znalosti

Navrhování, vytváření a testování těchto prostředků nějakou dobu trvá a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky.

Váš technický tým by měl mít znalosti o následujících technologiích společnosti Microsoft:

- Základní znalosti [služeb Azure](https://azure.microsoft.com/services/)
- Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
- Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)a Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/)
- Pracovní znalostjové společnosti [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Navrhované nástroje

Chcete-li spravovat aplikaci Azure, zvolte jedno nebo obě následující skriptovací prostředí:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Doporučujeme přidat do vývojového prostředí následující nástroje:

- [Průzkumník úložišť Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) s následujícími rozšířeními:

  - Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také zkontrolovat dostupné nástroje na stránce [Nástroje pro vývojáře Azure](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [tržiště Visual Studia](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Další kroky

[Vytvoření nabídky aplikací Azure](./cpp-create-offer.md)

