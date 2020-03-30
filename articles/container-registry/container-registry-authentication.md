---
title: Možnosti ověřování registru
description: Možnosti ověřování pro soukromý registr kontejnerů Azure, včetně přihlášení pomocí identity Služby Azure Active Directory, použití instancí a použití volitelných přihlašovacích údajů správce.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247043"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Ověření pomocí registru kontejnerů Azure

Existuje několik způsobů, jak ověřit pomocí registru kontejnerů Azure, z nichž každý je použitelný pro jeden nebo více scénářů využití registru.

Mezi doporučené způsoby patří ověřování do registru přímo prostřednictvím [individuálního přihlášení](#individual-login-with-azure-ad)nebo vaše aplikace a orchestrátory kontejnerů mohou provádět bezobslužné nebo "bezhlavé" ověřování pomocí [instančního objektu služby](#service-principal)Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

V následující tabulce jsou uvedeny dostupné metody ověřování a doporučené scénáře. Podrobnosti najdete v tématu propojený obsah.

| Metoda                               | Jak ověřit                                           | Scénáře                                                            | RBAC                             | Omezení                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuální identita AD](#individual-login-with-azure-ad)                | `az acr login` v azure cli                             | Interaktivní push / pull vývojáři, testeři                                    | Ano                              | Token AD musí být obnovován každé 3 hodiny.     |
| [Instanční objekt služby AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`v azure cli<br/><br/> Nastavení přihlášení registru v souborech API nebo nástrojích<br/><br/> [Kubernetes vytáhnout tajemství](container-registry-auth-kubernetes.md)                                           | Bezobslužné nabízení z potrubí CI/CD<br/><br/> Bezobslužná vyžádat azure nebo externí služby  | Ano                              | Výchozí vypršení platnosti hesla SP je 1 rok       |                                                           
| [Integrace s AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Připojit registr při vytvoření nebo aktualizaci clusteru AKS  | Bezobslužná vyžádat do clusteru AKS                                                  | Ne, pouze přístup k vytažení             | K dispozici pouze v clusteru AKS            |
| [Spravovaná identita pro prostředky Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` v azure cli                                       | Bezobslužné nabízení z kanálu Azure CI/CD<br/><br/> Bezobslužná vyžádat ke službám Azure<br/><br/>   | Ano                              | Používejte jenom ze služeb Azure, které [podporují spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Správce uživatele](#admin-account)                            | `docker login`                                          | Interaktivní push/pull od jednotlivých vývojářů nebo testerů                           | Ne, vždy zatáhněte za přístup  | Jeden účet na registr, nedoporučuje se pro více uživatelů         |
| [Přístupový token s rozsahem úložiště](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`v azure cli   | Interaktivní push/pull do úložiště od jednotlivých vývojářů nebo testerů<br/><br/> Bezobslužné push/pull do úložiště podle jednotlivých systémů nebo externích zařízení                  | Ano                              | Není aktuálně integrováno s identitou Služby AD  |

## <a name="individual-login-with-azure-ad"></a>Individuální přihlášení pomocí Azure AD

Při přímé práci s registrem, jako je například vytahování obrázků do vývojové pracovní stanice a odesílání bitových kopií, ověřte pomocí příkazu [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v příkazu [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Když se přihlásíte pomocí `az acr login`, cli používá token vytvořený při spuštění [az přihlášení](/cli/azure/reference-index#az-login) bezproblémově ověřit relaci s registrem. K dokončení toku ověřování musí být Docker nainstalován a spuštěn ve vašem prostředí. `az acr login`pomocí klienta Dockeru nastaví token `docker.config` Azure Active Directory v souboru. Po přihlášení tímto způsobem jsou vaše pověření uložena do `docker` mezipaměti a následné příkazy v relaci nevyžadují uživatelské jméno ani heslo.

> [!TIP]
> Také `az acr login` použít k ověření jednotlivé identity, pokud chcete push nebo pull artefakty než image Dockeru do registru, jako jsou [artefakty OCI](container-registry-oci-artifacts.md).  


Pro přístup k registru `az acr login` je používaný token platný po dobu **3 hodin**, proto `docker` doporučujeme, abyste se před spuštěním příkazu vždy přihlásili do registru. Pokud platnost tokenu vyprší, můžete jej `az acr login` znovu aktualizovat pomocí příkazu k opětovnému ověření. 

Použití `az acr login` s identitami Azure poskytuje [přístup založený na rolích](../role-based-access-control/role-assignments-portal.md). V některých případech můžete chtít přihlásit do registru s vlastní individuální identity ve službě Azure AD. U scénářů mezi službami nebo pro zpracování potřeb pracovní skupiny nebo pracovního postupu vývoje, kde nechcete spravovat individuální přístup, se můžete také přihlásit pomocí [spravované identity pro prostředky Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Instanční objekt

Pokud k registru přiřadíte [instanční objekt,](../active-directory/develop/app-objects-and-service-principals.md) může ji aplikace nebo služba použít k bezhotovostnímu ověřování. Instanční objekty umožňují přístup k registru [na základě rolí](../role-based-access-control/role-assignments-portal.md) a můžete přiřadit registru více instančních objektů. Více instančních objektů umožňuje definovat jiný přístup pro různé aplikace.

Mezi dostupné role pro registr kontejneru patří:

* **AcrPull**: tažná

* **AcrPush:** tahat a tlačit

* **Vlastník**: pull, push a přiřadit role ostatním uživatelům

Úplný seznam rolí najdete v tématu [Role a oprávnění registru kontejnerů Azure](container-registry-roles.md).

Skripty příkazového příkazového příkazu k vytvoření instančního objektu pro ověřování pomocí registru kontejnerů Azure a další pokyny naleznete v [tématu ověřování registru kontejnerů Azure s instančními objekty](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý registr kontejneru obsahuje uživatelský účet správce, který je ve výchozím nastavení zakázán. Můžete povolit správce uživatele a spravovat jeho přihlašovací údaje na webu Azure Portal, nebo pomocí Azure CLI nebo jiných nástrojů Azure.

> [!IMPORTANT]
> Účet správce je určen pro jednoho uživatele pro přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílet přihlašovací údaje účtu správce mezi více uživateli. Všichni uživatelé, kteří ověřují účet správce, se zobrazují jako jeden uživatel s přístupem push and pull k registru. Změna nebo zakázání tohoto účtu zakáže přístup k registru pro všechny uživatele, kteří používají jeho pověření. Individuální identita je doporučena pro uživatele a instanční objekty pro bezhlavé scénáře.
>

Účet správce je opatřen dvěma hesly, která lze obnovit. Dvě hesla umožňují udržovat připojení k registru pomocí jednoho hesla při regeneraci druhého. Pokud je povolen účet správce, můžete po zobrazení výzvy k základnímu ověření registru předat `docker login` příkazuživatelské jméno a heslo. Například:

```
docker login myregistry.azurecr.io 
```

Doporučené postupy pro správu přihlašovacích údajů naleznete v odkazu příkazu [pro přihlášení do dockeru.](https://docs.docker.com/engine/reference/commandline/login/)

Chcete-li povolit uživatele správce pro existující `--admin-enabled` registr, můžete použít parametr příkazu [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) v rozhraní příkazového řádku Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Uživatele správce na webu Azure Portal můžete povolit tak, že navigujete svůj registr, vyberete **přístupové klávesy** v části **NASTAVENÍ**a pak **povolit** v části **Správce .**

![Povolení uživatelského uživatelského rozhraní správce na webu Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Další kroky

* [Posunutí první bitové kopie pomocí příkazového příkazového příkazu Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
