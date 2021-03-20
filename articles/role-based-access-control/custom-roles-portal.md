---
title: Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure Portal – Azure RBAC
description: Naučte se vytvářet vlastní role Azure pomocí Azure Portal a řízení přístupu na základě role v Azure (Azure RBAC). Zahrnuje to, jak vypisovat, vytvářet, aktualizovat a odstraňovat vlastní role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369222"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Vytvoření nebo aktualizace vlastních rolí pomocí portálu Azure Portal

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní vlastní role Azure. Stejně jako předdefinované role můžete uživatelům, skupinám a instančním objektům přiřadit vlastní role v oborech předplatného a skupiny prostředků. Vlastní role se ukládají v adresáři Azure Active Directory (Azure AD) a můžou se sdílet mezi předplatnými. Každý adresář může mít až 5000 vlastních rolí. Vlastní role se dají vytvořit pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API. Tento článek popisuje, jak pomocí Azure Portal vytvořit vlastní role.

## <a name="prerequisites"></a>Předpoklady

K vytvoření vlastních rolí budete potřebovat:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Krok 1: určení potřebných oprávnění

Azure má tisíce oprávnění, která můžete zahrnout do vaší vlastní role. Tady je několik metod, které vám pomůžou určit oprávnění, která budete chtít přidat do vlastní role:

- Podívejte se na stávající [předdefinované role](built-in-roles.md).
- Seznam služeb Azure, ke kterým chcete udělit přístup
- Určete [poskytovatele prostředků, kteří se mapují ke službám Azure](../azure-resource-manager/management/azure-services-resource-providers.md). Metoda hledání je popsána dále v [kroku 4: oprávnění](#step-4-permissions).
- V části [dostupná oprávnění](resource-provider-operations.md) vyhledejte oprávnění, která chcete zahrnout. Metoda hledání je popsána dále v [kroku 4: oprávnění](#step-4-permissions).

## <a name="step-2-choose-how-to-start"></a>Krok 2: Volba způsobu spuštění

Existují tři způsoby, jak můžete začít vytvářet vlastní role. Můžete naklonovat stávající roli, začít od začátku nebo začít se souborem JSON. Nejjednodušší způsob je najít stávající roli, která má většinu oprávnění, která potřebujete, a pak ji klonovat a upravit pro váš scénář. 

### <a name="clone-a-role"></a>Naklonování role

Pokud existující role nemá poměrně potřebná oprávnění, můžete ji klonovat a pak změnit její oprávnění. Pomocí těchto kroků spustíte klonování role.

1. V Azure Portal otevřete předplatné nebo skupinu prostředků, ve které chcete přiřadit vlastní roli, a pak otevřete **řízení přístupu (IAM)**.

    Na následujícím snímku obrazovky vidíte stránku řízení přístupu (IAM) otevřenou pro předplatné.

    ![Stránka řízení přístupu (IAM) pro předplatné](./media/custom-roles-portal/access-control-subscription.png)

1. Kliknutím na kartu **role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

1. Vyhledejte roli, kterou chcete klonovat, jako je například role čtecího modulu fakturace.

1. Na konci řádku klikněte na tři tečky (**...**) a pak klikněte na **klonovat**.

    ![Klonovat kontextovou nabídku](./media/custom-roles-portal/clone-menu.png)

    Tím se otevře Editor vlastních rolí s vybranou možností **klonovat roli** .

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

### <a name="start-from-scratch"></a>Začátek od nuly

Pokud budete chtít, můžete podle těchto kroků začít vlastní roli úplně od začátku.

1. V Azure Portal otevřete předplatné nebo skupinu prostředků, ve které chcete přiřadit vlastní roli, a pak otevřete **řízení přístupu (IAM)**.

1. Klikněte na **Přidat** a pak klikněte na **Přidat vlastní roli**.

    ![Přidat vlastní nabídku role](./media/custom-roles-portal/add-custom-role-menu.png)

    Otevře se Editor vlastních rolí s vybranou možností **začít od začátku** .

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

### <a name="start-from-json"></a>Začít od formátu JSON

Pokud dáváte přednost, můžete v souboru JSON zadat většinu vlastních hodnot role. Tento soubor můžete otevřít v Editoru vlastních rolí, udělat další změny a pak vytvořit vlastní roli. Pomocí následujícího postupu začněte se souborem JSON.

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

1. V souboru JSON zadejte hodnoty pro různé vlastnosti. Tady je příklad s přidanými hodnotami. Informace o různých vlastnostech najdete v tématu [vysvětlení definic rolí Azure](role-definitions.md).

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
    
1. V Azure Portal otevřete stránku **řízení přístupu (IAM)** .

1. Klikněte na **Přidat** a pak klikněte na **Přidat vlastní roli**.

    ![Přidat vlastní nabídku role](./media/custom-roles-portal/add-custom-role-menu.png)

    Otevře se Editor vlastních rolí.

1. Na kartě základy vyberte v části **základní oprávnění** možnost **začít od formátu JSON**.

1. Vedle pole vybrat soubor klikněte na tlačítko Složka a otevřete dialogové okno otevřít.

1. Vyberte soubor JSON a pak klikněte na **otevřít**.

1. Přejděte ke [kroku 3: Základy](#step-3-basics).

## <a name="step-3-basics"></a>Krok 3: Základy

Na kartě **základy** zadáte název, popis a základní oprávnění pro vlastní roli.

1. Do pole **název vlastní role** zadejte název vlastní role. Název musí být pro adresář služby Azure AD jedinečný. Název může obsahovat písmena, číslice, mezery a speciální znaky.

1. Do pole **Popis** zadejte volitelný popis vlastní role. Tím se stane popis pro vlastní roli.

    Možnost **oprávnění standardních hodnot** by již měla být nastavena v závislosti na předchozím kroku, ale lze ji změnit.

    ![Karta základy se zadanými hodnotami](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Krok 4: oprávnění

Na kartě **oprávnění** zadáte oprávnění pro vlastní roli. V závislosti na tom, jestli jste naklonoval roli nebo pokud jste začali s JSON, může mít karta oprávnění už seznam oprávnění.

![Karta oprávnění pro vytvoření vlastní role](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Přidat nebo odebrat oprávnění

Pomocí těchto kroků můžete přidat nebo odebrat oprávnění pro vlastní roli.

1. Chcete-li přidat oprávnění, klikněte na tlačítko **Přidat oprávnění** a otevřete podokno přidat oprávnění.

    V tomto podokně jsou uvedena všechna dostupná oprávnění seskupená do různých kategorií ve formátu karty. Každá kategorie představuje *poskytovatele prostředků*, což je služba, která poskytuje prostředky Azure.

1. V poli **Hledat oprávnění** zadejte řetězec, ve kterém chcete vyhledat oprávnění. Vyhledejte například *fakturu* a vyhledejte oprávnění související s fakturou.

    Na základě vašeho hledaného řetězce se zobrazí seznam karet poskytovatele prostředků. Seznam způsobu mapování poskytovatelů prostředků na služby Azure najdete v tématu [poskytovatelé prostředků pro služby Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Přidání podokna oprávnění s poskytovatelem prostředků](./media/custom-roles-portal/add-permissions-provider.png)

1. Klikněte na kartu poskytovatel prostředků, která může mít oprávnění, která chcete přidat do vlastní role, jako je například **fakturace společnosti Microsoft**.

    V závislosti na hledaném řetězci se zobrazí seznam oprávnění pro správu pro tohoto poskytovatele prostředků.

    ![Přidat seznam oprávnění](./media/custom-roles-portal/add-permissions-list.png)

1. Pokud hledáte oprávnění, která se vztahují na rovinu dat, klikněte na položku **akce s daty**. V opačném případě ponechte přepínač akce nastaven na **Akce** k zobrazení seznamu oprávnění, která se vztahují na rovinu správy. Další informace o rozdílech mezi rovinou správy a rovinou dat najdete v tématu [Správa a operace s daty](role-definitions.md#management-and-data-operations).

1. V případě potřeby aktualizujte hledaný řetězec a upřesněte hledání.

1. Jakmile najdete jedno nebo více oprávnění, která chcete přidat do vlastní role, přidejte u nich zaškrtnutí u oprávnění. Například zaškrtněte políčko další **: stáhnout fakturu** , abyste mohli přidat oprávnění ke stažení faktur.

1. Kliknutím na **Přidat** přidejte oprávnění do seznamu oprávnění.

    Oprávnění se přidá jako `Actions` nebo `DataActions` .

    ![Přidaná oprávnění](./media/custom-roles-portal/permissions-list-add.png)

1. Chcete-li odebrat oprávnění, klikněte na ikonu Odstranit na konci řádku. Vzhledem k tomu, že uživatel nebude potřebovat možnost vytvářet lístky podpory, může být v tomto příkladu `Microsoft.Support/*` oprávnění odstraněno.

### <a name="add-wildcard-permissions"></a>Přidat oprávnění zástupných znaků

V závislosti na tom, jak se rozhodnete začít, můžete mít ve svém seznamu oprávnění zástupné znaky ( `*` ). Zástupný znak ( `*` ) rozšiřuje oprávnění na vše, co odpovídá řetězci akce, který zadáte. Například následující zástupný řetězec přidá všechna oprávnění související s Azure Cost Management a exporty. To by také zahrnovalo všechna budoucí oprávnění k exportu, která by se mohla přidat.

```
Microsoft.CostManagement/exports/*
```

Pokud chcete přidat nové oprávnění zástupných znaků, nemůžete ho přidat pomocí podokna **Přidat oprávnění** . Chcete-li přidat oprávnění pro zástupný znak, je nutné jej přidat ručně pomocí karty **JSON** . Další informace najdete v části [Krok 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Vyloučit oprávnění

Pokud má vaše role zástupné `*` oprávnění () a chcete vyloučit nebo odečíst konkrétní oprávnění od těchto oprávnění, můžete je vyloučit. Řekněme například, že máte následující oprávnění zástupných znaků:

```
Microsoft.CostManagement/exports/*
```

Pokud nechcete povolit odstranění exportu, můžete vyloučit následující oprávnění odstranit:

```
Microsoft.CostManagement/exports/delete
```

Pokud oprávnění vyloučíte, přidá se jako `NotActions` nebo `NotDataActions` . Efektivní oprávnění pro správu jsou vypočítána přidáním všech `Actions` a poté odečtením všech `NotActions` . Platná oprávnění k datům jsou vypočítána přidáním všech `DataActions` a poté odečtením všech `NotDataActions` .

> [!NOTE]
> Vyloučení oprávnění není stejné jako odepřít. Vyloučení oprávnění je jednoduše pohodlný způsob, jak odečíst oprávnění od oprávnění zástupných znaků.

1. Pokud chcete vyloučit nebo odečíst oprávnění od povoleného oprávnění zástupných znaků, klikněte na **vyloučit oprávnění** a otevřete podokno vyloučení oprávnění.

    V tomto podokně zadáte oprávnění pro správu nebo data, která jsou vyloučena nebo odečtena.

1. Po nalezení jednoho nebo více oprávnění, která chcete vyloučit, přidejte u oprávnění zaškrtnutí a pak klikněte na tlačítko **Přidat** .

    ![Podokno vyloučení oprávnění – vybrané oprávnění](./media/custom-roles-portal/exclude-permissions-select.png)

    Oprávnění se přidá jako `NotActions` nebo `NotDataActions` .

    ![Vyloučená oprávnění](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Krok 5: přiřazení oborů

Na kartě **přiřadit obory** určete, kde má být vaše vlastní role k dispozici pro přiřazení, jako je například předplatné nebo skupina prostředků. V závislosti na tom, jak se rozhodnete začít, může tato karta obsahovat seznam rozsahů, ve kterých jste otevřeli stránku řízení přístupu (IAM). Nastavení přiřaditelné oboru ke kořenovému oboru ("/") není podporováno. V současné době nemůžete přidat skupinu pro správu jako přiřazovatelné obory.

1. Kliknutím na **Přidat přidružitelné obory** otevřete podokno přidat přiřazovatelné obory.

    ![Karta přiřadit obory](./media/custom-roles-portal/assignable-scopes.png)

1. Klikněte na jeden nebo víc oborů, které chcete použít, obvykle v rámci vašeho předplatného.

    ![Přidat obory, které lze přiřadit](./media/custom-roles-portal/add-assignable-scopes.png)

1. Kliknutím na tlačítko **Přidat** přidáte svůj přidružitelné obor.

## <a name="step-6-json"></a>Krok 6: JSON

Na kartě **JSON** uvidíte vlastní roli formátovanou ve formátu JSON. Pokud chcete, můžete JSON přímo upravit. Chcete-li přidat oprávnění zástupného znaku ( `*` ), je nutné použít tuto kartu.

1. Pokud chcete kód JSON upravit, klikněte na **Upravit**.

    ![Karta JSON zobrazující vlastní roli](./media/custom-roles-portal/json.png)

1. Proveďte změny ve formátu JSON.

    Pokud formát JSON není správně naformátovaný, zobrazí se červená zubatá čára a indikátor na svislém hřbetu.

1. Po dokončení úprav klikněte na **Uložit**.

## <a name="step-7-review--create"></a>Krok 7: Kontrola a vytvoření

Na kartě **Revize + vytvořit** můžete zkontrolovat vlastní nastavení role.

1. Zkontrolujte vlastní nastavení role.

    ![Revize + vytvořit kartu](./media/custom-roles-portal/review-create.png)

1. Kliknutím na **vytvořit** vytvořte vlastní roli.

    Po chvíli se zobrazí okno se zprávou oznamující, že vaše vlastní role se úspěšně vytvořila.

    ![Vytvořit vlastní zprávu role](./media/custom-roles-portal/custom-role-success.png)

    Pokud jsou zjištěny nějaké chyby, zobrazí se zpráva.

    ![Zkontrolovat + vytvořit chybu](./media/custom-roles-portal/review-create-error.png)

1. Zobrazení nové vlastní role v seznamu **rolí** . Pokud nevidíte svoji vlastní roli, klikněte na **aktualizovat**.

     Může to trvat několik minut, než se vaše vlastní role zobrazí všude.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Pomocí těchto kroků můžete zobrazit vlastní role.

1. Otevřete předplatné nebo skupinu prostředků a pak otevřete **řízení přístupu (IAM)**.

1. Kliknutím na kartu **role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

1. V seznamu **typ** vyberte **CustomRole** , aby se zobrazily pouze vaše vlastní role.

    Pokud jste právě vytvořili vlastní roli a v seznamu ji nevidíte, klikněte na **aktualizovat**.

    ![Vlastní seznam rolí](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

1. Jak je popsáno výše v tomto článku, otevřete seznam vlastních rolí.

1. Klikněte na tlačítko se třemi tečkami (**...**) pro vlastní roli, kterou chcete aktualizovat, a pak klikněte na **Upravit**. Všimněte si, že nemůžete aktualizovat předdefinované role.

    Vlastní role se otevře v editoru.

    ![Nabídka vlastní role](./media/custom-roles-portal/edit-menu.png)

1. K aktualizaci vlastní role použijte různé karty.

1. Až změny dokončíte, klikněte na kartu **Revize + vytvořit** a Prohlédněte si změny.

1. Kliknutím na tlačítko **aktualizovat** aktualizujte svou vlastní roli.

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

1. Jak je popsáno výše v tomto článku, otevřete seznam vlastních rolí.

1. Odeberte všechna přiřazení rolí, která používají vlastní roli.

1. Klikněte na tlačítko se třemi tečkami (**...**) pro vlastní roli, kterou chcete odstranit, a pak klikněte na **Odstranit**.

    ![Snímek obrazovky zobrazující seznam vlastních rolí, které se dají vybrat k odstranění](./media/custom-roles-portal/delete-menu.png)

    Zcela odstranění vlastní role může trvat několik minut.

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell](tutorial-custom-role-powershell.md)
- [Vlastní role Azure](custom-roles.md)
- [Operace poskytovatele prostředků Azure](resource-provider-operations.md)
