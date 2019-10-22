---
title: Použití analýzy změn aplikace v Azure Monitor k nalezení problémů s webovými aplikacemi | Microsoft Docs
description: Pomocí analýzy změn aplikace v Azure Monitor můžete řešit problémy s aplikacemi na živých webech na Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 3805d7b39c25bcb213a1d4f110161dcd00eb3630
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678250"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Použití analýzy změn aplikace (Preview) v Azure Monitor

Když dojde k problému nebo výpadku živého webu, rychle určit hlavní příčinu je kritická. Standardní řešení monitorování vás můžou upozornit na problém. Můžou dokonce označovat, která součást selhává. Tato výstraha ale nebude vždycky okamžitě vysvětlit příčinu selhání. Věděli jste, že váš web pracoval před pěti minutami a teď je porušený. Co se změnilo za posledních pět minut? Jedná se o otázku, kterou analýza změn aplikace je navržená tak, aby odpovídala v Azure Monitor.

Při sestavování výkonného [grafu prostředků Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)vám analýza změn poskytuje přehledy o změnách aplikací Azure, aby se zvýšila pozorování a snižovala MTTR (což znamená, jak dlouho se má opravit).

> [!IMPORTANT]
> Změna analýzy je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Tato verze se nedoporučuje pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="overview"></a>Přehled

Změna analýzy detekuje různé typy změn, od vrstvy infrastruktury po nasazení aplikace. Jedná se o poskytovatele prostředků Azure na úrovni předplatného, který kontroluje změny prostředků v předplatném. Změna analýz poskytuje data různých diagnostických nástrojů, které uživatelům pomůžou pochopit, jaké změny mohly způsobovat problémy.

Následující diagram znázorňuje architekturu analýzy změn:

![Diagram architektury, jak analýza změn získává data změny a poskytuje je klientským nástrojům](./media/change-analysis/overview.png)

V současné době se analýza změn integruje do **diagnostiky a řešení problémů** v App Service webové aplikaci. Pokud chcete povolit detekci změn a zobrazit změny ve webové aplikaci, přečtěte si část *Analýza změn pro Web Apps funkce* dále v tomto článku.

### <a name="azure-resource-manager-deployment-changes"></a>Změny nasazení Azure Resource Manager

Pomocí [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview)vám analýza změn poskytuje historický záznam o tom, jak se prostředky Azure, které hostují vaši aplikaci, v průběhu času změnily. Změna analýz může například rozpoznat změny v pravidlech konfigurace protokolu IP, spravovaných identit a nastavení SSL. Takže pokud je do webové aplikace přidána značka, změny se projeví v analýze. Tyto informace jsou k dispozici, pokud je poskytovatel prostředků `Microsoft.ChangeAnalysis` v předplatném Azure povolený.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Změny v nasazení a konfiguraci webové aplikace

Změna analýz zachytí stav nasazení a konfigurace aplikace každé 4 hodiny. Může zjistit například změny v proměnných prostředí aplikace. Nástroj počítá rozdíly a prezentuje, co se změnilo. Na rozdíl od Správce prostředků změn nemusí být informace o změně nasazení kódu v nástroji k dispozici okamžitě. Chcete-li zobrazit nejnovější změny v rámci analýzy změn, vyberte **nyní možnost Prohledat změny**.

![Snímek obrazovky s tlačítkem "Vyhledat změny nyní"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislosti

Změny závislostí prostředků mohou také způsobovat problémy ve webové aplikaci. Například pokud webová aplikace volá do mezipaměti Redis, může být SKU Redis Cache ovlivněn výkon webové aplikace. Pokud chcete zjistit změny v závislostech, změňte analýzu na záznam DNS webové aplikace. Tímto způsobem identifikuje změny ve všech součástech aplikace, které by mohly způsobovat problémy.
V současné době jsou podporovány následující závislosti:
- Web Apps
- Azure Storage
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Změna analýzy pro funkci Web Apps

V Azure Monitor je analýza změn v současnosti integrovaná v rámci samoobslužné **diagnostiky a řešení problémů** . K tomuto prostředí se dostanete ze stránky **Přehled** vaší aplikace App Service.

![Snímek obrazovky s tlačítkem "Přehled" a "diagnostikovat a řešit problémy"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Povolení analýzy změn v nástroji Diagnostika a řešení problémů

1. Vyberte **dostupnost a výkon**.

    ![Snímek možností Poradce při potížích s dostupností a výkonem](./media/change-analysis/availability-and-performance.png)

1. Vyberte **změny aplikace**. Tato funkce není k dispozici také při **selhání aplikace**.

   ![Snímek obrazovky s tlačítkem pro zhroucení aplikace](./media/change-analysis/application-changes.png)

1. Pokud chcete povolit analýzu změn, vyberte **Povolit nyní**.

   ![Snímek obrazovky s možnostmi zhroucení aplikací](./media/change-analysis/enable-changeanalysis.png)

1. Zapněte **analýzu změn** a vyberte **Uložit**.

    ![Snímek obrazovky s uživatelským rozhraním povolit analýzu změn](./media/change-analysis/change-analysis-on.png)


1. Chcete-li získat přístup k analýze změn, vyberte možnost **Diagnostika a řešení problémů**  > **dostupnosti a výkonu**  > **selhání aplikace**. Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách:

     ![Snímek obrazovky se zobrazením rozdílů změn](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Povolit škálovatelnou analýzu změn

Pokud vaše předplatné obsahuje mnoho webových aplikací, povolení služby na úrovni webové aplikace bude neefektivní. Spuštěním následujícího skriptu povolte všechny webové aplikace v rámci vašeho předplatného.

Požadavky:
* PowerShell AZ Module. Postupujte podle pokynů v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) .

Spusťte tento skript:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Další kroky

- Povolí Application Insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte Application Insights pro virtuální počítače [Azure a Azure Virtual Machine Scale set pro aplikace hostované službou IIS](azure-vm-vmss-apps.md).
- Přečtěte si další informace o [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview), který pomáhá analyzovat změny napájení.
