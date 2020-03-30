---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032353"
---
## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Chcete-li vytvořit instanční objekt s přístupem k registru kontejnerů, spusťte následující skript v [prostředí Azure Cloud Shell](../articles/cloud-shell/overview.md) nebo místní instalaci [příkazového příkazového příkazu k řešení Azure](/cli/azure/install-azure-cli). Skript je formátován pro prostředí Bash.

Před spuštěním skriptu `ACR_NAME` aktualizujte proměnnou názvem registru kontejneru. Hodnota `SERVICE_PRINCIPAL_NAME` musí být v rámci klienta služby Azure Active Directory jedinečná. Pokud se zobrazí`'http://acr-service-principal' already exists.`chyba " " , zadejte jiný název instančního objektu.

Volitelně můžete upravit `--role` hodnotu v příkazu [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] pokud chcete udělit různá oprávnění. Úplný seznam rolí naleznete v tématu [Role a oprávnění ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po spuštění skriptu si poznamenejte **ID** a heslo instančního **objektu**. Jakmile budete mít jeho pověření, můžete nakonfigurovat aplikace a služby k ověření do registru kontejneru jako instanční objekt.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Použití existujícího instančního objektu

Chcete-li udělit přístup registru k existujícímu instančnímu objektu, musíte přiřadit nový objekt zabezpečení služby. Stejně jako při vytváření nového instančního objektu můžete mimo jiné udělit pull, push and pull a přístup vlastníka.

Následující skript používá příkaz [az role create][az-role-assignment-create] k udělení oprávnění k `SERVICE_PRINCIPAL_ID` *vyžádat* instančníobjekt, který zadáte v proměnné. Pokud `--role` chcete udělit jinou úroveň přístupu, upravte hodnotu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
