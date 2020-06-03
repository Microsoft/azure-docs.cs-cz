---
title: Reference k rozhraní příkazového řádku Azure CLI pro Azure Data Share
description: Referenční cílová stránka Azure CLI pro sdílenou složku Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300616"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI pro sdílení dat Azure

Rozhraní příkazového řádku Azure ([Azure CLI](/cli/azure/what-is-azure-cli)) je sada příkazů používaných k vytváření a správě prostředků Azure.  Je k dispozici napříč řadou služeb Azure, včetně Azure Data Share.  K dispozici jsou více než 65 různých příkazů pro sdílení dat.  Tyto příkazy poskytují možnost efektivně pracovat se službou z příkazového řádku.

## <a name="references-for-data-share"></a>Odkazy na sdílení dat

Všechny příkazy rozhraní příkazového řádku Azure pro sdílenou složku Azure jsou aktuálně rozšířením rozhraní příkazového řádku Azure CLI.  Rozšíření poskytuje přístup k experimentálním a předběžným příkazům.  Přečtěte si další informace o odkazech na rozšíření v [používání rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

|Reference k rozhraní příkazového řádku Azure CLI |Popis
|-|-|-|
| [AZ datashare](/cli/azure/ext/datashare/datashare) | Všechny příkazy pro správu Azure Data Share.
| [AZ datashare Account](/cli/azure/ext/datashare/datashare/account) | Příkazy pro správu účtů Azure Data Share
| [AZ datashare Consumer](/cli/azure/ext/datashare/datashare/consumer) | Příkazy pro uživatele pro správu sdílené složky Azure
| [AZ datashare DataSet](/cli/azure/ext/datashare/datashare/dataset) | Příkazy pro poskytovatele správy datových sad Azure Data Share
| [AZ datashare Pozvánka](/cli/azure/ext/datashare/datashare/invitation) | Příkazy pro uživatele, kteří budou spravovat pozvánky ke sdílení dat Azure
| [AZ datashare Provider-Share-Subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Příkazy pro poskytovatele správy předplatných Azure Data Share
| [AZ datashare synchroniz](/cli/azure/ext/datashare/datashare/synchronization)  | Příkazy pro správu synchronizace Azure Data Share.
| [AZ datashare synchroniz-Setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Příkazy pro poskytovatele správy nastavení synchronizace Azure Data Share.

## <a name="reference-examples"></a>Příklady odkazů

Příklady jsou k dispozici u každého odkazu rozhraní příkazového řádku Azure CLI. I když tyto úlohy můžete také dokončit prostřednictvím Azure Portal, vyžaduje použití Azure CLI jeden příkazový řádek.  Tady je několik bloků kódu, které vám poskytnou představu o tom, jak snadné je používat rozhraní příkazového řádku Azure CLI.

Pokud chcete pracovat se sdílenými daty Azure, budete nejdřív potřebovat skupinu prostředků.  Skupiny prostředků Azure je snadné vytvořit a spravovat pomocí Azure CLI.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Vytvoření účtu pro sdílení dat je jednoduché.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Viz také

* [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli) , kde se dozvíte o instalaci a přihlášení

* Seznamte se s dalšími odkazy na [základní](/cli/azure/reference-index) a [rozšiřující](/cli/azure/azure-cli-extensions-list) informace v dokumentaci k Azure CLI.
