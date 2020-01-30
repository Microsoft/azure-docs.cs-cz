---
title: Odstranění a obnovení pracovního prostoru Azure Log Analytics | Microsoft Docs
description: Naučte se, jak odstranit pracovní prostor Log Analytics v případě, že jste ho vytvořili v osobním předplatném nebo změníte uspořádání modelu pracovního prostoru.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: fabb2524547bd7837d3644d79f0023311ddccdfc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845552"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Odstranění a obnovení pracovního prostoru služby Azure Log Analytics

Tento článek vysvětluje koncept obnovitelného odstranění pracovního prostoru Azure Log Analytics a postup obnovení odstraněného pracovního prostoru. 

## <a name="considerations-when-deleting-a-workspace"></a>Předpoklady při odstraňování pracovního prostoru

Když odstraníte Log Analytics pracovní prostor, provede se operace obnovitelného odstranění, která umožňuje obnovení pracovního prostoru včetně jeho dat a připojených agentů do 14 dnů, bez ohledu na to, zda bylo odstranění nechtěné nebo úmyslné. Po období obnovitelného odstranění jsou prostředky pracovního prostoru a jeho data neobnovitelná – jeho data jsou zařazená do fronty pro trvalé odstranění a kompletně vyprázdněna do 30 dnů. Název pracovního prostoru je uvolněný a můžete ho použít k vytvoření nového pracovního prostoru.

> [!NOTE]
> Pokud chcete přepsat chování podmíněného odstranění a trvale odstranit pracovní prostor, postupujte podle kroků v části [trvalé odstranění pracovního prostoru](#Permanent workspace delete).

Při odstraňování pracovního prostoru chcete postupovat opatrně, protože může dojít k důležitým datům a konfiguracím, které by mohly mít negativní dopad na provoz služby. Přečtěte si, jaké agenty, řešení a další služby a zdroje Azure ukládají svá data v Log Analytics, například:

* Řešení pro správu
* Azure Automation
* Agenti běžící na virtuálních počítačích s Windows a Linux
* Agenti běžící v počítačích se systémem Windows a Linux ve vašem prostředí
* System Center Operations Manager

Operace obnovitelného odstranění odstraní prostředek pracovního prostoru a veškerá oprávnění přidružených uživatelů budou přerušena. Pokud jsou uživatelé přidružení k jiným pracovním prostorům, mohou nadále používat Log Analytics s těmito ostatními pracovními prostory.

## <a name="soft-delete-behavior"></a>Chování obnovitelného odstranění

Operace odstranění pracovního prostoru odstraní Správce prostředků prostředek pracovního prostoru, ale jeho konfigurace a data se uchovávají 14 dnů a zároveň se tím dodrží zobrazení, že se pracovní prostor odstranil. Všichni agenti a System Center Operations Manager skupiny pro správu, které se nakonfigurují pro sestavy do pracovního prostoru, zůstanou během období obnovitelného odstranění v osamoceném stavu. Služba dále poskytuje mechanismus pro obnovování odstraněného pracovního prostoru, včetně jeho dat a připojených prostředků, v podstatě zrušení odstranění.

> [!NOTE] 
> Nainstalovaná řešení a propojené služby, jako je váš účet Azure Automation, se trvale odeberou z pracovního prostoru v době odstranění a nedají se obnovit. Ty by měly být překonfigurovány po operaci obnovení, aby byl pracovní prostor v dříve nakonfigurovaném stavu.

Pracovní prostor můžete odstranit pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)nebo v [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Portál Azure

1. Pokud se chcete přihlásit, otevřete [Azure Portal](https://portal.azure.com). 
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor a pak v horní části prostředního podokna klikněte na **Odstranit** .
   ![možnost odstranit z podokna vlastností pracovního prostoru](media/delete-workspace/log-analytics-delete-workspace.png)
4. Až se zobrazí okno potvrzovací zpráva s výzvou, abyste potvrdili odstranění pracovního prostoru, klikněte na **Ano**.
   ![Potvrdit odstranění pracovního prostoru](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Odstranění trvalého pracovního prostoru
Metoda obnovitelného odstranění se nemusí vejít do některých scénářů, jako je například vývoj a testování, kde je třeba opakovat nasazení se stejným nastavením a názvem pracovního prostoru. V takových případech můžete pracovní prostor trvale odstranit a "přepsat" období obnovitelného odstranění. Operace odstranění trvalého pracovního prostoru uvolní název pracoviště a můžete vytvořit nový pracovní prostor pomocí stejného názvu.


> [!IMPORTANT]
> Při trvalém odstranění pracovního prostoru buďte opatrní, protože operace je nevratná a váš pracovní prostor a jeho data nebudou obnovitelné.

Trvalé odstranění pracovního prostoru lze nyní provést prostřednictvím REST API.

> [!NOTE]
> Jakýkoli požadavek rozhraní API musí v hlavičce požadavku zahrnovat autorizační token nosiče.
>
> Token můžete získat pomocí:
> - [Registrace aplikací](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - V prohlížeči přejděte na Azure Portal pomocí konzoly pro vývojáře (F12). Podívejte se do jedné z **dávek?** instance pro řetězec ověřování v části **záhlaví požadavků**. Tato akce se bude nacházet v rámci *autorizace vzoru: nosiče <token>* . Zkopírujte a přidejte to do volání rozhraní API, jak je znázorněno v příkladech.
> - Přejděte na stránku dokumentace k Azure REST. stiskněte **vyzkoušet** na jakémkoli rozhraní API, zkopírujte token nosiče a přidejte ho do volání rozhraní API.
Pokud chcete trvale odstranit pracovní prostor, použijte [pracovní prostory – odstranit]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) volání rozhraní REST API pomocí značky Force:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```

## <a name="recover-workspace"></a>Obnovit pracovní prostor

Pokud máte oprávnění přispěvatele u předplatného a skupiny prostředků, ve které byl pracovní prostor přidružený před operací obnovitelného odstranění, můžete ho obnovit během období obnovitelného odstranění, včetně jeho dat, konfigurace a připojených agentů. Po období obnovitelného odstranění je pracovní prostor neobnovitelná a přiřazený k trvalému odstranění. Názvy odstraněných pracovních prostorů se během období obnovitelného odstranění uchovávají a nedají se použít při pokusu o vytvoření nového pracovního prostoru.  

Pracovní prostor můžete obnovit tak, že ho znovu vytvoříte pomocí následujících metod Create v pracovním prostoru: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) nebo [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , pokud jsou vyplněny následující vlastnosti s podrobnostmi o odstraněných pracovních prostorů:

* ID předplatného
* Název skupiny prostředků
* Název pracovního prostoru
* Region (Oblast)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Pracovní prostor a všechna jeho data se po operaci obnovení vrátí zpět. Řešení a propojené služby byly při odstranění trvale odebrány z pracovního prostoru a měly by být překonfigurovány, aby byl pracovní prostor nastaven do dříve nakonfigurovaného stavu. Některá data nemusí být k dispozici pro dotaz po obnovení pracovního prostoru, dokud nebudou přidružená řešení znovu nainstalována a jejich schémata jsou přidána do pracovního prostoru.

> [!NOTE]
> * Obnovení pracovního prostoru není v [Azure Portal](https://portal.azure.com)podporováno. 
> * Po opětovném vytvoření pracovního prostoru během období obnovitelného odstranění se zobrazí informace o tom, že tento název pracovního prostoru se už používá. 
> 
