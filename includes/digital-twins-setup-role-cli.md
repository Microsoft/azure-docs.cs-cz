---
author: baanders
description: zahrnout soubor pro požadavek role v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407434"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Požadavky: požadavky na oprávnění

Aby bylo možné dokončit všechny kroky v tomto článku, musíte být klasifikováni jako vlastník v rámci vašeho předplatného Azure. 

Úroveň oprávnění můžete ověřit spuštěním tohoto příkazu v Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Pokud jste vlastníkem, `roleDefinitionName` je hodnota ve výstupu *vlastníkem*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell okno zobrazující výstup příkazu AZ role Assignment list":::

Pokud zjistíte, že hodnota je *Přispěvatel* nebo jiná než *vlastník*, můžete pokračovat jedním z následujících způsobů:
* Obraťte se na vlastníka předplatného a požádejte ho o dokončení kroků v tomto článku vaším jménem.
* Obraťte se na vlastníka předplatného nebo na uživatele s rolí správce přístupu uživatele v daném předplatném a požádejte ho, aby vám přistoupili k předplatnému, abyste měli oprávnění, abyste mohli pokračovat sami. To, jestli je to vhodné, závisí na vaší organizaci a vaší roli.