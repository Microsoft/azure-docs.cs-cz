---
title: Vytvoření image kontejnerů Azure technických prostředků | Azure Marketplace
description: Vytvoření technických prostředků pro kontejner Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794346"
---
# <a name="prepare-your-container-technical-assets"></a>Příprava kontejneru technické prostředky

Tento článek popisuje kroky a požadavky na konfiguraci kontejneru nabídky na webu Azure Marketplace.

## <a name="before-you-begin"></a>Než začnete

Zkontrolujte [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentaci, která poskytuje rychlé starty, kurzy a ukázky.

## <a name="fundamental-technical-knowledge"></a>Základní znalosti

Navrhování, sestavování a testování tyto prostředky dobu trvat a vyžaduje technické znalosti platformy Azure a s technologiemi použitými k vytvoření nabídky.
 
Kromě vaší domény řešení váš technický tým by měl mít znalosti následující technologie společnosti Microsoft:

-   Základní znalosti o [služeb Azure](https://azure.microsoft.com/services/) 
-   Jak [návrhu a architektury aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktické znalosti [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [služby Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítě Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktické znalosti [Azure Resource Manageru](https://azure.microsoft.com/features/resource-manager/)
-   Praktické znalosti [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Doporučené nástroje

Zvolte jednu nebo obě z následujících skriptovací prostředí ke správě svou image kontejneru:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme přidat následující nástroje do svého vývojového prostředí:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Rozšíření: [Nástroje Azure Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Rozšíření: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také kontrola nástroje dostupné v [Azure Developer Tools](https://azure.microsoft.com/tools/) stránky a pokud používáte Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvořte image kontejneru

Si přečtěte následující informace:

* [Kurz: Vytvoření image kontejneru pro nasazení do služby Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Kurz: Sestavování a nasazování imagí kontejnerů v cloudu pomocí služby Azure Container Registry úlohy](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Další postup

[Vytvoření nabídky kontejneru](./cpp-create-offer.md)
