---
title: Možnosti ověřování v registru
description: Možnosti ověřování privátního služby Azure Container Registry, včetně přihlašování pomocí Azure Active Directory identity, pomocí instančních objektů a použití volitelných přihlašovacích údajů správce.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: d12895502ecd30991fbef836903a8ceea445b770
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285497"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Ověřování pomocí služby Azure Container Registry

Existuje několik způsobů, jak ověřit pomocí služby Azure Container Registry, z nichž každá je platná pro jeden nebo více scénářů použití registru.

Mezi doporučené způsoby patří ověřování v registru přímo přes [jednotlivé přihlašovací údaje](#individual-login-with-azure-ad), nebo vaše aplikace a orchestrace kontejnerů můžou provádět bezobslužné nebo "nezabezpečené" ověřování pomocí [instančního objektu služby](#service-principal)Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

Následující tabulka obsahuje seznam dostupných metod ověřování a typických scénářů. Podrobnosti najdete v tématu s propojeným obsahem.

| Metoda                               | Ověřování                                           | Scénáře                                                            | Řízení přístupu na základě role Azure (Azure RBAC)                             | Omezení                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuální identita AD](#individual-login-with-azure-ad)                | `az acr login` v Azure CLI                             | Interaktivní nabízení a vyžádané sdílení pro vývojáře, testery                                    | Yes                              | Token AD se musí obnovit každé 3 hodiny.     |
| [Instanční objekt služby AD](#service-principal)                  | `docker login`<br/><br/>`az acr login` v Azure CLI<br/><br/> Nastavení přihlášení do registru v rozhraních API nebo nástrojích<br/><br/> [Tajný kód pro vyžádání obsahu Kubernetes](container-registry-auth-kubernetes.md)                                           | Bezobslužné vkládání z kanálu CI/CD<br/><br/> Bezobslužné stažení do Azure nebo externích služeb  | Yes                              | Výchozí platnost hesla SP je 1 rok.       |                                                           
| [Integrace s AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Připojit registr při vytvoření nebo aktualizaci clusteru AKS  | Bezobslužné stažení do clusteru AKS                                                  | Ne, jenom přístup pro vyžádání             | K dispozici pouze v clusteru AKS            |
| [Spravovaná identita pro prostředky Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` v Azure CLI                                       | Bezobslužné vkládání z kanálu CI/CD z Azure<br/><br/> Bezobslužné získání dat do služeb Azure<br/><br/>   | Yes                              | Používejte jenom z vybraných služeb Azure, které [podporují spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) .              |
| [Uživatel s rolí správce](#admin-account)                            | `docker login`                                          | Interaktivní nabízená oznámení nebo vyžádaná osoba pro jednotlivé vývojáře nebo testery<br/><br/>Nasazení bitové kopie z registru portálu do Azure App Service nebo Azure Container Instances                      | Ne, vždycky získávat a nabízet přístup  | Jeden účet na registr, nedoporučuje se pro více uživatelů         |
| [Přístupový token s oborem úložiště](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` v Azure CLI   | Interaktivní nabízená oznámení nebo vyžádání do úložiště podle individuálního vývojáře nebo testerů<br/><br/> Bezobslužné odeslání/vyžádání do úložiště podle individuálního systému nebo externího zařízení                  | Yes                              | Aktuálně není integrováno se službou AD identity  |

## <a name="individual-login-with-azure-ad"></a>Individuální přihlášení pomocí Azure AD

Při přímém práci s registrem, jako je například navýšení a vkládání imagí z vývojové pracovní stanice do registru, který jste vytvořili, můžete ověřit pomocí konkrétní identity Azure. Přihlaste se k [Azure CLI](/cli/azure/install-azure-cli) pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login)a potom spusťte příkaz [AZ ACR Login](/cli/azure/acr#az-acr-login) :

```azurecli
az login
az acr login --name <acrName>
```

Při přihlášení pomocí `az acr login` rozhraní příkazového řádku použije token vytvořený při spuštění `az login` k bezproblémovému ověření relace s registrem. K dokončení toku ověřování musí být ve vašem prostředí nainstalovaný a spuštěný démon Docker CLI a Docker. `az acr login` k nastavení tokenu Azure Active Directory v souboru používá klienta Docker `docker.config` . Po přihlášení tohoto postupu jsou vaše přihlašovací údaje uložené v mezipaměti a následné `docker` příkazy ve vaší relaci nevyžadují uživatelské jméno ani heslo.

> [!TIP]
> Slouží také `az acr login` k ověření individuální identity v případě, že chcete vyžádat nebo načíst artefakty jiné než Image Docker do registru, například [artefakty OCI](container-registry-oci-artifacts.md).  

V případě přístupu k registru je token používaný nástrojem `az acr login` platný po dobu **3 hodin**, proto doporučujeme, abyste se před spuštěním příkazu vždy přihlásili k registru `docker` . V případě vypršení platnosti tokenu ho můžete znovu aktualizovat pomocí `az acr login` příkazu znovu a ověřit. 

Použití `az acr login` s identitami Azure poskytuje [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). V některých scénářích se můžete chtít přihlásit k registru s vlastní individuální identitou v Azure AD nebo nakonfigurovat jiné uživatele Azure s konkrétními [rolemi a oprávněními Azure](container-registry-roles.md). Pro scénáře mezi službami nebo pro zpracování potřeb pracovní skupiny nebo pracovního postupu vývoje, kde nechcete spravovat individuální přístup, se můžete také přihlásit pomocí [spravované identity pro prostředky Azure](container-registry-authentication-managed-identity.md).

### <a name="az-acr-login-with---expose-token"></a>AZ ACR Login with--zpřístupnit-token

V některých případech může být nutné provést ověření v `az acr login` případě, že démon Docker neběží ve vašem prostředí. Například může být nutné spustit `az acr login` skript v Azure Cloud Shell, který poskytuje Docker CLI, ale nespustí démona Docker.

Pro tento scénář spusťte `az acr login` nejprve `--expose-token` parametr. Tato možnost zpřístupňuje přístupový token místo přihlášení prostřednictvím Docker CLI.

```azurecli
az acr login --name <acrName> --expose-token
```

Výstup zobrazuje přístupový token, který se tady zkracuje:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Pro ověřování v registru doporučujeme ukládat přihlašovací údaje tokenu v bezpečném umístění a postupovat podle doporučených postupů pro správu přihlašovacích údajů uživatele [Docker](https://docs.docker.com/engine/reference/commandline/login/). Například uložte hodnotu tokenu do proměnné prostředí:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Pak spusťte `docker login` , předejte `00000000-0000-0000-0000-000000000000` jako uživatelské jméno a pomocí přístupového tokenu jako hesla:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Instanční objekt

Pokud k registru přiřadíte [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) , vaše aplikace nebo služba ho můžou použít pro bezobslužné ověřování. Instanční objekty umožňují [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) k registru a k registru můžete přiřadit více instančních objektů. Několik instančních objektů vám umožní definovat různý přístup pro různé aplikace.

K dispozici jsou role pro registr kontejnerů:

* **AcrPull**: pull

* **AcrPush**: vyžádání a vložení

* **Vlastník**: vyžádání, vložení a přiřazení rolí jiným uživatelům

Úplný seznam rolí najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

Pro skripty rozhraní příkazového řádku pro vytvoření instančního objektu pro ověřování pomocí služby Azure Container registry a další pokyny najdete v tématu [Azure Container Registry ověřování pomocí instančních objektů](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů obsahuje účet uživatele správce, který je ve výchozím nastavení zakázaný. Můžete povolit uživatele správce a spravovat jeho přihlašovací údaje v Azure Portal nebo pomocí Azure CLI nebo jiných nástrojů Azure. Účet správce má úplná oprávnění k registru.

Účet správce se v současnosti vyžaduje, aby některé scénáře nasadily image z registru kontejneru do určitých služeb Azure. Například účet správce je potřeba při použití Azure Portal k nasazení image kontejneru z registru přímo do [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) nebo [Azure Web Apps for Containers](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> Účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílení přihlašovacích údajů účtu správce mezi více uživateli. Všichni uživatelé, kteří se ověřují pomocí účtu správce, se zobrazí jako jednotliví uživatelé s přístupem push a pull do registru. Změna nebo zakázání tohoto účtu zakáže přístup k registru pro všechny uživatele, kteří používají své přihlašovací údaje. Pro uživatele a instanční objekty se doporučuje použít pro scénáře s doplňováním provozu individuální identitu.
>

Účet správce je k dispozici se dvěma hesly, z nichž lze znovu vygenerovat. Dvě hesla umožňují udržovat připojení k registru pomocí jednoho hesla při opětovném vygenerování. Pokud je povolen účet správce, můžete `docker login` při zobrazení výzvy k zadání základního ověřování do registru předat uživatelské jméno a heslo k příkazu. Například:

```
docker login myregistry.azurecr.io 
```

Doporučené postupy pro správu přihlašovacích údajů najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) .

Pokud chcete povolit uživatele s oprávněními správce pro existující registr, můžete použít `--admin-enabled` parametr příkazu [AZ ACR Update](/cli/azure/acr#az-acr-update) v rozhraní příkazového řádku Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Uživatele s oprávněními správce můžete v Azure Portal povolit tak, že přejdete do registru, vyberete **přístupové klíče** v části **Nastavení** a potom **povolíte** v části **uživatel s oprávněními správce**.

![Povolit uživatelské rozhraní uživatele pro správu v Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Další kroky

* [Vložení první Image pomocí Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
