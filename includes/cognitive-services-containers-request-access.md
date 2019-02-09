---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: d5a6692aceedb6dc7fad7a434a433882cdbea015
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984887"
---
Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejnery pro zpracování obrazu Cognitive Services](https://aka.ms/VisionContainersPreview) chcete požádat o přístup ke kontejneru. Formulář požádá o informace o vás, vaše společnost a uživatelský scénář, který budete používat kontejneru. Po odeslání týmu Azure Cognitive Services zkontroluje formulář pro ověření, že splňujete kritéria pro přístup k registru kontejneru soukromého.

> [!IMPORTANT]
> Musíte použít e-mailovou adresu, přidružené k účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD) účtu ve formuláři.

Pokud vaše žádost se schválí, pak dostanete e-mail s pokyny popisují, jak získat vaše přihlašovací údaje a přístup privátním registru kontejneru.

## <a name="log-in-to-the-private-container-registry"></a>Přihlásit do soukromého registru kontejnerů

Existuje několik způsobů, jak ověřování pomocí kontejneru soukromého registru kontejnerů Cognitive Services, ale je doporučená metoda z příkazového řádku s použitím [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Použití [docker login](https://docs.docker.com/engine/reference/commandline/login/) příkaz, jak je znázorněno v následujícím příkladu, pro přihlášení k `containerpreview.azurecr.io`, privátní registr kontejnerů Cognitive Services. Nahraďte *\<uživatelské jméno\>* s uživatelským jménem a *\<heslo\>* s heslo zadané přihlašovací údaje, který jste dostali od Azure Týmu služeb cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud máte zabezpečený svoje přihlašovací údaje do textového souboru, lze zřetězit obsah textu soubor, pomocí `cat` příkaz, `docker login` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cestou a názvem textového souboru, který obsahuje heslo a *\<uživatelské jméno\>* s uživatelským jménem zadat svoje přihlašovací údaje.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

