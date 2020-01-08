---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704205"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Použití rozhraní příkazového řádku Docker k ověření privátního registru kontejnerů

Pomocí privátního registru kontejnerů pro kontejnery Cognitive Services můžete provést libovolným z několika způsobů, ale doporučenou metodou z příkazového řádku je použití [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Pomocí [příkazu`docker login`](https://docs.docker.com/engine/reference/commandline/login/), jak je znázorněno v následujícím příkladu, se přihlaste do `containerpreview.azurecr.io`soukromého registru kontejnerů pro Cognitive Services kontejnery. *\<username\>* nahraďte uživatelským jménem a *\<heslem\>* hesla, které jste zadali v přihlašovacích údajích, které jste dostali od týmu Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste přihlašovací údaje zabezpečili v textovém souboru, můžete zřetězením obsahu tohoto textového souboru zřetězit pomocí příkazu `cat` k příkazu `docker login`, jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cesta a název textového souboru obsahujícího heslo a *\<uživatelské jméno\>* s uživatelským jménem zadaným v přihlašovacích údajích.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
