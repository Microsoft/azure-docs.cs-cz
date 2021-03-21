---
title: Nastavení instance a ověřování (CLI)
titleSuffix: Azure Digital Twins
description: Viz postup nastavení instance služby digitálního vlákna Azure pomocí rozhraní příkazového řádku (CLI).
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7108ee956c18fbcc150b56cbcb8ae1c69841dda4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198570"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Nastavení instance a ověřování digitálních vláken Azure (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází tyto kroky ručně, jednu po jedné, pomocí rozhraní příkazového řádku.
* Pokud chcete projít tyto kroky ručně pomocí Azure Portal, přečtěte si článek verze tohoto článku na portálu: [*Postup: nastavení instance a ověřování (portál)*](how-to-set-up-instance-portal.md).
* Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Vytvoření instance digitálních vláken Azure

V této části **vytvoříte novou instanci digitálních vláken Azure** pomocí příkazu Cloud Shell. Budete muset zadat:
* Skupina prostředků, do které se instance nasadí. Pokud ještě nemáte existující skupinu prostředků, můžete si ji vytvořit pomocí tohoto příkazu:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Oblast pro nasazení. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Název vaší instance. Pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zadaný název, zobrazí se výzva k výběru jiného názvu.

Pomocí těchto hodnot v následujícím příkazu vytvořte instanci:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Pokud se instance úspěšně vytvořila, výsledek Cloud Shell vypadá nějak takto a vypisuje informace o prostředku, který jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si název **hostitele**, **název** a **zdroj zdrojové** instance služby Azure Digital revlákens z výstupu. Jedná se o všechny důležité hodnoty, které možná budete potřebovat, když budete pokračovat v práci s instancí digitálních vláken Azure, abyste mohli nastavit ověřování a související prostředky Azure. Pokud budou jiné uživatele programovat s instancí, měli byste je s těmito hodnotami sdílet.

> [!TIP]
> Tyto vlastnosti můžete zobrazit spolu se všemi vlastnostmi instance kdykoli spuštěním `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-user-access-permissions"></a>Nastavení uživatelských oprávnění pro přístup

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Pomocí následujícího příkazu přiřaďte roli (musí být spuštěná uživatelem s [dostatečnými oprávněními](#prerequisites-permission-requirements) v předplatném Azure). Příkaz vyžaduje, abyste předali *hlavní název uživatele* v účtu Azure AD pro uživatele, kterému by se měla přiřadit role. Ve většině případů to bude odpovídat e-mailu uživatele v účtu služby Azure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Výsledkem tohoto příkazu jsou informace o vytvořeném přiřazení role.

> [!NOTE]
> Pokud tento příkaz vrátí chybu s informací, že rozhraní příkazového řádku **nemůže najít uživatele nebo instanční objekt v databázi grafu**:
>
> Místo toho přiřaďte roli pomocí *ID objektu* uživatele. Tato situace může nastat pro uživatele na osobních [účtech Microsoft (účty spravované služby)](https://account.microsoft.com/account). 
>
> Pomocí [stránky Azure Portal Azure Active Directory uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) vyberte uživatelský účet a otevřete jeho podrobnosti. Zkopírujte *objectID* uživatele:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Zobrazení stránky uživatele v Azure Portal zvýrazňování identifikátoru GUID v poli ID objektu" lightbox="media/includes/user-id.png":::
>
> Pak opakujte příkaz seznamu přiřazení role pomocí *ID objektu* uživatele pro `assignee` parametr uvedený výše.

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT.

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](/cli/azure/ext/azure-iot/dt)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci s ověřovacím kódem:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)