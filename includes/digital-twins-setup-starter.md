---
author: baanders
description: zahrnout soubor pro úvodní informace v nastavení digitálních vláken Azure v Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 3f19674c0e25ebf5cddc2dfb45580a15d04fad46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096901"
---
>[!NOTE]
>Tyto operace mají být dokončeny uživatelem s rolí *vlastníka* v předplatném Azure. I když některé části je možné dokončit bez těchto oprávnění zvýšené úrovně, bude se muset spolupracovat vlastník, aby se úplně nastavila použitelná instance. Další informace najdete v části [*požadavky: požadovaná oprávnění*](#prerequisites-permission-requirements) níže.

Úplná instalace nové instance digitálních vláken Azure se skládá ze tří částí:
1. **Vytvoření instance**
2. **Nastavení přístupových oprávnění uživatele**: aby mohl uživatel Azure provádět aktivity správy, musí mít v instanci roli *vlastníka funkce Azure Digital revlákens (Preview)* . V tomto kroku sami přiřadíte tuto roli (Pokud jste vlastníkem předplatného Azure), nebo k předplatnému získáte vlastníka, který vám přiřadíte.
3. **Nastavení oprávnění k přístupu pro klientské aplikace**: je běžné napsat klientskou aplikaci, kterou používáte k interakci s vaší instancí. Aby klientská aplikace měla přístup k digitálním událostem Azure, musíte nastavit *registraci aplikace* v [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , kterou klientská aplikace použije k ověření instance.

Abyste mohli pokračovat, budete potřebovat předplatné Azure. Můžete si ho nastavit zdarma [tady](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Požadavky: požadavky na oprávnění

Aby bylo možné dokončit všechny kroky v tomto článku, musíte být klasifikováni jako vlastník v rámci vašeho předplatného Azure. 

Úroveň oprávnění můžete ověřit spuštěním tohoto příkazu v Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Pokud jste vlastníkem, `roleDefinitionName` je hodnota ve výstupu *vlastníkem*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Cloud Shell okno zobrazující výstup příkazu AZ role Assignment list":::

Pokud zjistíte, že je hodnota *Přispěvatel* nebo jinou než *vlastník*, můžete se obrátit na vlastníka předplatného a pokračovat jedním z následujících způsobů:
* Žádost, aby vlastník dokončil kroky v tomto článku vaším jménem
* Požádejte vlastníka, aby na předplatné nastavil taky oprávnění, abyste mohli pokračovat sami. To, jestli je to vhodné, závisí na vaší organizaci a vaší roli.
