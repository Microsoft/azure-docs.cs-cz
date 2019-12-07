---
title: Role a oprávnění RBAC
description: Využijte řízení přístupu na základě role (RBAC) Azure a správu identit a přístupu (IAM) k zajištění podrobných oprávnění k prostředkům v registru kontejnerů Azure.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893480"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry role a oprávnění

Služba Azure Container Registry podporuje sadu [integrovaných rolí Azure](../role-based-access-control/built-in-roles.md) , které poskytují různé úrovně oprávnění ke službě Azure Container Registry. Pomocí [řízení přístupu na základě role](../role-based-access-control/index.yml) (RBAC) v Azure můžete přiřadit konkrétní oprávnění uživatelům, instančním objektům nebo jiným identitám, které potřebují s registrem pracovat. 

| Role/oprávnění       | [Přístup Správce prostředků](#access-resource-manager) | [Vytvořit nebo odstranit registr](#create-and-delete-registry) | [Obrázek push](#push-image) | [Obrázek pro vyžádání obsahu](#pull-image) | [Odstranit data obrázku](#delete-image-data) | [Změnit zásady](#change-policies) |   [Podepsat obrázky](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Vlastník | × | × | × | × | × | × |  |  
| Přispěvatel | × | × | × |  × | × | × |  |  
| Čtenář | × |  |  | × |  |  |  |
| AcrPush |  |  | × | × | |  |  |  
| AcrPull |  |  |  | × |  |  |  |  
| AcrDelete |  |  |  |  | × |  |  |
| AcrImageSigner |  |  |  |  |  |  | × |

## <a name="differentiate-users-and-services"></a>Odlišení uživatelů a služeb

Pokaždé, když se uplatní oprávnění, osvědčeným postupem je poskytnout nejvíce omezené sady oprávnění pro osobu nebo službu, aby bylo možné úkol provést. Následující sady oprávnění reprezentují sadu funkcí, které mohou používat lidé a bezobslužné služby.

### <a name="cicd-solutions"></a>Řešení CI/CD

Při automatizaci `docker build` příkazů z řešení CI/CD potřebujete `docker push` možnosti. Pro tyto scénáře bezpostupné služby doporučujeme přiřadit roli **AcrPush** . Tato role na rozdíl od širší role **přispěvatele** zabraňuje účtu v provádění jiných operací registru nebo přístupu k Azure Resource Manager.

### <a name="container-host-nodes"></a>Uzly hostitele kontejneru

Podobně uzly se spuštěnými kontejnery potřebují roli **AcrPull** , ale neměly by vyžadovat možnosti **čtenářů** .

### <a name="visual-studio-code-docker-extension"></a>Rozšíření Docker Visual Studio Code

Pro nástroje, jako je [rozšíření docker](https://code.visualstudio.com/docs/azure/docker)Visual Studio Code, je pro výpis dostupných registrů kontejnerů Azure potřeba další přístup od poskytovatele prostředků. V takovém případě Poskytněte uživatelům přístup k roli **Čtenář** nebo **Přispěvatel** . Tyto role povolují `docker pull`, `docker push`, `az acr list`, `az acr build`a další funkce. 

## <a name="access-resource-manager"></a>Přístup Správce prostředků

Pro Azure Portal a správu registru se v [Azure CLI](/cli/azure/)vyžaduje přístup k Azure Resource Manager. Chcete-li například získat seznam registrů pomocí příkazu `az acr list`, budete potřebovat tuto sadu oprávnění. 

## <a name="create-and-delete-registry"></a>Vytvoření a odstranění registru

Možnost vytvářet a odstraňovat Registry kontejnerů Azure.

## <a name="push-image"></a>Obrázek push

Možnost `docker push` obrázku nebo Vložit jiný [podporovaný artefakt](container-registry-image-formats.md) , jako je například graf Helm, do registru. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity. 

## <a name="pull-image"></a>Obrázek pro vyžádání obsahu

Možnost `docker pull` image mimo v karanténě nebo z registru vyžádat další [podporovaný artefakt](container-registry-image-formats.md) , jako je například graf Helm. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity.

## <a name="delete-image-data"></a>Odstranit data obrázku

Možnost [Odstranit image kontejneru](container-registry-delete.md)nebo odstranit jiné [podporované artefakty](container-registry-image-formats.md) , jako jsou grafy Helm, z registru.

## <a name="change-policies"></a>Změnit zásady

Možnost konfigurovat zásady v registru. Zásady zahrnují vymazání imagí, povolení karantény a podepisování obrázků.

## <a name="sign-images"></a>Podepsat obrázky

Schopnost podepisovat obrázky obvykle přiřazené automatizovanému procesu, který by používal instanční objekt. Toto oprávnění je obvykle kombinováno s [imagí push](#push-image) , aby umožňovalo vložení důvěryhodného obrázku do registru. Podrobnosti najdete v tématu [vztah důvěryhodnosti obsahu v Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Vlastní role

Stejně jako u jiných prostředků Azure můžete vytvořit vlastní [role](../role-based-access-control/custom-roles.md) s jemně odstupňovaným oprávněním pro Azure Container Registry. Pak přiřaďte vlastní role uživatelům, instančním objektům nebo jiným identitám, které potřebují pracovat s registrem. 

Chcete-li určit, která oprávnění použít u vlastní role, Projděte si seznam [akcí](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft. ContainerRegistry, zkontrolujte povolené akce [integrovaných rolí ACR](../role-based-access-control/built-in-roles.md)nebo spusťte následující příkaz:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Pokud chcete definovat vlastní roli, přečtěte si téma [Postup vytvoření vlastní role](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> V rámci vlastní role Azure Container Registry aktuálně nepodporuje zástupné znaky, například `Microsoft.ContainerRegistry/*` nebo `Microsoft.ContainerRegistry/registries/*`, které udělují přístup ke všem odpovídajícím akcím. Zadejte všechny požadované akce jednotlivě v roli.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o přiřazení rolí RBAC k identitě Azure pomocí [Azure Portal](../role-based-access-control/role-assignments-portal.md), rozhraní příkazového [řádku Azure](../role-based-access-control/role-assignments-cli.md)nebo dalších nástrojů Azure.

* Seznamte se s [možnostmi ověřování](container-registry-authentication.md) pro Azure Container Registry.

* Přečtěte si, jak povolit [oprávnění vymezená v úložišti](container-registry-repository-scoped-permissions.md) (Preview) v registru kontejnerů.