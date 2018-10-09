---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a76f0205a34b106cf04b61938b1b576db9325c40
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858039"
---
## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

K vytvoření instančního objektu s přístupem do vašeho registru kontejneru, spusťte následující skript [Azure Cloud Shell](../articles/cloud-shell/overview.md) nebo místní instalaci [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). Skript formátována pro prostředí Bash.

Před spuštěním skriptu, aktualizujte `ACR_NAME` proměnné s názvem vašeho registru kontejneru. `SERVICE_PRINCIPAL_NAME` Hodnota musí být jedinečný v rámci vašeho tenanta Azure Active Directory. Pokud se zobrazí "`'http://acr-service-principal' already exists.`" Chyba, zadejte jiný název pro instanční objekt.

Volitelně můžete upravit `--role` hodnotu [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] příkazu, pokud budete chtít udělit jiná oprávnění.

Po spuštění skriptu, poznamenejte si hodnotu objektu služby **ID** a **heslo**. Jakmile budete mít svoje přihlašovací údaje, můžete nakonfigurovat aplikace a služby pro ověření do vašeho registru kontejneru jako instanční objekt služby.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Použít existující instanční objekt služby

Pokud chcete udělit přístup k registru do existujícího instančního objektu, musíte přiřadit nové role pro instanční objekt. Stejně jako u vytváření nového instančního objektu, můžete udělit o přijetí změn, push a pull a přístup vlastníka.

Následující skript pomocí [vytvořit přiřazení role az] [ az-role-assignment-create] příkaz *o přijetí změn* oprávnění instančnímu objektu služby, který jste zadali v `SERVICE_PRINCIPAL_ID` proměnné. Upravit `--role` hodnotu, pokud byste chtěli poskytnout různé úrovně přístupu.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
