---
title: Použití řízení přístupu na základě rolí pro StorSimple | Dokumentace Microsoftu
description: Popisuje způsob použití řízení přístupu na základě Role v Azure (RBAC) v kontextu StorSimple.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785380"
---
# <a name="role-based-access-control-for-storsimple"></a>Řízení přístupu na základě rolí pro StorSimple

Tento článek obsahuje stručný popis použití řízení přístupu v Azure Role-Based (RBAC) pro zařízení StorSimple. RBAC poskytuje propracovanou správu přístupu pro Azure. Použijte pro udělení, že ideální úroveň přístupu ke službě StorSimple uživatelů vykonávat svoji práci místo všem neomezený přístup. Další informace o základní správu přístupu v Azure najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../role-based-access-control/overview.md).

Tento článek se týká do zařízení řady StorSimple 8000 Update 3.0 nebo později na webu Azure portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Role RBAC pro StorSimple

RBAC je možné přiřadit na základě rolí. Role zajištění určité úrovně oprávnění na základě dostupných prostředků v prostředí. Existují dva typy rolí, které StorSimple uživatelé můžou vybírat: vestavěná nebo vlastní.

* **Předdefinované role** – předdefinované role může být vlastníkem, přispěvatelem, čtečky nebo správce přístupu uživatelů. Další informace najdete v tématu [předdefinované role pro řízení přístupu na základě Role v Azure](../role-based-access-control/built-in-roles.md).

* **Vlastní role** – Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role RBAC pro StorSimple. Chcete-li vytvořit vlastní roli RBAC, začněte s předdefinovanou roli, upravovat a importujte ji zpět do prostředí. Stažení a nahrání role se spravují pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [vytváření vlastních rolí pro řízení přístupu na základě Role](../role-based-access-control/custom-roles.md).

Chcete-li zobrazit různé role, které jsou k dispozici pro uživatele zařízení StorSimple na webu Azure Portal, přejděte do služby Správce zařízení StorSimple a potom přejděte ke **řízení přístupu (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Vytvořit vlastní roli správce infrastruktury pro StorSimple

V následujícím příkladu Začneme s předdefinovanou roli **čtečky** , který umožňuje uživatelům Chcete-li zobrazit všechny obory prostředků, ale ne upravovat nebo vytvářet nové. Rozšiřujeme pak této roli k vytvoření nové vlastní role správce StorSimple infrastruktury. Tato role je přiřazená k uživatelům, kteří můžou spravovat infrastrukturu pro zařízení StorSimple.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Přihlásí se k Azure.

    `Connect-AzAccount`

3. Role Čtenář exportujte jako šablonu JSON ve vašem počítači.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otevřete soubor JSON v sadě Visual Studio. Uvidíte, že typické role RBAC se skládá ze tří hlavních částí **akce**, **NotActions**, a **AssignableScopes**.

    V **akce** oddíl, všechny povolené operace pro tuto roli patří. Každá akce se přiřadí od zprostředkovatele prostředků. Pro správce StorSimple infrastruktury, použijte `Microsoft.StorSimple` poskytovatele prostředků.

    Chcete-li zobrazit všechny poskytovatele prostředků k dispozici a registrovaný ve vašem předplatném pomocí Powershellu.

    `Get-AzResourceProvider`

    Můžete také vyhledat všechny dostupné rutiny prostředí PowerShell můžete spravovat poskytovatele prostředků.

    V **NotActions** části, jsou uvedené akce s omezeným přístupem pro určité role RBAC. V tomto příkladu nejsou žádné akce s omezeným přístupem.
    
    V části **AssignableScopes**, jsou uvedeny ID předplatného. Ujistěte se, že RBAC role obsahuje ID explicitní předplatného, ve kterém se používá. Pokud není zadané ID správné předplatné, nemáte oprávnění importovat roli v rámci vašeho předplatného.

    Upravte soubor dodržujte při tom předchozím důležité informace.

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


Tato role by se měla objevit v seznamu rolí v **řízení přístupu** okno.

![Zobrazit role RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Další informace najdete v části [vlastní role](../role-based-access-control/custom-roles.md).

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

Přístup se uděluje z prostředku, skupiny prostředků nebo předplatného, které je v rozsahu platnosti přiřazení role. Při poskytování přístupu, berte v úvahu, že podřízené zdědí přístup na nadřazený uzel. Další informace najdete v části [řízení přístupu na základě rolí](../role-based-access-control/overview.md).

1. Přejděte na **řízení přístupu (IAM)**. Klikněte na tlačítko **+ přidat** v okně řízení přístupu.

    ![Přidat přístup k roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Vyberte roli, kterou chcete přiřadit, v tomto případě je **správce infrastruktury StorSimple**.

3. Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

4. Vyberte **Uložit** k vytvoření tohoto přiřazení.

    ![Přidejte oprávnění do RBAC role](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Přidává se uživatel** oznámení sleduje průběh. Po úspěšném přidání uživatele, se aktualizuje seznam uživatelů v řízení přístupu.

## <a name="view-permissions-for-the-custom-role"></a>Zobrazit oprávnění pro vlastní role

Po vytvoření této role se zobrazí oprávnění spojená s touto rolí na portálu Azure portal.

1. Chcete-li zobrazit oprávnění spojená s touto rolí, přejděte na **řízení přístupu (IAM) > role > Správce infrastruktury StorSimple**. Zobrazí se seznam uživatelů v této roli.

2. Vyberte uživatele správce infrastruktury StorSimple a klikněte na tlačítko **oprávnění**.

    ![Zobrazit oprávnění pro roli správce Infra StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Oprávnění přidružené k této roli jsou zobrazena.

    ![Zobrazit uživatele v roli správce Infra StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Další postup

Zjistěte, jak [přiřazení vlastních rolí pro interní a externí uživatele](../role-based-access-control/role-assignments-external-users.md).
