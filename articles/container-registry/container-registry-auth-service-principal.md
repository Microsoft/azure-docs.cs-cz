---
title: Ověřování pomocí instančního objektu
description: Poskytněte přístup k obrázkům v soukromém registru kontejneru pomocí Azure Active Directory instančního objektu.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: a32538e5fc5354427bafc5098634becdcedd1239
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285531"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry ověřování pomocí instančních objektů

K poskytnutí nabízených nebo přístupových práv k registru kontejneru můžete použít instanční objekt Azure Active Directory (Azure AD). Pomocí instančního objektu můžete poskytnout přístup k nebezobslužným službám a aplikacím.

## <a name="what-is-a-service-principal"></a>Co je instanční objekt?

*Instanční objekty služby* Azure AD poskytují přístup k prostředkům Azure v rámci vašeho předplatného. Instanční objekt si můžete představit jako identitu uživatele pro službu, kde "služba" je libovolná aplikace, služba nebo platforma, která potřebuje přístup k prostředkům. Instanční objekt s přístupovými právy můžete nakonfigurovat jenom na ty prostředky, které zadáte. Pak nakonfigurujte svoji aplikaci nebo službu tak, aby pro přístup k těmto prostředkům používala přihlašovací údaje instančního objektu.

V souvislosti s Azure Container Registry můžete vytvořit instanční objekt služby Azure AD s oprávněním Pull, push a pull nebo jinými oprávněními k privátnímu registru v Azure. Úplný seznam najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Proč používat instanční objekt?

Pomocí instančního objektu služby Azure AD můžete poskytnout vymezený přístup k privátnímu registru kontejnerů. Pro každou aplikaci nebo služby vytvořte různé instanční objekty, z nichž každá má přizpůsobená přístupová práva k vašemu registru. A vzhledem k tomu, že se můžete vyhnout přihlašovacím údajům pro sdílení mezi službami a aplikacemi, můžete své přihlašovací údaje otočit nebo odvolat přístup jenom pro instanční objekt (a tedy pro aplikaci), který zvolíte.

Například nakonfigurujte webovou aplikaci tak, aby používala instanční objekt, který poskytuje `pull` přístup pouze k bitové kopii, zatímco systém sestavení používá instanční objekt, který jim poskytuje `push` přístup i `pull` k. Pokud vývoj vaší aplikace se změní na ruce, můžete otočit své přihlašovací údaje instančního objektu, aniž by to ovlivnilo systém sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

K poskytnutí přístupu k registru v případě neintegrovaných **scénářů** byste měli použít instanční objekt. To znamená, že všechny aplikace, služby nebo skripty, které musí nabízet nebo vyčítat image kontejnerů automatizovaným nebo jinak nedobrým způsobem. Například:

  * *Pull*: nasaďte kontejnery z registru do systémů orchestrace, včetně KUBERNETES, DC/OS a Docker Swarm. Z registrů kontejnerů můžete také získat související služby Azure, jako je [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml)a další.

  * *Push*: Sestavte image kontejnerů a nahrajte je do registru pomocí řešení pro kontinuální integraci a nasazení, jako je Azure Pipelines nebo Jenkinse.

Pro individuální přístup k registru, například když ručně načtete image kontejneru do pracovní stanice pro vývoj, doporučujeme použít místo přístupu k registru vlastní [identitu Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) (například pomocí [AZ ACR Login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také ve verzích pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Ověřování pomocí instančního objektu

Jakmile budete mít instanční objekt, kterému jste udělili přístup k registru kontejneru, můžete nakonfigurovat jeho přihlašovací údaje pro přístup k nebezobslužným službám a aplikacím nebo je zadat pomocí `docker login` příkazu. Použijte následující hodnoty:

* **Uživatelské jméno** – ID aplikace instančního objektu (označuje se také jako *ID klienta*)
* **Heslo – Hlavní** heslo služby (označuje se taky jako *tajný klíč klienta*)

Každá hodnota má formát `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> Heslo instančního objektu můžete znovu vygenerovat spuštěním příkazu [AZ AD SP Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Použití přihlašovacích údajů se službami Azure

Přihlašovací údaje instančního objektu můžete použít ze všech služeb Azure, které se ověřují pomocí služby Azure Container Registry.  Použijte přihlašovací údaje instančního objektu místo přihlašovacích údajů správce registru pro celou řadu scénářů.

Použijte například přihlašovací údaje k načtení obrázku z služby Azure Container Registry do [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Použít s přihlášením Docker

Můžete spustit `docker login` pomocí instančního objektu. V následujícím příkladu je ID aplikace instančního objektu předáno do proměnné prostředí `$SP_APP_ID` a heslo v proměnné `$SP_PASSWD` . Doporučené postupy pro správu přihlašovacích údajů Docker najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po přihlášení Docker uloží přihlašovací údaje do mezipaměti.

### <a name="use-with-certificate"></a>Použít s certifikátem

Pokud jste přidali certifikát k instančnímu objektu, můžete se přihlásit k Azure CLI pomocí ověřování založeného na certifikátech a pak pomocí příkazu [AZ ACR Login][az-acr-login] získat přístup k registru. Použití certifikátu jako tajného klíče místo hesla poskytuje vyšší zabezpečení při použití rozhraní příkazového řádku. 

Certifikát podepsaný svým držitelem se dá vytvořit při [vytváření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli). Nebo přidejte jeden nebo více certifikátů k existujícímu instančnímu objektu. Pokud například použijete jeden z skriptů v tomto článku k vytvoření nebo aktualizaci instančního objektu s právy pro vyžádání nebo vložení imagí z registru, přidejte certifikát pomocí příkazu [AZ AD SP Credential Reset][az-ad-sp-credential-reset] .

Pokud chcete k [přihlášení do Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)použít instanční objekt s certifikátem, musí být certifikát ve formátu PEM a musí obsahovat privátní klíč. Pokud váš certifikát nemá požadovaný formát, použijte nástroj, jako `openssl` je třeba ho převést. Pokud spustíte příkaz [AZ Login][az-login] pro přihlášení k rozhraní příkazového řádku pomocí instančního objektu, zadejte také ID aplikace instančního objektu a ID tenanta služby Active Directory. Následující příklad zobrazuje tyto hodnoty jako proměnné prostředí:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Pak spusťte příkaz [AZ ACR Login][az-acr-login] k ověření pomocí registru:

```azurecli
az acr login --name myregistry
```

Rozhraní příkazového řádku používá token vytvořený při spuštění `az login` ověřování relace s registrem.

## <a name="next-steps"></a>Další kroky

* V tématu [Přehled ověřování](container-registry-authentication.md) pro jiné scénáře ověřování pomocí služby Azure Container Registry.

* Příklad použití trezoru klíčů Azure k ukládání a načítání přihlašovacích údajů instančního objektu pro registr kontejnerů najdete v kurzu [sestavení a nasazení image kontejneru pomocí úloh ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
