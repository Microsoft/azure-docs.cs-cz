---
author: baanders
description: zahrnutí souboru popisujícího řešení tokenů do omezení mezi klienty pomocí digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589320"
---
To lze provést pomocí následujícího příkazu rozhraní příkazového řádku, kde `<home-tenant-ID>` je ID tenanta Azure AD, který obsahuje instanci digitálních vláken Azure:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Po vyžádání bude identita přijímat token vydaný pro *https://digitaltwins.azure.net* prostředek služby Azure AD, který má stejnou deklaraci ID tenanta pro instanci digitálních vláken Azure. Použití tohoto tokenu v požadavcích rozhraní API nebo s vaším `Azure.Identity` kódem by mělo umožňovat federované identitě přístup k prostředku digitálních vláken Azure.