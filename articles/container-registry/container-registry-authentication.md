---
title: Ověřování pomocí služby Azure container registry
description: Možnosti ověřování pro služby Azure container registry, včetně přihlášení pomocí Azure Active Directory identitu, používání instančních objektů a pomocí přihlašovacích údajů správce volitelné.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9682b9b832a8fd7374cb84e8cc6faad69df15945
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979060"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Ověřování pomocí privátního registru kontejnerů Dockeru

Existuje několik způsobů, jak ověřování pomocí služby Azure container registry, z nichž každý se vztahuje na jeden nebo více scénářů využití registru.

Můžete se přihlásit k registru přímo prostřednictvím [jednotlivých přihlášení](#individual-login-with-azure-ad), nebo vaše aplikace a orchestrátorů kontejnerů můžete provádět bezobslužné nebo "bezobslužný", ověřování pomocí služby Azure Active Directory (Azure AD) [ instanční objekt služby](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Jednotlivé přihlášení pomocí Azure AD

Při práci s registrem přímo, jako je například přetahující Image do a nahráním Image z pracovní stanice vývoj ověřovat pomocí [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v příkaz [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po přihlášení pomocí `az acr login`, rozhraní příkazového řádku používá token, který vytvoří, když jste spustili [az login](/cli/azure/reference-index#az-login) bez problémů ověřování relace k vašemu registru. Po přihlášení tímto způsobem své přihlašovací údaje jsou uložené v mezipaměti a následné `docker` příkazy v relaci nevyžadují, aby uživatelské jméno nebo heslo. 

Pro přístup k registru token, který používá `az acr login` platí za 1 hodinu, proto doporučujeme, aby vždy přihlášení k registru dřív, než spustíte `docker` příkazu. Pokud vyprší platnost vašeho tokenu, můžete ho aktualizovat pomocí `az acr login` příkazu donutit. 

Pomocí `az acr login` s identitami, Azure poskytuje [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md). Pro některé scénáře můžete chtít přihlásit k registru s vlastním jednotlivé identity ve službě Azure AD. Pro scénáře mezi službami nebo pro zvládání potřeb pracovní skupiny, pokud nechcete spravovat jednotlivé přístup, můžete se také přihlásit [spravované identity pro prostředky Azure](container-registry-authentication-managed-identity.md).

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

Můžete taky přihlásit přímo pomocí instančního objektu. Když spustíte následující příkaz, interaktivně zadejte ID aplikace instančního objektu služby (uživatelské jméno) a heslo po zobrazení výzvy. Osvědčené postupy pro správu přihlašovací údaje, najdete v článku [docker login](https://docs.docker.com/engine/reference/commandline/login/) referenčních příkazu:

```
docker login myregistry.azurecr.io
```

Po přihlášení Docker ukládá do mezipaměti přihlašovací údaje, takže není potřeba pamatovat si ID aplikace.

> [!TIP]
> Spuštěním můžete obnovit heslo instančního objektu [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) příkazu.
>

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů zahrnuje uživatelský účet správce, který je ve výchozím nastavení zakázané. Můžete povolit uživatele s rolí správce a spravovat svoje přihlašovací údaje na webu Azure Portal nebo pomocí rozhraní příkazového řádku Azure nebo dalších nástrojích Azure.

> [!IMPORTANT]
> Účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. Nedoporučujeme přihlašovací údaje účtu správce pro sdílení obsahu s více uživateli. Všichni uživatelé ověřování pomocí účtu správce se zobrazí jako jeden uživatel s oprávněním k nabízení a vyžadování do registru. Změna nebo zakázání účtu zakáže přístup k registru pro všechny uživatele, kteří používají pověření uživatele. Jednotlivé identity se doporučuje pro uživatele a instančních objektů pro bezobslužné scénáře.
>

Účet správce je k dispozici se dvě hesla, které může být znovu vygenerován. Dvě hesla bylo možné zachovat připojení k registru pomocí jednoho hesla, zatímco si znovu vygenerujete druhé. Pokud je povolený účet správce, můžete předat uživatelské jméno a heslo buď `docker login` příkaz po zobrazení výzvy pro základní ověřování do registru. Příklad:

```
docker login myregistry.azurecr.io 
```


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
