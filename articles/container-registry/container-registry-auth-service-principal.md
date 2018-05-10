---
title: Azure kontejneru registru ověřování s objekty služby
description: Zjistěte, jak poskytnout přístup k bitové kopie v registru vaší privátní kontejneru pomocí Azure Active Directory instanční objekt.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: 16af83522dd55744c485f6dd3696481e16da1b22
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure kontejneru registru ověřování s objekty služby

Můžete použít objektu služby Azure Active Directory (Azure AD) k poskytování kontejneru image `docker push` a `pull` přístup k registru systému kontejneru. Pomocí objektu služby může poskytnout přístup k "bezobslužných" služeb a aplikací.

## <a name="what-is-a-service-principal"></a>Co je objekt služby?

Azure AD *služby objekty* poskytnout přístup k prostředkům Azure v rámci vašeho předplatného. Si můžete představit služby hlavní identita uživatele pro služby, kde je "služba" žádné aplikace, služby nebo platformy, které potřebuje přístup k prostředkům. Hlavní název služby můžete nakonfigurovat s přístupovými právy obor pouze na tyto prostředky, které zadáte. Potom můžete nakonfigurovat, aplikace nebo služba používat instanční objekt pověření pro přístup k tyto prostředky.

V kontextu registru kontejner Azure můžete vytvořit Azure AD instanční objekt s oprávněními pro vyžádání obsahu, nabízené a přijetí změn nebo vlastníka vaší privátní registru Docker v Azure.

## <a name="why-use-a-service-principal"></a>Proč používat objekt služby?

Pomocí služby Azure AD instanční objekt může být oborový přístup k vaší privátní kontejneru registru. Můžete vytvořit objekty jiné služby pro jednotlivé aplikace nebo služby, každý s šité na míru přístupová práva k registru. A protože sdílení přihlašovacích údajů mezi služby a aplikace se můžete vyhnout, můžete otočit přihlašovací údaje nebo odvolat přístup pouze hlavní služby (a proto aplikace), které zvolíte.

Webové aplikace můžete například použít objekt služby, která poskytuje bitové kopie `pull` přístup pouze, pokud systém sestavení můžete použít objekt služby, která poskytuje s oběma `push` a `pull` přístup. Pokud vývoj aplikace změní rukou, můžete svoje přihlašovací údaje služby Princip otočit bez ovlivnění systém sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít objekt služby

Měli byste použít objekt služby zajistit přístup k registru v **bezobslužných scénáře**. To znamená, že všechny aplikace, služby nebo skript, který musí push nebo pull kontejneru Image v nástroji automatické nebo jinak bezobslužném.

Pro jednotlivý přístup k registru, například pokud jste ručně pro vyžádání obsahu bitovou kopii kontejneru na stanici vývoje měli byste místo toho použít vlastní [Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) pro přístup k registru (například s [az acr přihlášení][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro rozhraní příkazového řádku Azure na Githubu, naleznete jako dobře verze pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Prostředí Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další postup

Jakmile máte objekt služby, že jste udělen přístup ke kontejneru registr, můžete použít její přihlašovací údaje do vaší aplikace a služby pro interakci registru.

Při konfiguraci jednotlivých aplikacím používat přihlašovací údaje hlavní služby je mimo rámec tohoto článku, najdete pokyny pro některé určitým službám a platformy tady:

* [Ověření pomocí Azure kontejneru registru ze služby Azure Kubernetes (AKS)](container-registry-auth-aks.md)
* [Ověření pomocí Azure kontejneru registru z instancí Azure kontejneru (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login