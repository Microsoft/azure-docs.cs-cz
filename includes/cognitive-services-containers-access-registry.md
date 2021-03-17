---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704205"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Použití rozhraní příkazového řádku Docker k ověření privátního registru kontejnerů

Pomocí privátního registru kontejnerů pro kontejnery Cognitive Services můžete provést libovolným z několika způsobů, ale doporučenou metodou z příkazového řádku je použití [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Pomocí [ `docker login` příkazu](https://docs.docker.com/engine/reference/commandline/login/), jak je znázorněno v následujícím příkladu, se přihlaste k `containerpreview.azurecr.io` privátnímu registru kontejnerů pro kontejnery Cognitive Services. Nahraďte *\<username\>* uživatelským jménem a *\<password\>* heslem, které jste zadali v přihlašovacích údajích, které jste dostali od týmu Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste své přihlašovací údaje zabezpečili v textovém souboru, můžete zřetězením obsahu tohoto textového souboru zřetězit pomocí `cat` příkazu, `docker login` jak je znázorněno v následujícím příkladu. Nahraďte *\<passwordFile\>* cestou a názvem textového souboru, který obsahuje heslo, a *\<username\>* s uživatelským jménem, které je zadané v přihlašovacích údajích.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
