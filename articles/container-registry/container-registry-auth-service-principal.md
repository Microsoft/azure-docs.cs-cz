---
title: Ověřování pomocí instančního objektu
description: Poskytněte přístup k bitovým kopiím v registru privátního kontejneru pomocí objektu zabezpečení služby Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455401"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Ověřování registru kontejnerů Azure pomocí instančních objektů

K poskytování image `docker push` kontejneru a `pull` přístupu k registru kontejnerů můžete použít instanční objekt služby Azure Active Directory (Azure AD). Pomocí instančního objektu můžete poskytnout přístup k "bezhlavým" službám a aplikacím.

## <a name="what-is-a-service-principal"></a>Co je instanční objekt?

*Objekty služeb* Azure AD poskytují přístup k prostředkům Azure v rámci vašeho předplatného. Můžete si myslet, instanční objekt jako identitu uživatele pro službu, kde "služba" je libovolná aplikace, služby nebo platformy, které potřebuje přístup k prostředkům. Instanční objekt služby s přístupovými právy můžete nakonfigurovat pouze pro ty prostředky, které zadáte. Potom nakonfigurujte aplikaci nebo službu tak, aby používala pověření instančního objektu pro přístup k těmto prostředkům.

V kontextu Azure Container Registry můžete vytvořit instanční objekt služby Azure AD s vyžádat, push and pull nebo jiná oprávnění k privátnímu registru v Azure. Úplný seznam najdete v tématu [Role a oprávnění registru kontejnerů Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Proč používat instanční objekt?

Pomocí instančního objektu služby Azure AD můžete poskytnout přístup k oboru vašeho registru privátního kontejneru. Vytvořte různé instanční objekty pro každou z vašich aplikací nebo služeb, z nichž každá má přizpůsobená přístupová práva k registru. A protože se můžete vyhnout sdílení přihlašovacích údajů mezi službami a aplikacemi, můžete otočit pověření nebo odvolat přístup pouze pro instanční objekt (a tedy aplikace), který zvolíte.

Například nakonfigurujte webovou aplikaci tak, `pull` aby používala instanční objekt, který jí `push` `pull` poskytuje pouze přístup k bitové kopii, zatímco systém sestavení používá instanční objekt, který mu poskytuje jak přístup, tak přístup. Pokud vývoj aplikace změní ruce, můžete otočit jeho pověření zaregistrovaný ch oddané služby bez ovlivnění systému sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

Měli byste použít instanční objekt k poskytnutí přístupu k registru v **bezhlavých scénářích**. To znamená, že všechny aplikace, služby nebo skript, který musí tlačit nebo vyžádat image kontejneru automatizovaným nebo jinak bezobslužným způsobem. Například:

  * *Vyžádat*: Nasazení kontejnerů z registru do orchestračních systémů včetně Kubernetes, DC/OS a Docker Swarm. Můžete také získat z registrů kontejnerů do souvisejících služeb Azure, jako je [služba Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Instance kontejnerů Azure](container-registry-auth-aci.md), Služba [aplikací](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)a další.

  * *Nabízená*řešení : Vytvářejte ibi kontejnerů a přesouvejte je do registru pomocí řešení průběžné integrace a nasazování, jako jsou Azure Pipelines nebo Jenkins.

Pro individuální přístup k registru, například při ručním vytahování image kontejneru na vývojové pracovní stanici, doporučujeme použít vlastní [identitu Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) místo pro přístup k registru (například s [az acr přihlášení).][az-acr-login]

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také verze pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Ověření pomocí instančního objektu

Jakmile máte instanční objekt služby, který jste udělili přístup k registru kontejneru, můžete nakonfigurovat jeho pověření `docker login` pro přístup k "bezhlavé" služby a aplikace nebo zadat pomocí příkazu. Použijte následující hodnoty:

* **Uživatelské jméno** - ID aplikace instančního objektu (také nazývané *ID klienta)*
* **Heslo** - heslo instančního objektu (také nazývané *tajný klíč klienta)*

Každá hodnota je identifikátor GUID formuláře `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Heslo instančního objektu můžete znovu vygenerovat spuštěním příkazu [reset-credentials az ad sp.](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
>

### <a name="use-credentials-with-azure-services"></a>Používání přihlašovacích údajů se službami Azure

Můžete použít pověření instančního objektu z libovolné služby Azure, která se ověřuje pomocí registru kontejnerů Azure.  Místo pověření správce registru používejte pověření instančního objektu pro různé scénáře.

Například použijte přihlašovací údaje k vyhledání bitové kopie z registru kontejneru Azure do [azure container instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Použití s přihlášením dockeru

Můžete spustit `docker login` pomocí instančního objektu. V následujícím příkladu je ID instančního `$SP_APP_ID`objektu služby předáno v proměnné prostředí a heslo v proměnné `$SP_PASSWD`. Doporučené postupy pro správu přihlašovacích údajů Dockeru najdete v tématu odkaz na [příkaz pro přihlášení do dockeru.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Po přihlášení Docker uloží pověření do mezipaměti.

### <a name="use-with-certificate"></a>Použití s certifikátem

Pokud jste přidali certifikát do instančního objektu, můžete se přihlásit do Azure CLI pomocí ověřování na základě certifikátu a pak použít příkaz [az acr přihlášení][az-acr-login] pro přístup k registru. Použití certifikátu jako tajného klíče namísto hesla poskytuje další zabezpečení při použití zaokreslovacího systému. 

Při [vytváření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli)lze vytvořit certifikát podepsaný svým držitelem . Nebo přidejte jeden nebo více certifikátů do existujícího instančního objektu. Pokud například použijete jeden ze skriptů v tomto článku k vytvoření nebo aktualizaci instančního objektu s právy k vytahování nebo nabízení obrázků z registru, přidejte certifikát pomocí příkazu [resetování pověření az ad sp.][az-ad-sp-credential-reset]

Chcete-li použít instanční objekt s certifikátem pro [přihlášení do příkazového příkazu k řešení Azure](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), musí být certifikát ve formátu PEM a musí obsahovat soukromý klíč. Pokud váš certifikát není v požadovaném formátu, `openssl` použijte nástroj, například k jeho převodu. Při spuštění [az přihlášení][az-login] k přihlášení do rozhraní se kzamičipomocí instančního objektu, zadejte také ID aplikace instančního objektu a ID klienta služby Active Directory. Následující příklad ukazuje tyto hodnoty jako proměnné prostředí:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Potom spusťte [az acr přihlášení][az-acr-login] k ověření s registrem:

```azurecli
az acr login --name myregistry
```

ClI používá token vytvořený při `az login` spuštění k ověření relace s registrem.

## <a name="next-steps"></a>Další kroky

* Podívejte se na [přehled ověřování](container-registry-authentication.md) pro jiné scénáře k ověření pomocí registru kontejneru Azure.

* Příklad použití trezoru klíčů Azure k ukládání a načítání přihlašovacích údajů k instančnímu objektu pro registr kontejneru najdete v kurzu k [sestavení a nasazení image kontejneru pomocí úloh ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
