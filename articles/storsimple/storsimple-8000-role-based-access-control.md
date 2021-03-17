---
title: Použití řízení přístupu na základě role v Azure pro StorSimple | Microsoft Docs
description: Popisuje, jak používat řízení přístupu na základě role Azure (Azure RBAC) v kontextu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518972"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Řízení přístupu na základě role v Azure pro StorSimple

Tento článek poskytuje stručný popis toho, jak se dá řízení přístupu na základě role Azure (RBAC) použít pro zařízení StorSimple. Azure RBAC nabízí jemně odstupňovanou správu přístupu pro Azure. Využijte Azure RBAC k udělení správného přístupu uživatelům StorSimple k tomu, aby mohli provádět své úlohy, a ne udělit všem neomezený přístup. Další informace o základech správy přístupu v Azure najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

Tento článek se týká zařízení řady StorSimple 8000 se systémem Update 3,0 nebo novějším v Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Role Azure pro StorSimple

Na základě rolí se dá přiřadit Azure RBAC. Role zajišťují určité úrovně oprávnění na základě dostupných prostředků v prostředí. Existují dva typy rolí, ze kterých mohou StorSimple uživatelé vybírat: předdefinované nebo vlastní.

* **Předdefinované role** – předdefinované role můžou být vlastník, přispěvatel, čtenář nebo správce přístupu uživatelů. Další informace najdete v tématu [předdefinované role pro Access Control na základě rolí Azure](../role-based-access-control/built-in-roles.md).

* **Vlastní role** – Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role Azure pro StorSimple. Pokud chcete vytvořit vlastní roli Azure, začněte s předdefinovanou rolí, upravte ji a pak ji znovu naimportujte do prostředí. Stažení a nahrání role se spravují pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Další informace najdete v tématu [Vytvoření vlastních rolí pro Access Control na základě rolí](../role-based-access-control/custom-roles.md).

Pokud chcete zobrazit různé role dostupné pro uživatele zařízení StorSimple v Azure Portal, přejděte do služby StorSimple Device Manager a pak přejděte do části **řízení přístupu (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Vytvoření vlastní role pro správce infrastruktury StorSimple

V následujícím příkladu Začínáme integrovanou **čtečkou** rolí, která umožňuje uživatelům zobrazit všechny obory prostředků, ale nemůžou je upravovat nebo vytvářet nové. Tuto roli pak rozšíříme a vytvoříme tak nového vlastního správce infrastruktury StorSimple. Tato role je přiřazená uživatelům, kteří můžou spravovat infrastrukturu pro zařízení StorSimple.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Přihlásí se k Azure.

    `Connect-AzAccount`

3. Exportujte roli Čtenář do svého počítače jako šablonu JSON.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otevřete soubor JSON v aplikaci Visual Studio. Uvidíte, že typická role Azure se skládá ze tří hlavních sekcí, **akcí**, **NotActions**a **AssignableScopes**.

    V části **Akce** jsou uvedeny všechny povolené operace pro tuto roli. Každá akce je přiřazena od poskytovatele prostředků. Pro správce infrastruktury StorSimple použijte `Microsoft.StorSimple` poskytovatele prostředků.

    K zobrazení všech poskytovatelů prostředků dostupných a registrovaných ve vašem předplatném použijte PowerShell.

    `Get-AzResourceProvider`

    Můžete také vyhledat všechny dostupné rutiny prostředí PowerShell pro správu poskytovatelů prostředků.

    V oddílech **NotActions** jsou uvedené všechny omezené akce pro určitou roli Azure. V tomto příkladu nejsou omezeny žádné akce.
    
    Pod **AssignableScopes**jsou uvedena ID předplatného. Ujistěte se, že role Azure obsahuje explicitní ID předplatného, kde se používá. Pokud není zadané správné ID předplatného, nebudete moct do svého předplatného importovat roli.

    Upravte soubor tak, aby byl v souladu s předchozími požadavky.

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

6. Naimportujte vlastní roli Azure zpátky do prostředí.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Tato role by se teď měla zobrazit v seznamu rolí v okně **řízení přístupu** .

![Zobrazit role Azure](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Další informace najdete na webu [Custom role](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Ukázkový výstup pro vytvoření vlastní role prostřednictvím PowerShellu

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

## <a name="add-users-to-the-custom-role"></a>Přidat uživatele do vlastní role

Přístup se uděluje z prostředku, skupiny prostředků nebo předplatného, které je v rozsahu platnosti přiřazení role. Při poskytování přístupu mějte na paměti, že podřízený objekt, který je udělený v nadřazeném uzlu, zdědí podřízená položka. Další informace najdete na webu [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

1. Přejděte na **řízení přístupu (IAM)**. V okně řízení přístupu klikněte na **+ Přidat** .

    ![Přidání přístupu do role Azure](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Vyberte roli, kterou chcete přiřadit. v tomto případě se jedná o **správce infrastruktury StorSimple**.

3. Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

4. Vyberte **Uložit** a vytvořte přiřazení.

    ![Přidání oprávnění do role Azure](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Přidávání oznámení o **uživateli** sleduje průběh. Po úspěšném přidání uživatele se seznam uživatelů v řízení přístupu aktualizuje.

## <a name="view-permissions-for-the-custom-role"></a>Zobrazit oprávnění pro vlastní roli

Po vytvoření této role můžete zobrazit oprávnění přidružená k této roli v Azure Portal.

1. Chcete-li zobrazit oprávnění přidružená k této roli, přejděte do části **řízení přístupu (IAM) > role > správce infrastruktury StorSimple**. Zobrazí se seznam uživatelů v této roli.

2. Vyberte uživatele s rolí správce infrastruktury StorSimple a klikněte na **oprávnění**.

    ![Oprávnění k zobrazení pro roli správce StorSimple (infrastruktura)](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Zobrazí se oprávnění přidružená k této roli.

    ![Zobrazit uživatele v roli správce StorSimple (infrastruktura)](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [přiřadit vlastní role pro interní a externí uživatele](../role-based-access-control/role-assignments-external-users.md).
