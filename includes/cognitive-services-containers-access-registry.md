---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704205"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Ověření registru privátního kontejneru pomocí cli Dockeru

Můžete ověřit pomocí registru soukromého kontejneru pro kontejnery cognitive services libovolným z několika způsobů, ale doporučená metoda z příkazového řádku je použití [příkazového řádku Dockeru](https://docs.docker.com/engine/reference/commandline/cli/).

Pomocí [ `docker login` příkazu](https://docs.docker.com/engine/reference/commandline/login/), jak je znázorněno v `containerpreview.azurecr.io`následujícím příkladu, se přihlaste k registru privátního kontejneru pro kontejnery služeb Cognitive Services. Nahraďte * \<uživatelské jméno\> * uživatelským jménem a * \<heslem\> * heslem, které je uvedeno v přihlašovacích údajích, které jste obdrželi od týmu Služby Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste zabezpečili svá pověření v textovém souboru, můžete obsah tohoto textového `cat` souboru `docker login` zřetězit pomocí příkazu do příkazu, jak je znázorněno v následujícím příkladu. Nahraďte * \<passwordFile\> * cestou a názvem textového souboru, který obsahuje heslo a * \<uživatelské jméno,\> * uživatelským jménem, které je uvedeno ve vašich pověřeních.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
