---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229323"
---
Vyplňte a odešlete [formulář žádosti o požadavky na kontejnery Cognitive Services Vision,](https://aka.ms/VisionContainersPreview) abyste požádali o přístup ke kontejneru. Formulář požaduje informace o vás, vaší společnosti a uživatelském scénáři, pro který budete kontejner používat. Po odeslání formuláře tým Azure Cognitive Services zkontroluje, zda splňujete kritéria pro přístup k registru privátního kontejneru.

> [!IMPORTANT]
> Ve formuláři musíte použít e-mailovou adresu přidruženou k účtu Microsoft (MSA) nebo k účtu Azure Active Directory (Azure AD).

Pokud je váš požadavek schválen, obdržíte e-mail s pokyny, které popisují, jak získat přihlašovací údaje a přístup k registru soukromého kontejneru.

## <a name="log-in-to-the-private-container-registry"></a>Přihlášení do registru privátního kontejneru

Existuje několik způsobů, jak ověřit pomocí registru soukromého kontejneru pro kontejnery Cognitive Services. Doporučujeme použít metodu příkazového řádku pomocí [příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Pomocí příkazu [přihlášení dockeru,](https://docs.docker.com/engine/reference/commandline/login/) jak je znázorněno `containerpreview.azurecr.io`v následujícím příkladu, se přihlaste k , což je soukromý registr kontejnerů pro kontejnery cognitive services. Nahraďte * \<uživatelské jméno\> * uživatelským jménem a * \<heslem\> * heslem, které je uvedeno v pověřeních, která jste obdrželi od týmu Služby Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste zabezpečili pověření v textovém souboru, můžete obsah tohoto `docker login` textového souboru zřetězit do příkazu. Použijte `cat` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte * \<passwordFile\> * cestou a názvem textového souboru, který obsahuje heslo. Nahraďte * \<uživatelské jméno\> * uživatelským jménem, které je uvedeno ve vašich pověřeních.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

