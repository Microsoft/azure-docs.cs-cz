---
title: Vytvoření definice aplikace spravované Azure | Microsoft Docs
description: Ukazuje, jak vytvořit spravovanou aplikaci Azure, která je určená pro členy vaší organizace.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804904"
---
# <a name="publish-an-azure-managed-application-definition"></a>Publikování definice aplikace spravované Azure

Tento rychlý start obsahuje úvod k práci se spravovanými aplikacemi. Přidáte definici spravované aplikace do interního katalogu pro uživatele ve vaší organizaci. Pro zjednodušení tohoto úvodu jsme soubory pro vaši spravovanou aplikaci sestavili předem. Tyto soubory jsou dostupné prostřednictvím GitHubu. Tyto soubory se naučíte sestavovat v kurzu [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).

Až si ho projdete, budete mít skupinu prostředků s názvem **appDefinitionGroup**, která obsahuje definici spravované aplikace.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Vytvoření skupiny prostředků pro definici

Definice vaší spravované aplikace existuje ve skupině prostředků. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Skupinu prostředků vytvoříte spuštěním následujícího příkazu:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Vytvoření definice spravované aplikace

Při definování spravované aplikace vybíráte uživatele, skupinu nebo aplikaci, která pro uživatele spravuje prostředky. Tato identita má oprávnění pro spravovanou skupinu prostředků podle přiřazené role. Obvykle pro správu prostředků vytvoříte skupinu Azure Active Directory. Pro tento článek ale použijte svou vlastní identitu.

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
* **authorizations:** Popisuje ID objektu zabezpečení a ID definice role, které slouží k udělení oprávnění pro spravovanou skupinu prostředků. tento parametr je zadaný ve formátu `<principalId>:<roleDefinitionId>`. Pokud je potřeba zadat více hodnot, zadejte je ve formátu `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Jednotlivé hodnoty jsou oddělené mezerou.
* **package-file-uri:** Umístění balíčku .zip, který obsahuje požadované soubory. Balíček musí obsahovat soubory **mainTemplate.json** a **createUiDefinition.json**. Soubor **mainTemplate.json** definuje prostředky Azure, které se mají vytvořit jako součást spravované aplikace. Šablona se nijak neliší od běžné šablony Resource Manageru. **createUiDefinition.json** generuje uživatelské rozhraní pro uživatele, kteří vytvářejí spravované aplikace prostřednictvím portálu.

## <a name="next-steps"></a>Další kroky

Publikovali jste definici spravované aplikace. Teď se dozvíte, jak nasadit instanci této definice.

> [!div class="nextstepaction"]
> [Rychlý start: Nasazení aplikace katalogu služeb](deploy-service-catalog-quickstart.md)