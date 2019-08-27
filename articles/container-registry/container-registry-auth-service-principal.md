---
title: Azure Container Registry ověřování pomocí instančního objektu
description: Poskytněte přístup k obrázkům v soukromém registru kontejneru pomocí Azure Active Directory instančního objektu.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032366"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry ověřování pomocí instančních objektů

Pomocí instančního objektu služby Azure Active Directory (Azure AD) můžete poskytnout image `docker push` kontejneru a `pull` přístup k registru kontejneru. Pomocí instančního objektu můžete poskytnout přístup k nebezobslužným službám a aplikacím.

## <a name="what-is-a-service-principal"></a>Co je instanční objekt?

*Instanční objekty služby* Azure AD poskytují přístup k prostředkům Azure v rámci vašeho předplatného. Instanční objekt si můžete představit jako identitu uživatele pro službu, kde "služba" je libovolná aplikace, služba nebo platforma, která potřebuje přístup k prostředkům. Instanční objekt s přístupovými právy můžete nakonfigurovat jenom na ty prostředky, které zadáte. Pak nakonfigurujte svoji aplikaci nebo službu tak, aby pro přístup k těmto prostředkům používala přihlašovací údaje instančního objektu.

V souvislosti s Azure Container Registry můžete vytvořit instanční objekt služby Azure AD s oprávněním Pull, push a pull nebo jinými oprávněními k privátnímu registru v Azure. Úplný seznam najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Proč používat instanční objekt?

Pomocí instančního objektu služby Azure AD můžete poskytnout vymezený přístup k privátnímu registru kontejnerů. Pro každou aplikaci nebo služby vytvořte různé instanční objekty, z nichž každá má přizpůsobená přístupová práva k vašemu registru. A vzhledem k tomu, že se můžete vyhnout přihlašovacím údajům pro sdílení mezi službami a aplikacemi, můžete své přihlašovací údaje otočit nebo odvolat přístup jenom pro instanční objekt (a tedy pro aplikaci), který zvolíte.

Například nakonfigurujte webovou aplikaci tak, aby používala instanční objekt, který poskytuje přístup pouze k `pull` bitové kopii, zatímco systém sestavení používá instanční objekt, který jim poskytuje `pull` přístup i `push` k. Pokud vývoj vaší aplikace se změní na ruce, můžete své hlavní přihlašovací údaje služby otočit bez vlivu na systém sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

K poskytnutí přístupu k registru v případě neintegrovaných **scénářů**byste měli použít instanční objekt. To znamená, že všechny aplikace, služby nebo skripty, které musí nabízet nebo vyčítat image kontejnerů automatizovaným nebo jinak nedobrým způsobem. Příklad:

  * *Vyžádání*obsahu: Nasaďte kontejnery z registru do systémů orchestrace, včetně Kubernetes, DC/OS a Docker Swarm. Z registrů kontejnerů můžete také získat související služby Azure, jako je [Azure Kubernetes Service (AKS)](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)a další.

  * *Nabízení*: Vytvářejte image kontejnerů a nahrajte je do registru pomocí řešení pro kontinuální integraci a nasazení, jako je Azure Pipelines nebo Jenkinse.

Pro individuální přístup k registru, například když ručně načtete image kontejneru do pracovní stanice pro vývoj, doporučujeme použít místo přístupu k registru vlastní [identitu Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) (například pomocí [AZ ACR Login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také ve verzích pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Ověřování pomocí instančního objektu

Jakmile budete mít instanční objekt, kterému jste udělili přístup k registru kontejneru, můžete nakonfigurovat jeho přihlašovací údaje pro přístup k nebezobslužným službám a aplikacím nebo je zadat pomocí `docker login` příkazu. Použijte následující hodnoty:

* **Uživatelské jméno** – ID aplikace instančního objektu (označuje se také jako *ID klienta*)
* Heslo – hlavní heslo služby (označuje se taky jako *tajný klíč klienta*)

Každá hodnota je identifikátor GUID formuláře `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Heslo instančního objektu můžete znovu vygenerovat spuštěním příkazu [AZ AD SP Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Použití přihlašovacích údajů se službami Azure

Přihlašovací údaje instančního objektu můžete použít v jakékoli službě Azure, která se dá ověřit pomocí služby Azure Container Registry. Příklady obsahují:

* [Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes (AKS)](container-registry-auth-aks.md)
* [Ověřování pomocí Azure Container Registry z Azure Container Instances (ACI)](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Použít s přihlášením Docker

Můžete také spustit `docker login` pomocí instančního objektu. V následujícím příkladu je ID aplikace instančního objektu předáno do proměnné `$SP_APP_ID`prostředí a heslo v proměnné. `$SP_PASSWD` Osvědčené postupy pro správu přihlašovacích údajů Docker najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po přihlášení Docker uloží přihlašovací údaje do mezipaměti.

## <a name="next-steps"></a>Další postup

* V tématu [Přehled ověřování](container-registry-authentication.md) pro jiné scénáře ověřování pomocí služby Azure Container Registry.

* Příklad použití trezoru klíčů Azure k ukládání a načítání přihlašovacích údajů instančního objektu pro registr kontejnerů najdete v kurzu [sestavení a nasazení image kontejneru pomocí úloh ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
