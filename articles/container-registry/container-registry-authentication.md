---
title: Ověřování pomocí služby Azure container registry
description: Možnosti ověřování pro službu Azure container registry, včetně Azure Active Directory service přímo a registru přihlášení objekty zabezpečení.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c0c2323d1864be24edbf6005d634ae1d08bba8ea
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116602"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Ověřování pomocí privátního registru kontejnerů Dockeru

Existuje několik způsobů, jak ověřování pomocí služby Azure container registry, z nichž každý se vztahuje na jeden nebo více scénářů využití registru.

Můžete se přihlásit k registru přímo prostřednictvím [jednotlivých přihlášení](#individual-login-with-azure-ad), a vaše aplikace a orchestrátorů kontejnerů můžete provádět bezobslužné nebo "bezobslužný", ověřování pomocí služby Azure Active Directory (Azure AD) [ instanční objekt služby](#service-principal).

Služba Azure Container Registry nepodporuje neověřené operace Dockeru nebo anonymní přístup. Pro veřejné Image, můžete použít [Docker Hubu](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Jednotlivé přihlášení pomocí Azure AD

Při práci s registrem přímo, jako je například přetahující Image do a nahráním Image z vaší pracovní stanici, ověřit pomocí [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v příkaz [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po přihlášení pomocí `az acr login`, rozhraní příkazového řádku používá token, který vytvoří, když jste spustili `az login` bez problémů ověřování relace k vašemu registru. Po přihlášení tímto způsobem své přihlašovací údaje jsou uložené v mezipaměti a následné `docker` příkazy nevyžadují, aby uživatelské jméno nebo heslo. Pokud vyprší platnost vašeho tokenu, můžete ho aktualizovat pomocí `az acr login` příkazu donutit. Pomocí `az acr login` s identitami, Azure poskytuje [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Instanční objekt

Můžete přiřadit [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md) do vašeho registru, a vaše aplikace nebo služba může používat pro bezobslužné ověření. Instanční objekty umožňují [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) do registru, a můžete přiřadit více objektů služby do registru. Více instanční objekty umožňují definovat různá přístupová pro různé aplikace.

Dostupné role jsou:

  * **Čtečka**: o přijetí změn
  * **Přispěvatel**: nahrání a stažení
  * **Vlastník**: o přijetí změn, push a přiřadit role jiným uživatelům

Instanční objekty umožňují bezobslužného připojení k registru v nabízení a vyžadování scénáře, jako jsou následující:

  * *Čtečka*: nasazení kontejneru z registru pro systémy Orchestrace včetně Kubernetes, DC/OS a Docker Swarm. Můžete také využít z registry kontejnerů pro související služby Azure, jako [AKS](../aks/index.yml), [služby App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), a ostatní.

  * *Přispěvatel*: průběžné integrace a nasazování řešení, jako je Azure kanály nebo Jenkinse, který sestavování imagí kontejneru a nahrajete do registru.

> [!TIP]
> Spuštěním můžete obnovit heslo instančního objektu [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) příkazu.
>

Můžete taky přihlásit přímo pomocí instančního objektu. Zadejte ID aplikace a heslo instančního objektu pro služby `docker login` příkaz:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Po přihlášení Docker ukládá do mezipaměti přihlašovací údaje, takže není potřeba pamatovat si ID aplikace.

V závislosti na verzi mít nainstalovaný Docker, může zobrazit upozornění zabezpečení doporučující použití `--password-stdin` parametru. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/) referenčních příkazu.

Další informace o použití instančního objektu pro bezobslužné ověření do služby ACR, naleznete v tématu [ověřování Azure Container Registry pomocí instančních objektů](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů zahrnuje uživatelský účet správce, který je ve výchozím nastavení zakázané. Můžete povolit uživatele s rolí správce a spravovat svoje přihlašovací údaje v [webu Azure portal](container-registry-get-started-portal.md#create-a-container-registry), nebo pomocí rozhraní příkazového řádku Azure.

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
