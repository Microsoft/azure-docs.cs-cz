---
title: Služba Azure Container Registry – role a oprávnění
description: Pomocí řízení přístupu Azure na základě rolí (RBAC) a správu identit a přístupu (IAM) poskytují jemně odstupňovaná oprávnění k prostředkům ve službě Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 5dabab02758b4ade0f668adddf7644a654e1804b
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53756452"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry role a oprávnění

Služba Azure Container Registry podporuje sadu role služby Azure, které poskytují různé úrovně oprávnění do služby Azure container registry. Použití Azure [řízení přístupu na základě rolí](../role-based-access-control/index.yml) (RBAC) k přiřazení konkrétních oprávnění uživatelům nebo instanční, které potřebují komunikovat pomocí registru.

| Role a oprávnění       | [Přístup k Resource Manageru](#access-resource-manage)| [Vytváření/odstraňování registru](#create/delete-registry) | [Nahrání image](#push-image) | [Stáhněte si image](#pull-image) | [Změna zásad](#change-polices) |   [Znak bitových kopií](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- |
| Vlastník | X | X | X | X | X |  |  
| Přispěvatel | X | X | X | X | X |  |  
| Čtenář | X |  |  | X |  |  | 
| AcrPush |  |  | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  
| AcrImageSigner |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Rozlišení uživatelů a služeb

Žádná oprávnění čas se použijí, je osvědčeným postupem zajistit nejvíc omezenou sadu oprávnění pro osoby nebo služby, k provádění různých úloh. Následující sady oprávnění představují sadu funkcí, které mohou využívat člověka a bezobslužného služby.

### <a name="cicd-solutions"></a>Řešení CI/CD

Při automatizaci `docker build` příkazy z řešení pro CI/CD, budete potřebovat `docker push` možnosti. Pro tyto scénáře bezobslužného služby doporučujeme přiřazení **AcrPush** role. Tato role, na rozdíl od širší **Přispěvatel** role, zabraňuje účtu z provádění jiných operací registru nebo přístup k Azure Resource Manageru.

### <a name="container-host-nodes"></a>Uzly Container hostitele

Podobně, musí uzly spuštěnými kontejnery **AcrPull** role, ale neměli byste potřebovat **čtečky** možnosti.

### <a name="visual-studio-code-docker-extension"></a>Rozšíření sady Visual Studio Code Dockeru

Pro nástroje, jako je Visual Studio Code [rozšíření Docker](https://code.visualstudio.com/docs/azure/docker), je nutné mít další prostředků poskytovatele přístup do seznamu k dispozici Azure container Registry. V takovém případě poskytují uživatelům přístup k **čtečky** nebo **Přispěvatel** role. Pracovníci v těchto rolích povolit `docker pull`, `docker push`, `az acr list`, `az acr build`a další možnosti. 

## <a name="access-resource-manager"></a>Přístup k Resource Manageru

Přístup ke službě Azure Resource Manageru se vyžaduje pro na webu Azure portal a [rozhraní příkazového řádku Azure](/cli/azure/). Například chcete získat seznam registrů pomocí `az acr list` příkaz, musíte toto oprávnění nastavena. 

## <a name="createdelete-registry"></a>Vytváření/odstraňování registru

Možnost vytvářet a odstraňovat registry kontejnerů Azure.

## <a name="push-image"></a>Nahrání image

Schopnost `docker push` bitové kopie, nebo jiné podporované artefaktu, nahrajete do registru. Vyžaduje [ověřování](container-registry-authentication.md) s registrem pomocí oprávnění identity. 

## <a name="pull-image"></a>Stáhněte si image

Schopnost `docker pull` jiných-umístí do karantény image ani přijmout jejich změny podporované jiným artefaktem, z registru. Vyžaduje [ověřování](container-registry-authentication.md) s registrem pomocí oprávnění identity.

## <a name="change-policies"></a>Změna zásad

Umožňuje nakonfigurovat zásady v registru. Zásady zahrnovat image vyprazdňování, povolení karantény a podepisování bitové kopie.

## <a name="sign-images"></a>Znak bitových kopií

Možnost přihlásit se Image, obvykle přiřazená automatizovaný proces, který by použít instanční objekt služby. Toto oprávnění je obvykle kombinovat s [nabízených image](#push-image) umožňující nahráním důvěryhodného image do registru. Podrobnosti najdete v tématu [obsahu důvěryhodnosti ve službě Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Další postup

* Další informace o přiřazení role RBAC pro Azure identity pomocí [webu Azure portal](../role-based-access-control/role-assignments-portal.md), [rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md), nebo jiné nástroje pro Azure.

* Další informace o [možnosti ověřování](container-registry-authentication.md) pro službu Azure Container Registry.
