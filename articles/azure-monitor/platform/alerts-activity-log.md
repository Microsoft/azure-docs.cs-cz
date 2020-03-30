---
title: Vytváření, zobrazení a správa výstrah protokolu aktivit v Azure Monitoru
description: Vytvářejte výstrahy protokolu aktivit pomocí portálu Azure, šablony Azure Resource Manageru a Azure PowerShellu.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132392"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Vytváření, zobrazení a správa výstrah protokolu aktivit pomocí Azure Monitoru  

## <a name="overview"></a>Přehled

Výstrahy protokolu aktivit jsou výstrahy, které se aktivují, když dojde k nové události protokolu aktivit, která odpovídá podmínkám zadaným ve výstraze.

Tyto výstrahy jsou pro prostředky Azure a lze vytvořit pomocí šablony Azure Resource Manager. Taky je dá vytvořit, aktualizovat nebo odstranit na webu Azure Portal. Obvykle vytvoříte výstrahy protokolu aktivit pro příjem oznámení, když dojde k určitým změnám prostředků ve vašem předplatném Azure. Výstrahy jsou často vymezeny na konkrétní skupiny prostředků nebo prostředky. Můžete například chtít být upozorněni, když je odstraněn libovolný virtuální počítač ve skupině ukázkových prostředků **myProductionResourceGroup.** Nebo můžete chtít dostat upozornění, pokud jsou nějaké nové role přiřazeny uživateli ve vašem předplatném.

> [!IMPORTANT]
> Upozornění na oznámení o stavu služby nelze vytvořit prostřednictvím rozhraní pro vytváření výstrah protokolu aktivit. Další informace o vytváření a používání oznámení o stavu služby najdete v [tématu Příjem upozornění protokolu aktivit na oznámení o stavu služby](alerts-activity-log-service-notifications.md).

Při vytváření pravidel výstrah zajistěte následující:

- Předplatné v oboru se neliší od předplatného, kde je vytvořena výstraha.
- Kritéria musí být úroveň, stav, volající, skupina prostředků, ID prostředku nebo kategorie událostí typu prostředku, ve které je výstraha nakonfigurována.
- Neexistuje žádný "anyOf" podmínku nebo vnořené podmínky v konfiguraci výstrahy JSON. V podstatě pouze jeden "allOf" podmínka je povolena bez dalších "allOf" nebo "anyOf" podmínky.
- Pokud je kategorie "administrativní", je nutné zadat alespoň jedno z předchozích kritérií ve výstraze. Nesmíte vytvořit výstrahu, která se aktivuje při každém vytvoření události v protokolech aktivit.

## <a name="azure-portal"></a>portál Azure

Portál Azure můžete použít k vytvoření a úpravě pravidel výstrah protokolu aktivit. Prostředí je integrované s protokolem aktivit Azure, aby bylo zajištěno bezproblémové vytváření výstrah pro konkrétní události, které vás zajímají.

### <a name="create-with-the-azure-portal"></a>Vytvoření pomocí portálu Azure

Použijte následující postup.

1. Na webu Azure Portal vyberte **Monitor** > **Alerts**.
2. V levém horním rohu okna **Výstrahy** vyberte **Nové pravidlo výstrahy.**

     ![Nové pravidlo výstrahy](media/alerts-activity-log/AlertsPreviewOption.png)

     Zobrazí se okno **Vytvořit pravidlo.**

      ![Nové možnosti pravidla výstrahy](media/alerts-activity-log/create-new-alert-rule-options.png)

3. V části **Definovat výstražnou podmínku**zadejte následující informace a vyberte **Hotovo**:

   - **Cíl výstrahy:** Chcete-li zobrazit a vybrat cíl pro novou výstrahu, použijte **filtr podle předplatného** / **Filtr podle typu prostředku**. Ze zobrazeného seznamu vyberte zdroj nebo skupinu prostředků.

     > [!NOTE]
     > 
     > Můžete vybrat jenom [azure resource manager](../../azure-resource-manager/management/overview.md) sledované prostředky, skupiny prostředků nebo celé předplatné pro signál protokolu aktivit. 

     **Ukázkové zobrazení cíle výstrahy**

     ![Výběr cíle](media/alerts-activity-log/select-target.png)

   - V části **Cílová kritéria**vyberte **Přidat kritéria**. Zobrazí se všechny dostupné signály pro cíl, které zahrnují signály z různých kategorií **protokolu aktivit**. Název kategorie je připojen k názvu **služby Monitor Service.**

   - Vyberte signál ze seznamu zobrazených různých možných operací pro typ **Protokol aktivit**.

     Můžete vybrat časovou osu historie protokolu a odpovídající logiku výstrah pro tento cílový signál:

     **Obrazovka Přidat kritéria**

     ![Přidat kritéria](media/alerts-activity-log/add-criteria.png)

     - **Čas historie**: Události dostupné pro vybranou operaci mohou být vykresleny za posledních 6, 12 nebo 24 hodin nebo za poslední týden.

     - **Logika výstrahy**:

       - **Úroveň události**: Úroveň závažnosti události: _Verbose_, _Informational_, _Warning_, _Error_nebo _Critical_.
       - **Stav**: Stav události: _Spuštěno_, _Nezdařilo se_nebo _Bylo úspěšné_.
       - **Událost iniciovaná**: Označuje se také jako volající. E-mailová adresa nebo identifikátor služby Azure Active Directory uživatele, který operaci provedl.

       Tento ukázkový signální graf má použitou logiku výstrahy:

       ![Vybraná kritéria](media/alerts-activity-log/criteria-selected.png)

4. V části **Definovat podrobnosti výstrahy**uveďte následující podrobnosti:

    - **Název pravidla výstrahy**: Název nového pravidla výstrahy.
    - **Popis**: Popis nového pravidla výstrahy.
    - **Uložit výstrahu do skupiny prostředků**: Vyberte skupinu prostředků, do které chcete toto nové pravidlo uložit.

5. V části **Skupina akcí**v rozevírací nabídce zadejte skupinu akcí, kterou chcete přiřadit k tomuto novému pravidlu výstrahy. Nebo [vytvořte novou skupinu akcí](../../azure-monitor/platform/action-groups.md) a přiřaďte ji k novému pravidlu. Chcete-li vytvořit novou skupinu, vyberte **možnost + Nová skupina**.

6. Chcete-li povolit pravidla po jejich vytvoření, vyberte **možnost Ano** pro možnost **Povolit pravidlo při vytvoření.**
7. Vyberte **Vytvořit pravidlo upozornění**.

    Vytvoří se nové pravidlo výstrahy pro protokol aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.

    Pravidlo můžete povolit, zakázat, upravit nebo odstranit. Přečtěte si další informace o tom, jak spravovat pravidla protokolu aktivit.


Jednoduchá analogie pro pochopení podmínek, na kterých lze vytvořit pravidla výstrah v protokolu aktivit, je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na portálu Azure](activity-log-view.md#azure-portal). Na obrazovce **Azure Monitor – protokol aktivit** můžete filtrovat nebo najít potřebnou událost a potom vytvořit výstrahu pomocí tlačítka Přidat upozornění protokolu **aktivit.** Pak postupujte podle kroků 4 až 7, jak je uvedeno dříve.
    
 ![Přidání výstrahy z protokolu aktivit](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Zobrazení a správa na webu Azure Portal

1. Na webu Azure Portal vyberte **Monitor** > **Alerts**. V levém horním rohu okna vyberte **Spravovat pravidla výstrah.**

    ![Správa pravidel výstrah](media/alerts-activity-log/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidel.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![Hledat pravidla výstrah protokolu aktivit](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Dostupné filtry, _Odběr_, _Skupina prostředků_, _Prostředek_, _Typ signálu_nebo _Stav_můžete použít k vyhledání pravidla aktivity, které chcete upravit.

   > [!NOTE]
   > 
   > Můžete upravovat pouze **popis**, **cílová kritéria**a **skupiny akcí**.

3. Vyberte pravidlo a poklepáním upravte možnosti pravidla. Proveďte požadované změny a pak vyberte **Uložit**.

   ![Správa pravidel výstrah](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pravidlo můžete povolit, zakázat nebo odstranit. Po výběru pravidla, jak je popsáno v kroku 2, vyberte příslušnou možnost v horní části okna.


## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Chcete-li vytvořit pravidlo výstrahprotokolu aktivit pomocí šablony Správce prostředků Azure, vytvořte prostředek typu `microsoft.insights/activityLogAlerts`. Pak vyplníte všechny související vlastnosti. Tady je šablona, která vytvoří pravidlo upozornění protokolu aktivit:

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
Předchozí ukázka JSON lze uložit jako například sampleActivityLogAlert.json pro účely tohoto návodu a lze nasadit pomocí [Správce prostředků Azure na webu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Následující pole jsou možnosti, které můžete použít v šabloně Azure Resource Manager pro pole podmínky: Všimněte si, že "Stav prostředků", "Poradce" a "Stav služby" mají další vlastnosti pole pro jejich zvláštní pole. 
1. resourceId: ID prostředku ovlivněného prostředku v události protokolu aktivit, na které by měla být výstraha generována.
2. kategorie: Kategorie události v protokolu aktivit. Příklad: Správa, ServiceHealth, ResourceHealth, Automatické škálování, Zabezpečení, Doporučení, Zásady.
3. Volající: E-mailová adresa nebo identifikátor služby Azure Active Directory uživatele, který provedl operaci události protokolu aktivit.
4. Level: Úroveň aktivity v události protokolu aktivit, na které by měla být výstraha generována. Například: Kritická, Chyba, Upozornění, Informační, Podrobné.
5. operationName: Název operace v události protokolu aktivit. Příklad: Microsoft.Resources/deployments/write
6. resourceGroup: Název skupiny prostředků pro ovlivněný prostředek v události protokolu aktivit.
7. resourceProvider: [Azure zprostředkovatelé prostředků a typy vysvětlení](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v [tématu zprostředkovatelé prostředků pro služby Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. stav: Řetězec popisující stav operace v události aktivity. Například: Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní, Vyřešeno
9. subStatus: Obvykle stavový kód HTTP odpovídající volání REST, ale může také obsahovat další řetězce popisující dílčí stav.   Například: OK (stavový kód HTTP: 200), Vytvořeno (stavový kód HTTP: 201), Přijato (STAVOVÝ KÓD HTTP: 202), Žádný obsah (stavový kód HTTP: 204), Chybný požadavek (Stavový kód HTTP: 400), Nebyl nalezen (STAVOVÝ KÓD HTTP: 404), Konflikt (Stavový kód HTTP: 409), Interní server Chyba (stavový kód HTTP: 500), Služba není k dispozici (stavový kód HTTP: 503), časový výtok brány (stavový kód HTTP: 504).
10. resourceType: Typ prostředku, který byl ovlivněn událostí. Například: Microsoft.Resources/deployments

Například:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Více informací o polích protokolu aktivit naleznete [zde](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Může trvat až 5 minut, než se nové pravidlo výstrahy protokolu aktivit stane aktivním.

## <a name="rest-api"></a>REST API 
[Rozhraní API výstrah protokolu aktivit monitorování Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) je rozhraní REST API. Je plně kompatibilní s rozhraním REST Azure Resource Manager. Lze použít prostřednictvím prostředí PowerShell pomocí rutiny Správce prostředků nebo Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Nasazení šablony Správce prostředků pomocí PowerShellu
Pokud chcete pomocí PowerShellu nasadit ukázkovou šablonu Správce prostředků zobrazenou v předchozí části [šablony Správce prostředků Azure,](#azure-resource-manager-template) použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

kde sampleActivityLogAlert.parameters.json obsahuje hodnoty uvedené pro parametry potřebné pro vytvoření pravidla výstrahy.

### <a name="use-activity-log-powershell-cmdlets"></a>Použití rutin PowerShellu protokolu aktivit

Upozornění protokolu aktivit mají k dispozici vyhrazené rutiny prostředí PowerShell:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Vytvoří novou výstrahu protokolu aktivit nebo aktualizuje existující výstrahu protokolu aktivit.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Získá jeden nebo více prostředků výstrahprotokolu aktivity.
- [Povolit-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Povolí existující výstrahu protokolu aktivit a nastaví jeho značky.
- [Zakázat-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Zakáže existující výstrahu protokolu aktivit a nastaví jeho značky.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Odebere výstrahu protokolu aktivit.

## <a name="azure-cli"></a>Azure CLI

Vyhrazené příkazy Azure CLI v rámci [nastaveného upozornění protokolu aktivit monitorování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) jsou k dispozici pro správu pravidel výstrah protokolu aktivit.

Chcete-li vytvořit nové pravidlo výstrahprotokolu aktivit, použijte v tomto pořadí následující příkazy:

1. [az monitor aktivita-log upozornění vytvořit](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Vytvořte nový prostředek pravidla výstrahy protokolu aktivit.
1. [az monitor uhlédnout obor výstrahprotokolu aktivity](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Přidejte obor pro pravidlo výstrahy vytvořeného protokolu aktivit.
1. [az sledovat skupinu akcí výstrahprotokolu aktivity](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Přidejte skupinu akcí do pravidla výstrahy protokolu aktivit.

Chcete-li načíst jeden prostředek pravidla výstrahprotokolu protokolu aktivit, použijte příkaz Azure CLI [az monitor výstrahy protokolu aktivity zobrazit](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Chcete-li zobrazit všechny prostředky pravidel výstrahprotokolu protokolu aktivit ve skupině prostředků, použijte [seznam výstrah protokolu aktivit sledování az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Prostředky pravidla výstrahprotokolu protokolu aktivit lze odebrat pomocí příkazu Azure CLI [az monitor výstrahy protokolu aktivit odstranit](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Další kroky

- Další informace o [schématu webhooku pro protokoly aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Přečtěte si [přehled protokolů aktivit](../../azure-monitor/platform/activity-log-alerts.md).
- Přečtěte si další informace o [skupinách akcí](../../azure-monitor/platform/action-groups.md).  
- Další informace o [oznámeních o stavu služby](../../azure-monitor/platform/service-notifications.md).
