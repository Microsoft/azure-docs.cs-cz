---
title: Odstranění a obnovení pracovního prostoru Azure Log Analytics | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odstranit pracovní prostor Log Analytics, pokud jste ho vytvořili v osobním předplatném nebo jste restrukturalizovali model pracovního prostoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: ead0ac04fbd2244fce97dd043ebd44f24fb0f67f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054922"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Odstranění a obnovení pracovního prostoru Azure Log Analytics

Tento článek vysvětluje koncept azure log analytics pracovního prostoru obnovitelné odstranění a jak obnovit odstraněný pracovní prostor. 

## <a name="considerations-when-deleting-a-workspace"></a>Důležité informace při odstranění pracovního prostoru

Když odstraníte pracovní prostor Log Analytics, provede se operace obnovitelného odstranění, která umožní obnovení pracovního prostoru včetně jeho dat a připojených agentů do 14 dnů, ať už bylo odstranění náhodné nebo úmyslné. Po období obnovitelného odstranění je prostředek pracovního prostoru a jeho data neobnovitelná – jeho data jsou zařazena do fronty pro trvalé odstranění a zcela vymazána do 30 dnů. Název pracovního prostoru je "uvolněno" a můžete jej použít k vytvoření nového pracovního prostoru.

> [!NOTE]
> Pokud chcete přepsat chování při odstraňování pomocí funkce soft a trvale odstranit pracovní prostor, postupujte podle pokynů v [části Trvalé odstranění pracovního prostoru](#permanent-workspace-delete).

Chcete postupovat opatrně při odstraňování pracovního prostoru, protože mohou existovat důležitá data a konfigurace, které mohou negativně ovlivnit provoz služby. Zkontrolujte, co agenti, řešení a další služby Azure a zdroje, které ukládají svá data v Log Analytics, jako jsou:

* Řešení pro správu
* Azure Automation
* Agenti spuštění na virtuálních počítačích se systémem Windows a Linux
* Agenti spuštěné na počítačích se systémem Windows a Linux ve vašem prostředí
* System Center Operations Manager

Operace obnovitelného odstranění odstraní prostředek pracovního prostoru a všechna oprávnění přidružených uživatelů budou poškozena. Pokud jsou uživatelé přidruženi k jiným pracovním prostorům, mohou pokračovat v používání analýzy protokolů s těmito jinými pracovními prostory.

## <a name="soft-delete-behavior"></a>Chování při slabém odstranění

Operace odstranění pracovního prostoru odebere prostředek Správce prostředků pracovního prostoru, ale jeho konfigurace a data jsou uchovávány po dobu 14 dnů a přidávají vzhled, že pracovní prostor je odstraněn. Všichni agenti a skupiny správy nástroje Operations Manager systémového centra nakonfigurované pro podřízení do pracovního prostoru zůstanou během období obnovitelného odstranění v osamoceném stavu. Služba dále poskytuje mechanismus pro obnovení odstraněného pracovního prostoru, včetně jeho dat a připojených prostředků, v podstatě vrácení odstranění.

> [!NOTE] 
> Nainstalovaná řešení a propojené služby, jako je váš účet Azure Automation, jsou trvale odebrány z pracovního prostoru v době odstranění a nelze je obnovit. Ty by měly být překonfigurovány po operaci obnovení, aby se pracovní prostor přepychoval do dříve nakonfigurovaného stavu.

Pracovní prostor můžete odstranit pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [rozhraní REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)nebo na webu Azure [Portal](https://portal.azure.com).

### <a name="azure-portal"></a>portál Azure

1. Pokud se chcete přihlásit, přejděte na [portál Azure](https://portal.azure.com). 
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor a v horní části prostředního podokna klikněte na **Odstranit.**
   ![Možnost Odstranit z podokna vlastností pracovního prostoru](media/delete-workspace/log-analytics-delete-workspace.png)
4. Po zobrazíse okno potvrzovací zprávy s žádostí o potvrzení odstranění pracovního prostoru, klepněte na tlačítko **Ano**.
   ![Potvrdit odstranění pracovního prostoru](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Trvalé odstranění pracovního prostoru
Metoda obnovitelného odstranění se nemusí vejít do některých scénářů, jako je vývoj a testování, kde je třeba opakovat nasazení se stejným nastavením a názvem pracovního prostoru. V takových případech můžete trvale odstranit pracovní prostor a "přepsat" období obnovitelného odstranění. Trvalá operace odstranění pracovního prostoru uvolní název pracovního prostoru a můžete vytvořit nový pracovní prostor se stejným názvem.


> [!IMPORTANT]
> Trvalé odstranění pracovního prostoru používejte opatrně, protože je nevratná, a nebudete moct obnovit pracovní prostor a jeho data.

Trvalé odstranění pracovního prostoru lze aktuálně provést prostřednictvím rozhraní REST API.

> [!NOTE]
> Každý požadavek rozhraní API musí obsahovat token autorizace nosiče v hlavičce požadavku.
>
> Token můžete získat pomocí:
> - [Registrace aplikací](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Přejděte na portál Azure pomocí vývojářské konzole (F12) v prohlížeči. Podívejte se do jedné z **instancí dávky?** pro ověřovací řetězec v části **Hlavičky požadavku**. To bude ve vzoru *povolení: <token>Nosič *. Zkopírujte a přidejte toto volání rozhraní API, jak je znázorněno v příkladech.
> - Přejděte na web dokumentace Azure REST. stiskněte **Try it** na libovolné rozhraní API, zkopírujte token Nosiče a přidejte ho do volání rozhraní API.
Pokud chcete pracovní prostor trvale odstranit, použijte [pracovní prostory – odstranit]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) volání rozhraní REST API pomocí značky force:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Kde 'eyJ0eXAiOiJKV1Qi...' představuje úplný autorizační token.

## <a name="recover-workspace"></a>Obnovení pracovního prostoru

Pokud máte oprávnění přispěvatele k odběru a skupině prostředků, kde byl pracovní prostor přidružen před operací obnovitelného odstranění, můžete jej obnovit během období obnovitelného odstranění včetně dat, konfigurace a připojených agentů. Po období obnovitelného odstranění je pracovní prostor neobnovitelný a je přiřazen k trvalému odstranění. Názvy odstraněných pracovních prostorů jsou zachovány během období obnovitelného odstranění a nelze je použít při pokusu o vytvoření nového pracovního prostoru.  

Pracovní prostor můžete obnovit opětovným vytvořením pomocí následujících metod vytvoření pracovního prostoru: [Rozhraní PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) nebo [rozhraní REST API,]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) pokud jsou následující vlastnosti naplněny odstraněnými podrobnostmi pracovního prostoru:

* ID předplatného
* Název skupiny prostředků
* Název pracovního prostoru
* Region (Oblast)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Pracovní prostor a všechna jeho data jsou přeneseny zpět po operaci obnovení. Řešení a propojené služby byly při odstranění trvale odebrány z pracovního prostoru a měly by být překonfigurovány tak, aby byl pracovní prostor uveden do dříve nakonfigurovaného stavu. Některá data nemusí být k dispozici pro dotaz po obnovení pracovního prostoru, dokud nejsou přidružená řešení znovu nainstalována a jejich schémata jsou přidána do pracovního prostoru.

> [!NOTE]
> * Obnovení pracovního prostoru není na [webu Azure Portal](https://portal.azure.com)podporováno. 
> * Opětovné vytvoření pracovního prostoru během období slabého odstranění naznačuje, že tento název pracovního prostoru je již používán. 
> 
