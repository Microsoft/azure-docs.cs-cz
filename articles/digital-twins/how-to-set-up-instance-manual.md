---
title: Nastavení instance a ověřování (ruční)
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby Azure Digital revlákens, včetně správného ověřování. Ruční verze
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371447"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Nastavení instance a ověřování pro digitální vlákna Azure (ruční)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází těmito kroky ručně, jedna po jedné. Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

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

### <a name="verify-success"></a>Ověřit úspěch

Pokud se instance úspěšně vytvořila, výsledek Cloud Shell vypadá nějak takto a vypisuje informace o prostředku, který jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="okno Příkaz s úspěšným vytvořením skupiny prostředků a instance digitálních vláken Azure":::

Poznamenejte si název *hostitele*, *název*a *zdroj zdrojové* instance služby Azure Digital revlákens z výstupu. Jedná se o všechny důležité hodnoty, které možná budete potřebovat, když budete pokračovat v práci s instancí digitálních vláken Azure, abyste mohli nastavit ověřování a související prostředky Azure.

> [!TIP]
> Tyto vlastnosti můžete zobrazit spolu se všemi vlastnostmi instance kdykoli spuštěním `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-your-users-access-permissions"></a>Nastavení přístupových oprávnění uživatele

Funkce digitálních vláken Azure používá [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) pro řízení přístupu na základě role (RBAC). To znamená, že před tím, než může uživatel provést volání datové roviny do instance digitálního vlákna Azure, musí být k tomuto uživateli nejprve přiřazena role s oprávněním.

U digitálních vláken Azure je tato role _**vlastníkem Azure Digital Revlákens (Preview)**_. Další informace o rolích a zabezpečení najdete v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md).

V této části se dozvíte, jak vytvořit přiřazení role pro uživatele v instanci digitálního vlákna Azure pomocí e-mailu přidruženého k tenantovi Azure AD v předplatném Azure. V závislosti na vaší roli a vašich oprávněních k vašemu předplatnému Azure to buď nastavíte sami, nebo ji nastavte jménem někoho jiného, kdo bude spravovat instanci digitálních vláken Azure.

### <a name="assign-the-role"></a>Přiřazení role

Pokud chcete uživatelům udělit oprávnění ke správě instance digitálních vláken Azure, musíte jim přiřadit roli _**vlastník Azure Digital Realiass (Preview)**_ v rámci této instance. 

> [!NOTE]
> Všimněte si, že se tato role liší od role *vlastníka* Azure AD, kterou je také možné přiřadit v oboru instance digitálních vláken Azure. Jedná se o dvě odlišné role správy a *vlastník* Azure AD neuděluje přístup k funkcím roviny dat, které jsou uděleny s *vlastníkem digitálních vláken Azure (Preview)*.

K přiřazení této role použijte následující příkaz (musí být spuštěný vlastníkem předplatného Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Výsledkem tohoto příkazu jsou informace o vytvořeném přiřazení role.

> [!TIP]
> Pokud se zobrazí chyba *400: důvodu chybného požadavku* , spusťte následující příkaz, který získá *identifikátor objectID* pro uživatele:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Pak opakujte příkaz přiřazení role pomocí *ID objektu* uživatele místo jejich e-mailu.

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT. Potom nastavíte oprávnění pro klientské aplikace pro přístup k ní.

## <a name="set-up-access-permissions-for-client-applications"></a>Nastavení přístupových oprávnění pro klientské aplikace

Po nastavení instance digitálního vlákna Azure je běžné pracovat s touto instancí prostřednictvím klientské aplikace, kterou vytvoříte. Abyste to mohli udělat, musíte zajistit, aby se klientská aplikace mohla ověřit proti digitálním vazbám Azure. K tomu je potřeba nastavit [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **registraci aplikace** pro použití vaší klientské aplikace.

V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](how-to-use-apis-sdks.md). Později se klientská aplikace ověří proti registraci aplikace a v důsledku toho se jim přidělí nakonfigurovaná přístupová oprávnění k rozhraním API.

>[!TIP]
> Jako vlastník předplatného můžete chtít nastavit novou registraci aplikace pro každou novou instanci digitálních vláken Azure *nebo* to udělat jenom jednou a vytvořit jednu registraci aplikace, která se bude sdílet mezi všemi instancemi digitálních vláken Azure v předplatném.

### <a name="create-the-registration"></a>Vytvořit registraci

Pokud chcete vytvořit registraci aplikace, musíte zadat ID prostředku pro rozhraní API digitálních vláken Azure a základní oprávnění pro rozhraní API. V pracovním adresáři otevřete nový soubor a zadejte následující fragment kódu JSON pro konfiguraci těchto podrobností: 

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

Uložte tento soubor jako *manifest.jsna*.

> [!NOTE] 
> K dispozici jsou některá místa, kde je možné použít "popisný" čitelný řetězec `https://digitaltwins.azure.net` pro místo identifikátoru GUID pro ID aplikace prostředků Azure Digital Replaced `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Mnoho příkladů v celé této dokumentaci například používá ověřování pomocí knihovny MSAL a popisný řetězec lze použít pro tuto sadu. Během tohoto kroku vytváření registrace aplikace je ale ve formátu identifikátoru GUID IDENTIFIKÁTORu vyžadováno, jak je uvedeno výše. 

V okně Cloud Shell klikněte na ikonu nahrát/stáhnout soubory a vyberte Odeslat.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Cloud Shell okno zobrazující výběr možnosti nahrání":::
Přejděte na *manifest.js* právě vytvořeného a stiskněte tlačítko "otevřít".

Potom spuštěním následujícího příkazu vytvořte registraci aplikace (podle potřeby nahraďte zástupné symboly):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Tady je ukázka výstupu z tohoto příkazu, ve kterém se zobrazují informace o registraci, kterou jste vytvořili:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Cloud Shell výstup nové registrace aplikace Azure AD":::

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Nejdřív ověřte, že nastavení z nahraných *manifest.jsbyla v* registraci správně nastavena. Provedete to tak, že v řádku nabídek vyberete *manifest* a zobrazí se kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte pole z *manifest.jsv* části `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Shromažďovat důležité hodnoty

V dalším kroku na řádku nabídek vyberte *Přehled* a zobrazte podrobnosti o registraci aplikace:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Zobrazení důležitých hodnot pro registraci aplikace na portálu":::

Poznamenejte si *ID aplikace (klienta)* a *ID adresáře (tenanta)* **zobrazené na stránce** . Tyto hodnoty budete potřebovat později při [ověřování klientské aplikace proti rozhraním API pro digitální vlákna Azure](how-to-authenticate-client.md). Pokud nejste osoba, která bude psát kód pro takové aplikace, budete muset tyto hodnoty sdílet s osobou, která bude.

### <a name="other-possible-steps-for-your-organization"></a>Další možné kroky pro vaši organizaci

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Další kroky

Napsání ověřovacího kódu klientské aplikace najdete v tématu Postup připojení klientské aplikace k instanci.
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)
