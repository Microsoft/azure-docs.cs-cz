---
title: Technické koncepty pro Azure Container nabídky – komerční tržiště Microsoftu
description: Technický zdroj a pokyny, které vám pomůžou s konfigurací nabídky kontejneru na Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: b3c6f88df151cc497f0de670d5d78a05c7477459
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791880"
---
# <a name="create-an-azure-container-offer"></a>Vytvoření nabídky kontejneru Azure

> [!IMPORTANT]
> Přesouváme správu nabídek vašich kontejnerů Azure z portál partnerů cloudu do partnerského centra. Dokud nebudou vaše nabídky migrovány, postupujte podle pokynů v části [Příprava vašich technických prostředků kontejneru](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) pro portál partnerů cloudu ke správě nabídek.

Tento článek poskytuje technické prostředky a doporučení, které vám pomůžou vytvořit kontejnerovou nabídku na Azure Marketplace.

## <a name="before-you-begin"></a>Před zahájením

Informace o rychlých startech, kurzech a ukázkách najdete v [dokumentaci k Azure Container Instances](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto assetů trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky.

Kromě vaší domény řešení by váš technický tým měl mít znalosti o následujících technologiích Microsoftu:

- Základní porozumění [službám Azure](https://azure.microsoft.com/services/)
- [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)a [Azure](https://azure.microsoft.com/services/?filter=networking)
- Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktické znalosti formátu [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou se správou image kontejneru:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

Doporučujeme přidat tyto nástroje do vašeho vývojového prostředí:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Projděte si dostupné nástroje na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/) . Pokud používáte aplikaci Visual Studio, přečtěte si nástroje, které jsou k dispozici v [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvoření image kontejneru

Další informace najdete v následujících kurzech:

- [Kurz: vytvoření image kontejneru pro nasazení do Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Kurz: sestavování a nasazování imagí kontejneru v cloudu s využitím Azure Container Registrych úkolů](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Další kroky

- [Vytvořte nabídku kontejneru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
