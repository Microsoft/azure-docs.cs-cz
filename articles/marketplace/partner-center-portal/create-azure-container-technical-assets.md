---
title: Vytvoření technického majetku virtuálního počítače Azure
description: Tento článek popisuje kroky a požadavky na konfiguraci nabídky kontejnerů na Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 262129084be5c6a68bfd8d8708c9a10fbb606144
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383728"
---
# <a name="create-an-azure-vm-technical-asset"></a>Vytvoření technického majetku virtuálního počítače Azure

> [!IMPORTANT]
> Správu nabídek Azure Container přesouváme z portálu partnerů cloudu do Centra partnerů. Dokud nebudou vaše nabídky migrovány, postupujte podle pokynů v [části Příprava technických prostředků kontejneru](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) pro portál cloudových partnerů pro správu nabídek.

Tento článek popisuje kroky a požadavky na konfiguraci nabídky kontejneru na Azure Marketplace.

## <a name="before-you-begin"></a>Než začnete

Rychlé starty, kurzy a ukázky najdete v [tématu Instance kontejneru Azure](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Základní technické znalosti

Navrhování, vytváření a testování těchto prostředků vyžaduje čas a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky.

Kromě domény řešení by váš technický tým měl mít znalosti o následujících technologiích společnosti Microsoft:

- Základní znalosti [služeb Azure](https://azure.microsoft.com/services/)
- Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
- Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)a Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/)
- Pracovní znalosti [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Navrhované nástroje

Chcete-li spravovat image kontejneru, zvolte jedno nebo obě následující skriptovací prostředí:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Doporučujeme přidat tyto nástroje do vývojového prostředí:

- [Azure Storage Explorer](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Zkontrolujte dostupné nástroje na stránce [Nástroje pro vývojáře Azure.](https://azure.microsoft.com/) Pokud používáte Visual Studio, přečtěte si nástroje dostupné na [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvoření bitové kopie kontejneru

Další informace naleznete v následujících kurzech:

- [Kurz: Vytvoření image kontejneru pro nasazení do instancí kontejneru Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Kurz: Vytvářejte a nasazujte ibi kontejnerů v cloudu pomocí úloh registru kontejnerů Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-step"></a>Další krok

- [Vytvořte nabídku kontejneru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
