---
title: Vytvoření spravované aplikace Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea192220ced18b25d60030527d1f76fb37962a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Vytvoření a nasazení spravované aplikace Azure pomocí rozhraní příkazového řádku Azure

Tento článek obsahuje úvod k práci se spravovanými aplikacemi. Přidáte definici spravované aplikace do interního katalogu pro uživatele ve vaší organizaci. Potom tuto spravovanou aplikaci nasadíte do vašeho předplatného. Pro zjednodušení tohoto úvodu jsme soubory pro vaši spravovanou aplikaci sestavili předem. Jeden soubor definuje infrastrukturu pro vaše řešení. Druhý soubor definuje uživatelské rozhraní pro nasazení této spravované aplikace prostřednictvím portálu. Tyto soubory jsou dostupné prostřednictvím GitHubu. Tyto soubory se naučíte sestavovat v kurzu [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).

Až skončíte, budete mít tři skupiny prostředků obsahující různé části spravované aplikace.

| Skupina prostředků | Contains | Popis |
| -------------- | -------- | ----------- |
| appDefinitionGroup | Definice spravované aplikace. | Tuto skupinu prostředků a definici spravované aplikace vytvoří vydavatel. Tuto spravovanou aplikaci může nasadit kdokoli, kdo má přístup k její definici. |
| applicationGroup | Instance spravované aplikace. | Tuto skupinu prostředků a instanci spravované aplikace vytvoří uživatel. Uživatel může spravovanou aplikaci aktualizovat prostřednictvím této instance. |
| infrastructureGroup | Prostředky pro spravovanou aplikaci. | Tato skupina prostředků se vytvoří automaticky při vytvoření spravované aplikace. Vydavatel má k této skupině prostředků přístup pro správu aplikace. Uživatel má k této skupině prostředků omezený přístup. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Vytvoření skupiny prostředků pro definici

Definice vaší spravované aplikace existuje ve skupině prostředků. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Skupinu prostředků vytvoříte spuštěním následujícího příkazu:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Při definování spravované aplikace vybíráte uživatele, skupinu nebo aplikaci, která spravuje prostředky v zastoupení uživatele. Tato identita má oprávnění pro spravovanou skupinu prostředků podle přiřazené role. Obvykle pro správu prostředků vytvoříte skupinu Azure Active Directory. Pro tento článek ale použijte svou vlastní identitu.

Pro získání ID objektu vaší identity v následujícím příkazu zadejte hlavní název uživatele (UPN):

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Dál potřebujete ID definice role pro předdefinovanou roli RBAC, ke které chcete uživateli udělit přístup. Následující příkaz ukazuje, jak získat ID definice role pro roli vlastníka:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Teď vytvoříte prostředek definice spravované aplikace. Spravovaná aplikace obsahuje jenom účet úložiště.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Po dokončení příkazu máte definici spravované aplikace ve vaší skupině prostředků. 

Některé z parametrů použitých v předchozím příkladu:

* **resource-group:** Název skupiny prostředků, kde se vytvoří definice spravované aplikace.
* **lock-level:** Typ zámku nastaveného pro spravovanou skupinu prostředků. Zabraňuje zákazníkovi v provádění nežádoucích operací s touto skupinou prostředků. Jedinou podporovanou úrovní zámku momentálně je ReadOnly. Prostředky, které jsou ve spravované skupině prostředků dostupné, může při zadání úrovně ReadOnly zákazník jenom číst. Zámek se nevztahuje na identity vydavatelů s uděleným přístupem ke spravované skupině prostředků.
* **authorizations:** Popisuje ID objektu zabezpečení a ID definice role, které slouží k udělení oprávnění pro spravovanou skupinu prostředků. tento parametr je zadaný ve formátu `<principalId>:<roleDefinitionId>`. Pro tuto vlastnost je také možné zadat několik hodnot. Pokud je potřeba několik hodnot, měly by být zadané ve tvaru `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Jednotlivé hodnoty jsou oddělené mezerou.
* **package-file-uri:** Umístění balíčku .zip, který obsahuje požadované soubory. Tento balíček obsahuje minimálně soubory **mainTemplate.json** a **createUiDefinition.json**. **mainTemplate.json** definuje prostředky Azure, které jsou zřízené jako součást spravované aplikace. Šablona se nijak neliší od běžné šablony Resource Manageru. **createUiDefinition.json** generuje uživatelské rozhraní pro uživatele, kteří vytvářejí spravované aplikace prostřednictvím portálu.

## <a name="create-resource-group-for-managed-application"></a>Vytvoření skupiny prostředků pro spravovanou aplikaci

Teď jste připravení spravovanou aplikaci nasadit. 

K držení nasazené spravované aplikace potřebujete skupinu prostředků. Jako umístění použijte **westcentralus**.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Nasazení spravované aplikace

Aplikaci nasadíte pomocí následujících příkazů:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Některé z parametrů použitých v předchozím příkladu:

* **managedapp-definition-id:** ID definice, kterou jste vytvořili dříve v tomto článku.
* **managed-rg-id:** ID skupiny prostředků pro prostředky přidružené ke spravované aplikaci. Příkaz vytvoří tuto skupinu prostředků. **Před spuštěním tohoto příkazu nesmí existovat**. Tuto skupinu prostředků spravuje vydavatel. 
* **resource-group:** Skupina prostředků, kde se vytvoří prostředek spravované aplikace.
* **parameters:** Parametry, které jsou potřeba pro prostředky přidružené ke spravované aplikaci.

Po úspěšném dokončení nasazení uvidíte, že spravovaná aplikace se vytvoří ve skupině applicationGroup. Prostředek storageAccount se vytvoří ve skupině infrastructureGroup.

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Příklady souborů najdete v tématu [Ukázky spravovaných aplikací](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
