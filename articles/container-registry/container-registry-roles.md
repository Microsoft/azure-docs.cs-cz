---
title: Role a oprávnění RBAC
description: Pomocí řízení přístupu azure na základě rolí (RBAC) a správy identit a přístupu (IAM) poskytovat jemně odstupňovaná oprávnění k prostředkům v registru kontejnerů Azure.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893480"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Role a oprávnění registru kontejnerů Azure

Služba Azure Container Registry podporuje sadu [předdefinovaných rolí Azure,](../role-based-access-control/built-in-roles.md) které poskytují různé úrovně oprávnění registru kontejnerů Azure. Pomocí [řízení přístupu azure na základě rolí](../role-based-access-control/index.yml) (RBAC) přiřadit konkrétní oprávnění uživatelům, instanční objekty nebo jiné identity, které potřebují k interakci s registrem. 

| Role/oprávnění       | [Správce prostředků aplikace Access](#access-resource-manager) | [Vytvořit nebo odstranit registr](#create-and-delete-registry) | [Nabízený obrázek](#push-image) | [Vyžádat obrázek](#pull-image) | [Odstranění obrazových dat](#delete-image-data) | [Změnit zásady](#change-policies) |   [Podepisovat obrázky](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Vlastník | × | × | × | × | × | × |  |  
| Přispěvatel | × | × | × |  × | × | × |  |  
| Čtenář | × |  |  | × |  |  |  |
| AcrPush |  |  | × | × | |  |  |  
| AcrPull |  |  |  | × |  |  |  |  
| AcrDelete |  |  |  |  | × |  |  |
| AcrImageSigner |  |  |  |  |  |  | × |

## <a name="differentiate-users-and-services"></a>Odlišit uživatele a služby

Kdykoli jsou použita časová oprávnění, osvědčeným postupem je poskytnout nejomezenější sadu oprávnění pro osobu nebo službu k provedení úkolu. Následující sady oprávnění představují sadu funkcí, které mohou být používány lidmi a bezhlavé služby.

### <a name="cicd-solutions"></a>Ci/CD řešení

Při automatizaci `docker build` příkazů z řešení CI/CD potřebujete `docker push` možnosti. Pro tyto scénáře bezhlavé služby doporučujeme přiřadit roli **AcrPush.** Tato role, na rozdíl od širší role **přispěvatele,** brání účtu provádět jiné operace registru nebo přístup ke Správci prostředků Azure.

### <a name="container-host-nodes"></a>Uzly hostitele kontejneru

Podobně uzly spuštěné kontejnery potřebují roli **AcrPull,** ale neměly by vyžadovat možnosti **čtečky.**

### <a name="visual-studio-code-docker-extension"></a>Rozšíření Kiktor kódu Visual Studio

Pro nástroje, jako je rozšíření Visual Studio Code [Docker](https://code.visualstudio.com/docs/azure/docker), další přístup poskytovatele prostředků je nutné uvést seznam dostupných registrů kontejnerů Azure. V takovém případě poskytněte uživatelům přístup k roli **Čtečka** nebo **Přispěvatel.** Tyto role `docker pull` `docker push`umožňují `az acr list` `az acr build`, , , a další funkce. 

## <a name="access-resource-manager"></a>Správce prostředků aplikace Access

Přístup k Prostředkům Azure je vyžadován pro správu portálu Azure a správy registru pomocí [příkazového příkazového příkazu k Řešení Azure](/cli/azure/). Chcete-li například získat seznam registrů `az acr list` pomocí příkazu, potřebujete tuto sadu oprávnění. 

## <a name="create-and-delete-registry"></a>Vytvoření a odstranění registru

Možnost vytvářet a odstraňovat registry kontejnerů Azure.

## <a name="push-image"></a>Nabízený obrázek

Schopnost bitové `docker push` kopie nebo nabízení jiného [podporovaného artefaktu,](container-registry-image-formats.md) například grafu Helm, do registru. Vyžaduje [ověření](container-registry-authentication.md) s registrem pomocí autorizované identity. 

## <a name="pull-image"></a>Vyžádat obrázek

Možnost `docker pull` image bez karantény nebo vyžádat jiný [podporovaný artefakt,](container-registry-image-formats.md) například graf Helm, z registru. Vyžaduje [ověření](container-registry-authentication.md) s registrem pomocí autorizované identity.

## <a name="delete-image-data"></a>Odstranění obrazových dat

Možnost [odstranit image kontejneru](container-registry-delete.md)nebo odstranit jiné [podporované artefakty,](container-registry-image-formats.md) jako jsou grafy Helm, z registru.

## <a name="change-policies"></a>Změnit zásady

Možnost konfigurace zásad v registru. Zásady zahrnují vymazání bitové kopie, povolení karantény a podepisování obrázků.

## <a name="sign-images"></a>Podepisovat obrázky

Možnost podepisovat obrázky, obvykle přiřazené automatizovanému procesu, který by používal instanční objekt. Toto oprávnění je obvykle v kombinaci s [nabízenou bitovou kopii](#push-image) povolit odesílání důvěryhodné bitové kopie do registru. Podrobnosti najdete [v tématu Obsah důvěryhodnosti v Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Vlastní role

Stejně jako u jiných prostředků Azure můžete vytvořit vlastní [role](../role-based-access-control/custom-roles.md) s jemně odstupňovanými oprávněními k azure kontejneru registru. Potom přiřaďte vlastní role uživatelům, instančním objektům nebo jiným identitám, které je třeba pracovat s registrem. 

Chcete-li zjistit, která oprávnění chcete použít pro vlastní roli, podívejte se na seznam [akcí](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft.ContainerRegistry , zkontrolujte povolené akce [předdefinovaných rolí ACR](../role-based-access-control/built-in-roles.md)nebo spusťte následující příkaz:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Pokud chcete definovat vlastní roli, [přečtěte si postup vytvoření vlastní role](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> Ve vlastní roli Azure Container Registry aktuálně nepodporuje zástupné znaky, jako `Microsoft.ContainerRegistry/*` jsou nebo `Microsoft.ContainerRegistry/registries/*` které udělují přístup ke všem odpovídajícím akcím. Zadejte požadovanou akci jednotlivě v roli.

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazení rolí RBAC k identitě Azure pomocí [portálu Azure](../role-based-access-control/role-assignments-portal.md), [rozhraní příkazového příkazu Azure](../role-based-access-control/role-assignments-cli.md)nebo jiných nástrojů Azure.

* Přečtěte si o [možnostech ověřování](container-registry-authentication.md) pro Azure Container Registry.

* Další informace o povolení [oprávnění s rozsahem úložiště](container-registry-repository-scoped-permissions.md) (náhled) v registru kontejneru.