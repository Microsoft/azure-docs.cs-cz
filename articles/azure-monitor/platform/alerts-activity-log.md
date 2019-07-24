---
title: Vytváření, zobrazování a správa výstrah protokolu aktivit v Azure Monitor
description: Výstrahy protokolu aktivit Vytvářejte pomocí Azure Portal, šablony Azure Resource Manager a Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: a7f80698791831b3d4404ea0f687a75c660c2222
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852744"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor  

## <a name="overview"></a>Přehled
Výstrahy protokolu aktivit jsou výstrahy, které se aktivují, když dojde k nové události protokolu aktivit, která odpovídá podmínkám zadaným ve výstraze.

Tyto výstrahy jsou pro prostředky Azure a je možné je vytvořit pomocí šablony Azure Resource Manager. Také je možné je vytvořit, aktualizovat nebo odstranit v Azure Portal. Obvykle vytváříte výstrahy protokolu aktivit pro příjem oznámení, když dojde k určitým změnám prostředků ve vašem předplatném Azure. Výstrahy jsou často vymezené na konkrétní skupiny prostředků nebo prostředky. Například můžete chtít upozornit, když se odstraní libovolný virtuální počítač ve vzorové skupině prostředků **myProductionResourceGroup** . Nebo můžete chtít dostávat oznámení, pokud jsou všechny nové role přiřazené uživateli v rámci vašeho předplatného.

> [!IMPORTANT]
> Výstrahy týkající se oznámení o stavu služby nelze vytvořit prostřednictvím rozhraní pro vytvoření upozornění protokolu aktivit. Další informace o tom, jak vytvořit a používat oznámení o stavu služby, najdete v tématu [příjem výstrah protokolu aktivit pro oznámení o stavu služby](alerts-activity-log-service-notifications.md).

Při vytváření pravidel výstrah zajistěte následující:

- Předplatné v oboru se neliší od předplatného, ve kterém se výstraha vytvořila.
- Tato kritéria musí být kategorie události úroveň, stav, volající, skupina prostředků, ID prostředku nebo typ prostředku, na kterém je výstraha nakonfigurovaná.
- Ve formátu JSON konfigurace výstrahy není k dispozici žádný stav anyOf nebo vnořené podmínky. V podstatě je povolená jenom jedna podmínka "allOf" bez dalších podmínek "allOf" nebo "anyOf".
- Když je kategorie "administrativa", musíte zadat alespoň jedno z výše uvedených kritérií v upozornění. Nemůžete vytvořit výstrahu, která se aktivuje při každém vytvoření události v protokolech aktivit.


## <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal můžete vytvářet a upravovat pravidla upozornění protokolu aktivit. Prostředí se integruje s protokolem aktivit Azure a zajišťuje tak bezproblémové vytváření výstrah pro konkrétní události, které vás zajímají.

### <a name="create-with-the-azure-portal"></a>Vytvořit pomocí Azure Portal

Použijte následující postup.

1. V Azure Portal vyberte monitorovat  > **výstrahy**.
2. V levém horním rohu okna **výstrahy** vyberte **nové pravidlo výstrahy** .

     ![nové pravidlo výstrahy](media/alerts-activity-log/AlertsPreviewOption.png)

     Zobrazí se okno **vytvořit pravidlo** .

      ![nové možnosti pravidla výstrahy](media/alerts-activity-log/create-new-alert-rule-options.png)

3. V části **definovat podmínku výstrahy**zadejte následující informace a vyberte Hotovo :

   - **Cíl výstrahy:** Pokud chcete zobrazit a vybrat cíl pro novou výstrahu, použijte filtr **filtrovat podle** / předplatného**podle typu prostředku**. Vyberte prostředek nebo skupinu prostředků ze zobrazeného seznamu.

     > [!NOTE]
     > 
     > Pro signál protokolu aktivit můžete vybrat jen [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sledovaný prostředek, skupinu prostředků nebo celé předplatné. 

     **Zobrazení cílové ukázkové výstrahy**

     ![Výběr cíle](media/alerts-activity-log/select-target.png)

   - V části **cílová kritéria**vyberte **Přidat kritéria**. Zobrazí se všechny dostupné signály pro cíl, včetně těch, které se nacházejí v různých kategoriích **protokolu aktivit**. Název kategorie se připojí k názvu **monitorovací služby** .

   - Vyberte signál ze seznamu zobrazených různých operací pro **protokol aktivity**typu.

     Můžete vybrat časovou osu Historie protokolu a odpovídající logiku výstrahy pro tento cílový signál:

     **Obrazovka Přidat kritéria**

     ![Přidat kritéria](media/alerts-activity-log/add-criteria.png)

     - **Čas historie**: Události, které jsou k dispozici pro vybranou operaci, lze vykreslit za posledních 6, 12 nebo 24 hodin nebo za poslední týden.

     - **Logika výstrahy**:

       - **Úroveň události**: Úroveň závažnosti události: _Verbose_, _informativní_, _varovná_, _Chyba_nebo _kritická_.
       - **Stav**: Stav události: _Spuštěno_, _selhalo_nebo _bylo úspěšné_.
       - **Událost iniciovaná**: Označuje se také jako volající. E-mailová adresa nebo Azure Active Directory identifikátor uživatele, který operaci provedl.

       Tento vzorový graf signálu má použitou logiku výstrahy:

       ![vybraná kritéria](media/alerts-activity-log/criteria-selected.png)

4. V části **definovat podrobnosti výstrahy**zadejte následující podrobnosti:

    - **Název pravidla výstrahy**: Název nového pravidla výstrahy.
    - **Popis**: Popis nového pravidla výstrahy.
    - **Uložit upozornění do skupiny prostředků**: Vyberte skupinu prostředků, do které chcete toto nové pravidlo Uložit.

5. V části **Skupina akcí**v rozevírací nabídce určete skupinu akcí, kterou chcete přiřadit k tomuto novému pravidlu výstrahy. Případně můžete [vytvořit novou skupinu akcí](../../azure-monitor/platform/action-groups.md) a přiřadit ji k novému pravidlu. Pokud chcete vytvořit novou skupinu, vyberte **+ Nová skupina**.

6. Chcete-li po vytvoření pravidla povolit, vyberte možnost **Ano** pro **pravidlo Povolit při vytváření** .
7. Vyberte **vytvořit pravidlo upozornění**.

    Vytvoří se nové pravidlo výstrahy pro protokol aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.

    Pravidlo můžete povolit, zakázat, upravit nebo odstranit. Přečtěte si další informace o tom, jak spravovat pravidla protokolů aktivit.


Jednoduchá analogie pro porozumění podmínkám, na kterých je možné vytvořit pravidla výstrah v protokolu aktivit, je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit v Azure Portal](activity-log-view.md#azure-portal). Na obrazovce **protokolu Azure monitor-Activity** můžete filtrovat nebo najít nezbytnou událost a pak vytvořit výstrahu pomocí tlačítka **Přidat upozornění protokolu aktivit** . Pak postupujte podle kroků 4 až 7, jak je uvedeno výše.
    
 ![Přidat výstrahu z protokolu aktivit](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Zobrazení a správa v Azure Portal

1. V Azure Portal vyberte monitorovat  > **výstrahy**. V levém horním rohu okna vyberte **Spravovat pravidla výstrah** .

    ![Správa pravidel výstrah](media/alerts-activity-log/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidel.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![vyhledat pravidla upozornění protokolu aktivit](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    K vyhledání pravidla aktivity, které chcete upravit, můžete použít dostupné filtry, _předplatné_, _skupinu prostředků_, _prostředek_, _typ signálu_nebo _stav_.

   > [!NOTE]
   > 
   > Můžete upravit pouze **Popis**, **cílové kritérium**a **skupiny akcí**.

3. Vyberte pravidlo a dvakrát klikněte pro úpravu možností pravidla. Proveďte požadované změny a pak vyberte **Uložit**.

   ![Správa pravidel výstrah](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pravidlo můžete povolit, zakázat nebo odstranit. Po výběru pravidla, jak je popsáno v kroku 2, vyberte příslušnou možnost v horní části okna.


## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Chcete-li vytvořit upozornění protokolu aktivit pomocí šablony Azure Resource Manager, vytvořte prostředek typu `microsoft.insights/activityLogAlerts`. Pak vyplníte všechny související vlastnosti. Tady je šablona, která vytvoří upozornění protokolu aktivit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Předchozí vzorový JSON se dá uložit jako například sampleActivityLogAlert. JSON pro účely tohoto Názorného postupu a dá se nasadit pomocí [Azure Resource Manager v Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Může trvat až 5 minut, než se nové pravidlo upozornění protokolu aktivit aktivuje.

## <a name="rest-api"></a>REST API 
[Rozhraní API upozornění protokolu aktivit Azure monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) je REST API. Je plně kompatibilní s Azure Resource Manager REST API. Dá se použít přes PowerShell pomocí rutiny Správce prostředků nebo Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Nasazení šablony Správce prostředků pomocí prostředí PowerShell
Pokud chcete použít PowerShell k nasazení ukázkové Správce prostředků šablony zobrazené v předchozí části [Azure Resource Manager šablony](#azure-resource-manager-template) , použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

kde sampleActivityLogAlert. Parameters. JSON obsahuje hodnoty, které jsou k dispozici pro parametry potřebné pro vytvoření pravidla výstrahy.

### <a name="use-activity-log-powershell-cmdlets"></a>Použít rutiny prostředí PowerShell pro protokol aktivit

Výstrahy protokolu aktivit mají k dispozici vyhrazené rutiny PowerShellu:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Vytvoří novou výstrahu protokolu aktivit nebo aktualizuje existující upozornění protokolu aktivit.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Získá jeden nebo více prostředků upozornění protokolu aktivit.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Povolí existující upozornění protokolu aktivit a nastaví jeho značky.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Zakáže existující upozornění protokolu aktivit a nastaví jeho značky.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Odebere upozornění protokolu aktivit.

## <a name="azure-cli"></a>Azure CLI

Vyhrazené příkazy rozhraní příkazového řádku Azure v rámci nastavení [AZ monitor Activity-log Alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) jsou k dispozici pro správu pravidel upozornění protokolu aktivit.

Chcete-li vytvořit nové pravidlo upozornění protokolu aktivit, použijte následující příkazy v tomto pořadí:

1. [AZ monitor Activity-log Alert Create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Vytvoří nový prostředek pravidla upozornění protokolu aktivit.
1. [AZ monitor Activity-Scope Alert log](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Přidejte obor pro pravidlo vytvoření výstrahy protokolu aktivit.
1. [AZ monitor Activity-log Alert Action-Group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Přidejte skupinu akcí do pravidla upozornění protokolu aktivit.

Pokud chcete načíst jeden prostředek pravidla upozornění protokolu aktivit, použijte příkaz [Azure CLI AZ monitor Activity-log Alert show.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) Chcete-li zobrazit všechny prostředky pravidla upozornění protokolu aktivit ve skupině prostředků, použijte příkaz [AZ monitor Activity-log Alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Prostředky pravidla upozornění protokolu aktivit je možné odebrat pomocí příkazu rozhraní příkazového řádku Azure CLI [AZ monitor Activity-log Alert Delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Další postup

- Přečtěte si o [schématu Webhooku pro protokoly aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Přečtěte si [Přehled protokolů aktivit](../../azure-monitor/platform/activity-log-alerts.md).
- Přečtěte si další informace o [skupinách akcí](../../azure-monitor/platform/action-groups.md).  
- Přečtěte si o oznámeních o [stavu služby](../../azure-monitor/platform/service-notifications.md).
