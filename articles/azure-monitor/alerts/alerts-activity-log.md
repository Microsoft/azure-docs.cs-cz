---
title: Vytváření, zobrazování a správa výstrah protokolu aktivit v Azure Monitor
description: Výstrahy protokolu aktivit Vytvářejte pomocí Azure Portal, šablony Azure Resource Manager a Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bb4c1410d046389ae9e82986c6b0ed3d133fcf2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704459"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor  

## <a name="overview"></a>Přehled

Výstrahy protokolu aktivit jsou výstrahy, které se aktivují, když dojde k nové události protokolu aktivit, která odpovídá podmínkám zadaným ve výstraze.

Tyto výstrahy jsou pro prostředky Azure a je možné je vytvořit pomocí šablony Azure Resource Manager. Také je možné je vytvořit, aktualizovat nebo odstranit v Azure Portal. Obvykle vytváříte výstrahy protokolu aktivit pro příjem oznámení, když dojde k určitým změnám prostředků ve vašem předplatném Azure. Výstrahy jsou často vymezené na konkrétní skupiny prostředků nebo prostředky. Například můžete chtít upozornit, když se odstraní libovolný virtuální počítač ve vzorové skupině prostředků **myProductionResourceGroup** . Nebo můžete chtít dostávat oznámení, pokud jsou všechny nové role přiřazené uživateli v rámci vašeho předplatného.

> [!IMPORTANT]
> Výstrahy týkající se oznámení o stavu služby nelze vytvořit prostřednictvím rozhraní pro vytvoření upozornění protokolu aktivit. Další informace o tom, jak vytvořit a používat oznámení o stavu služby, najdete v tématu [příjem výstrah protokolu aktivit pro oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md).

Při vytváření pravidel výstrah zajistěte následující:

- Předplatné v oboru se neliší od předplatného, ve kterém se výstraha vytvořila.
- Tato kritéria musí být kategorie události úroveň, stav, volající, skupina prostředků, ID prostředku nebo typ prostředku, na kterém je výstraha nakonfigurovaná.
- Je povolena pouze jedna podmínka "allOf".
- ' AnyOf ' lze použít k povolení více podmínek více než více polí (například v případě, že se pole "stav" nebo "dílčí stav" rovnají určité hodnotě). Všimněte si, že použití ' AnyOf ' je aktuálně omezené na vytvoření pravidla výstrahy pomocí nasazení šablony ARM.
- ' ContainsAny ' lze použít k povolení více hodnot stejného pole (například pokud "operace" se rovná "Delete" nebo "Modify"). Všimněte si, že použití ' ContainsAny ' je aktuálně omezené na vytvoření pravidla výstrahy pomocí nasazení šablony ARM.
- Když je kategorie "administrativa", musíte zadat alespoň jedno z výše uvedených kritérií v upozornění. Nemůžete vytvořit výstrahu, která se aktivuje při každém vytvoření události v protokolech aktivit.
- Pro události v kategorii výstrahy protokolu aktivit nelze vytvořit výstrahy.

## <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal můžete vytvářet a upravovat pravidla upozornění protokolu aktivit. Prostředí se integruje s protokolem aktivit Azure a zajišťuje tak bezproblémové vytváření výstrah pro konkrétní události, které vás zajímají.

### <a name="create-with-the-azure-portal"></a>Vytvořit pomocí Azure Portal

Použijte následující postup.

1. V Azure Portal vyberte **monitorovat**  >  **výstrahy**.
2. V levém horním rohu okna **výstrahy** vyberte **nové pravidlo výstrahy** .

     ![Nové pravidlo výstrahy](media/alerts-activity-log/AlertsPreviewOption.png)

     Zobrazí se okno **vytvořit pravidlo** .

      ![Nové možnosti pravidla výstrahy](media/alerts-activity-log/create-new-alert-rule-options.png)

3. V části **definovat podmínku výstrahy** zadejte následující informace a vyberte **Hotovo**:

   - **Cíl výstrahy:** Pokud chcete zobrazit a vybrat cíl pro novou výstrahu, použijte filtr **filtrovat podle předplatného**  /  **podle typu prostředku**. Vyberte prostředek nebo skupinu prostředků ze zobrazeného seznamu.

     > [!NOTE]
     > 
     > Pro signál protokolu aktivit můžete vybrat jen [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sledovaný prostředek, skupinu prostředků nebo celé předplatné. 

     **Zobrazení cílové ukázkové výstrahy**

     ![Výběr cíle](media/alerts-activity-log/select-target.png)

   - V části **cílová kritéria** vyberte **Přidat kritéria**. Zobrazí se všechny dostupné signály pro cíl, včetně těch, které se nacházejí v různých kategoriích **protokolu aktivit**. Název kategorie se připojí k názvu **monitorovací služby** .

   - Vyberte signál ze seznamu zobrazených různých operací pro **protokol aktivity** typu.

     Můžete vybrat časovou osu Historie protokolu a odpovídající logiku výstrahy pro tento cílový signál:

     **Obrazovka Přidat kritéria**

     ![Přidat kritéria](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Aby bylo možné mít vysoce kvalitní a efektivní pravidla, požádáme o přidání alespoň jedné další podmínky do pravidel se signálem "All Administrative". 
     > Jako součást definice výstrahy musíte vyplnit jedno z rozevíracích seznamu: "úroveň události", "stav" nebo "iniciováno" a tím, že bude pravidlo konkrétnější.

     - **Čas historie**: události, které jsou k dispozici pro vybranou operaci, se dají vykreslovat za posledních 6, 12 nebo 24 hodin nebo za poslední týden.

     - **Logika výstrahy**:

       - **Úroveň události**: úroveň závažnosti události: _verbose_, _informativní_, _varovná_, _Chyba_ nebo _kritická_.
       - **Stav**: stav události: _spuštěno_, _selhalo_ nebo _proběhlo úspěšně_.
       - **Událost iniciovaná**: taky se označuje jako volající. E-mailová adresa nebo Azure Active Directory identifikátor uživatele, který operaci provedl.

       Tento vzorový graf signálu má použitou logiku výstrahy:

       ![Vybraná kritéria](media/alerts-activity-log/criteria-selected.png)

4. V části **definovat podrobnosti výstrahy** zadejte následující podrobnosti:

    - **Název pravidla výstrahy**: název nového pravidla výstrahy.
    - **Popis**: popis nového pravidla výstrahy.
    - **Uložit upozornění do skupiny prostředků**: vyberte skupinu prostředků, do které chcete toto nové pravidlo Uložit.

5. V části **Skupina akcí** v rozevírací nabídce určete skupinu akcí, kterou chcete přiřadit k tomuto novému pravidlu výstrahy. Případně můžete [vytvořit novou skupinu akcí](./action-groups.md) a přiřadit ji k novému pravidlu. Pokud chcete vytvořit novou skupinu, vyberte **+ Nová skupina**.

6. Chcete-li po vytvoření pravidla povolit, vyberte možnost **Ano** pro **pravidlo Povolit při vytváření** .
7. Vyberte **Vytvořit pravidlo upozornění**.

    Vytvoří se nové pravidlo výstrahy pro protokol aktivit a v pravém horním rohu okna se zobrazí potvrzovací zpráva.

    Pravidlo můžete povolit, zakázat, upravit nebo odstranit. Přečtěte si další informace o tom, jak spravovat pravidla protokolů aktivit.


Jednoduchá analogie pro porozumění podmínkám, na kterých je možné vytvořit pravidla výstrah v protokolu aktivit, je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit v Azure Portal](../essentials/activity-log.md#view-the-activity-log). Na obrazovce **protokolu Azure monitor-Activity** můžete filtrovat nebo najít nezbytnou událost a pak vytvořit výstrahu pomocí tlačítka **Přidat upozornění protokolu aktivit** . Pak postupujte podle kroků 4 až 7, jak je uvedeno výše.
    
 ![Přidat výstrahu z protokolu aktivit](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Zobrazení a správa v Azure Portal

1. V Azure Portal vyberte **monitorovat**  >  **výstrahy**. V levém horním rohu okna vyberte **Spravovat pravidla výstrah** .

    ![Snímek obrazovky s vybraným vyhledávacím polem zobrazí protokol aktivit.](media/alerts-activity-log/manage-alert-rules.png)

    Zobrazí se seznam dostupných pravidel.

2. Vyhledejte pravidlo protokolu aktivit, které chcete upravit.

    ![Vyhledat pravidla upozornění protokolu aktivit](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    K vyhledání pravidla aktivity, které chcete upravit, můžete použít dostupné filtry, _předplatné_, _skupinu prostředků_,  _prostředek_, _typ signálu_ nebo _stav_.

   > [!NOTE]
   > 
   > Můžete upravit pouze **Popis**, **cílové kritérium** a **skupiny akcí**.

3. Vyberte pravidlo a dvakrát klikněte pro úpravu možností pravidla. Proveďte požadované změny a pak vyberte **Uložit**.

   ![Správa pravidel výstrah](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pravidlo můžete povolit, zakázat nebo odstranit. Po výběru pravidla, jak je popsáno v kroku 2, vyberte příslušnou možnost v horní části okna.


## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Chcete-li vytvořit pravidlo upozornění protokolu aktivit pomocí šablony Azure Resource Manager, vytvořte prostředek typu `microsoft.insights/activityLogAlerts` . Pak vyplníte všechny související vlastnosti. Tady je šablona, která vytvoří pravidlo upozornění protokolu aktivit:

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
Předchozí vzorový JSON se dá uložit jako například sampleActivityLogAlert.jspro účely tohoto podrobného návodu a dá se nasadit pomocí [Azure Resource Manager v Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Všimněte si, že je možné definovat výstrahy protokolu aktivit nejvyšší úrovně v rámci předplatného.
  > To znamená, že není k dispozici možnost k definování výstrahy u několika předplatných, proto by měla být tato definice výstraha v rámci předplatného.

Následující pole jsou možnosti, které můžete použít v šabloně Azure Resource Manager pro pole podmínky: Všimněte si, že "Resource Health", "Advisor" a "Service Health" mají pole s dalšími vlastnostmi pro jejich speciální pole. 
1. resourceId: ID prostředku ovlivněného prostředku v události protokolu aktivit, na které má být výstraha vygenerována.
2. Category (kategorie): kategorie v události protokolu aktivit. Například: administrativní, ServiceHealth, ResourceHealth, automatické škálování, zabezpečení, doporučení, zásady.
3. volající: e-mailová adresa nebo Azure Active Directory identifikátor uživatele, který provedl operaci události protokolu aktivit.
4. úroveň: úroveň aktivity v události protokolu aktivit, pro kterou má být výstraha vygenerována. Například: kritická, chyba, upozornění, informativní, verbose.
5. OperationName: název operace v události protokolu aktivit. Příklad: Microsoft. Resources/Deployments/Write
6. Skupina prostředků: název skupiny prostředků pro ovlivněný prostředek v události protokolu aktivit.
7. resourceProvider: [vysvětlení poskytovatelů prostředků a typů Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. stav: řetězec popisující stav operace v události aktivity. Příklad: spuštěno, dokončeno, úspěšné, neúspěšné, aktivní, vyřešené
9. Dílčí stav: obvykle stavový kód HTTP odpovídajícího volání REST, ale může také zahrnovat další řetězce popisující dílčí stav.   Příklad: OK (kód stavu HTTP: 200), Vytvořeno (kód stavu HTTP: 201), přijato (kód stavu HTTP: 202), žádný obsah (kód stavu HTTP: 204), chybný požadavek (kód stavu HTTP: 400), Nenalezeno (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), interní chyba serveru (Stavový kód http: 500), nedostupná služba (Stavový kód http: 503), časový limit brány (kód stavu http: 504).
10. ResourceType: typ prostředku, který byl ovlivněn událostí. Například: Microsoft. Resources/Deployments

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
Další podrobnosti o polích protokolu aktivit najdete [tady](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Může trvat až 5 minut, než se nové pravidlo upozornění protokolu aktivit aktivuje.

## <a name="rest-api"></a>REST API 
[Rozhraní API upozornění protokolu aktivit Azure monitor](/rest/api/monitor/activitylogalerts) je REST API. Je plně kompatibilní s Azure Resource Manager REST API. Dá se použít přes PowerShell pomocí rutiny Správce prostředků nebo Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Nasazení šablony Správce prostředků pomocí prostředí PowerShell
Pokud chcete použít PowerShell k nasazení ukázkové Správce prostředků šablony zobrazené v předchozí části [Azure Resource Manager šablony](#azure-resource-manager-template) , použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

kde sampleActivityLogAlert.parameters.json obsahuje hodnoty, které jsou k dispozici pro parametry potřebné pro vytvoření pravidla výstrahy.

### <a name="use-activity-log-powershell-cmdlets"></a>Použít rutiny prostředí PowerShell pro protokol aktivit

Výstrahy protokolu aktivit mají k dispozici vyhrazené rutiny PowerShellu:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): Vytvoří novou výstrahu protokolu aktivit nebo aktualizuje stávající upozornění protokolu aktivit.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): získá jeden nebo více prostředků upozornění protokolu aktivit.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): povoluje existující upozornění protokolu aktivit a nastavuje jeho značky.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): zakáže existující upozornění protokolu aktivit a nastaví jeho značky.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): Odebere upozornění protokolu aktivit.

## <a name="azure-cli"></a>Azure CLI

Vyhrazené příkazy rozhraní příkazového řádku Azure v rámci nastavení [AZ monitor Activity-log Alert](/cli/azure/monitor/activity-log/alert) jsou k dispozici pro správu pravidel upozornění protokolu aktivit.

Chcete-li vytvořit nové pravidlo upozornění protokolu aktivit, použijte následující příkazy v tomto pořadí:

1. [AZ monitor Activity-log Alert Create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): vytvořit nový prostředek pravidla upozornění protokolu aktivit.
1. [AZ monitor Activity-Scope Alert log](/cli/azure/monitor/activity-log/alert/scope): přidání oboru pro vytvořené pravidlo upozornění protokolu aktivit.
1. [AZ monitor Activity-log Alert Action-Group](/cli/azure/monitor/activity-log/alert/action-group): přidejte skupinu akcí do pravidla upozornění protokolu aktivit.

Pokud chcete načíst jeden prostředek pravidla upozornění protokolu aktivit, použijte příkaz Azure CLI [AZ monitor Activity-log Alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Chcete-li zobrazit všechny prostředky pravidla upozornění protokolu aktivit ve skupině prostředků, použijte příkaz [AZ monitor Activity-log Alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Prostředky pravidla upozornění protokolu aktivit je možné odebrat pomocí příkazu rozhraní příkazového řádku Azure CLI [AZ monitor Activity-log Alert Delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [schématu Webhooku pro protokoly aktivit](./activity-log-alerts-webhook.md).
- Přečtěte si [Přehled protokolů aktivit](./activity-log-alerts.md).
- Přečtěte si další informace o [skupinách akcí](./action-groups.md).  
- Přečtěte si o [oznámeních o stavu služby](../../service-health/service-notifications.md).
