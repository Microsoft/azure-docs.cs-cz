---
author: baanders
description: zahrnout soubor pro požadavek role v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832316"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Požadavky: požadavky na oprávnění

Aby bylo možné dokončit všechny kroky v tomto článku, musíte být klasifikováni jako vlastník v rámci vašeho předplatného Azure. 

Úroveň oprávnění můžete ověřit spuštěním tohoto příkazu v Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Pokud tento příkaz vrátí chybu s informací, že rozhraní příkazového řádku **nemůže najít uživatele nebo instanční objekt v databázi grafu**:
>
> Pro zbývající část tohoto článku použijte *ID objektu* uživatele místo vašeho e-mailu. Tato situace může nastat pro uživatele na osobních [účtech Microsoft (účty spravované služby)](https://account.microsoft.com/account). 
>
> Pomocí [stránky Azure Portal Azure Active Directory uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) vyberte svůj uživatelský účet a otevřete jeho podrobnosti. Zkopírujte *objectID*uživatele:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Zobrazení stránky uživatele v Azure Portal zvýrazňování identifikátoru GUID v poli ID objektu" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Pak opakujte příkaz seznamu přiřazení role pomocí *ID objektu* uživatele místo vašeho e-mailu.

Pokud jste vlastníkem, je po spuštění příkazu seznamu přiřazení role vlastníkem `roleDefinitionName` hodnota ve výstupu: *Owner*

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell okno zobrazující výstup příkazu AZ role Assignment list":::

Pokud zjistíte, že hodnota je *Přispěvatel* nebo jiná než *vlastník*, můžete pokračovat jedním z následujících způsobů:
* Obraťte se na vlastníka předplatného a požádejte ho o dokončení kroků v tomto článku vaším jménem.
* Obraťte se na vlastníka předplatného nebo na uživatele s rolí správce přístupu uživatele v daném předplatném a požádejte ho, aby vám přistoupili k předplatnému, abyste měli oprávnění, abyste mohli pokračovat sami. To, jestli je to vhodné, závisí na vaší organizaci a vaší roli.