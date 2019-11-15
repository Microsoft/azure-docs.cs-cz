---
title: Přesunutí pracovního prostoru Log Analytics v Azure Monitor | Microsoft Docs
description: Naučte se, jak přesunout pracovní prostor Log Analytics do jiného předplatného nebo skupiny prostředků.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093784"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Přesunutí pracovního prostoru Log Analytics do jiného předplatného nebo skupiny prostředků

V tomto článku se seznámíte s postupem přesunutí Log Analyticsho pracovního prostoru do jiné skupiny prostředků nebo předplatného ve stejné oblasti. Další informace o přesunu prostředků Azure můžete získat prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Při [přesunu prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Pracovní prostor nemůžete přesunout do jiné oblasti.

## <a name="verify-active-directory-tenant"></a>Ověřit tenanta služby Active Directory
Zdrojové a cílové odběry pracovního prostoru musí existovat v rámci stejného Azure Active Directory tenanta. Pomocí Azure PowerShell ověříte, že obě předplatná mají stejné ID tenanta.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Odebrat řešení
Spravovaná řešení, která jsou nainstalovaná v pracovním prostoru, se přesunou pomocí operace přesunu pracovního prostoru Log Analytics. Vzhledem k tomu, že je nutné odebrat odkaz z pracovního prostoru na libovolný účet služby Automation, je třeba odebrat řešení, která spoléhají na tento odkaz.

Mezi řešení, která je nutné odebrat, patří následující: 

- Update Management
- Sledování změn
- Spuštění/zastavení virtuálních počítačů mimo špičku


### <a name="azure-portal"></a>portál Azure
K odebrání řešení pomocí Azure Portal použijte následující postup:

1. Otevřete nabídku pro skupinu prostředků, ve které jsou nainstalována nějaká řešení.
2. Vyberte řešení, která chcete odebrat.
3. Klikněte na **Odstranit prostředky** a potvrďte odebrání prostředků kliknutím na **Odstranit**.

![Odstranit řešení](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

Pokud chcete řešení odebrat pomocí prostředí PowerShell, použijte rutinu [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , jak je znázorněno v následujícím příkladu:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Odebrat pravidla upozornění
Pro řešení **spouštění a zastavování virtuálních počítačů** je také potřeba odebrat pravidla upozornění vytvořená řešením. Tato pravidla odeberete pomocí následujícího postupu v Azure Portal.

1. Otevřete nabídku **monitorování** a pak vyberte **výstrahy**.
2. Klikněte na **Spravovat pravidla výstrah**.
3. Vyberte následující tři pravidla výstrahy a pak klikněte na **Odstranit**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Odstranit pravidla](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Zrušit propojení účtu Automation
Pomocí následujícího postupu odpojte účet Automation z pracovního prostoru pomocí Azure Portal:

1. Otevřete nabídku **účty Automation** a potom vyberte účet, který chcete odebrat.
2. V části **související prostředky** v nabídce vyberte **propojený pracovní prostor**. 
3. Kliknutím na **Zrušit propojení pracovního prostoru** můžete zrušit propojení pracovního prostoru s vaším účtem Automation.

    ![Zrušit propojení pracovního prostoru](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Přesunout pracovní prostor

### <a name="azure-portal"></a>portál Azure
Pomocí následujícího postupu můžete přesunout svůj pracovní prostor pomocí Azure Portal:

1. Otevřete nabídku **pracovní prostory Log Analytics** a pak vyberte svůj pracovní prostor.
2. Na stránce **Přehled** klikněte na **změnit** vedle **skupiny prostředků** nebo **předplatného**.
3. Otevře se nová stránka se seznamem prostředků, které se vztahují k pracovnímu prostoru. Vyberte prostředky, které se mají přesunout do stejného cílového předplatného a skupiny prostředků jako pracovní prostor. 
4. Vyberte cílové **předplatné** a **skupinu prostředků**. Pokud přesouváte pracovní prostor do jiné skupiny prostředků ve stejném předplatném, možnost **předplatné** se nezobrazí.
5. Kliknutím na tlačítko **OK** přesunete pracovní prostor a vybrané prostředky.

    ![Portál](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Pokud chcete přesunout svůj pracovní prostor pomocí PowerShellu, použijte rutinu [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) jako v následujícím příkladu:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Po operaci přesunu je potřeba překonfigurovat řešení a propojit účet Automation, aby se pracovní prostor vrátil zpátky do předchozího stavu.


## <a name="next-steps"></a>Další kroky
- Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/move-support-resources.md).
