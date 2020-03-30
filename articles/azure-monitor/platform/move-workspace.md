---
title: Přesunutí pracovního prostoru Analýzy protokolů ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přesunout pracovní prostor Log Analytics do jiného předplatného nebo skupiny prostředků.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659488"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Přesunutí pracovního prostoru Analýzy protokolů do jiného předplatného nebo skupiny prostředků

V tomto článku se dozvíte, jak přesunout pracovní prostor Log Analytics do jiné skupiny prostředků nebo předplatného ve stejné oblasti. Další informace o přesunutí prostředků Azure prostřednictvím portálu Azure, PowerShellu, rozhraní API Azure nebo rozhraní REST API. na [přesunout prostředky do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Pracovní prostor nelze přesunout do jiné oblasti.

## <a name="verify-active-directory-tenant"></a>Ověření klienta služby Active Directory
Zdroj pracovního prostoru a cílové odběry musí existovat v rámci stejného klienta Služby Azure Active Directory. Azure PowerShell slouží k ověření, že obě předplatná mají stejné ID klienta.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Důležité informace o přesunutí pracovního prostoru
Spravovaná řešení, která jsou nainstalovaná v pracovním prostoru, budou přesunuta pomocí operace přesunutí pracovního prostoru Analýzy protokolů. Připojení agenti zůstanou připojeni a po přesunutí zůstanou odesílat data do pracovního prostoru. Vzhledem k tomu, že operace přesunutí vyžaduje, aby neexistovala žádná vazba z pracovního prostoru na jakýkoli účet automatizace, musí být odebrána řešení, která spoléhají na toto propojení.

Řešení, která je nutné před odpojením účtu automatizace odebrat:

- Update Management
- Sledování změn
- Spuštění/zastavení virtuálních počítačů mimo špičku


### <a name="delete-in-azure-portal"></a>Odstranění na webu Azure Portal
Pomocí následujícího postupu odeberte řešení pomocí portálu Azure:

1. Otevřete nabídku pro skupinu prostředků, ve které jsou nainstalována všechna řešení.
2. Vyberte řešení, která chcete odebrat.
3. Klepněte na tlačítko **Odstranit prostředky** a potvrďte prostředky, které mají být odebrány, klepnutím na tlačítko **Odstranit**.

![Odstranit řešení](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Odstranění pomocí PowerShellu

Chcete-li odstranit řešení pomocí prostředí PowerShell, použijte rutinu [Remove-AzResource,](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) jak je znázorněno v následujícím příkladu:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Odebrání pravidel výstrah
Pro **řešení Start/Stop virtuálních her** je také nutné odebrat pravidla výstrah vytvořených řešením. Pomocí následujícího postupu na webu Azure Portal tato pravidla odeberte.

1. Otevřete nabídku **Monitor** a vyberte **Výstrahy**.
2. Klikněte na **Spravovat pravidla výstrah**.
3. Vyberte následující tři pravidla výstrah a klepněte na tlačítko **Odstranit**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Odstranit pravidla](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Zrušení propojení účtu automatizace
Pomocí následujícího postupu odpojte účet Automation z pracovního prostoru pomocí portálu Azure:

1. Otevřete nabídku **Účty automatizace** a vyberte účet, který chcete odebrat.
2. V části **Související zdroje** v nabídce vyberte Propojený **pracovní prostor**. 
3. Kliknutím na **Odpojit pracovní prostor** odpojíte pracovní prostor od účtu automatizace.

    ![Zrušení propojení s pracovním prostorem](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Přesunutí pracovního prostoru

### <a name="azure-portal"></a>portál Azure
Pomocí následujícího postupu můžete pracovní prostor přesunout pomocí portálu Azure:

1. Otevřete nabídku **pracovních prostorů Analýzy protokolů** a vyberte pracovní prostor.
2. Na stránce **Přehled** klikněte na **změnit** vedle **skupiny prostředků** nebo **předplatného**.
3. Otevře se nová stránka se seznamem zdrojů souvisejících s pracovním prostorem. Vyberte prostředky, které chcete přesunout do stejného cílového předplatného a skupiny prostředků jako pracovní prostor. 
4. Vyberte cílovou **skupinu** **Odběr** a Prostředky . Pokud přesouváte pracovní prostor do jiné skupiny prostředků ve stejném předplatném, možnost **Předplatné** se nezobrazí.
5. Klepnutím na **tlačítko OK** přesuňte pracovní prostor a vybrané zdroje.

    ![Portál](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Pokud chcete pracovní prostor přesunout pomocí PowerShellu, použijte [prostředek Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) jako v následujícím příkladu:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Po operaci přesunutí by odebraná řešení a propojení účtů automatizace měly být překonfigurovány tak, aby se pracovní prostor vrátil do předchozího stavu.


## <a name="next-steps"></a>Další kroky
- Seznam, které prostředky podporují přesunout, naleznete v tématu [Přesunutí podpory operace pro zdroje](../../azure-resource-manager/management/move-support-resources.md).
