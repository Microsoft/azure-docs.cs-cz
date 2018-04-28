---
title: Pomocí řízení přístupu na základě rolí pro StorSimple | Microsoft Docs
description: Popisuje způsob použití řízení přístupu na základě Role v Azure (RBAC) v kontextu zařízení StorSimple.
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
ms.openlocfilehash: 412098ad6b8a6620b165b6cecaa0d0b3ee05f63e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="role-based-access-control-for-storsimple"></a>Řízení přístupu na základě rolí pro StorSimple

Tento článek obsahuje stručný popis použití řízení řízení přístupu (RBAC) pro zařízení StorSimple. RBAC nabízí vyladění správy přístupu pro Azure. Použijte funkci RBAC udělit, že právě správného množství přístup uživatelům StorSimple pro svou práci místo všem uživatelům udělíte neomezený přístup. Další informace o základy správy přístupu v Azure najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](../role-based-access-control/overview.md).

Tento článek se týká na řadu zařízení StorSimple 8000 spuštění aktualizace 3.0 nebo novější na portálu Azure.

## <a name="rbac-roles-for-storsimple"></a>Role RBAC pro StorSimple

RBAC lze přiřadit na základě rolí. Role zajistěte určité úrovně oprávnění na základě dostupných prostředků v prostředí. Existují dva typy rolí, které uživatelé StorSimple můžete vybírat z: vestavěná nebo vlastní.

* **Předdefinované role** -integrovaných rolí může být vlastník, Přispěvatel, čtečky nebo správce uživatelského přístupu. Další informace najdete v tématu [předdefinované role pro řízení přístupu na základě Role v Azure](../role-based-access-control/overview.md#built-in-roles).

* **Vlastní role** – Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role RBAC pro StorSimple. Pokud chcete vytvořit vlastní role RBAC, začínat předdefinovaná role, upravovat a importujte ji zpět do prostředí. Stažení a nahrání role se spravují pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [vytvářet vlastní role pro řízení přístupu na základě Role](../role-based-access-control/custom-roles.md).

Chcete-li zobrazit dostupné pro uživatele zařízení StorSimple jiné role na portálu Azure, přejděte do služby StorSimple Manager zařízení a potom přejděte na **přístup k ovládacímu prvku (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Vytvořit vlastní role správce infrastruktury pro StorSimple

V následujícím příkladu Začneme s předdefinovaná role **čtečky** umožňuje uživatelům zobrazíte všechny obory prostředků, ale ne upravovat nebo vytvářet nové. Rozšiřujeme pak této roli k vytvoření nové vlastní role správce infrastruktury StorSimple. Tato role je přiřazená uživatelům, kteří mohou spravovat infrastrukturu pro zařízení StorSimple.

1. Spusťte prostředí Windows PowerShell jako správce.

2. Přihlásí se k Azure.

    `Connect-AzureRmAccount`

3. Role čtenáře exportujte jako šablonu JSON ve vašem počítači.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otevřete soubor JSON v sadě Visual Studio. Uvidíte, že typické role RBAC obsahuje tři hlavní části **akce**, **NotActions**, a **AssignableScopes**.

    V **akce** část, všechny jsou uvedeny povolených operací pro tuto roli. Každá akce je přiřazen od zprostředkovatele prostředků. Pro správce infrastruktury StorSimple, použijte `Microsoft.StorSimple` poskytovatele prostředků.

    Pomocí prostředí PowerShell najdete v části všech poskytovatelů prostředků k dispozici a registrovaný ve vašem předplatném.

    `Get-AzureRMResourceProvider`

    Můžete také vyhledat všechny dostupné rutiny prostředí PowerShell ke správě zprostředkovatelé prostředků.

    V **NotActions** částech, všechny akce s omezeným přístupem pro konkrétní role RBAC jsou uvedeny. V tomto příkladu nejsou žádné akce s omezeným přístupem.
    
    V části **AssignableScopes**, jsou uvedeny ID předplatného. Ujistěte se, že RBAC role obsahuje ID explicitní předplatného, kde se používá. Pokud není zadáno ID správné předplatné, nejsou povoleny pro import role v rámci vašeho předplatného.

    Upravte soubor, přičemž mějte předchozí aspekty.

    ```
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

6. Importujte vlastní role RBAC zpět do prostředí.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Tato role by se měla zobrazit v seznamu rolí v **řízení přístupu** okno.

![Role RBAC zobrazení](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Další informace, přejděte na [vytvořit vlastní role RBAC pomocí prostředí PowerShell](../role-based-access-control/role-assignments-external-users.md#create-a-custom-rbac-role-to-open-support-requests-using-powershell).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Ukázkový výstup pro vytvoření vlastní role pomocí prostředí PowerShell

```
PS C:\WINDOWS\system32> Connect-AzureRmAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

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

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Přidání uživatelů do vlastní role

Přístup se uděluje z prostředku, skupiny prostředků nebo předplatného, které je v rozsahu platnosti přiřazení role. Při poskytování přístupu, berte v úvahu, že podřízená zdědí přístup udělit na nadřazený uzel. Další informace, přejděte na [prostředků přístupu a hierarchii dědičnosti](../role-based-access-control/overview.md#resource-hierarchy-and-access-inheritance).

1. Přejděte na **přístup k ovládacímu prvku (IAM)**. Klikněte na tlačítko **+ přidat** v okně řízení přístupu.

    ![Přidat přístup do RBAC role](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Vyberte roli, kterou chcete přiřadit, v takovém případě je **správce infrastruktury StorSimple**.

3. Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

4. Vyberte **Uložit** vytvořte přiřazení.

    ![Přidejte oprávnění do RBAC role](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Přidává se uživatel** oznámení sleduje průběh. Po úspěšném přidání uživatele, se aktualizuje seznam uživatelů v řízení přístupu.

## <a name="view-permissions-for-the-custom-role"></a>Zobrazení oprávnění pro vlastní role

Po vytvoření této role, můžete zobrazit oprávnění spojená s touto rolí na portálu Azure.

1. Chcete-li zobrazit oprávnění spojená s touto rolí, přejděte na **přístup k ovládacímu prvku (IAM) > role > Správce infrastruktury StorSimple**. Zobrazí se seznam uživatelů v této roli.

2. Vyberte uživatele správce infrastruktury StorSimple a klikněte na tlačítko **oprávnění**.

    ![Zobrazení oprávnění pro roli správce infrastruktura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Zobrazí se oprávnění spojená s touto rolí.

    ![Zobrazit uživatele v roli správce infrastruktura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Další postup

Zjistěte, jak [přiřadit vlastní role pro uživatele, interních a externích](../role-based-access-control/role-assignments-external-users.md).

