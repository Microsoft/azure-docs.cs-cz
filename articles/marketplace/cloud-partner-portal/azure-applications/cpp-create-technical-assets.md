---
title: Vytvoření aplikace Azure technických prostředků | Dokumentace Microsoftu
description: Vytvoření technických prostředků pro nabídku aplikací Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6050ad98c87dbe38516a6ee3c4862495ad868031
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414383"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Připravit vaše aplikace Azure technické prostředky

Tento článek popisuje prostředky pro přípravu technických prostředků pro vaši nabídku aplikací Azure.

## <a name="before-you-begin"></a>Před zahájením

Projděte si následující dokumentaci aplikace Azure, která poskytuje rychlé starty, kurzy a ukázky.

- [Pochopení šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Šablony rychlý start:

  - [Šablony Azure Quickstart](https://azure.microsoft.com/documentation/templates/)
  - [Šablony Azure pro rychlý start Githubu](https://github.com/azure/azure-quickstart-templates)
  - [Publikování definice aplikace](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Nasazení aplikace katalogu služeb](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Kurzy:

  - [Vytvořit definiční soubory](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publikování aplikace Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

 - Ukázky:

   - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
   - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
   - [Řešení spravovaných aplikací](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Základní znalosti

Navrhování, sestavování a testování tyto prostředky dobu trvat a vyžaduje technické znalosti platformy Azure a s technologiemi použitými k vytvoření nabídky.

Váš technický tým by měl mít znalosti o technologiích Microsoftu pro následující:

- Základní znalosti o [služeb Azure](https://azure.microsoft.com/services/)
- Jak [návrhu a architektury aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [služby Azure Storage](https://azure.microsoft.com/services/?filter=storage), a [sítě Azure](https://azure.microsoft.com/services/?filter=networking)
- Praktické znalosti [Azure Resource Manageru](https://azure.microsoft.com/features/resource-manager/)
- Praktické znalosti [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Doporučené nástroje

Zvolte jednu nebo obě z následujících skriptovací prostředí ke správě aplikace Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Doporučujeme přidat následující nástroje do svého vývojového prostředí:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) s těmito příponami:

  - Linka: [Nástroje Azure Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Linka: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Linka: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také kontrola nástroje dostupné v [Azure Developer Tools](https://azure.microsoft.com/tools/) stránky a pokud používáte Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Další postup

[Vytvoření nabídky aplikací Azure](./cpp-create-offer.md)

