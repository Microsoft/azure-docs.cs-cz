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
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309949"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry ověřování pomocí instančních objektů

Pomocí instančního objektu služby Azure Active Directory (Azure AD) můžete poskytnout image `docker push` kontejneru a `pull` přístup k registru kontejneru. Pomocí instančního objektu můžete poskytnout přístup k nebezobslužným službám a aplikacím.

## <a name="what-is-a-service-principal"></a>Co je instanční objekt?

*Instanční objekty služby* Azure AD poskytují přístup k prostředkům Azure v rámci vašeho předplatného. Instanční objekt si můžete představit jako identitu uživatele pro službu, kde "služba" je libovolná aplikace, služba nebo platforma, která potřebuje přístup k prostředkům. Instanční objekt s přístupovými právy můžete nakonfigurovat jenom na ty prostředky, které zadáte. Pak nakonfigurujte svoji aplikaci nebo službu tak, aby pro přístup k těmto prostředkům používala přihlašovací údaje instančního objektu.

V souvislosti s Azure Container Registry můžete vytvořit instanční objekt služby Azure AD s oprávněním Pull, push a pull nebo jinými oprávněními k privátnímu registru v Azure. Úplný seznam najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Proč používat instanční objekt?

Pomocí instančního objektu služby Azure AD můžete poskytnout vymezený přístup k privátnímu registru kontejnerů. Pro každou aplikaci nebo služby můžete vytvořit různé instanční objekty, z nichž každá má přizpůsobená přístupová práva k vašemu registru. A vzhledem k tomu, že se můžete vyhnout přihlašovacím údajům pro sdílení mezi službami a aplikacemi, můžete své přihlašovací údaje otočit nebo odvolat přístup jenom pro instanční objekt (a tedy pro aplikaci), který zvolíte.

Vaše webová aplikace může například použít instanční objekt, který poskytuje přístup pouze k bitové kopii `pull` , zatímco systém sestavení může použít instanční objekt, který jim poskytuje `pull` přístup i `push` . Pokud vývoj vaší aplikace se změní na ruce, můžete své hlavní přihlašovací údaje služby otočit bez vlivu na systém sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

K poskytnutí přístupu k registru v případě neintegrovaných **scénářů**byste měli použít instanční objekt. To znamená, že všechny aplikace, služby nebo skripty, které musí nabízet nebo vyčítat image kontejnerů automatizovaným nebo jinak nedobrým způsobem.

Pro individuální přístup k registru, například když ručně načtete image kontejneru do pracovní stanice pro vývoj, byste měli místo toho použít vlastní [identitu Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) pro přístup k registru (například pomocí [AZ ACR Login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také ve verzích pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další postup

Jakmile budete mít instanční objekt, kterému jste udělili přístup ke svému registru kontejnerů, můžete použít jeho přihlašovací údaje v aplikacích a službách pro bezobslužnou interakci registru. Přihlašovací údaje instančního objektu můžete použít v jakékoli službě Azure, která se dá ověřit pomocí služby Azure Container Registry. Příklady obsahují:

* [Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes (AKS)](container-registry-auth-aks.md)
* [Ověřování pomocí Azure Container Registry z Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
