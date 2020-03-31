---
title: Vytvoření nebo aktualizace vlastních rolí Azure pomocí portálu Azure (preview) – Azure RBAC
description: Zjistěte, jak vytvořit vlastní role Azure pro řízení přístupu na základě rolí Azure (Azure RBAC) pomocí portálu Azure. To zahrnuje způsob, jak vypsat, vytvořit, aktualizovat a odstranit vlastní role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674867"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Vytvoření nebo aktualizace vlastních rolí Azure pomocí portálu Azure (Preview)

> [!IMPORTANT]
> Vlastní role Azure pomocí portálu Azure je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role Azure](built-in-roles.md) nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní vlastní role Azure. Stejně jako předdefinované role můžete přiřadit vlastní role uživatelům, skupinám a instančním objektům v oborech předplatného a skupiny prostředků. Vlastní role se ukládají v adresáři Azure Active Directory (Azure AD) a můžou být sdíleny mezi předplatnými. Každý adresář může mít až 5000 vlastních rolí. Vlastní role se můžou vytvářet pomocí portálu Azure, Azure PowerShellu, rozhraní API Azure nebo rozhraní REST API. Tento článek popisuje, jak vytvořit vlastní role pomocí portálu Azure (aktuálně ve verzi Preview).

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit vlastní role, potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Krok 1: Určení oprávnění, která potřebujete

Azure má tisíce oprávnění, která můžete potenciálně zahrnout do vlastní role. Zde jsou čtyři způsoby, jak můžete určit oprávnění, která budete chtít přidat do vlastní role:

| Metoda | Popis |
| --- | --- |
| Podívejte se na existující role | Můžete se podívat na existující role a zjistit, jaká oprávnění se používají. Další informace najdete [v tématu Předdefinované role Azure](built-in-roles.md). |
| Hledání oprávnění podle klíčového slova | Když vytvoříte vlastní roli pomocí portálu Azure, můžete vyhledávat oprávnění podle klíčového slova. Můžete například vyhledat oprávnění k *virtuálnímu počítači* nebo *fakturaci.* Tato funkce hledání je popsána dále v [kroku 4: Oprávnění](#step-4-permissions). |
| Stáhnout všechna oprávnění | Když vytvoříte vlastní roli pomocí portálu Azure, můžete stáhnout všechna oprávnění jako soubor CSV a pak prohledat tento soubor. V podokně **Přidat oprávnění** klikněte na tlačítko Stáhnout **všechna oprávnění** a stáhněte všechna oprávnění. Další informace o podokně Přidat oprávnění naleznete v [tématu Krok 4: Oprávnění](#step-4-permissions). |
| Zobrazení oprávnění v dokumentech | Dostupná oprávnění můžete zobrazit v [operacích zprostředkovatele prostředků Azure Resource Manageru](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Krok 2: Zvolte způsob spuštění

Existují tři způsoby, jak můžete začít vytvářet vlastní roli. Můžete klonovat existující roli, začít od začátku nebo začít se souborem JSON. Nejjednodušší způsob je najít existující roli, která má většinu oprávnění, které potřebujete, a potom klonovat a upravit pro váš scénář. 

### <a name="clone-a-role"></a>Klonovat roli

Pokud existující role zcela nemá potřebná oprávnění, můžete ji naklonovat a potom je upravit. Chcete-li zahájit klonování role, postupujte takto.

1. Na webu Azure Portal otevřete předplatné nebo skupinu prostředků, kde chcete přiřadit vlastní roli, a pak otevřete **řízení přístupu (IAM).**

    Následující snímek obrazovky ukazuje stránku řízení přístupu (IAM) otevřenou pro předplatné.

    ![Stránka řízení přístupu (IAM) pro předplatné](./media/custom-roles-portal/access-control-subscription.png)

1. Kliknutím na kartu **Role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

1. Vyhledejte roli, kterou chcete klonovat, například roli Čtenář fakturace.

1. Na konci řádku klikněte na tři tečky (**...**) a potom klikněte na **Klonovat**.

    ![Kontextová nabídka Klonování](./media/custom-roles-portal/clone-menu.png)

    Tím se otevře editor vlastních rolí s vybranou možností **Klonovat roli.**

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

### <a name="start-from-scratch"></a>Začátek od nuly

Pokud chcete, můžete podle těchto kroků spustit vlastní roli od začátku.

1. Na webu Azure Portal otevřete předplatné nebo skupinu prostředků, kde chcete přiřadit vlastní roli, a pak otevřete **řízení přístupu (IAM).**

1. Klikněte na **Přidat** a potom na **Přidat vlastní roli (náhled).**

    ![Přidat vlastní nabídku rolí](./media/custom-roles-portal/add-custom-role-menu.png)

    Tím se otevře editor vlastních rolí s vybranou volbou **Start od začátku.**

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

### <a name="start-from-json"></a>Začněte od JSON

Pokud chcete, můžete zadat většinu vlastních hodnot rolí v souboru JSON. Soubor můžete otevřít v editoru vlastních rolí, provést další změny a potom vytvořit vlastní roli. Chcete-li začít se souborem JSON, postupujte takto.

1. Vytvořte soubor JSON, který má následující formát:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. V souboru JSON zadejte hodnoty pro různé vlastnosti. Zde je příklad s některými přidanými hodnotami. Informace o různých vlastnostech naleznete v [tématu Understand role definitions](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Na webu Azure Portal otevřete stránku **řízení přístupu (IAM).**

1. Klikněte na **Přidat** a potom na **Přidat vlastní roli (náhled).**

    ![Přidat vlastní nabídku rolí](./media/custom-roles-portal/add-custom-role-menu.png)

    Tím se otevře editor vlastních rolí.

1. Na kartě Základy vyberte v **části Oprávnění směrného plánu**možnost Začít z pole **JSON**.

1. Vedle pole Vybrat soubor klepnutím na tlačítko složky otevřete dialogové okno Otevřít.

1. Vyberte soubor JSON a klepněte na tlačítko **Otevřít**.

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

## <a name="step-3-basics"></a>Krok 3: Základy

Na kartě **Základy** zadáte název, popis a směrný plán oprávnění pro vlastní roli.

1. Do pole **Vlastní název role** zadejte název vlastní role. Název musí být jedinečný pro adresář Azure AD. Název může obsahovat písmena, číslice, mezery a speciální znaky.

1. Do pole **Popis** zadejte volitelný popis vlastní role. To se stane popisem pro vlastní roli.

    Možnost **oprávnění podle směrného plánu** by již měla být nastavena na základě předchozího kroku, ale můžete ji změnit.

    ![Karta Základy se zadanými hodnotami](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Krok 4: Oprávnění

Na kartě **Oprávnění** zadáte oprávnění pro vlastní roli. V závislosti na tom, zda jste naklonovali roli nebo pokud jste začali s JSON, může karta Oprávnění již uvádět některá oprávnění.

![Karta Oprávnění vytvořit vlastní roli](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Přidání nebo odebrání oprávnění

Chcete-li přidat nebo odebrat oprávnění pro vlastní roli, postupujte takto.

1. Pokud chcete přidat oprávnění, kliknutím na **Přidat oprávnění** otevřete podokno Přidat oprávnění.

    V tomto podokně jsou uvedena všechna dostupná oprávnění seskupená do různých kategorií ve formátu karty. Každá kategorie představuje *poskytovatele prostředků*, což je služba, která poskytuje prostředky Azure.

1. Do pole **Hledat oprávnění** zadejte řetězec pro hledání oprávnění. Například vyhledejte *fakturu* a vyhledejte oprávnění související s fakturou.

    Na základě vyhledávacího řetězce se zobrazí seznam karet poskytovatele prostředků. Seznam toho, jak se poskytovatelé prostředků mapují na služby Azure, najdete v tématu [Zprostředkovatelé prostředků pro služby Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Podokno oprávnění přidání s poskytovatelem prostředků](./media/custom-roles-portal/add-permissions-provider.png)

1. Klikněte na kartu poskytovatele prostředků, která může mít oprávnění, která chcete přidat do vlastní role, například **Fakturace společnosti Microsoft**.

    Seznam oprávnění pro správu tohoto zprostředkovatele prostředků se zobrazí na základě vyhledávacího řetězce.

    ![Přidat seznam oprávnění](./media/custom-roles-portal/add-permissions-list.png)

1. Pokud hledáte oprávnění, která se vztahují k rovině dat, klepněte na **položku Akce dat**. V opačném případě ponechte akce nastavenou na **Akce** a uveďte oprávnění, která se vztahují k rovině správy. Další informace o rozdílech mezi rovinou správy a rovinou dat naleznete v [tématu Správa a operace s daty](role-definitions.md#management-and-data-operations).

1. V případě potřeby aktualizujte hledaný řetězec a upřesněte hledání.

1. Jakmile najdete jedno nebo více oprávnění, která chcete přidat do vlastní role, zaškrtněte vedle oprávnění. Chcete-li například přidat oprávnění ke stažení faktur, zaškrtněte políčko **Jiné : Stáhnout fakturu.**

1. Kliknutím na **Přidat** přidáte oprávnění do seznamu oprávnění.

    Oprávnění získá přidán `Actions` jako `DataActions`nebo .

    ![Bylo přidáno oprávnění.](./media/custom-roles-portal/permissions-list-add.png)

1. Chcete-li odebrat oprávnění, klepněte na ikonu odstranění na konci řádku. V tomto příkladu vzhledem k tomu, že uživatel `Microsoft.Support/*` nebude potřebovat možnost vytvářet lístky podpory, oprávnění lze odstranit.

### <a name="add-wildcard-permissions"></a>Přidání oprávnění se zástupnými symboly

V závislosti na tom, jak jste se rozhodli\*začít, můžete mít v seznamu oprávnění oprávnění oprávnění se zástupnými znaky ( ). Zástupný znak\*( ) rozšiřuje oprávnění na vše, co odpovídá řetězci, který zadáte. Předpokládejme například, že jste chtěli přidat všechna oprávnění související se správou nákladů Azure a exporty. Můžete přidat všechna tato oprávnění:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Namísto přidání všech těchto oprávnění můžete pouze přidat oprávnění se zástupnými znaky. Například následující oprávnění se zástupnými symboly je ekvivalentní předchozím pěti oprávněním. To by také zahrnovalo všechna budoucí oprávnění k exportu, která by mohla být přidána.

```
Microsoft.CostManagement/exports/*
```

Pokud chcete přidat nové oprávnění se zástupnými symboly, nemůžete je přidat pomocí podokna **Přidat oprávnění.** Chcete-li přidat oprávnění se zástupnými symboly, musíte je přidat ručně pomocí karty **JSON.** Další informace naleznete [v tématu Krok 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Vyloučit oprávnění

Pokud má vaše role\*oprávnění se zástupným znakem ( ) a chcete vyloučit nebo odečíst určitá oprávnění od tohoto oprávnění se zástupnými symboly, můžete je vyloučit. Řekněme například, že máte následující oprávnění se zástupnými symboly:

```
Microsoft.CostManagement/exports/*
```

Pokud nechcete povolit odstranění exportu, můžete vyloučit následující oprávnění k odstranění:

```
Microsoft.CostManagement/exports/delete
```

Pokud vyloučíte oprávnění, `NotActions` je `NotDataActions`přidáno jako nebo . Skutečná oprávnění pro správu jsou vypočítána přidáním všech `Actions` a `NotActions`následným odečtením všech souborů . Skutečná oprávnění dat jsou vypočítána přidáním všech `DataActions` a následným `NotDataActions`odečtením všech .

> [!NOTE]
> Vyloučení oprávnění není totéž jako odepřít. Vyloučení oprávnění je jednoduše pohodlný způsob, jak odečíst oprávnění od oprávnění se zástupnými symboly.

1. Pokud chcete vyloučit nebo odečíst oprávnění od povoleného oprávnění se zástupnými symboly, otevřete klepnutím na **tlačítko Vyloučit oprávnění** podokno Vyloučit oprávnění.

    V tomto podokně určíte oprávnění pro správu nebo data, která jsou vyloučena nebo odečtena.

1. Jakmile najdete jedno nebo více oprávnění, která chcete vyloučit, přidejte zaškrtnutí vedle oprávnění a klikněte na tlačítko **Přidat.**

    ![Podokno Vyloučit oprávnění – je vybráno oprávnění](./media/custom-roles-portal/exclude-permissions-select.png)

    Oprávnění se přidá `NotActions` jako `NotDataActions`a nebo .

    ![Vyloučeno oprávnění](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Krok 5: Přiřaditelné obory

Na kartě **Přiřaditelné obory** určíte, kde je vaše vlastní role k dispozici pro přiřazení, například předplatné nebo skupina prostředků. V závislosti na tom, jak jste se rozhodli začít, může tato karta vypsat obor, kde jste otevřeli stránku řízení přístupu (IAM). Nastavení přiřaditelného oboru kořenového oboru ("/") není podporováno. V tomto náhledu nelze přidat skupinu pro správu jako přiřaditelný obor.

1. Kliknutím na **Přidat přiřaditelné obory** otevřete podokno Přidat přiřaditelné obory.

    ![Karta Přiřaditelné obory](./media/custom-roles-portal/assignable-scopes.png)

1. Klikněte na jeden nebo více oborů, které chcete použít, obvykle na vaše předplatné.

    ![Přidání přiřaditelných oborů](./media/custom-roles-portal/add-assignable-scopes.png)

1. Kliknutím na tlačítko **Přidat** přidejte přiřaditelný obor.

## <a name="step-6-json"></a>Krok 6: JSON

Na kartě **JSON** uvidíte vlastní roli formátovanou v JSON. Pokud chcete, můžete přímo upravit JSON. Chcete-li přidat oprávnění se\*zástupným znakem ( ), musíte použít tuto kartu.

1. Chcete-li upravit položku JSON, klepněte na tlačítko **Upravit**.

    ![Karta JSON s vlastní rolí](./media/custom-roles-portal/json.png)

1. Proveďte změny json.

    Pokud JSON není správně formátován, zobrazí se ve svislém žlabu červená zubatá čára a indikátor.

1. Po dokončení úprav klepněte na **tlačítko Uložit**.

## <a name="step-7-review--create"></a>Krok 7: Recenze + vytvořit

Na kartě **Revize + vytvoření** můžete zkontrolovat vlastní nastavení role.

1. Zkontrolujte vlastní nastavení role.

    ![Karta Revize + vytvoření](./media/custom-roles-portal/review-create.png)

1. Chcete-li vytvořit vlastní roli, klepněte na **tlačítko Vytvořit.**

    Po chvíli se zobrazí okno se zprávou, které označuje, že vaše vlastní role byla úspěšně vytvořena.

    ![Vytvořit vlastní zprávu o roli](./media/custom-roles-portal/custom-role-success.png)

    Pokud jsou zjištěny nějaké chyby, zobrazí se zpráva.

    ![Kontrola + chyba vytvoření](./media/custom-roles-portal/review-create-error.png)

1. Zobrazení nové vlastní role v seznamu **rolí.** Pokud vlastní roli nevidíte, klikněte na **Aktualizovat**.

     Může trvat několik minut, než se vaše vlastní role zobrazí všude.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li zobrazit vlastní role, postupujte takto.

1. Otevřete předplatné nebo skupinu prostředků a potom otevřete **řízení přístupu (IAM).**

1. Kliknutím na kartu **Role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

1. V seznamu **Typ** vyberte **Vlastní role,** abyste viděli jen své vlastní role.

    Pokud jste právě vytvořili vlastní roli a v seznamu ji nevidíte, klikněte na **Aktualizovat**.

    ![Vlastní seznam rolí](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

1. Jak je popsáno výše v tomto článku, otevřete seznam vlastních rolí.

1. Klepněte na tři tečky (**...**) pro vlastní roli, kterou chcete aktualizovat, a potom klepněte na tlačítko **Upravit**. Všimněte si, že nelze aktualizovat předdefinované role.

    Vlastní role se otevře v editoru.

    ![Vlastní nabídka rolí](./media/custom-roles-portal/edit-menu.png)

1. Pomocí různých karet aktualizujte vlastní roli.

1. Po dokončení změn klikněte na kartu **Revize + vytvoření** a zkontrolujte změny.

1. Kliknutím na tlačítko **Aktualizovat** aktualizujte vlastní roli.

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

1. Jak je popsáno výše v tomto článku, otevřete seznam vlastních rolí.

1. Odeberte všechna přiřazení rolí, která používají vlastní roli.

1. Klepněte na tři tečky (**...**) pro vlastní roli, kterou chcete odstranit, a potom klepněte na **tlačítko Odstranit**.

    ![Vlastní nabídka rolí](./media/custom-roles-portal/delete-menu.png)

    Může trvat několik minut, než bude vaše vlastní role zcela odstraněna.

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role pomocí Azure PowerShellu](tutorial-custom-role-powershell.md)
- [Vlastní role v Azure](custom-roles.md)
- [Operace zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md)
