---
title: Odstranění a obnovení pracovního prostoru Azure Log Analytics | Microsoft Docs
description: Naučte se, jak odstranit pracovní prostor Log Analytics v případě, že jste ho vytvořili v osobním předplatném nebo změníte uspořádání modelu pracovního prostoru.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: magoedte
ms.openlocfilehash: 4f03fc71a11c1ecb2e96b316efac9249395fc333
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285537"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Odstranění a obnovení pracovního prostoru služby Azure Log Analytics
Tento článek vysvětluje koncept obnovitelného odstranění pracovního prostoru Azure Log Analytics a postup obnovení odstraněného pracovního prostoru. 

## <a name="considerations-when-deleting-a-workspace"></a>Předpoklady při odstraňování pracovního prostoru
Když odstraníte Log Analytics pracovní prostor, provede se operace obnovitelného odstranění, která umožňuje obnovení pracovního prostoru včetně jeho dat a připojených agentů do 14 dnů, bez ohledu na to, zda bylo odstranění nechtěné nebo úmyslné. Po období obnovitelného odstranění jsou pracovní prostor a jeho data neobnovitelná a jsou zařazeny do fronty k trvalému odstranění do 30 dnů.

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
> Nainstalovaná řešení a propojené služby, jako je účet Automation, se trvale odeberou z pracovního prostoru v době odstranění a nejde je obnovit. Ty by měly být překonfigurovány poté, co operace obnovení přenese pracovní prostor do předchozí funkce. 

Pracovní prostor můžete odstranit pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)nebo v [Azure Portal](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Odstranit pracovní prostor v Azure Portal
1. Pokud se chcete přihlásit, otevřete [Azure Portal](https://portal.azure.com). 
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor a pak v horní části prostředního podokna klikněte na **Odstranit** .
   @no__t – možnost 0Delete z podokna vlastností pracovního prostoru @ no__t-1
4. Až se zobrazí okno potvrzovací zpráva s výzvou, abyste potvrdili odstranění pracovního prostoru, klikněte na **Ano**.
   @no__t 0Confirm odstranění pracovního prostoru @ no__t-1

## <a name="recover-workspace"></a>Obnovit pracovní prostor
Pokud máte oprávnění přispěvatele k předplatnému a skupině prostředků, ke které byl pracovní prostor přidružen před operací obnovitelného odstranění, můžete ho obnovit během období obnovitelného odstranění, včetně jeho dat, konfigurace a připojených agentů. Po období obnovitelného odstranění je pracovní prostor neobnovitelná a přiřazený k trvalému odstranění.

Pracovní prostor můžete obnovit opětovným vytvořením pracovního prostoru pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) nebo [REST APIch]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) metod Create v pracovním prostoru, pokud se tyto vlastnosti naplní podrobnostmi o odstraněných pracovních prostorech, včetně:
1.  ID předplatného
2.  Název skupiny prostředků
3.  Název pracovního prostoru
4.  Oblast

> [!NOTE]
> * Obnovení pracovního prostoru není v [Azure Portal](https://portal.azure.com)podporováno. Po opětovném vytvoření pracovního prostoru během období obnovitelného odstranění se zobrazí informace o tom, že tento název pracovního prostoru se už používá.
> * Názvy odstraněných pracovních prostorů se uchovávají pro dobu obnovitelného odstranění a nedají se použít při vytváření nového pracovního prostoru.
