---
title: Použít řízení přístupu na základě rolí pro StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak používat řízení přístupu na základě rolí Azure (RBAC) v kontextu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159131"
---
# <a name="role-based-access-control-for-storsimple"></a>Řízení přístupu na základě rolí pro StorSimple

Tento článek obsahuje stručný popis toho, jak azure řízení přístupu na základě rolí (RBAC) lze použít pro vaše zařízení StorSimple. RBAC nabízí jemně odstupňovanou správu přístupu pro Azure. Pomocí RBAC udělit správné množství přístupu k uživatelům StorSimple dělat svou práci namísto poskytování všichni neomezený přístup. Další informace o základech správy přístupu v Azure najdete [v tématu Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md).

Tento článek se vztahuje na zařízení řady StorSimple 8000 se spuštěním aktualizace 3.0 nebo novější na webu Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Role RBAC pro StorSimple

RBAC lze přiřadit na základě rolí. Role zajišťují určité úrovně oprávnění na základě dostupných prostředků v prostředí. Existují dva typy rolí, které si mohou uživatelé StorSimple vybrat: předdefinované nebo vlastní.

* **Předdefinované role** – předdefinované role mohou být vlastníkem, přispěvatelem, čtečkou nebo správcem přístupu uživatelů. Další informace najdete [v tématu předdefinované role pro řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md).

* **Vlastní role** – Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role RBAC pro StorSimple. Chcete-li vytvořit vlastní roli RBAC, začněte s předdefinovanou rolí, upravte ji a importujte ji zpět do prostředí. Stahování a nahrávání role se spravují buď pomocí Azure PowerShellu nebo rozhraní příkazového příkazu k onomu Azure. Další informace naleznete v [tématu Vytvoření vlastních rolí pro řízení přístupu založené na rolích](../role-based-access-control/custom-roles.md).

Chcete-li zobrazit různé role, které jsou k dispozici pro uživatele zařízení StorSimple na webu Azure Portal, přejděte na službu Správce zařízení StorSimple a potom přejděte na **> rolích řízení přístupu (IAM).**


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Vytvoření vlastní role pro správce infrastruktury StorSimple

V následujícím příkladu začneme s předdefinovanou **čtečkou** rolí, která uživatelům umožňuje zobrazit všechny obory prostředků, ale ne je upravovat ani vytvářet nové. Tuto roli pak rozšíříme a vytvoříme novou vlastní roli správce infrastruktury StorSimple. Tato role je přiřazena uživatelům, kteří mohou spravovat infrastrukturu pro zařízení StorSimple.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Přihlásí se k Azure.

    `Connect-AzAccount`

3. Exportujte roli čtečky jako šablonu JSON v počítači.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otevřete soubor JSON v sadě Visual Studio. Uvidíte, že typická role RBAC se skládá ze tří hlavních částí **Akce**, **NotActions**a **AssignableScopes**.

    V části **Akce** jsou uvedeny všechny povolené operace pro tuto roli. Každá akce je přiřazena od poskytovatele prostředků. Pro správce infrastruktury StorSimple `Microsoft.StorSimple` použijte poskytovatele prostředků.

    Pomocí PowerShellu můžete zobrazit všechny poskytovatele prostředků, kteří jsou k dispozici a zaregistrováni ve vašem předplatném.

    `Get-AzResourceProvider`

    Můžete také zkontrolovat všechny dostupné rutiny prostředí PowerShell pro správu poskytovatelů prostředků.

    V části **NotActions** jsou uvedeny všechny akce s omezeným přístupem pro určitou roli RBAC. V tomto příkladu nejsou omezeny žádné akce.
    
    V části **AssignableScopes**jsou uvedena ID předplatného. Ujistěte se, že role RBAC obsahuje explicitní ID předplatného, kde se používá. Pokud není zadáno správné ID předplatného, není povoleno importovat roli ve vašem předplatném.

    Upravte soubor s ohledem na předchozí aspekty.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importujte vlastní roli RBAC zpět do prostředí.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Tato role by se nyní měla zobrazit v seznamu rolí v okně **řízení aplikace Access.**

![Zobrazit role RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Další informace naleznete v části [Vlastní role](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Ukázkový výstup pro vytvoření vlastní role pomocí prostředí PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Přidání uživatelů do vlastní role

Přístup se uděluje z prostředku, skupiny prostředků nebo předplatného, které je v rozsahu platnosti přiřazení role. Při poskytování přístupu mějte na paměti, že přístup udělený v nadřazeném uzlu je zděděn podřízeným. Další informace naleznete v části [řízení přístupu na základě rolí](../role-based-access-control/overview.md).

1. Přejděte na **řízení přístupu (IAM)**. Klepněte na **tlačítko + Přidat** v okně řízení přístupu.

    ![Přidání přístupu k roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Vyberte roli, kterou chcete přiřadit, v tomto případě je **To StorSimple Infrastructure Admin**.

3. Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

4. Chcete-li vytvořit přiřazení, vyberte **uložit.**

    ![Přidání oprávnění k roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Přidání oznámení uživatele** sleduje průběh. Po úspěšném přidání uživatele se aktualizuje seznam uživatelů v řízení přístupu.

## <a name="view-permissions-for-the-custom-role"></a>Zobrazit oprávnění pro vlastní roli

Po vytvoření této role můžete zobrazit oprávnění přidružená k této roli na webu Azure Portal.

1. Chcete-li zobrazit oprávnění přidružená k této roli, přejděte na **téma Řízení přístupu (IAM) > role > správce infrastruktury StorSimple**. Zobrazí se seznam uživatelů v této roli.

2. Vyberte uživatele správce infrastruktury StorSimple a klepněte na tlačítko **Oprávnění**.

    ![Zobrazit oprávnění pro roli správce Infra StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Zobrazí se oprávnění přidružená k této roli.

    ![Zobrazit uživatele v roli správce Infra StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak přiřadit vlastní role interním a externím uživatelům](../role-based-access-control/role-assignments-external-users.md).
