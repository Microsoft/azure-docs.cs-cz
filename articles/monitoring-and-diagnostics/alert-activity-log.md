---
title: Vytvoření, zobrazení a Správa upozornění protokolu aktivit ve službě Azure Monitor
description: Postup vytvoření upozornění protokolu aktivit z webu Azure Portal, šablon prostředků a prostředí PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 526c50fa4d261a30738c3f24d537fe5e0d765f6d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951300"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Vytvoření, zobrazení a správa pomocí Azure monitoru upozornění protokolu aktivit  

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivovat při výskytu nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze.

Tyto výstrahy jsou pro prostředky Azure, můžete vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Obvykle je vytvoření upozornění protokolu aktivit pro příjem oznámení, když dojde k určité změny na prostředky ve vašem předplatném Azure, často obor skupiny určitého prostředku nebo prostředku. Například můžete chtít upozorněni, když některý virtuální počítač (ukázkové skupiny prostředků) **myProductionResourceGroup** je odstraněn, nebo můžete chtít získat vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.

> [!IMPORTANT]
> Upozornění na stav služby notification nelze vytvořit pomocí rozhraní pro vytvoření upozornění protokolu aktivit. Další informace o vytváření a používání oznámení o stavu služby, najdete v článku [dostávat upozornění protokolu aktivit na oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="manage-alert-rules-for-activity-log-using-azure-portal"></a>Spravovat pravidla upozornění protokolu aktivit pomocí webu Azure portal

> [!NOTE]

>  Při vytváření pravidel upozornění, zkontrolujte následující:

> - Předplatné v oboru není liší od předplatného, kde se vytvoří výstraha.
- Kritéria musí být úroveň/status/volající / skupiny prostředků nebo id prostředku / typ prostředku / kategorie událostí, ve které je nakonfigurována oznámení.
- Není žádný "anyOf" podmínky nebo vnořené podmínky v konfiguraci výstrahy JSON (v podstatě u žádné další allOf/anyOf je povoleno pouze jeden allOf).
- Pokud je kategorie "správce". Musíte zadat alespoň jeden z předchozích kritéria v upozornění. Nelze vytvořit výstrahu, která se aktivuje vždy, když se vytvoří událost v protokolech aktivit.

### <a name="create-an-alert-rule-for-an-activity-log-using-azure-portal"></a>Vytvoření pravidla upozornění protokolu aktivit pomocí webu Azure portal

Použijte následující postup:

1. Z webu Azure portal, vyberte **monitorování** > **výstrahy**
2. Klikněte na tlačítko **nové pravidlo upozornění** v horní části **výstrahy** okna.

     ![Nové pravidlo upozornění](./media/monitor-alerts-unified/AlertsPreviewOption.png)

     **Vytvořit pravidlo** zobrazí se okno.

      ![nové možnosti pravidlo upozornění](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **V části definovat výstražný stav** zadejte následující informace a klikněte na tlačítko **provádí**.

    - **Cíl upozornění:** k zobrazení a vyberte cíl pro nové výstrahy, použijte **filtrovat podle předplatného** / **filtrovat podle typu prostředku** a vyberte prostředek nebo skupinu prostředků z Zobrazí seznam.

    > [!NOTE]

    > můžete vybrat prostředek, skupinu prostředků nebo celé předplatné pro signálu protokolu aktivit.

    **Cíl ukázkové zobrazení výstrahy** ![vyberte cíl](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - V části **cílová kritéria**, klikněte na tlačítko **přidat kritéria** a jsou zobrazeny všechny dostupné signály pro cíl, včetně těch, které z různých kategorií **protokolu aktivit**; nebyla úspěšná Název kategorie připojí v **Monitor Service** název.

    - Vyberte signál, který se v seznamu zobrazí různých operací možné pro daný typ **protokolu aktivit**.

    Můžete vybrat na časové ose historie protokolu a odpovídající logika upozornění pro tento cíl signál:

    **Přidání obrazovky kritéria**

    ![Přidat kritéria](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Historie čas**: událostí, které jsou k dispozici pro vybranou operaci je mohou být vykreslena za posledních 6/12/24 hodin (nebo) za poslední týden.

    **Upozornění logiky**:

     - **Úroveň události**– úroveň závažnosti události. _Podrobné_, _informační_, _upozornění_, _chyba_, nebo _kritické_.
     - **Stav**: stav události. _Spuštění_, _nepovedlo_, nebo _úspěšné_.
     - **Zahájit událost**: označuje se taky jako volajícího E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

        Ukázkový graf signál s logika upozornění použít:

        ![ kritéria vybraná](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. V části **definujte podrobnosti pravidla upozornění**, zadejte následující podrobnosti:

    - **Název pravidla upozornění** – název nové pravidlo upozornění
    - **Popis** – popis pro nové pravidlo upozornění
    - **Uložit upozornění do skupiny prostředků** – vyberte skupinu prostředků, ve které chcete uložit toto nové pravidlo.

5. V části **skupiny akcí**, z rozevírací nabídky, určete skupinu akcí, které chcete přiřadit k této nové pravidlo upozornění. Alternativně [vytvořit novou skupinu akcí](monitoring-action-groups.md) a přiřadit nové pravidlo. Chcete-li vytvořit novou skupinu, klikněte na tlačítko **+ nová skupina**.

6. Po vytvoření povolit pravidla, klikněte na tlačítko **Ano** pro **Povolit pravidlo po vytvoření** možnost.
7. Klikněte na tlačítko **vytvořit pravidlo upozornění**.

    Vytvořit nové pravidlo upozornění protokolu aktivit a potvrzovací zpráva se zobrazí v horní části napravo od okna.

    Můžete povolit, zakázat, úprava nebo odstranění pravidla. [Další informace](#view-and-manage-activity-log-alert-rules-in-azure-portal) o správu pravidel protokolu aktivit.


Alternativně je jednoduchý přirovnání pro vysvětlení podmínky, na kterých se dají vytvořit pravidla upozornění na protokol aktivit, prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure portal](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). Ve službě Azure Monitor – protokol aktivit, jeden můžete filtrovat nebo najít potřebné události a pak vytvořte výstrahu pomocí **přidat upozornění protokolu aktivit** tlačítko; potom postupujte podle kroků 4 a vyšší jak je uvedeno v kurzu výše.
    
 ![ Přidat upozornění protokolu aktivit](./media/monitoring-activity-log-alerts-new-experience/add-activity-log.png)
    

### <a name="view-and-manage-activity-log-alert-rules-in-azure-portal"></a>Umožňuje zobrazit a spravovat pravidla upozornění protokolu aktivit na webu Azure portal

1. Z webu Azure portal, klikněte na tlačítko **monitorování** > **výstrahy** a klikněte na tlačítko **spravovat pravidla** v levém horním rohu okna.

    ![ Spravovat pravidla výstrah](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidla.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![ Hledat pravidla upozornění protokolu aktivit](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Můžete použít k dispozici tyto filtry – _předplatné_, _skupiny prostředků_, _prostředků_, _typ signálu_, nebo _stav_  najít pravidlo aktivit, které chcete upravit.

    > [!NOTE]

    > Můžete upravit pouze **popis** , **cílová kritéria** a **skupiny akcí**.

3.  Vyberte pravidlo a dvakrát klikněte pro úpravu možností pravidla. Proveďte požadované změny a pak klikněte na tlačítko **Uložit**.

    ![ Spravovat pravidla výstrah](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Zakázání, povolení nebo odstranění pravidla. Vyberte příslušnou možnost v horní části okna, po výběru pravidla, jak je uvedeno v kroku 2.


## <a name="manage-alert-rules-for-activity-log-using-azure-resource-template"></a>Spravovat pravidla upozornění protokolu aktivit pomocí šablony Azure Resource
Pokud chcete vytvořit upozornění protokolu aktivit s využitím šablony Resource Manageru, vytvoříte prostředek typu `microsoft.insights/activityLogAlerts`. Potom vyplňte všechny související vlastnosti. Zde je šablona, která vytvoří upozornění protokolu aktivit.

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
Výše uvedené ukázky json se dají uložit jako (Řekněme) sampleActivityLogAlert.json pro účely Tento názorný postup vás provede a je možné nasadit s použitím [Azure Resource Manageru na webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Může trvat až 5 minut nové pravidlo upozornění protokolu aktivit aktivuje

## <a name="manage-alert-rules-for-activity-log-using-powershell-cli-or-api"></a>Spravovat pravidla upozornění protokolu aktivit pomocí Powershellu, rozhraní příkazového řádku nebo rozhraní API
[Azure Monitor – rozhraní API upozornění protokolu aktivit](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) je rozhraní REST API a plně kompatibilní s rozhraním REST API Azure Resource Manageru. Proto může sloužit prostřednictvím Powershellu pomocí rutiny Resource Manageru a Azure CLI.

Znázorněno níže využití přes rutiny Powershellu pro Azure Resource Manager pro ukázku výše uvedenou šablonu prostředků (sampleActivityLogAlert.json) [oddíl prostředků šablony](#manage-alert-rules-for-activity-log-using-azure-resource-template) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Ve které sampleActivityLogAlert.parameters.json má hodnoty poskytnutý pro parametry potřebné pro vytvoření pravidla upozornění.

Znázorněno níže použití pomocí příkazu Azure Resource Manageru v Azure CLI v ukázce výše uvedenou šablonu prostředků (sampleActivityLogAlert.json) [oddíl prostředků šablony](#manage-alert-rules-for-activity-log-using-azure-resource-template) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
Ve které sampleActivityLogAlert.parameters.json má hodnoty poskytnutý pro parametry potřebné pro vytvoření pravidla upozornění.


## <a name="next-steps"></a>Další postup

- [Schéma Webhooku pro protokoly aktivit](monitoring-activity-log-alerts-webhook.md)
- [Přehled protokolů aktivit](monitoring-activity-log-alerts.md) 
- Další informace o [skupiny akcí](monitoring-action-groups.md).  
- Další informace o [služby oznámení o stavu](monitoring-service-notifications.md).
