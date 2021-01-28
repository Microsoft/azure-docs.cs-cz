---
title: Odstranění a obnovení pracovního prostoru Azure Log Analytics | Microsoft Docs
description: Naučte se, jak odstranit pracovní prostor Log Analytics v případě, že jste ho vytvořili v osobním předplatném nebo změníte uspořádání modelu pracovního prostoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/20/2020
ms.openlocfilehash: 5bb072d0ea710e35b4f741836aed143e962a326e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927699"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Odstranění a obnovení pracovního prostoru Azure Log Analytics

Tento článek vysvětluje koncept obnovitelného odstranění pracovního prostoru Azure Log Analytics a postup obnovení odstraněného pracovního prostoru.

## <a name="considerations-when-deleting-a-workspace"></a>Předpoklady při odstraňování pracovního prostoru

Když odstraníte Log Analytics pracovní prostor, provede se operace obnovitelného odstranění, která umožňuje obnovení pracovního prostoru včetně jeho dat a připojených agentů do 14 dnů, bez ohledu na to, zda bylo odstranění nechtěné nebo úmyslné. Po období obnovitelného odstranění jsou prostředky pracovního prostoru a jeho data neobnovitelná a zařazené do fronty se kompletně vyprázdní do 30 dnů. Název pracovního prostoru je uvolněný a můžete ho použít k vytvoření nového pracovního prostoru.

> [!NOTE]
> Pokud chcete přepsat chování podmíněného odstranění a trvale odstranit pracovní prostor, postupujte podle kroků v části [trvalé odstranění pracovního prostoru](#permanent-workspace-delete).

Při odstraňování pracovního prostoru chcete postupovat opatrně, protože může dojít k důležitým datům a konfiguracím, které by mohly mít negativní dopad na provoz služby. Přečtěte si, co agenti, řešení a další služby Azure ukládají svá data v Log Analytics, například:

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

Pracovní prostor můžete odstranit pomocí [PowerShellu](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace), [REST API](/rest/api/loganalytics/workspaces/delete)nebo v [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Portál Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor a pak v horní části prostředního podokna klikněte na **Odstranit**  .
4. Zobrazí se stránka s potvrzením, která zobrazuje příjem dat do pracovního prostoru za minulý týden. 
5. Pokud chcete trvale odstranit pracovní prostor odebráním možnosti pro pozdější obnovení, zaškrtněte políčko **Odstranit pracovní prostor trvale** .
6. Zadejte název pracovního prostoru, který chcete potvrdit, a pak klikněte na **Odstranit**.

   ![Potvrdit odstranění pracovního prostoru](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Odstranění trvalého pracovního prostoru
Metoda obnovitelného odstranění se nemusí vejít do některých scénářů, jako je například vývoj a testování, kde je třeba opakovat nasazení se stejným nastavením a názvem pracovního prostoru. V takových případech můžete pracovní prostor trvale odstranit a "přepsat" období obnovitelného odstranění. Operace odstranění trvalého pracovního prostoru uvolní název pracovního prostoru a nový pracovní prostor můžete vytvořit pomocí stejného názvu.

> [!IMPORTANT]
> Používejte trvalou operaci odstranění pracovního prostoru s opatrností, protože ji nejde vrátit zpět a nebudete moct obnovit pracovní prostor a jeho data.

Pokud chcete trvale odstranit pracovní prostor pomocí Azure Portal, zaškrtněte políčko **Odstranit pracovní prostor trvale** a potom klikněte na tlačítko **Odstranit** .

Pokud chcete trvale odstranit pracovní prostor pomocí PowerShellu, přidejte značku '-ForceDelete ' k trvalému odstranění pracovního prostoru. Možnost-ForceDelete je aktuálně dostupná pomocí příkazu AZ. OperationalInsights 2.3.0 nebo vyšší. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Obnovit pracovní prostor
Když pracovní prostor odstraníte Log Analytics omylem nebo záměrně, služba ho umístí do stavu obnovitelného odstranění, takže nebude mít přístup k žádné operaci. Název odstraněného pracovního prostoru se zachová během období obnovitelného odstranění a nedá se použít k vytvoření nového pracovního prostoru. Po období obnovitelného odstranění je pracovní prostor neobnovitelný, je naplánován na trvalé odstranění a jeho název, který lze použít k vytvoření nového pracovního prostoru.

Během období obnovitelného odstranění si můžete pracovní prostor obnovit, včetně jeho dat, konfigurace a připojených agentů. Musíte mít oprávnění přispěvatele k předplatnému a skupině prostředků, ve které byl pracovní prostor umístěn před operací obnovitelného odstranění. Obnovení pracovního prostoru se provádí opětovným vytvořením pracovního prostoru Log Analytics s podrobnostmi o odstraněném pracovním prostoru, včetně:

- ID předplatného
- Název skupiny prostředků
- Název pracovního prostoru
- Region (Oblast)

> [!IMPORTANT]
> Pokud se váš pracovní prostor odstranil jako součást operace odstranění skupiny prostředků, musíte nejdřív znovu vytvořit skupinu prostředků.

### <a name="azure-portal"></a>Portál Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**. Zobrazí se seznam pracovních prostorů, které máte ve vybraném oboru.
3. Kliknutím na tlačítko **obnovit** v levém horním rohu otevřete stránku s pracovními prostory ve stavu obnovitelného odstranění, který je možné obnovit.

   ![Snímek obrazovky Log Analytics pracovní prostory v Azure Portal se zvýrazněnou možností obnovit v řádku nabídek.](media/delete-workspace/recover-menu.png)

4. Vyberte pracovní prostor a kliknutím na **obnovit** tento pracovní prostor obnovte.

   ![Snímek obrazovky dialogového okna obnovit odstraněné Log Analytics pracovní prostory v Azure Portal s zvýrazněným pracovním prostorem a vybraným tlačítkem obnovit.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Pracovní prostor a všechna jeho data se po operaci obnovení vrátí zpět. Řešení a propojené služby byly při odstranění trvale odebrány z pracovního prostoru a měly by být překonfigurovány, aby byl pracovní prostor nastaven do dříve nakonfigurovaného stavu. Některá data nemusí být k dispozici pro dotaz po obnovení pracovního prostoru, dokud nebudou přidružená řešení znovu nainstalována a jejich schémata jsou přidána do pracovního prostoru.

## <a name="troubleshooting"></a>Řešení potíží

K odstranění pracovního prostoru musíte mít aspoň *Log Analytics oprávnění přispěvatele* .

* Pokud si nejste jistí, jestli je odstraněný pracovní prostor ve stavu obnovitelného odstranění a že se dá obnovit, klikněte na stránce [otevřít koš](#recover-workspace) na stránce *Log Analytics pracovní prostory* a zobrazte seznam odstraněných pracovních prostorů na předplatné. Trvale odstraněné pracovní prostory nejsou v seznamu zahrnuté.
* Pokud se zobrazí chybová zpráva *Tento název pracovního prostoru se už používá nebo je v* *konfliktu* při vytváření pracovního prostoru, může to být od:
  * Název pracovního prostoru není dostupný a používá ho někdo ve vaší organizaci, nebo jiný zákazník.
  * Pracovní prostor se odstranil za posledních 14 dní a jeho název se zachová rezervovaný pro období obnovitelného odstranění. Chcete-li přepsat obnovitelné odstranění a trvale odstranit pracovní prostor a vytvořit nový pracovní prostor se stejným názvem, postupujte podle následujících kroků a nejprve obnovte pracovní prostor a pak proveďte trvalé odstranění:<br>
    1. [Obnovte](#recover-workspace) pracovní prostor.
    2. [Trvale odstraňte](#permanent-workspace-delete) pracovní prostor.
    3. Vytvoří nový pracovní prostor s použitím stejného názvu pracovního prostoru.
 
      Po úspěšném dokončení volání odstranění na back-endu můžete pracovní prostor obnovit a dokončit operaci trvalého odstranění v jedné z výše navrhovaných metod.

* Pokud obdržíte kód odpovědi 204 s *prostředkem nenalezeným* při odstraňování pracovního prostoru, může dojít k operacím po sobě jdoucích opakovaných pokusech. 204 je prázdná odpověď, což obvykle znamená, že prostředek neexistuje, takže se odstranění dokončilo bez jakéhokoli zásahu.
* Pokud odstraníte skupinu prostředků a váš pracovní prostor, uvidíte stránku odstraněný pracovní prostor na stránce [otevřít koš](#recover-workspace) , ale operace obnovení selže s kódem chyby 404, protože skupina prostředků neexistuje – vytvořte novou skupinu prostředků a zkuste obnovení zopakovat.
