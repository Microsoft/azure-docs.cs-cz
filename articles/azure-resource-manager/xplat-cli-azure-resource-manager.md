---
title: Správa prostředků pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Použití rozhraní příkazového řádku Azure (CLI) ke správě prostředků Azure a skupiny
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: dd111c33cbd348a05ed0f0c04f7325347612e54d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590321"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Pomocí Azure CLI ke správě prostředků a skupin prostředků Azure

V tomto článku se dozvíte, jak ke správě vašich řešení pomocí rozhraní příkazového řádku Azure a Azure Resource Manageru. Pokud nejste obeznámeni s Resource Managerem, přečtěte si téma [přehled Resource Manageru](resource-group-overview.md). Tento článek se zaměřuje na úlohy správy. Vaším úkolem je:

1. Vytvoření skupiny prostředků
2. Přidejte prostředek do skupiny prostředků
3. Přidat značku k prostředku
4. Dotazování podle jména nebo hodnoty značek prostředků
5. Použijte a odeberte zámek na prostředek
6. Odstranit skupinu prostředků

Tento článek není uveden postup nasazení šablony Resource Manageru k vašemu předplatnému. Informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat rozhraní příkazového řádku místně, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Nastavení předplatného

Pokud máte více než jedno předplatné, můžete přepnout do jiného předplatného. Nejprve Podíváme se, Všechna předplatná pro váš účet.

```azurecli-interactive
az account list
```

Vrátí seznam povolených a zakázaných předplatných.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Všimněte si, že jedno předplatné je označen jako výchozí. Toto předplatné je aktuální kontext pro operace. Pokud chcete přepnout do jiného předplatného, zadejte název předplatného s **az účet sady** příkazu.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Chcete-li zobrazit nastavení aktuálního kontextu předplatného, použijte **zobrazit účet az** bez parametru:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před nasazením všechny prostředky ke svému předplatnému, musíte vytvořit skupinu prostředků, která bude obsahovat prostředky.

Skupinu prostředků vytvoříte pomocí příkazu **az group create**. Příkaz používá **název** parametr a zadat název skupiny prostředků a **umístění** parametr a zadejte jeho umístění.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Výstup bude v následujícím formátu:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Pokud budete potřebovat později načíst skupinu prostředků, použijte následující příkaz:

```azurecli-interactive
az group show --name TestRG1
```

Pokud chcete získat všechny skupiny prostředků ve vašem předplatném, použijte:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Přidat prostředky do skupiny prostředků

Přidat prostředek do skupiny prostředků, můžete použít **vytvořit az resource** příkazu nebo příkaz, který je specifický pro typ prostředku, kterou vytváříte (jako je **vytvořit účet úložiště az**). Můžete se setkat se snadněji používá příkaz, který je specifická pro typ prostředku, protože obsahuje parametry pro vlastnosti, které jsou potřeba pro nový prostředek. Použití **az resource vytvořit**, musíte znát všechny vlastnosti chcete nastavit bez zobrazování výzev pro ně.

Přidání prostředků prostřednictvím skriptu však může způsobit zmatení budoucí nový prostředek neexistuje v šabloně Resource Manageru. Šablony vám umožňují spolehlivě a opakovaně nasazovat vaše řešení.

Následující příkaz vytvoří účet úložiště. Namísto použití názvu je znázorněno v příkladu, zadejte jedinečný název pro účet úložiště. Název musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena. Pokud použijete název je znázorněno v příkladu, obdržíte chybu, protože tento název se už používá.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Pokud budete potřebovat později znovu načíst tento prostředek, použijte následující příkaz:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Přidat značku

Značky umožňují uspořádání prostředků podle jiné vlastnosti. Například může mít několik prostředků v různých skupinách prostředků, které patří do stejného oddělení. Oddělení značky a hodnoty můžete použít k těmto prostředkům označit jako patřící do stejné kategorie. Nebo můžete označit, zda prostředek se používá v produkčním i testovacím prostředí. V tomto článku použijete značky na jen jeden prostředek, ale ve vašem prostředí pravděpodobně je vhodné použít značky ke všem prostředkům.

Následující příkaz použije dvě značky k vašemu účtu úložiště:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Značky jsou aktualizovány v jako jeden objekt. Pokud chcete přidat značku k prostředku, který již obsahuje značky, nejdřív načtěte existující značky. Přidat novou značku na objekt, který obsahuje existující značky a znovu zásadu použijte všechny značky na prostředek.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Hledat prostředky

Použití **az resource list** příkaz pro načtení prostředků pro různé vyhledávací podmínky.

* Chcete-li získat prostředek podle názvu, zadejte **název** parametr:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Chcete-li získat všechny prostředky ve skupině prostředků, zadejte **; resource-group** parametr:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Pokud chcete získat všechny prostředky s názvem a hodnotou značky, zadejte **značka** parametr:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Na všechny prostředky s konkrétní typ prostředku, zadejte **typ prostředku** parametr:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Získejte ID prostředku

Mnoho příkazů trvat jako parametr ID prostředku. Pokud chcete získat ID prostředků a úložiště do proměnné, použijte:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Uzamknout prostředek

Pokud je potřeba Ujistěte se, že důležitých prostředků není možné omylem odstranit nebo upravit, použít zámek na prostředek. Můžete zadat argument **CanNotDelete** nebo **jen pro čtení**.

Pokud chcete vytvořit nebo odstranit zámky pro správu, musí mít přístup k `Microsoft.Authorization/*` nebo `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí jsou udělena pouze vlastník a správce uživatelských přístupů těchto akcí.

Chcete-li použít zámek, použijte následující příkaz:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Uzamčené prostředků v předchozím příkladu nelze odstranit, dokud se zámek neodebere. Pokud chcete odstranit zámek, použijte:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Další informace o nastavení uzamčení, naleznete v tématu [zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Odebrat prostředky nebo skupiny prostředků
Můžete odebrat prostředek nebo skupinu prostředků. Při odebrání skupiny prostředků odstraníte také všechny prostředky v příslušné skupině prostředků.

* Pokud chcete odstranit prostředek ze skupiny prostředků, použijte příkaz delete pro typ prostředku, který odstraňujete. Tento příkaz odstraní prostředek, ale nedojde k odstranění skupiny prostředků.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Pokud chcete odstranit skupinu prostředků a všechny její prostředky, použijte příkaz **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Pro oba příkazy zobrazí se výzva k potvrzení, že chcete odebrat prostředek nebo skupinu prostředků.

## <a name="next-steps"></a>Další postup
* Další informace o vytváření šablon Resource Manageru najdete v tématu [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Další informace o nasazování šablon najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy-cli.md).
* Přesunutí stávajících prostředků do nové skupiny prostředků. Příklady najdete v tématu [přesun prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).