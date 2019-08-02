---
title: Azure Container Registry – role a oprávnění
description: Využijte řízení přístupu na základě role (RBAC) Azure a správu identit a přístupu (IAM) k zajištění podrobných oprávnění k prostředkům v registru kontejnerů Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 793dbf056201a3315a9b77dfebbb9331a8ed7db1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310605"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry role a oprávnění

Služba Azure Container Registry podporuje sadu rolí Azure, které poskytují různé úrovně oprávnění ke službě Azure Container Registry. [Řízení přístupu na základě role](../role-based-access-control/index.yml) (RBAC) v Azure můžete použít k přiřazení konkrétních oprávnění uživatelům nebo instančním objektům, které potřebují pracovat s registrem.

| Role/oprávnění       | [Přístup Správce prostředků](#access-resource-manager) | [Vytvořit nebo odstranit registr](#create-and-delete-registry) | [Obrázek push](#push-image) | [Obrázek pro vyžádání obsahu](#pull-image) | [Odstranit data obrázku](#delete-image-data) | [Změnit zásady](#change-policies) |   [Podepsat obrázky](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Owner | X | X | X | X | X | X |  |  
| Přispěvatel | X | X | X |  X | X | X |  |  
| Čtenář | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Odlišení uživatelů a služeb

Pokaždé, když se uplatní oprávnění, osvědčeným postupem je poskytnout nejvíce omezené sady oprávnění pro osobu nebo službu, aby bylo možné úkol provést. Následující sady oprávnění reprezentují sadu funkcí, které mohou používat lidé a bezobslužné služby.

### <a name="cicd-solutions"></a>Řešení CI/CD

Při automatizaci `docker build` příkazů z řešení CI/CD potřebujete `docker push` možnosti. Pro tyto scénáře bezpostupné služby doporučujeme přiřadit roli **AcrPush** . Tato role na rozdíl od širší role **přispěvatele** zabraňuje účtu v provádění jiných operací registru nebo přístupu k Azure Resource Manager.

### <a name="container-host-nodes"></a>Uzly hostitele kontejneru

Podobně uzly se spuštěnými kontejnery potřebují roli **AcrPull** , ale neměly by vyžadovat možnosti **čtenářů** .

### <a name="visual-studio-code-docker-extension"></a>Rozšíření Docker Visual Studio Code

Pro nástroje, jako je [rozšíření docker](https://code.visualstudio.com/docs/azure/docker)Visual Studio Code, je pro výpis dostupných registrů kontejnerů Azure potřeba další přístup od poskytovatele prostředků. V takovém případě Poskytněte uživatelům přístup k roli **Čtenář** nebo **Přispěvatel** . Tyto role umožňují `docker pull` `docker push` ,,`az acr build`,adalšífunkce. `az acr list` 

## <a name="access-resource-manager"></a>Přístup Správce prostředků

Pro Azure Portal a správu registru se v [Azure CLI](/cli/azure/)vyžaduje přístup k Azure Resource Manager. Chcete-li například získat seznam registrů pomocí `az acr list` příkazu, budete potřebovat tuto sadu oprávnění. 

## <a name="create-and-delete-registry"></a>Vytvoření a odstranění registru

Možnost vytvářet a odstraňovat Registry kontejnerů Azure.

## <a name="push-image"></a>Obrázek push

Možnost `docker push` obrázku nebo vložení dalšího podporovaného artefaktu [](container-registry-image-formats.md) , jako je například graf Helm, do registru. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity. 

## <a name="pull-image"></a>Obrázek pro vyžádání obsahu

Možnost `docker pull` obrázku mimo v karanténě nebo si z registru vyžádejte jiný [podporovaný artefakt](container-registry-image-formats.md) , jako je například Helm graf. Vyžaduje [ověření](container-registry-authentication.md) pomocí registru pomocí autorizované identity.

## <a name="delete-image-data"></a>Odstranit data obrázku

Možnost [Odstranit image kontejneru](container-registry-delete.md)nebo odstranit jiné [podporované artefakty](container-registry-image-formats.md) , jako jsou grafy Helm, z registru.

## <a name="change-policies"></a>Změnit zásady

Možnost konfigurovat zásady v registru. Zásady zahrnují vymazání imagí, povolení karantény a podepisování obrázků.

## <a name="sign-images"></a>Podepsat obrázky

Schopnost podepisovat obrázky obvykle přiřazené automatizovanému procesu, který by používal instanční objekt. Toto oprávnění je obvykle kombinováno s [imagí push](#push-image) , aby umožňovalo vložení důvěryhodného obrázku do registru. Podrobnosti najdete v tématu [vztah důvěryhodnosti obsahu v Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Další postup

* Přečtěte si další informace o přiřazení rolí RBAC k identitě Azure pomocí [Azure Portal](../role-based-access-control/role-assignments-portal.md), rozhraní příkazového [řádku Azure](../role-based-access-control/role-assignments-cli.md)nebo dalších nástrojů Azure.

* Seznamte se s [možnostmi ověřování](container-registry-authentication.md) pro Azure Container Registry.
