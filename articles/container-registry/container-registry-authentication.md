---
title: Ověřování pomocí služby Azure kontejneru registru
description: Možnosti ověřování pro služby Azure kontejneru registru, včetně služby Azure Active Directory služby přihlášení objekty přímo a registru.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8fcebae21d73db75e19bd1091faa8f389f0ba40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165505"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Ověření pomocí privátní registru kontejner Docker

Existuje několik způsobů ověření s registru kontejner Azure, z nichž každý se vztahuje na jeden nebo více scénáře použití registru.

Můžete se přihlásit do registru přímo prostřednictvím [jednotlivých přihlášení](#individual-login-with-azure-ad), a vaše aplikace a kontejner orchestrators můžete provádět bez dozoru nebo "bezobslužných", ověřování pomocí Azure Active Directory (Azure AD) [ instanční objekt](#service-principal).

Azure kontejneru registru nepodporuje neověřené operations Docker nebo anonymní přístup. Pro veřejné Image, můžete použít [úložiště Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Jednotlivé přihlášení s Azure AD

Při práci s registr přímo, například bitové kopie k vyžádání a předání bitové kopie z vaší pracovní stanici, ověřit pomocí [az acr přihlášení](/cli/azure/acr?view=azure-cli-latest#az_acr_login) v [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Po přihlášení s `az acr login`, rozhraní příkazového řádku používá token vytvořen při provedení `az login` bezproblémově ověřit relace s registrem. Jakmile jste se přihlásili tímto způsobem, jsou vaše přihlašovací údaje uložené v mezipaměti a následné `docker` příkazy nevyžadují uživatelské jméno nebo heslo. Pokud vyprší platnost vašeho tokenu, můžete ho obnovit pomocí `az acr login` příkaz znovu k novému ověření. Pomocí `az acr login` s identitami, Azure poskytuje [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md).

## <a name="service-principal"></a>Instanční objekt

Můžete přiřadit [instanční objekt](../active-directory/develop/active-directory-application-objects.md) do registru, a aplikace nebo služby můžete použít pro bezobslužných ověřování. Objekty služby povolit [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) do registru, a několik hlavních objektů služby lze přiřadit registru. Několik hlavních objektů služby umožňují definovat jiný přístup pro různé aplikace.

Dostupné role jsou:

  * **Čtečka**: vyžádání obsahu
  * **Přispěvatel**: pull a push
  * **Vlastník**: pro vyžádání obsahu, push a přiřadit role jiným uživatelům

Objekty služby povolit bezobslužných připojení k registru ve scénářích nabízení a vyžadování takto:

  * *Čtečka*: nasazení kontejnerů z registru pro systémy orchestration včetně Kubernetes, DC/OS a Docker Swarm. Vám může také načítat z kontejneru registrech k související služby Azure, jako [AKS](../aks/index.yml), [služby App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), a ostatní.

  * *Přispěvatel*: průběžnou integraci a nasazení řešení jako Visual Studio Team Services (VSTS) nebo volaných, který vytvářet bitové kopie kontejneru a vložit je registru.

> [!TIP]
> Můžete obnovit heslo objekt služby spuštěním [az sp resetování-přihlašovací údaje služby ad](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) příkaz.
>

Můžete také přihlásit přímo s hlavní službou. Zadejte ID aplikace a heslo k objektu zabezpečení služby `docker login` příkaz:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Po přihlášení, Docker ukládá do mezipaměti přihlašovací údaje, takže nemusíte pamatovat ID aplikace.

V závislosti na verzi Docker instalaci, zobrazí upozornění zabezpečení doporučujeme použít `--password-stdin` parametr. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker přihlášení](https://docs.docker.com/engine/reference/commandline/login/) příkaz odkaz.

Další informace o používání objektu služby pro bezobslužných ověřování ACR, najdete v části [registru kontejner Azure ověřování s objekty služby](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý kontejner registru zahrnuje uživatelský účet správce, který je ve výchozím nastavení zakázaný. Můžete povolit uživatel s oprávněními správce a spravovat svoje přihlašovací údaje v [portál Azure](container-registry-get-started-portal.md#create-a-container-registry), nebo pomocí rozhraní příkazového řádku Azure.

> [!IMPORTANT]
> Účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. Nedoporučujeme sdílení přihlašovací údaje účtu správce s více uživateli. Všichni uživatelé, ověřování pomocí účtu správce se zobrazí jako uživatel s jednotným nabízení a vyžadování přístup k registru. Změna nebo zakázání účtu zakáže přístup k registru pro všechny uživatele, kteří použít její přihlašovací údaje. Jednotlivé identity se doporučuje pro uživatele a objekty služby pro scénáře bez periferních zařízení.
>

Účet správce se s dvě hesla, které mohou vytvořit znovu. Dvě hesla umožňují udržování připojení k registru pomocí jedno heslo, zatímco si znovu vygenerujete druhý. Pokud je povolen účet správce, můžete předat uživatelské jméno a heslo buď `docker login` příkazu pro základní ověřování do registru. Příklad:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Znovu Docker doporučuje používat `--password-stdin` parametr místo zadávání na příkazový řádek pro zvýšení zabezpečení. Můžete také zadat jenom svoje uživatelské jméno bez `-p`a zadejte své heslo při zobrazení výzvy.

Chcete-li zajistit, aby uživatel správce pro existující registru, můžete použít `--admin-enabled` parametr [az acr aktualizace](/cli/azure/acr?view=azure-cli-latest#az_acr_update) v Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Správce portálu Azure můžete povolit tak, že přejdete v registru Výběr **přístupové klíče** pod **nastavení**, pak **povolit** pod **správce Uživatel**.

![Povolit správce uživatelského rozhraní na portálu Azure][auth-portal-01]

## <a name="next-steps"></a>Další postup

* [Push vaší první image pomocí rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
