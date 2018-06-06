---
title: Sledování využití a odhadované náklady v Azure monitorování | Microsoft Docs
description: Přehled procesu používání Azure sledování využití a odhadované náklady na stránku
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: 4e6b3a2e8769c6e7e93071aed27b81c87ae336ca
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715553"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Sledování využití a odhadované náklady

V centru monitorování na portálu Azure **využití a odhadované náklady** stránka vysvětluje použití základní monitorovací funkce, jako například [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky na cenovou plány, které jsou k dispozici před 2018 duben to také zahrnuje zakoupené prostřednictvím k přehledům využití analýzy protokolů a nabízet Analytics.

Na této stránce mohou uživatelé zobrazit jejich využití prostředků za posledních 31 dní agregovat jedno předplatné. Rozbalení in zobrazit trendy využití po dobu 31 dnů. Velké množství dat je potřeba použít současně pro tento odhad, proto buďte trpěliví jako načtení stránky.

Tento příklad ukazuje odhad příslušné náklady a sledování využití:

![Snímek obrazovky portálu odhadované náklady a využití](./media/monitoring-usage-and-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití otevřete graf, který zobrazuje trendy využití za posledních 31 dnů období:

![Zahrnuto na uzel panelu grafu – snímek obrazovky](./media/monitoring-usage-and-estimated-costs/002.png)

Tady je jiné podobné využití a souhrn náklady. Tento příklad ukazuje předplatné v nové duben 2018 na základě spotřeby cenový model. Poznámka: nedostatek žádné fakturaci na základě uzlu. Přijímání dat a uchování pro analýzy protokolů a Application Insights jsou nyní ohlášeny nové běžné měření.

![Využití a odhadované náklady na portálu obrazovka – duben 2018 ceny](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nový cenový model

V dubnu 2018 [nové monitorování cenový model byl vydán](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Tato funkce cloud zařízení, na základě spotřeby ceny. Platíte jenom pro co používáte, aniž by na základě uzlu závazky. Podrobnosti o nový cenový model jsou k dispozici pro [výstrahy, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [analýzy protokolů](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Pro zákazníky registrace analýzy protokolů nebo Application Insights po 2. dubna 2018 nový cenový model je jedinou možností. Pro zákazníky, kteří už používají tyto služby se přesouvat na nový cenový model volitelné.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Hodnocení dopadu nový cenový model
Nový cenový model může mít různé dopady na každého zákazníka a to na základě jejich monitorování způsobů využití. Pro zákazníky, kteří používali před 2. dubna 2018, analýzy protokolů nebo Application Insights **využití a odhadované náklady** stránky v Azure monitorování Odhadne všechny změny v nákladech, pokud se přesouvají do nový cenový model. Poskytuje způsob, jak přesunout předplatné do nového modelu. Pro většinu zákazníků bude výhodné nový cenový model. Pro zákazníky s vzorce používání zejména vysoké dat nebo v oblastech vyšší náklady nemusí to být případ.

Zobrazíte odhad náklady pro odběry, které jste zvolili na **využití a odhadované náklady** vyberte blue hlavičky v horní části stránky. Je vhodné provést tento jeden odběr současně, protože se jedná o úrovni, na které můžete používat nový cenový model.

![Sledování využití a odhadované náklady na nový snímek obrazovky cenovou modelu](./media/monitoring-usage-and-estimated-costs/004.png)

Nová stránka zobrazuje podobné verzi na předchozí stránku s zelená banner:

![Sledování využití a odhadované náklady na aktuální cenová snímku obrazovky modelu](./media/monitoring-usage-and-estimated-costs/005.png)

Stránce také ukazuje jinou sadu měřidla, které odpovídají nový cenový model. Tento seznam je příklad:

- Přehledy a Analytics\Overage na uzel
- Přehledy a Analytics\Included na uzel
- Nadlimitní Data Insights\Basic aplikací
- Insights\Included Data aplikací

Nový cenový model nemá přidělení zahrnuté dat na základě uzlu. Proto tato měřidla přijímání dat spojují se do nové běžné data přijímání měřidla názvem **sdílené přijímání Services\Data**. 

Existuje jiné změny dat nasávaného analýzy protokolů nebo Application Insights v oblastech s vyšší náklady. Data pro tyto oblasti vysokou náklady budou zobrazeny s novou místní měřidla. Příkladem je **přijímání dat (USA – západ centrální)**.

> [!NOTE]
> Na jedno předplatné odhadované náklady není zohlednit na úrovni účtu na uzlu oprávnění předplatného Operations Management Suite (OMS). V takovém případě najdete vaším zástupcem účet podrobné informace o nový cenový model.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nové ceny modelu a oprávnění předplatné služby Operations Management Suite

Zákazníci, kteří si zakoupili služby Microsoft Operations Management Suite E1 a E2 jsou způsobilé pro oprávnění přijímání dat podle uzlu pro [analýzy protokolů](https://www.microsoft.com/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Chcete-li zobrazit tato oprávnění pro analýzy protokolů pracovních prostorů nebo prostředkům Application Insights v daném předplatném: 

- Cenový model odběru musí zůstat v předběžné – duben 2018 modelu.
- Log Analytics pracovních prostorů měli uzel můžete použít "za-(OMS)" cenová úroveň.
- Application Insights prostředky měli používat "Podniku" ceny plánu.

V závislosti na počtu uzlů sady, které vaše organizace koupila přesunutí některých odběrů nový cenový model může být výhodné, ale vyžaduje pečlivě zvážit. Obecně se doporučuje jednoduše zůstat v modelu 2018 pre duben, jak je popsáno výše.

> [!WARNING]
> Pokud vaše organizace koupila E1 Microsoft Operations Management Suite a E2, obvykle je nejvhodnější mějte cenový model 2018 pre duben vašich předplatných. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Změny při přesunování na nový cenový model

Nový cenový model zjednodušuje analýzy protokolů a Application Insights ceny možností pouze jednu úroveň (nebo plánu). Přesun do nového cenovou modelu bude předplatné:

- Změnit cenovou úroveň pro každý analýzy protokolů na nové vrstvě za GB (nazývané "pergb2018" ve službě Správce prostředků Azure)
- Všechny prostředky Application Insights v plánu podnikového se změní na základní plán.

Odhad nákladů ukazuje důsledky tyto změny.

> [!WARNING]
> Zde důležitá Poznámka: Pokud používáte k nasazení Azure Resource Manager nebo prostředí PowerShell [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) nebo [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) v předplatném byl přesunut do nového cenový model. Pokud zadáte cenovou úroveň nebo plán než "pergb2018" pro analýzy protokolů nebo "Základní" pro službu Application Insights, místo selhání nasazení kvůli určení neplatný cenová úroveň nebo plán, proběhne úspěšně **, ale bude používat pouze platné cenová úroveň nebo plán** (nevztahuje na úroveň protokolu analýzy Free nichž se vyvolá neplatná zpráva cenové úrovně).
>

## <a name="moving-to-the-new-pricing-model"></a>Přesunutí na nový cenový model

Pokud jste se rozhodli použít nový cenový model pro předplatné, vyberte **ceny Výběr modelu** možnost v horní části **využití a odhadované náklady** stránky:

![Sledování využití a odhadované náklady na nový snímek obrazovky cenovou modelu](./media/monitoring-usage-and-estimated-costs/006.png)

**Ceny Výběr modelu** otevře se stránka. Zobrazuje seznam všech předplatných, které jste si zobrazili na předchozí stránce:

![Snímek obrazovky výběru modelu – ceny](./media/monitoring-usage-and-estimated-costs/007.png)

Chcete-li přesunout předplatné nový cenový model, stačí vybrat pole a pak vyberte **Uložit**. Je můžete přesunout zpět do starší cenový model stejným způsobem. Mějte na paměti tento vlastník předplatného nebo chcete-li změnit model tvorby cen jsou potřeba oprávnění přispěvatele.

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizovat přesunu na nový cenový model

Skripty níže vyžadují modul Azure PowerShell. Chcete-li zkontrolovat, jestli máte nejnovější verzi najdete v části [modul nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.1.0).

Jakmile máte nejnovější verzi prostředí Azure PowerShell, byste nejprve musíte spustit ``Connect-AzureRmAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

V části isGrandFatherableSubscription výsledku TRUE označuje, že cenový model tento odběr je možné přesouvat mezi cenových úrovních. Chybí hodnota v části optedInDate znamená, že toto předplatné je nastaveno na staré cenový model.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

K migraci na nový cenový model spustit tento odběr:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Potvrďte, že změna byl úspěšně znovu spusťte:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Pokud byla migrace úspěšná, vaše výsledek by měl nyní vypadat jako:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

OptInDate nyní obsahuje časové razítko když toto předplatné přihlášených k nový cenový model.

Pokud budete potřebovat vrátit se zpátky do původního cenový model, spustili byste:

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Pokud pak znovu spusťte předchozí skript, který má ``-Action listmigrationdate``, měli byste vidět prázdný optedInDate hodnotu označující, opět vaše předplatné starší verze cenový model.

Pokud máte více předplatných, které chcete migrovat, které jsou hostované na stejném tenantovi můžete vytvořit vlastní typ variant pomocí součásti z následujících skriptů:

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzureRMSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Chcete-li zkontrolovat, zda všechny odběry ve vašem klientovi jsou vhodné pro nový cenový model, můžete spustit:

```PowerShell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

Skript může být přesnějších další vytvořením skriptu, který generuje tři pole. Jedno pole bude obsahovat všechny id předplatného na které mají ```isGrandFatherableSubscription``` nastavit na hodnotu True a optedInDate aktuálně nemá hodnotu. Druhé pole žádné předplatné aktuálně na nový cenový model. A třetí pole pouze naplněný ID předplatného v klientovi, které nejsou vhodné pro nový cenový model:

```PowerShell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> V závislosti na počtu předplatných skript výše může trvat nějakou dobu spuštění. Kvůli použití metody .add() bude okno prostředí PowerShell echo narůstajícími hodnoty jako položky budou přidány do každého pole.

Teď, když máte předplatné rozdělené do tří polí byste měli pečlivě zkontrolovat výsledky. Chcete vytvořit záložní kopii obsah pole, abyste mohli snadno vrátit změny bude nutné v budoucnu. Pokud jste se rozhodli, kdybyste chtěli převést veškeré oprávněné předplatné, které jsou aktuálně na staré cenový model na nový cenový model, který tato úloha je nyní možné dosáhnout s:

```PowerShell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```