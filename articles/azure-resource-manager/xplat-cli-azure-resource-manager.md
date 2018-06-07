---
title: Správa prostředků pomocí rozhraní příkazového řádku Azure | Microsoft Docs
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
ms.openlocfilehash: 4111d1d990f3e9efb78fae0476b027d5bae013af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603242"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Použití rozhraní příkazového řádku Azure ke správě prostředků Azure a skupiny prostředků

V tomto článku zjistěte, jak spravovat vaše řešení pomocí rozhraní příkazového řádku Azure a Azure Resource Manager. Pokud nejste obeznámeni s Resource Managerem, přečtěte si téma [Přehled služby Správce prostředků](resource-group-overview.md). Tento článek se zaměřuje na úlohy správy. Vaším úkolem je:

1. Vytvoření skupiny prostředků
2. Přidání prostředku do skupiny prostředků
3. Přidání značky k prostředku
4. Zadat dotaz na prostředky na základě názvy nebo hodnoty značky
5. Použít a odebere se zámek na prostředek
6. Odstranit skupinu prostředků.

Tento článek nezobrazuje postup nasazení šablony Resource Manageru do vašeho předplatného. Podrobnosti naleznete v tématu [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Instalace a použití rozhraní příkazového řádku místně, najdete v části [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Předplatné sadu

Pokud máte více než jedno předplatné, můžete přepnout do jiného předplatného. První Podíváme se, Všechna předplatná pro váš účet.

```azurecli-interactive
az account list
```

Vrátí seznam předplatných povolení i Zakázaní.

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

Všimněte si, že jedno předplatné je označen jako výchozí. Toto předplatné je váš aktuální kontext pro operace. Chcete-li přepnout do jiného předplatného, zadejte název odběru s **nastaven účet az** příkaz.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Chcete-li zobrazit aktuální kontext předplatného, použijte **az účet zobrazit** bez parametru:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před nasazením žádné prostředky k vašemu předplatnému, musíte vytvořit skupinu prostředků, která bude obsahovat prostředky.

Skupinu prostředků vytvoříte pomocí příkazu **az group create**. Příkaz používá **název** parametr zadejte název pro skupinu prostředků a **umístění** parametr k určení jeho umístění.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Výstup je v následujícím formátu:

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

Všechny skupiny prostředků v rámci vašeho předplatného, použijte:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Přidat prostředky do skupiny prostředků

Chcete-li přidat prostředek do skupiny prostředků, můžete použít **vytvořit prostředek az** příkaz nebo příkaz, který je specifické pro daný typ prostředku, kterou vytváříte (jako **vytvořit účet úložiště az**). Může pro vás jednodušší použít příkaz, který je specifická pro typ prostředku, protože obsahuje parametry pro vlastnosti, které jsou potřebné pro nový prostředek. Použít **vytvořit prostředek az**, musíte znát všechny vlastnosti, které chcete nastavit bez výzvy pro ně.

Přidání prostředku pomocí skriptu však může být matoucí budoucí protože nový prostředek neexistuje v šabloně Resource Manager. Šablony umožňují spolehlivě a opakovaného nasazování svého řešení.

Následující příkaz vytvoří účet úložiště. Místo použití názvu v příkladu, zadejte jedinečný název pro účet úložiště. Název musí být v rozmezí 3 až 24 znaků a použít pouze čísla a malá písmena. Pokud použijete název v příkladu, zobrazí chybu, protože tento název je již používán.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Pokud budete potřebovat později načíst tento prostředek, použijte následující příkaz:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Přidání značky

Značky umožňují uspořádání prostředků podle jiné vlastnosti. Například může mít několik prostředků v různých prostředků skupiny, které patří do stejného oddělení. Můžete použít oddělení značky a hodnoty pro tyto prostředky označit je jako náležící do stejné kategorii. Nebo můžete označit, zda je prostředek použít v produkčním i testovacím prostředí. V tomto článku pouze jeden prostředek použijete značky, ale ve vašem prostředí nejpravděpodobnější má smysl použití značek ke všem prostředkům.

Následující příkaz platí dvě značky pro váš účet úložiště:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Značky jsou aktualizovány jako jednoho objektu. Postup přidání značky k prostředku, který již obsahuje značky, nejdřív načtěte stávající značky. Přidejte novou značku k objektu, který obsahuje stávající značky a znovu použít všechny značky k prostředku.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Hledat prostředky

Použití **seznam zdrojů az** příkaz k načtení prostředků pro různé vyhledávací podmínky.

* Chcete-li získat prostředek podle názvu, zadejte **název** parametr:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Chcete-li získat všechny prostředky ve skupině prostředků, zadejte **skupiny prostředků** parametr:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Chcete-li získat všechny prostředky se název značky a hodnotou, zadat **značky** parametr:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Na všechny prostředky s konkrétní typ prostředku, zadejte **typ prostředku** parametr:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Získání ID prostředku

Mnoho příkazů trvat ID prostředku jako parametr. Pokud chcete načíst ID prostředků a úložiště do proměnné, použijte:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Zamknout prostředku

Pokud potřebujete zajistit kritické prostředků je náhodně odstraněna nebo upravena, použije zámek k prostředku. Můžete buď zadat **CanNotDelete** nebo **jen pro čtení**.

Vytvořit nebo odstranit zámky správy, musíte mít přístup k `Microsoft.Authorization/*` nebo `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí pouze vlastník a správce přístupu uživatelů mají tyto akce.

Použít zámek, použijte následující příkaz:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Uzamčení prostředků v předchozím příkladu nelze odstranit, dokud se neodstraní zámek. Chcete-li odebrat zámek, použijte:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Další informace o nastavení zámky najdete v tématu [zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Odebrat prostředky nebo skupinu prostředků
Můžete odebrat prostředek nebo skupina prostředků. Když odeberete skupinu prostředků, je také odstranit všechny prostředky v příslušné skupině prostředků.

* Pokud chcete odstranit prostředek ze skupiny prostředků, použijte příkaz delete pro typ prostředku, který chcete odstranit. Příkaz odstraní prostředek, ale nedojde k odstranění skupiny prostředků.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Pokud chcete odstranit skupinu prostředků a všechny její prostředky, použijte příkaz **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Pro oba příkazy zobrazí se výzva k potvrzení, že chcete odebrat prostředek nebo skupina prostředků.

## <a name="next-steps"></a>Další postup
* Další informace o vytváření šablon Resource Manageru, najdete v části [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Další informace o nasazení šablony najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy-cli.md).
* Existující prostředky můžete přesunout do nové skupiny prostředků. Příklady najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).