---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013088"
---
Metoda DefaultAzureCredential v naší aplikaci spoléhá na tři proměnné prostředí: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` a `AZURE_TENANT_ID` . Nastavte tyto proměnné na hodnoty clientId, clientSecret a tenantId, které byly vráceny v kroku "Vytvoření instančního objektu" pomocí `export VARNAME=VALUE` formátu. (Tato metoda nastavuje jenom proměnné pro vaše aktuální prostředí a procesy vytvořené z prostředí. Pokud chcete tyto proměnné do svého prostředí přidat trvale, upravte `/etc/environment ` soubor.) 

Název trezoru klíčů si taky budete muset uložit jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
