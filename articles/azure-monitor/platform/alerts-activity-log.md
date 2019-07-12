---
title: Vytvoření, zobrazení a Správa upozornění protokolu aktivit ve službě Azure Monitor
description: Vytvořte upozornění protokolu aktivit pomocí webu Azure portal šablony Azure Resource Manageru a Azure Powershellu.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705294"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Vytvoření, zobrazení a Správa upozornění protokolu aktivit s využitím Azure monitoru  

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivovat při výskytu nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze.

Tyto výstrahy jsou určené pro prostředky Azure a lze vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Obvykle je vytvoření upozornění protokolu aktivit pro příjem oznámení při výskytu změn konkrétní prostředky ve vašem předplatném Azure. Výstrahy jsou často jako svůj obor konkrétní prostředek skupiny nebo prostředky. Například můžete chtít upozorněni, až všechny virtuální počítače ve skupině prostředků ukázkové **myProductionResourceGroup** se odstraní. Nebo můžete chtít získat vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.

> [!IMPORTANT]
> Nelze vytvořit upozornění na oznámení o stavu služby přes rozhraní pro vytvoření upozornění protokolu aktivit. Další informace o tom, jak vytvořit a používat oznámení o stavu služby, najdete v článku [dostávat upozornění protokolu aktivit na oznámení o stavu služby](alerts-activity-log-service-notifications.md).

Při vytváření pravidel upozornění, zkontrolujte následující:

- Předplatné v oboru se liší od předplatného kde se vytvoří výstraha.
- Kritéria musí být úroveň, stav, volající, skupinu prostředků, ID prostředku nebo kategorie události typ prostředku ve které výstraha nakonfigurována.
- Neexistuje žádná podmínka "anyOf" nebo vnořené podmínky v konfiguraci výstrahy JSON. V podstatě je povolen pouze jednu podmínku "allOf" bez jakýchkoli dalších "allOf" nebo "anyOf" podmínky.
- Pokud kategorie "správce", musíte zadat alespoň jeden z předchozích kritéria v upozornění. Nelze vytvořit výstrahu, která se aktivuje vždy, když se vytvoří událost v protokolech aktivit.


## <a name="azure-portal"></a>portál Azure

Na webu Azure portal můžete použít k vytvoření a úprava pravidla upozornění protokolu aktivit. Možnosti jsou součástí protokolu aktivit v Azure zajistit bezproblémové vytvoření výstrahy pro konkrétní události, které vás zajímají.

### <a name="create-with-the-azure-portal"></a>Vytvořit pomocí webu Azure portal

Pomocí následujícího postupu.

1. Na webu Azure Portal, vyberte **monitorování** > **výstrahy**.
2. Vyberte **nové pravidlo upozornění** v levém horním rohu **výstrahy** okna.

     ![Nové pravidlo upozornění](media/alerts-activity-log/AlertsPreviewOption.png)

     **Vytvořit pravidlo** zobrazí se okno.

      ![nové možnosti pravidlo upozornění](media/alerts-activity-log/create-new-alert-rule-options.png)

3. V části **Definujte podmínku upozornění**, zadejte následující informace a vyberte **provádí**:

   - **Cíl upozornění:** Chcete-li zobrazit a vybrat cíl pro nové výstrahy, použijte **filtrovat podle předplatného** / **filtrovat podle typu prostředku**. Ze zobrazeného seznamu vyberte prostředek nebo skupinu prostředků.

     > [!NOTE]
     > 
     > Můžete vybrat prostředek, skupinu prostředků nebo celé předplatné pro signálu protokolu aktivit.

     **Cíl upozornění ukázkové zobrazení**

     ![Výběr cíle](media/alerts-activity-log/select-target.png)

   - V části **cílová kritéria**vyberte **přidat kritéria**. Se zobrazí všechny dostupné signály pro cíl, což zahrnuje ty z různých kategorií **protokolu aktivit**. Se připojí název kategorie **Monitor Service** název.

   - Vyberte signál, který se v seznamu zobrazí různých operací možné pro daný typ **protokolu aktivit**.

     Můžete vybrat na časové ose historie protokolu a odpovídající logika upozornění pro tento cíl signál:

     **Přidání obrazovky kritéria**

     ![Přidat kritéria](media/alerts-activity-log/add-criteria.png)

     - **Historie čas**: K dispozici pro vybranou operaci události mohou být vykreslena za posledních 6, 12 nebo 24 hodin nebo za poslední týden.

     - **Upozornění logiky**:

       - **Úroveň události**: Úroveň závažnosti události: _Podrobné_, _informační_, _upozornění_, _chyba_, nebo _kritické_.
       - **Stav**: Stav události: _Spuštění_, _nepovedlo_, nebo _úspěšné_.
       - **Zahájit událost**: Označované také jako volající. E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

       Tento ukázkový signál graf má logika upozornění použít:

       ![kritéria vybraná](media/alerts-activity-log/criteria-selected.png)

4. V části **definujte podrobnosti o upozornění**, zadejte následující podrobnosti:

    - **Název pravidla upozornění**: Název pro nové pravidlo upozornění.
    - **Popis**: Popis pro nové pravidlo upozornění.
    - **Uložit upozornění do skupiny prostředků**: Vyberte skupinu prostředků, ve které chcete uložit toto nové pravidlo.

5. V části **skupiny akcí**, z rozevírací nabídky, určete skupinu akcí, které chcete přiřadit k této nové pravidlo upozornění. Nebo, [vytvořit novou skupinu akcí](../../azure-monitor/platform/action-groups.md) a přiřaďte ho ke nové pravidlo. Chcete-li vytvořit novou skupinu, vyberte **+ nová skupina**.

6. Chcete-li povolit pravidla po jejich vytvoření, vyberte **Ano** pro **Povolit pravidlo po vytvoření** možnost.
7. Vyberte **vytvořit pravidlo upozornění**.

    Vytvořit nové pravidlo upozornění protokolu aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.

    Můžete povolit, zakázat, úprava nebo odstranění pravidla. Další informace o tom, jak spravovat pravidla protokolu aktivit.


Jednoduché přirovnání vysvětlení podmínky, na kterých je možné vytvořit pravidla upozornění v protokolu aktivit je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure Portal](activity-log-view.md#azure-portal). V **Azure Monitor – protokol aktivit** obrazovky, můžete filtrovat nebo najít potřebné události a pak vytvořte výstrahu pomocí **přidat upozornění protokolu aktivit** tlačítko. Potom postupujte podle uvedených kroků 4 až 7 stejně jako předtím.
    
 ![Přidat upozornění protokolu aktivit](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Umožňuje zobrazit a spravovat na webu Azure Portal

1. Na webu Azure Portal, vyberte **monitorování** > **výstrahy**. Vyberte **spravovat pravidla výstrah** v levém horním rohu okna.

    ![Spravovat pravidla výstrah](media/alerts-activity-log/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidla.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![Hledat pravidla upozornění protokolu aktivit](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Můžete použít k dispozici tyto filtry, _předplatné_, _skupiny prostředků_, _prostředků_, _signalizuje, že typ_, nebo _stav_ chtěli pravidlo aktivit, které chcete upravit.

   > [!NOTE]
   > 
   > Můžete upravit pouze **popis**, **cílová kritéria**, a **skupiny akcí**.

3. Vyberte pravidlo a dvakrát klikněte pro úpravu možností pravidla. Proveďte požadované změny a pak vyberte **Uložit**.

   ![Spravovat pravidla výstrah](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Můžete povolit, zakázat nebo odstranit pravidlo. Jakmile vyberete pravidla, jak je popsáno v kroku 2, vyberte příslušnou možnost v horní části okna.


## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Pokud chcete vytvořit upozornění protokolu aktivit s použitím šablony Azure Resource Manageru, vytvoříte prostředek typu `microsoft.insights/activityLogAlerts`. Potom vyplňte všechny související vlastnosti. Zde je šablona, která vytvoří upozornění protokolu aktivit:

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
Předchozí ukázce JSON se dají uložit jako, například sampleActivityLogAlert.json pro účely tohoto návodu a je možné nasadit s použitím [Azure Resource Manageru na webu Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Může trvat až 5 minut, než nové pravidlo upozornění protokolu aktivit aktivuje.

## <a name="rest-api"></a>REST API 
[Rozhraní API služby Azure Monitor aktivity protokolu upozornění](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) je rozhraní REST API. Je plně kompatibilní s rozhraním REST API Azure Resource Manageru. Můžete použít prostřednictvím Powershellu pomocí rutiny Resource Manageru nebo rozhraní příkazového řádku Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Nasazení šablony Resource Manageru pomocí Powershellu
Použití Powershellu k nasazení Ukázková šablona Resource Manageru je znázorněno v předchozí [šablony Azure Resource Manageru](#azure-resource-manager-template) oddílu, použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Pokud sampleActivityLogAlert.parameters.json obsahuje hodnoty poskytnutý pro parametry potřebné pro vytvoření pravidla upozornění.

### <a name="use-activity-log-powershell-cmdlets"></a>Použití protokolu aktivit rutin prostředí PowerShell

Upozornění protokolu aktivit mít vyhrazené rutiny Powershellu, které jsou k dispozici:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Vytvoří nové upozornění protokolu aktivit nebo aktualizuje existující upozornění protokolu aktivit.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Získá jeden nebo více aktivit prostředky upozornění protokolů.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Umožňuje existujícího upozornění protokolu aktivit a nastaví její klíčová slova.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Zakáže existujícího upozornění protokolu aktivit a nastaví její klíčová slova.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Odebere upozornění protokolu aktivit.

## <a name="azure-cli"></a>Azure CLI

Vyhrazené příkazy rozhraní příkazového řádku Azure v rámci sady [upozornění protokolu aktivit monitorování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) jsou dostupné pro správu pravidla upozornění protokolu aktivit.

Pokud chcete vytvořit nové pravidlo upozornění protokolu aktivit, použijte následující příkazy v tomto pořadí:

1. [Vytvoření upozornění protokolu aktivit monitorování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Vytvořte nový prostředek pravidlo upozornění protokolu aktivit.
1. [upozornění oboru protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Přidáte obor pro pravidlo upozornění vytvořené aktivity protokolu.
1. [AZ monitor protokolu aktivit výstrahy – skupina akcí](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Přidání skupiny akcí pro pravidlo upozornění protokolu aktivit.

Pokud chcete načíst jeden prostředek pravidlo upozornění protokolu aktivit, použijte příkaz rozhraní příkazového řádku Azure [az monitor protokolu aktivit upozornění zobrazit](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Chcete-li zobrazit všechny prostředky pravidlo upozornění protokolu aktivit ve skupině prostředků, použijte [seznam výstrah protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Prostředky pravidlo upozornění protokolu aktivit můžete odebrat pomocí příkazového řádku Azure [odstranění upozornění protokolu aktivit az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Další kroky

- Další informace o [schéma webhooku pro protokoly aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Přečtěte si [Přehled protokolů aktivit](../../azure-monitor/platform/activity-log-alerts.md).
- Další informace o [skupiny akcí](../../azure-monitor/platform/action-groups.md).  
- Další informace o [služby oznámení o stavu](../../azure-monitor/platform/service-notifications.md).
