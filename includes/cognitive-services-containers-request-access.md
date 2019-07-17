---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229323"
---
Vyplňte a odešlete [formulář žádosti o kontejnery pro zpracování obrazu Cognitive Services](https://aka.ms/VisionContainersPreview) chcete požádat o přístup ke kontejneru. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat kontejneru. Po odeslání formuláře, tým Azure Cognitive Services a ujistit se, že splňují kritéria pro přístup k registru kontejneru soukromého kontroly.

> [!IMPORTANT]
> Musíte použít e-mailovou adresu, přidružené k účtu Microsoft (MSA) nebo účet Azure Active Directory (Azure AD) ve formuláři.

Pokud vaše žádost se schválí, obdržíte e-mail s pokyny, které popisují, jak získat vaše přihlašovací údaje a přístup k registru kontejneru soukromého.

## <a name="log-in-to-the-private-container-registry"></a>Přihlásit do soukromého registru kontejnerů

Existuje několik způsobů, jak ověřením v daném kontejneru soukromého registru kontejnerů služby Cognitive Services. Doporučujeme používat metodu příkazového řádku pomocí [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Použití [docker login](https://docs.docker.com/engine/reference/commandline/login/) příkaz, jak je znázorněno v následujícím příkladu se přihlaste k `containerpreview.azurecr.io`, což je privátní registr kontejnerů služby Cognitive Services. Nahraďte *\<uživatelské jméno\>* s uživatelským jménem a *\<heslo\>* s heslo zadané přihlašovací údaje, který jste dostali od Azure Týmu služeb cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste zabezpečili svoje přihlašovací údaje do textového souboru, lze zřetězit obsah tohoto souboru text na `docker login` příkazu. Použití `cat` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cestou a názvem textového souboru, který obsahuje heslo. Nahraďte *\<uživatelské jméno\>* s uživatelským jménem, zadat svoje přihlašovací údaje.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

