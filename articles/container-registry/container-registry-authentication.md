---
title: Možnosti ověřování v registru
description: Možnosti ověřování pro službu Azure Container Registry, včetně přihlašování pomocí Azure Active Directory identity, pomocí instančních objektů a použití volitelných přihlašovacích údajů správce.
ms.topic: article
ms.date: 12/21/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbe77dee4104e3c654aad58db82765733b2c3e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264505"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Ověřování pomocí privátního registru kontejnerů Docker

Existuje několik způsobů, jak ověřit pomocí služby Azure Container Registry, z nichž každá je platná pro jeden nebo více scénářů použití registru.

Mezi doporučené způsoby patří ověřování v registru přímo přes [jednotlivé přihlašovací údaje](#individual-login-with-azure-ad), nebo vaše aplikace a orchestrace kontejnerů můžou provádět bezobslužné nebo "nezabezpečené" ověřování pomocí [instančního objektu služby](#service-principal)Azure Active Directory (Azure AD).

## <a name="individual-login-with-azure-ad"></a>Individuální přihlášení pomocí Azure AD

Při přímém práci s registrem, jako je například navýšení a vkládání imagí z vývojové pracovní stanice, proveďte ověření pomocí příkazu [AZ ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Když se přihlásíte pomocí `az acr login`, rozhraní příkazového řádku použije token vytvořený při provedení [AZ Login](/cli/azure/reference-index#az-login) k bezproblémovému ověření relace s registrem. Po přihlášení budete přihlašovací údaje ukládat do mezipaměti a následné `docker` příkazy ve vaší relaci nevyžadují uživatelské jméno ani heslo. 

V případě přístupu k registru je token používaný `az acr login` platný po dobu **3 hodin**, takže před spuštěním příkazu `docker` doporučujeme, abyste se k registru vždy přihlásili. V případě vypršení platnosti tokenu ho můžete znovu aktualizovat pomocí příkazu `az acr login` znovu ověřit. 

Použití `az acr login` s identitami Azure zajišťuje [přístup založený na rolích](../role-based-access-control/role-assignments-portal.md). V některých scénářích se můžete chtít přihlásit k registru s vlastní individuální identitou v Azure AD. Pro scénáře mezi službami nebo pro zpracování potřeb pracovní skupiny, u kterých nechcete spravovat individuální přístup, se můžete také přihlásit pomocí [spravované identity pro prostředky Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Instanční objekt

Pokud k registru přiřadíte [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) , vaše aplikace nebo služba ho můžou použít pro bezobslužné ověřování. Instanční objekty umožňují [přístup na základě rolí](../role-based-access-control/role-assignments-portal.md) k registru a k registru můžete přiřadit více instančních objektů. Několik instančních objektů vám umožní definovat různý přístup pro různé aplikace.

K dispozici jsou role pro registr kontejnerů:

* **AcrPull**: pull

* **AcrPush**: vyžádání a vložení

* **Vlastník**: vyžádání, vložení a přiřazení rolí jiným uživatelům

Úplný seznam rolí najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

Pokud chcete, aby skripty rozhraní příkazového řádku vytvořily instanční objekt pro ověřování pomocí služby Azure Container registry a pokyny k používání instančního objektu, přečtěte si téma [Azure Container Registry ověřování s instančními objekty](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů obsahuje účet uživatele správce, který je ve výchozím nastavení zakázaný. Můžete povolit uživatele správce a spravovat jeho přihlašovací údaje v Azure Portal nebo pomocí Azure CLI nebo jiných nástrojů Azure.

> [!IMPORTANT]
> Účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílení přihlašovacích údajů účtu správce mezi více uživateli. Všichni uživatelé, kteří se ověřují pomocí účtu správce, se zobrazí jako jednotliví uživatelé s přístupem push a pull do registru. Změna nebo zakázání tohoto účtu zakáže přístup k registru pro všechny uživatele, kteří používají své přihlašovací údaje. Pro uživatele a instanční objekty se doporučuje použít pro scénáře s doplňováním provozu individuální identitu.
>

Účet správce je k dispozici se dvěma hesly, z nichž lze znovu vygenerovat. Dvě hesla umožňují udržovat připojení k registru pomocí jednoho hesla při opětovném vygenerování. Pokud je povolen účet správce, můžete po zobrazení výzvy k zadání základního ověřování do registru předat uživatelské jméno a heslo k příkazu `docker login`. Například:

```
docker login myregistry.azurecr.io 
```

Osvědčené postupy pro správu přihlašovacích údajů najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) .

Pokud chcete povolit uživatele s oprávněními správce pro existující registr, můžete použít parametr `--admin-enabled` příkazu [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) v rozhraní příkazového řádku Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Uživatele s oprávněními správce můžete v Azure Portal povolit tak, že přejdete do registru, vyberete **přístupové klíče** v části **Nastavení**a potom **povolíte** v části **uživatel s oprávněními správce**.

![Povolit uživatelské rozhraní uživatele pro správu v Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Další kroky

* [Vložení první Image pomocí Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
