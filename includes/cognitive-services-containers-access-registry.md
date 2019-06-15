---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063944"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Použití rozhraní příkazového řádku Dockeru k ověření privátním registru kontejneru

Můžete ověřit pomocí kontejneru soukromého registru kontejnerů Cognitive Services v několika způsoby, ale doporučujeme z příkazového řádku, je použít [rozhraní příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Použití [ `docker login` příkaz](https://docs.docker.com/engine/reference/commandline/login/), jak je znázorněno v následujícím příkladu se přihlaste k `containerpreview.azurecr.io`, privátní registr kontejnerů Cognitive Services. Nahraďte *\<uživatelské jméno\>* s uživatelským jménem a *\<heslo\>* s heslem, které je k dispozici v přihlašovacích údajů, který jste dostali od Tým Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste zabezpečený svoje přihlašovací údaje do textového souboru, lze zřetězit obsah textového souboru, s použitím `cat` příkaz, `docker login` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cestou a názvem textového souboru, který obsahuje heslo a *\<uživatelské jméno\>* s uživatelským jménem který je součástí svoje přihlašovací údaje.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
