---
title: Role a oprávnění Azure
description: Využijte řízení přístupu na základě role Azure (Azure RBAC) a správu identit a přístupu (IAM) k zajištění podrobných oprávnění k prostředkům v registru kontejnerů Azure.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: b8562d3e33cd49082d4ba4d8567d5f0c816070b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661380"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry role a oprávnění

Služba Azure Container Registry podporuje sadu [integrovaných rolí Azure](../role-based-access-control/built-in-roles.md) , které poskytují různé úrovně oprávnění ke službě Azure Container Registry. Použijte [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/index.yml) k přiřazení konkrétních oprávnění uživatelům, instančním objektům nebo jiným identitám, které potřebují pracovat s registrem. Můžete také definovat [vlastní role](#custom-roles) s podrobnými oprávněními k registru pro různé operace.

| Role/oprávnění       | [Přístup Správce prostředků](#access-resource-manager) | [Vytvořit nebo odstranit registr](#create-and-delete-registry) | [Obrázek push](#push-image) | [Obrázek pro vyžádání obsahu](#pull-image) | [Odstranit data obrázku](#delete-image-data) | [Změnit zásady](#change-policies) |   [Podepsat obrázky](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Vlastník | X | X | X | X | X | X |  |  
| Přispěvatel | X | X | X |  X | X | X |  |  
| Čtenář | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | × |  |  |  |  
| AcrDelete |  |  |  |  | × |  |  |
| AcrImageSigner |  |  |  |  |  |  | × |

## <a name="differentiate-users-and-services"></a>Odlišení uživatelů a služeb

Pokaždé, když se uplatní oprávnění, osvědčeným postupem je poskytnout nejvíce omezené sady oprávnění pro osobu nebo službu, aby bylo možné úkol provést. Následující sady oprávnění reprezentují sadu funkcí, které mohou používat lidé a bezobslužné služby.

### <a name="cicd-solutions"></a>Řešení CI/CD

Při automatizaci `docker build` příkazů z řešení CI/CD potřebujete `docker push` Možnosti. Pro tyto scénáře bezpostupné služby doporučujeme přiřadit roli **AcrPush** . Tato role na rozdíl od širší role **přispěvatele** zabraňuje účtu v provádění jiných operací registru nebo přístupu k Azure Resource Manager.

### <a name="container-host-nodes"></a>Uzly hostitele kontejneru

Podobně uzly se spuštěnými kontejnery potřebují roli **AcrPull** , ale neměly by vyžadovat možnosti **čtenářů** .

### <a name="visual-studio-code-docker-extension"></a>Rozšíření Docker Visual Studio Code

Pro nástroje, jako je [rozšíření docker](https://code.visualstudio.com/docs/azure/docker)Visual Studio Code, je pro výpis dostupných registrů kontejnerů Azure potřeba další přístup od poskytovatele prostředků. V takovém případě Poskytněte uživatelům přístup k roli **Čtenář** nebo **Přispěvatel** . Tyto role umožňují `docker pull` , `docker push` , `az acr list` , a `az acr build` Další funkce. 

## <a name="access-resource-manager"></a>Přístup Správce prostředků

Pro Azure Portal a správu registru se v [Azure CLI](/cli/azure/)vyžaduje přístup k Azure Resource Manager. Chcete-li například získat seznam registrů pomocí `az acr list` příkazu, budete potřebovat tuto sadu oprávnění. 

## <a name="create-and-delete-registry"></a>Vytvoření a odstranění registru

Možnost vytvářet a odstraňovat Registry kontejnerů Azure.

## <a name="push-image"></a>Obrázek push

Možnost `docker push` obrázku nebo vložení dalšího [podporovaného artefaktu](container-registry-image-formats.md) , jako je například graf Helm, do registru. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity. 

## <a name="pull-image"></a>Obrázek pro vyžádání obsahu

Možnost `docker pull` obrázku mimo v karanténě nebo si z registru vyžádejte jiný [podporovaný artefakt](container-registry-image-formats.md) , jako je například Helm graf. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity.

## <a name="delete-image-data"></a>Odstranit data obrázku

Možnost [Odstranit image kontejneru](container-registry-delete.md)nebo odstranit jiné [podporované artefakty](container-registry-image-formats.md) , jako jsou grafy Helm, z registru.

## <a name="change-policies"></a>Změnit zásady

Možnost konfigurovat zásady v registru. Zásady zahrnují vymazání imagí, povolení karantény a podepisování obrázků.

## <a name="sign-images"></a>Podepsat obrázky

Schopnost podepisovat obrázky obvykle přiřazené automatizovanému procesu, který by používal instanční objekt. Toto oprávnění je obvykle kombinováno s [imagí push](#push-image) , aby umožňovalo vložení důvěryhodného obrázku do registru. Podrobnosti najdete v tématu [vztah důvěryhodnosti obsahu v Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Vlastní role

Stejně jako u jiných prostředků Azure můžete vytvořit [vlastní role](../role-based-access-control/custom-roles.md) s jemně odstupňovaným oprávněním pro Azure Container Registry. Pak přiřaďte vlastní role uživatelům, instančním objektům nebo jiným identitám, které potřebují pracovat s registrem. 

Chcete-li určit, která oprávnění použít u vlastní role, Projděte si seznam [akcí](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft. ContainerRegistry, zkontrolujte povolené akce [integrovaných rolí ACR](../role-based-access-control/built-in-roles.md)nebo spusťte následující příkaz:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Pokud chcete definovat vlastní roli, přečtěte si téma [Postup vytvoření vlastní role](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> V rámci vlastní role Azure Container Registry aktuálně nepodporuje zástupné znaky, například `Microsoft.ContainerRegistry/*` nebo `Microsoft.ContainerRegistry/registries/*` , které udělují přístup ke všem odpovídajícím akcím. Zadejte všechny požadované akce jednotlivě v roli.

### <a name="example-custom-role-to-import-images"></a>Příklad: vlastní role pro import imagí

Následující JSON například definuje minimální akce pro vlastní roli, která povoluje [Import imagí](container-registry-import-images.md) do registru.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

Pokud chcete vytvořit nebo aktualizovat vlastní roli pomocí popisu JSON, použijte rozhraní příkazového [řádku Azure](../role-based-access-control/custom-roles-cli.md), [Azure Resource Manager šablonu](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)nebo jiné nástroje Azure. Přidejte nebo odeberte přiřazení rolí pro vlastní roli stejným způsobem, jakým spravujete přiřazení rolí pro předdefinované role Azure.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o přiřazení rolí Azure k identitě Azure pomocí [Azure Portal](../role-based-access-control/role-assignments-portal.md), rozhraní příkazového [řádku Azure](../role-based-access-control/role-assignments-cli.md)nebo dalších nástrojů Azure.

* Seznamte se s [možnostmi ověřování](container-registry-authentication.md) pro Azure Container Registry.

* Přečtěte si, jak povolit [oprávnění vymezená v úložišti](container-registry-repository-scoped-permissions.md) (Preview) v registru kontejnerů.