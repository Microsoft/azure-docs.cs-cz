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
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245046"
---
## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Pokud chcete vytvořit instanční objekt s přístupem k registru kontejneru, spusťte následující skript v [Azure Cloud Shell](../articles/cloud-shell/overview.md) nebo místní instalaci rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Skript je naformátován pro prostředí bash shell.

Před spuštěním skriptu aktualizujte `ACR_NAME` proměnnou názvem registru kontejneru. `SERVICE_PRINCIPAL_NAME`Hodnota musí být jedinečná v rámci vašeho tenanta Azure Active Directory. Pokud se zobrazí `'http://acr-service-principal' already exists.` Chyba, zadejte jiný název instančního objektu.

`--role`V případě, že chcete udělit různá oprávnění, můžete volitelně upravit hodnotu v příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] . Úplný seznam rolí najdete v tématu [ACR role a oprávnění](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po spuštění skriptu si poznamenejte **ID** a **heslo** instančního objektu. Jakmile budete mít své přihlašovací údaje, můžete nakonfigurovat své aplikace a služby pro ověřování v registru kontejneru jako instanční objekt.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Použití existujícího instančního objektu

Chcete-li udělit přístup k registru existujícímu instančnímu objektu, je nutné objektu služby přiřadit novou roli. Stejně jako u vytváření nového instančního objektu můžete udělit přístup pro vyžádání, nabízení a vyžádání a přístup k vlastníkovi mimo jiné.

Následující skript pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] udělí oprávnění k *vyžádanému* objektu služby, který zadáte v `SERVICE_PRINCIPAL_ID` proměnné. Pokud chcete `--role` udělit jinou úroveň přístupu, upravte hodnotu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
