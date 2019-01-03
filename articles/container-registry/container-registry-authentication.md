---
title: Ověřování pomocí služby Azure container registry
description: Možnosti ověřování pro služby Azure container registry, včetně přihlášení pomocí Azure Active Directory identitu, používání instančních objektů a pomocí přihlašovacích údajů správce volitelné.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a68e4f70dac7aace9d49a41ecf282525ce6b1fd6
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752873"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Ověřování pomocí privátního registru kontejnerů Dockeru

Existuje několik způsobů, jak ověřování pomocí služby Azure container registry, z nichž každý se vztahuje na jeden nebo více scénářů využití registru.

Můžete se přihlásit k registru přímo prostřednictvím [jednotlivých přihlášení](#individual-login-with-azure-ad), nebo vaše aplikace a orchestrátorů kontejnerů můžete provádět bezobslužné nebo "bezobslužný", ověřování pomocí služby Azure Active Directory (Azure AD) [ instanční objekt služby](#service-principal).

Služba Azure Container Registry nepodporuje neověřené operace Dockeru nebo anonymní přístup. Pro veřejné Image, můžete použít [Docker Hubu](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Jednotlivé přihlášení pomocí Azure AD

Při práci s registrem přímo, jako je například přetahující Image do a nahráním Image z vaší pracovní stanici, ověřit pomocí [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v příkaz [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po přihlášení pomocí `az acr login`, rozhraní příkazového řádku používá token, který vytvoří, když jste spustili [az login](/cli/azure/reference-index#az-login) bez problémů ověřování relace k vašemu registru. Po přihlášení tímto způsobem své přihlašovací údaje jsou uložené v mezipaměti a následné `docker` příkazy nevyžadují, aby uživatelské jméno nebo heslo. Pokud vyprší platnost vašeho tokenu, můžete ho aktualizovat pomocí `az acr login` příkazu donutit. Pomocí `az acr login` s identitami, Azure poskytuje [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Instanční objekt

Pokud přiřadíte [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md) do vašeho registru, aplikace nebo služby lze použít pro bezobslužné ověření. Instanční objekty umožňují [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) do registru, a můžete přiřadit více objektů služby do registru. Více instanční objekty umožňují definovat různá přístupová pro různé aplikace.

Dostupné role pro registr kontejneru patří:

* **AcrPull**: o přijetí změn

* **AcrPush**: nahrání a stažení

* **Vlastník**: o přijetí změn, push a přiřadit role jiným uživatelům

Úplný seznam rolí, najdete v části [Azure Container Registry role a oprávnění](container-registry-roles.md).

Skripty rozhraní příkazového řádku k vytvoření ID aplikace instančního objektu služby a heslo pro ověřování pomocí služby Azure container registry, nebo použít existující instanční objekt služby, najdete v části [ověřování Azure Container Registry pomocí instančních objektů](container-registry-auth-service-principal.md).

Instanční objekty umožňují bezobslužného připojení k registru v pull a push scénáře, jako jsou následující:

  * *O přijetí změn*: Nasazování kontejnerů z registru pro systémy Orchestrace včetně Kubernetes, DC/OS a Docker Swarm. Můžete také využít z registry kontejnerů pro související služby Azure, jako [Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [služby App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)a další.

  * *Push*: Sestavování imagí kontejneru a nahrajete je do registru pomocí průběžné integrace a nasazování řešení, jako je Azure kanály nebo Jenkinse.

Můžete taky přihlásit přímo pomocí instančního objektu. Zadejte ID aplikace a heslo instančního objektu pro služby `docker login` příkaz:

```
docker login myregistry.azurecr.io -u <SP_APP_ID> -p <SP_PASSWD>
```

Po přihlášení Docker ukládá do mezipaměti přihlašovací údaje, takže není potřeba pamatovat si ID aplikace.

V závislosti na verzi mít nainstalovaný Docker, může zobrazit upozornění zabezpečení doporučující použití `--password-stdin` parametru. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/) referenčních příkazu.

> [!TIP]
> Spuštěním můžete obnovit heslo instančního objektu [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) příkazu.
>

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů zahrnuje uživatelský účet správce, který je ve výchozím nastavení zakázané. Můžete povolit uživatele s rolí správce a spravovat svoje přihlašovací údaje v [webu Azure portal](container-registry-get-started-portal.md#create-a-container-registry), nebo pomocí rozhraní příkazového řádku Azure nebo dalších nástrojích Azure.

> [!IMPORTANT]
> Účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. Nedoporučujeme přihlašovací údaje účtu správce pro sdílení obsahu s více uživateli. Všichni uživatelé ověřování pomocí účtu správce se zobrazí jako jeden uživatel s oprávněním k nabízení a vyžadování do registru. Změna nebo zakázání účtu zakáže přístup k registru pro všechny uživatele, kteří používají pověření uživatele. Jednotlivé identity se doporučuje pro uživatele a instančních objektů pro bezobslužné scénáře.
>

Účet správce je k dispozici se dvě hesla, které může být znovu vygenerován. Dvě hesla bylo možné zachovat připojení k registru pomocí jednoho hesla, zatímco si znovu vygenerujete druhé. Pokud je povolený účet správce, můžete předat uživatelské jméno a heslo buď `docker login` příkaz pro základní ověřování do registru. Příklad:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Znovu se doporučuje používat Docker `--password-stdin` parametr místo jeho zadáním na příkazovém řádku pro zvýšení zabezpečení. Můžete také určit jenom svoje uživatelské jméno bez `-p`a zadejte své heslo po zobrazení výzvy.

Pokud chcete povolit uživatele s rolí správce pro existující registr, můžete použít `--admin-enabled` parametr [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) příkaz v rozhraní příkazového řádku Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Můžete povolit uživatele s rolí správce na webu Azure Portal přejděte v registru, vyberete **přístupové klíče** pod **nastavení**, pak **povolit** pod **správce Uživatel**.

![Povolit uživatele s rolí správce uživatelského rozhraní na webu Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Další postup

* [Nahrání první image pomocí rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
