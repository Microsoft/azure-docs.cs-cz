---
title: Příprava technických prostředků kontejneru Azure
description: Technický zdroj a pokyny, které vám pomůžou s konfigurací nabídky kontejneru na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459324"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Příprava technických prostředků kontejneru Azure

Tento článek poskytuje technické prostředky a doporučení, které vám pomůžou vytvořit kontejnerovou nabídku na Azure Marketplace.

## <a name="before-you-begin"></a>Než začnete

Informace o rychlých startech, kurzech a ukázkách najdete v [dokumentaci k Azure Container Instances](../container-instances/index.yml).

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

- [Azure PowerShell](/powershell/azure/)
- Rozhraní příkazového [řádku Azure](/cli/azure/)

Doporučujeme přidat tyto nástroje do vašeho vývojového prostředí:

- [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Projděte si dostupné nástroje na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/) . Pokud používáte aplikaci Visual Studio, přečtěte si nástroje, které jsou k dispozici v [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvoření image kontejneru

Nemůžete nasadit image, která se Azure Container Instances z místního registru.

- Pokud už máte pracovní kontejner v místním registru, vytvořte registr Azure a nahrajte image kontejneru do Azure Container Registry. Další informace najdete v tématu [kurz: sestavování a nasazování imagí kontejnerů v cloudu s Azure Container Registry úkoly](../container-registry/container-registry-tutorial-quick-task.md).

- Pokud ještě nemáte image kontejneru a potřebujete kontejnerizace stávající aplikaci nebo vytvořit novou aplikaci založenou na kontejnerech, naklonujte zdrojový kód aplikace z GitHubu, vytvořte image kontejneru ze zdroje aplikace a otestujte image v místním prostředí Docker. Další informace najdete v tématu [kurz: vytvoření image kontejneru pro nasazení do Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky kontejneru](create-azure-container-offer.md)