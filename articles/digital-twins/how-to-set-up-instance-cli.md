---
title: Nastavení instance a ověřování (CLI)
titleSuffix: Azure Digital Twins
description: Viz postup nastavení instance služby digitálního vlákna Azure pomocí rozhraní příkazového řádku (CLI).
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0dfc86503f1b3aa648cb8c7cefe14fbd123f1459
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047501"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Nastavení instance a ověřování digitálních vláken Azure (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází tyto kroky ručně, jednu po jedné, pomocí rozhraní příkazového řádku.
* Pokud chcete projít tyto kroky ručně pomocí Azure Portal, přečtěte si článek verze tohoto článku na portálu: [*Postup: nastavení instance a ověřování (portál)*](how-to-set-up-instance-portal.md).
* Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Vytvoření instance digitálních vláken Azure

V této části **vytvoříte novou instanci digitálních vláken Azure** pomocí příkazu Cloud Shell. Budete muset zadat:
* Skupina prostředků, ve které se má nasadit. Pokud ještě nemáte existující skupinu prostředků, můžete si ji vytvořit pomocí tohoto příkazu:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Oblast pro nasazení. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Název vaší instance. Název nové instance musí být v rámci vašeho předplatného jedinečný (to znamená, že pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zvolený název, zobrazí se výzva k výběru jiného názvu).

Pomocí těchto hodnot v následujícím příkazu vytvořte instanci:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Pokud se instance úspěšně vytvořila, výsledek Cloud Shell vypadá nějak takto a vypisuje informace o prostředku, který jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si název *hostitele*, *název*a *zdroj zdrojové* instance služby Azure Digital revlákens z výstupu. Jedná se o všechny důležité hodnoty, které možná budete potřebovat, když budete pokračovat v práci s instancí digitálních vláken Azure, abyste mohli nastavit ověřování a související prostředky Azure. Pokud budou jiné uživatele programovat s instancí, měli byste je s těmito hodnotami sdílet.

> [!TIP]
> Tyto vlastnosti můžete zobrazit spolu se všemi vlastnostmi instance kdykoli spuštěním `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-user-access-permissions"></a>Nastavení uživatelských oprávnění pro přístup

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Pomocí následujícího příkazu přiřaďte roli (musí být spuštěná uživatelem s [dostatečnými oprávněními](#prerequisites-permission-requirements) v předplatném Azure). Příkaz vyžaduje, abyste předali *hlavní název uživatele* v účtu Azure AD pro uživatele, kterému by se měla přiřadit role. Ve většině případů to bude odpovídat e-mailu uživatele v účtu služby Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Výsledkem tohoto příkazu jsou informace o vytvořeném přiřazení role.

> [!NOTE]
> Pokud tento příkaz vrátí chybu s informací, že rozhraní příkazového řádku **nemůže najít uživatele nebo instanční objekt v databázi grafu**:
>
> Místo toho přiřaďte roli pomocí *ID objektu* uživatele. Tato situace může nastat pro uživatele na osobních [účtech Microsoft (účty spravované služby)](https://account.microsoft.com/account). 
>
> Pomocí [stránky Azure Portal Azure Active Directory uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) vyberte uživatelský účet a otevřete jeho podrobnosti. Zkopírujte *objectID*uživatele:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure" lightbox="media/includes/user-id.png":::
>
> Pak opakujte příkaz seznamu přiřazení role pomocí *ID objektu* uživatele pro `assignee` parametr uvedený výše.

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT. Potom nastavíte oprávnění pro klientské aplikace pro přístup k ní.

## <a name="set-up-access-permissions-for-client-applications"></a>Nastavení přístupových oprávnění pro klientské aplikace

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Pokud chcete vytvořit registraci aplikace, musíte zadat ID prostředku pro rozhraní API digitálních vláken Azure a základní oprávnění pro rozhraní API.

V pracovním adresáři vytvořte nový soubor a zadejte následující fragment kódu JSON pro konfiguraci těchto podrobností: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Uložte tento soubor jako _**manifest.jsna**_.

> [!NOTE] 
> K dispozici jsou některá místa, kde je možné použít "popisný" čitelný řetězec `https://digitaltwins.azure.net` pro místo identifikátoru GUID pro ID aplikace prostředků Azure Digital Replaced `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Mnoho příkladů v celé této dokumentaci například používá ověřování pomocí knihovny MSAL a popisný řetězec lze použít pro tuto sadu. Během tohoto kroku vytváření registrace aplikace je ale ve formátu identifikátoru GUID IDENTIFIKÁTORu vyžadováno, jak je uvedeno výše. 

Potom tento soubor nahrajete do Cloud Shell. V okně Cloud Shell klikněte na ikonu nahrát/stáhnout soubory a vyberte Odeslat.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::
Přejděte na *manifest.js* právě vytvořeného a stiskněte tlačítko "otevřít".

Potom spuštěním následujícího příkazu vytvořte registraci aplikace s adresou URL odpovědi *veřejného klienta/nativního (mobilní & Desktop)* `http://localhost` . Zástupné symboly nahraďte podle potřeby:

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Tady je ukázka výstupu z tohoto příkazu, ve kterém se zobrazují informace o registraci, kterou jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Dále ověřte, že nastavení z nahraných *manifest.jsbyla v* registraci správně nastavena. Provedete to tak, že v řádku nabídek vyberete *manifest* a zobrazí se kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte pole z *manifest.jsv* části `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Shromažďovat důležité hodnoty

V dalším kroku na řádku nabídek vyberte *Přehled* a zobrazte podrobnosti o registraci aplikace:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si *ID aplikace (klienta)* a *ID adresáře (tenanta)* **zobrazené na stránce** . Tyto hodnoty budete potřebovat později při [ověřování klientské aplikace proti rozhraním API pro digitální vlákna Azure](how-to-authenticate-client.md). Pokud nejste osoba, která bude psát kód pro takové aplikace, budete muset tyto hodnoty sdílet s osobou, která bude.

### <a name="other-possible-steps-for-your-organization"></a>Další možné kroky pro vaši organizaci

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci, napsáním ověřovacího kódu klientské aplikace:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)