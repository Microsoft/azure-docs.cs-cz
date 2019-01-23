---
title: Monitorování využití a odhadované náklady ve službě Azure Monitor
description: Přehled procesu použití Azure monitorování využití a odhadované náklady na stránce
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: c326c255636bac7e698a64044e9ab65c8c05d01c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460715"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorování využití a odhadované náklady

> [!NOTE]
> Tento článek popisuje, jak zobrazit využití a odhadované náklady napříč více monitorovacích funkcí Azure pro různé cenové modely.  Přečtěte si následující články související informace.
> - [Spravovat náklady pomocí řízení objemu dat a uchovávání dat v Log Analytics](../../azure-monitor/platform/manage-cost-storage.md) popisuje, jak řídit své náklady tak, že změníte vaše data uchovávají.
> - [Analýza využití dat v Log Analytics](../../azure-monitor/platform/data-usage.md) popisuje, jak analyzovat a upozornění na data využití.
> - [Správa cen a objemů dat ve službě Application Insights](../../azure-monitor/app/pricing.md) popisuje, jak analyzovat využití dat v Application Insights.

V centru monitorování na portálu Azure Portal **využití a odhadované náklady** stránka vysvětluje použití základní monitorovací funkce, jako [upozornění, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), a [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pro zákazníky na cenové plány, které jsou k dispozici před dubnem 2018 to také zahrnuje využití Log Analytics koupíte přehledy a analýzy nabízejí.

Na této stránce Uživatelé mohou zobrazit jejich využití prostředků za posledních 31 dnů, agregují na jedno předplatné. Procházení se změnami zobrazit trendy využití po dobu 31 dní. Velké množství dat, musí pocházet dohromady pro tento odhad, proto prosím buďte trpěliví jako načtení stránky.

Tento příklad ukazuje monitorování využití a odhad výsledné náklady:

![Využití a odhadované náklady na portálu obrazovka](./media/usage-estimated-costs/001.png)

Vyberte odkaz ve sloupci měsíční využití otevřete graf, který zobrazuje trendy využití za posledních 31 dnů:

![Zahrnuto na uzel panelu snímek obrazovky grafu](./media/usage-estimated-costs/002.png)

Tady je další podobné využití a náklady na souhrn. Tento příklad ukazuje předplatného v novém. dubna 2018 založenou na skutečné spotřebě cenovém modelu. Všimněte si, absenci jakékoli účtování podle uzlů. Příjem dat a jejich uchovávání pro Log Analytics a Application Insights se teď reportovány nový měřič běžné.

![Využití a odhadované náklady na portálu obrazovka – ceny. dubna 2018](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nový cenový model

V dubnu 2018 [monitorování nový cenový model byl uvolněn](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Tato funkce přizpůsobený cloudu, založenou na skutečné spotřebě ceny. Platíte jenom za využité, bez závazků založené na uzlu. Podrobnosti o novém cenovém modelu jsou k dispozici pro [upozornění, metriky, oznámení](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) a [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Nový cenový model pro zákazníky připojení ke službě Log Analytics nebo Application Insights po 2. dubnem 2018, je jedinou možností. Pro zákazníky, kteří již používají tyto služby přechod na nový cenový model je volitelný.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Hodnocení dopadu na nový cenový model
Nový cenový model může mít jiný dopad na jednotlivých zákazníků na základě jejich monitorování způsobů využití. Pro zákazníky, kteří používali před 2. dubnem 2018, Log Analytics nebo Application Insights **využití a odhadované náklady** stránky ve službě Azure Monitor odhady žádné změny v náklady na jejich přesunutí na nový cenový model. Poskytuje způsob, jak přejít u určitého předplatného na nový model. Pro většinu zákazníků bude nejvýhodnější nový cenový model. Pro zákazníky s vzory využití zejména vysoké dat nebo v oblastech vyšší náklady nemusí to být případ.

Chcete-li zobrazit odhad nákladů pro předplatné, které jste vybrali na **využití a odhadované náklady** vyberte modrý banner v horní části stránky. Je vhodné provést tento jeden odběr v době, protože to je úroveň, jakou můžou být přijata nový cenový model.

![Monitorování využití a odhadované náklady na nový cenový model snímku obrazovky](./media/usage-estimated-costs/004.png)

Nová stránka zobrazuje podobné verzi předchozí stránka s zelené hlavičky:

![Monitorování využití a odhadované náklady pro aktuální cenový model – snímek obrazovky](./media/usage-estimated-costs/005.png)

Na stránce také ukazuje různé sady měřiče, které odpovídají na nový cenový model. Tento seznam je příklad:

- Insight and Analytics\Overage za uzel
- Insight and Analytics\Included za uzel
- Nadlimitní Data úrovně Insights\Basic aplikace
- Insights\Included Data aplikací

Nový cenový model nemá přidělení zahrnutých dat založené na uzlu. Proto těchto měřičů ingestování dat jsou sloučeny do nový common data ingestování měřič volá **sdílené Ingestování Services\Data**. 

Existuje jiná změna dat přijatých do Log Analytics nebo v oblastech s vyšší náklady na Application Insights. Data pro tyto oblasti vysoké náklady se zobrazí se nové dílčím měřením. Příkladem je **Ingestování (USA – Středozápad)**.

> [!NOTE]
> Na jedno předplatné odhadované náklady není faktor na úrovni účtu, za uzel oprávnění v Operations Management Suite (OMS) předplatného. Podrobnější diskuzi o novém cenovém modelu najdete v tomto případě vašeho obchodního zástupce.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nový cenový model modelu a nároky na Operations Management Suite předplatné

Zákazníci, kteří si zakoupili Microsoft Operations Management Suite E1 a E2 nárok nároků příjem dat podle uzlu [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) a [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Chcete-li tyto nároky pracovních prostorů Log Analytics a prostředky Application Insights se zobrazí v daném předplatném: 

- Cenový model předplatného musí zůstat v modelu pre dubna 2018.
- Pracovní prostory log Analytics používali "Za uzel (OMS)," cenovou úroveň.
- Prostředky Application Insights používali "Enterprise" cenový plán.

V závislosti na počtu uzlů, které vaše organizace koupila, sady přesunutí některých předplatných na nový cenový model může být výhodné, ale to vyžaduje, aby pečlivě zvážit. Obecně je vhodné jednoduše zůstat v model před dubnem 2018, jak je popsáno výše.

> [!WARNING]
> Pokud vaše organizace zakoupila Microsoft Operations Management Suite E1 a E2, je obvykle nejlepší mít předplatného na cenový model pre dubna 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Změny při přechodu na nový cenový model

Nový cenový model zjednodušuje Log Analytics a Application Insights cenové možnosti pro pouze jednu úroveň (nebo plán). Přesun předplatného na nový cenový model se:

- Změnit cenovou úroveň pro každý Log Analytics na novou úroveň Per GB (označované jako "pergb2018" v Azure Resource Manageru)
- Žádné prostředky Application Insights v plánu Enterprise se změní na základní plán.

Odhad nákladů ukazuje účinky tyto změny.

> [!WARNING]
> Tady důležitá Poznámka: Pokud používáte Azure Resource Manageru a Powershellu k nasazení [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) nebo [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) v rámci předplatného jste přesunuli na nový cenový model. Pokud zadáte cenovou úroveň/plán než "pergb2018" pro Log Analytics nebo "Základní" pro službu Application Insights, spíše než selhání nasazení kvůli určení neplatný cenové úrovně nebo plán, proběhne úspěšně **ale budou používat pouze platné cenové úrovně nebo plán** (neplatí pro Log Analytics zdarma úroveň, kde je generována neplatná zpráva cenová úroveň).
>

## <a name="moving-to-the-new-pricing-model"></a>Přechod na nový cenový model

Pokud jste se rozhodli přijme nový cenový model pro odběr, vyberte **výběr cenového modelu** možnosti v horní části **využití a odhadované náklady** stránky:

![Monitorování využití a odhadované náklady na nový cenový model snímku obrazovky](./media/usage-estimated-costs/006.png)

**Výběr cenového modelu** stránka se otevře. Zobrazí seznam všech předplatných, které jste zobrazili na předchozí stránce:

![Ceny – snímek obrazovky výběru modelu](./media/usage-estimated-costs/007.png)

Chcete-li přejít u určitého předplatného na nový cenový model, stačí vybrat pole a vyberte **Uložit**. Můžete přesunout zpátky na starší cenový model stejným způsobem. Mějte na paměti vlastníkem tohoto předplatného nebo oprávnění přispěvatele je potřeba změnit cenový model.

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizovat přechod na nový cenový model

Následující skripty vyžaduje modul Azure PowerShell. Chcete-li zkontrolovat, pokud máte nejnovější verzi naleznete v tématu [instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.1.0).

Až budete mít nejnovější verzi Azure Powershellu, je nejprve třeba spustit ``Connect-AzureRmAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Pod isGrandFatherableSubscription výsledkem TRUE označuje, že toto předplatné cenový model lze přesunout mezi cenovými modely. Chybějící hodnoty v rámci optedInDate znamená, že toto předplatné je aktuálně nastavený na starý cenový model.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Toto předplatné migrovat na nový cenový model, spusťte:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Potvrďte, že změny se úspěšně znovu spustit:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Pokud byla migrace úspěšná, váš výsledek by měl nyní vypadat:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

OptInDate nyní obsahuje časové razítko z když toto předplatné výslovného souhlasu na nový cenový model.

Pokud je potřeba vrátit zpět na starý cenový model, měli byste spustit:

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Pokud pak znovu spusťte předchozí skript, který má ``-Action listmigrationdate``, měli byste vidět optedInDate prázdnou hodnotu, která byla vrácena vašeho předplatného na starší verzi cenového modelu.

Pokud máte více předplatných, které chcete migrovat, které jsou hostované ve stejném tenantovi můžete například vytvořit vlastní typ variant použití kusů z následujících skriptů:

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzureRMSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Pokud chcete zkontrolovat, jestli jsou všechna předplatná v tenantovi nárok na nový cenový model, můžete spustit:

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

Skript může být kontrast další tak, že vytvoříte skript, který generuje tří polí. Jedno pole bude obsahovat všechny předplatné id, které mají ```isGrandFatherableSubscription``` nastavena na hodnotu True a optedInDate momentálně nemá hodnotu. Druhé pole žádné předplatné aktuálně na nový cenový model. A třetí pole vyplní pouze ID předplatných ve vašem tenantovi, které nemají nárok na nový cenový model:

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
> V závislosti na počtu předplatných, která skript výše může trvat nějakou dobu pro spuštění. Z důvodu použití metody .add() bude okno Powershellu echo narůstající hodnoty jako položky budou přidány do každého pole.

Teď, když máte předplatné rozdělené do tří polí byste měli pečlivě zkontrolovat výsledky. Můžete chtít vytvořit záložní kopii obsah polí tak, aby mohli snadno vrátit změny budete chtít v budoucnu. Pokud jste se rozhodli, byste chtěli převést všechny oprávněné předplatné, které jsou aktuálně podle starého cenového modelu na nový cenový model, který tento úkol je teď možné dosáhnout s:

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
