---
title: Přesunutí pracovního prostoru Log Analytics v Azure Monitor | Microsoft Docs
description: Naučte se, jak přesunout pracovní prostor Log Analytics do jiného předplatného nebo skupiny prostředků.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 8f48ed1aa7422d6925c3a7b0ad30b59a479e4614
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034944"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Přesunutí pracovního prostoru Log Analytics do jiného předplatného nebo skupiny prostředků

V tomto článku se seznámíte s postupem přesunutí Log Analyticsho pracovního prostoru do jiné skupiny prostředků nebo předplatného ve stejné oblasti. Další informace o přesunu prostředků Azure můžete získat prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Při [přesunu prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Pracovní prostor nemůžete přesunout do jiné oblasti.

## <a name="verify-active-directory-tenant"></a>Ověřit tenanta služby Active Directory
Zdrojové a cílové odběry pracovního prostoru musí existovat v rámci stejného Azure Active Directory tenanta. Pomocí Azure PowerShell ověříte, že obě předplatná mají stejné ID tenanta.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Požadavky na přesun v pracovním prostoru
- Spravovaná řešení, která jsou nainstalovaná v pracovním prostoru, se přesunou pomocí operace přesunu pracovního prostoru Log Analytics. 
- Klíče pracovního prostoru (primární i sekundární) se znovu generují pomocí operace přesunu pracovního prostoru. Pokud uchováváte kopii klíčů pracovního prostoru v trezoru klíčů, aktualizujte je pomocí nových klíčů vygenerovaných po přesunu pracovního prostoru. 
- Připojení agenti zůstanou připojeni a po přesunutí budou data v pracovním prostoru posílat. 
- Vzhledem k tomu, že operace přesunutí vyžaduje, aby z pracovního prostoru neexistovaly žádné propojené služby, je nutné odebrat řešení, která spoléhají na tento odkaz, aby bylo možné pracovní prostor přesunout. Řešení, která je potřeba odebrat, než budete moct zrušit propojení svého účtu Automation:
  - Update Management
  - Sledování změn
  - Spuštění/zastavení virtuálních počítačů mimo špičku
  - Azure Security Center

>[!IMPORTANT]
> **Zákazníci s Sentinel Azure**
> - V současné době je po nasazení služby Azure Sentinel v pracovním prostoru přesun pracovního prostoru do jiné skupiny prostředků nebo předplatného nepodporován. 
> - Pokud jste pracovní prostor již přesunuli, zakažte v rámci **analýz** všechna aktivní pravidla a po pěti minutách je znovu povolte. To by mělo být efektivní řešení ve většině případů, ale pro opakování iterace není podporované a neprovádí se na vlastní riziko.
> 
> **Znovu vytvořit výstrahy**
> - Po přesunutí je nutné znovu vytvořit všechny výstrahy, protože oprávnění jsou založena na ID prostředku Azure pracovního prostoru, které se během přesunu pracovního prostoru mění.
>
> **Aktualizace cest k prostředkům**
> - Po přesunu pracovního prostoru musí být všechny prostředky Azure nebo externí prostředky, které odkazují na pracovní prostor, zkontrolovány a aktualizovány tak, aby odkazovaly na novou cílovou cestu prostředku.
> 
>   *Příklady:*
>   - [Pravidla upozornění Azure Monitor](../alerts/alerts-resource-move.md)
>   - Aplikace jiných výrobců
>   - Vlastní skriptování
>

### <a name="delete-solutions-in-azure-portal"></a>Odstranění řešení v Azure Portal
K odebrání řešení pomocí Azure Portal použijte následující postup:

1. Otevřete nabídku pro skupinu prostředků, ve které jsou nainstalována nějaká řešení.
2. Vyberte řešení, která chcete odebrat.
3. Klikněte na **Odstranit prostředky** a potvrďte odebrání prostředků kliknutím na **Odstranit**.

![Odstranit řešení](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Odstranění pomocí PowerShellu

Pokud chcete řešení odebrat pomocí prostředí PowerShell, použijte rutinu [Remove-AzResource](/powershell/module/az.resources/remove-azresource) , jak je znázorněno v následujícím příkladu:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Odebrat pravidla upozornění pro řešení spuštění/zastavení virtuálních počítačů
Chcete-li odebrat řešení **Spustit nebo zastavit virtuální počítače** , je také nutné odebrat pravidla upozornění vytvořená řešením. Tato pravidla odeberete pomocí následujícího postupu v Azure Portal.

1. Otevřete nabídku **monitorování** a pak vyberte **výstrahy**.
2. Klikněte na **Spravovat pravidla výstrah**.
3. Vyberte následující tři pravidla výstrahy a pak klikněte na **Odstranit**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Odstraňování pravidel](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Zrušit propojení účtu Automation
Pomocí následujícího postupu odpojte účet Automation z pracovního prostoru pomocí Azure Portal:

1. Otevřete nabídku **účty Automation** a potom vyberte účet, který chcete odebrat.
2. V části **související prostředky** v nabídce vyberte **propojený pracovní prostor**. 
3. Kliknutím na **Zrušit propojení pracovního prostoru** můžete zrušit propojení pracovního prostoru s vaším účtem Automation.

    ![Zrušení propojení s pracovním prostorem](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Přesunout pracovní prostor

### <a name="azure-portal"></a>portál Azure
Pomocí následujícího postupu můžete přesunout svůj pracovní prostor pomocí Azure Portal:

1. Otevřete nabídku **pracovní prostory Log Analytics** a pak vyberte svůj pracovní prostor.
2. Na stránce **Přehled** klikněte na **změnit** vedle **skupiny prostředků** nebo **předplatného**.
3. Otevře se nová stránka se seznamem prostředků, které se vztahují k pracovnímu prostoru. Vyberte prostředky, které se mají přesunout do stejného cílového předplatného a skupiny prostředků jako pracovní prostor. 
4. Vyberte cílové **předplatné** a **skupinu prostředků**. Pokud přesouváte pracovní prostor do jiné skupiny prostředků ve stejném předplatném, možnost **předplatné** se nezobrazí.
5. Kliknutím na tlačítko **OK** přesunete pracovní prostor a vybrané prostředky.

    ![Snímek obrazovky zobrazuje podokno přehled v pracovním prostoru Log Analytics s možnostmi změny skupiny prostředků a názvu předplatného.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Pokud chcete přesunout svůj pracovní prostor pomocí PowerShellu, použijte rutinu [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) jako v následujícím příkladu:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Po operaci přesunu je potřeba překonfigurovat řešení a propojit účet Automation, aby se pracovní prostor vrátil zpátky do předchozího stavu.


## <a name="next-steps"></a>Další kroky
- Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/management/move-support-resources.md).
