---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698559"
---
Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a ověří, jestli splňujete kritéria pro přístup k privátnímu registru kontejneru.

> [!IMPORTANT]
> Ve formuláři je nutné použít e-mailovou adresu přidruženou k účtu Microsoft (MSA) nebo k účtu Azure Active Directory (Azure AD).

Pokud je vaše žádost schválena, obdržíte e-mail s pokyny, jak získat vaše přihlašovací údaje a získat přístup k soukromému registru kontejneru.

## <a name="log-in-to-the-private-container-registry"></a>Přihlášení k privátnímu registru kontejnerů

Existuje několik způsobů, jak ověřit pomocí privátního registru kontejnerů pro kontejnery Cognitive Services. Doporučujeme použít metodu příkazového řádku pomocí [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Pomocí příkazu [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) , jak je znázorněno v následujícím příkladu, se přihlaste ke službě `containerpreview.azurecr.io` , což je privátní registr kontejnerů pro kontejnery Cognitive Services. V poli uživatelské jméno a * \< \> heslo* nahraďte heslem, které jste zadali v přihlašovacích údajích, které jste dostali od týmu Azure Cognitive Services. * \< \> *

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pokud jste přihlašovací údaje zabezpečili v textovém souboru, můžete zřetězení obsahu tohoto textového souboru do `docker login` příkazu. Použijte `cat` příkaz, jak je znázorněno v následujícím příkladu. Nahraďte * \< passwordFile \> * cestou a názvem textového souboru, který obsahuje heslo. Nahraďte * \< uživatelské \> * jméno uživatelským jménem, které jste zadali v přihlašovacích údajích.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

