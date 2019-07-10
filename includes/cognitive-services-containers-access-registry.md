---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704205"
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
