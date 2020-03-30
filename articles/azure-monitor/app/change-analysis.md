---
title: Hledání problémů s webovými aplikacemi pomocí analýzy změn aplikací v Azure Monitoru | Dokumenty společnosti Microsoft
description: Pomocí analýzy změn aplikací v Azure Monitoru můžete řešit problémy s aplikacemi na živých webech ve službě Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348735"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Použití analýzy změn aplikací (preview) v Azure Monitoru

Dojde-li k problému nebo výpadku živého webu, je důležité rychle určit hlavní příčinu. Standardní řešení monitorování vás mohou upozornit na problém. Mohou dokonce indikují, která součást selhává. Ale toto upozornění nebude vždy okamžitě vysvětlit příčinu selhání. Víte, že vaše stránky fungovaly před pěti minutami a teď jsou rozbité. Co se změnilo za posledních pět minut? To je otázka, že analýza změn aplikací je navržen tak, aby odpověď v Azure Monitor.

Na základě výkonu [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), Change Analysis poskytuje přehled o změnách vašich aplikací Azure pro zvýšení pozorovatelnosti a snížení MTTR (střední doba na opravu).

> [!IMPORTANT]
> Analýza změn je aktuálně ve verzi Preview. Tato verze preview je k dispozici bez smlouvy o úrovni služeb. Tato verze se nedoporučuje pro produkční úlohy. Některé funkce nemusí být podporovány nebo mohou mít omezené možnosti. Další informace najdete v [tématu Doplňkové podmínky použití pro microsoft azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Přehled

Analýza změn detekuje různé typy změn, od vrstvy infrastruktury až po nasazení aplikace. Je to poskytovatel prostředků Azure na úrovni předplatného, který kontroluje změny prostředků v předplatném. Analýza změn poskytuje data pro různé diagnostické nástroje, které uživatelům pomáhají pochopit, jaké změny mohly způsobit problémy.

Následující diagram znázorňuje architekturu analýzy změn:

![Diagram architektury, jak analýza změn získává data změn a poskytuje je klientským nástrojům](./media/change-analysis/overview.png)

V současné době change analysis je integrovaná do **diagnostikovat a řešit problémy** zkušenosti ve webové aplikaci App Service, stejně jako k dispozici jako samostatná karta na webu Azure Portal.
Podívejte *se na zobrazení změny pro všechny prostředky v části Azure* pro přístup k změnit analýzu okno a změnit *analýzu pro webové aplikace funkce* části pro použití v rámci portálu Webové aplikace dále v tomto článku.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Změny sledovaných vlastností Azure Resource Manager

Pomocí [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), Change Analysis poskytuje historický záznam o tom, jak prostředky Azure, které hostují vaši aplikaci se změnily v průběhu času. Lze zjistit sládkovaná nastavení, jako jsou spravované identity, upgrade operačního systému platformy a názvy hostitelů.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Změny nastavení proxied Správce prostředků Azure
Nastavení, jako je pravidlo konfigurace IP, nastavení TLS a verze rozšíření, ještě nejsou v ARG k dispozici, takže change analysis se na tyto změny bezpečně dotazuje a vypočítává, aby poskytl další podrobnosti o tom, co se v aplikaci změnilo. Tyto informace ještě nejsou k dispozici v Azure Resource Graph, ale brzy budou k dispozici.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Změny v nasazení a konfiguraci webových aplikací (změny u hosta)

Analýza změn zachycuje stav nasazení a konfigurace aplikace každé 4 hodiny. Dokáže například detekovat změny v proměnných prostředí aplikace. Nástroj vypočítá rozdíly a zobrazí, co se změnilo. Na rozdíl od změn správce prostředků nemusí být informace o změně nasazení kódu v nástroji okamžitě k dispozici. Chcete-li zobrazit nejnovější změny v analýze změn, vyberte **možnost Prohledávat změny nyní**.

![Snímek obrazovky s tlačítkem "Skenovat změny nyní"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislostí

Změny závislostí prostředků mohou také způsobit problémy ve webové aplikaci. Pokud například webová aplikace volá do mezipaměti Redis, skladová položka mezipaměti Redis může ovlivnit výkon webové aplikace. Chcete-li zjistit změny v závislostech, change analysis zkontroluje záznam DNS webové aplikace. Tímto způsobem identifikuje změny ve všech součástech aplikace, které by mohly způsobit problémy.
V současné době jsou podporovány následující závislosti:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Enablement
Zprostředkovatel prostředků "Microsoft.ChangeAnalysis" musí být zaregistrován s předplatným pro sledované vlastnosti Azure Resource Manager a proxied nastavení změnit data, která mají být k dispozici. Když zadáte nástroj diagnostiky webové aplikace a vyřešíte problémy nebo zobrazíte samostatnou kartu Analýza změn, tento poskytovatel prostředků se automaticky zaregistruje. Nemá žádné implementace výkonu a nákladů pro vaše předplatné. Když povolíte analýzu změn pro webové aplikace (nebo povolíte v nástroji Diagnostika a řešení problémů), bude to mít zanedbatelný dopad na výkon webové aplikace a žádné náklady na fakturaci.
Pro změny mezi hosty ve webové aplikaci je vyžadováno samostatné povolení pro skenování souborů kódu v rámci webové aplikace. Další informace naleznete [v tématu Enable Change Analysis in the Diagnose and solve problems tool](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) section later in this article for more details.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Zobrazení změn pro všechny prostředky v Azure
Ve službě Azure Monitor existuje samostatné okno pro analýzu změn pro zobrazení všech změn pomocí přehledů a prostředků závislostí aplikací.

Vyhledejte analýzu změn na panelu hledání na webu Azure Portal a spusťte okno.

![Snímek obrazovky s hledáním analýzy změn na webu Azure Portal](./media/change-analysis/search-change-analysis.png)

Chcete-li začít zobrazovat změny, vyberte skupina zdrojů a zdroje.

![Snímek obrazovky s blade analýzy změn na webu Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Můžete zobrazit Přehledy a související prostředky závislostí, které jsou hostitelem vaší aplikace. Toto zobrazení je navrženo tak, aby bylo pro vývojáře zaměřené na aplikace k řešení problémů.

Mezi aktuálně podporované zdroje patří:
- Virtuální počítače
- Škálovací sada virtuálních strojů
- Síťové prostředky Azure
- Webová aplikace se změnami sledování souborů hosta a proměnných prostředí

Pro jakoukoli zpětnou vazbu použijte tlačítko changeanalysisteam@microsoft.comOdeslat zpětnou vazbu v okně nebo e-mailu .

![Snímek obrazovky s tlačítkem zpětné vazby v okně Analýza změn](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Změnit analýzu pro funkci Webové aplikace

Ve službě Azure Monitor je analýza změn integrovaná také do samoobslužné **diagnostiky a řešení problémů.** K tomuto prostředí se dostanete na stránce **Přehled** aplikace App Service.

![Snímek obrazovky s tlačítkem "Přehled" a tlačítkem Diagnostika a řešení problémů](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Povolit analýzu změn v nástroji Diagnostika a řešení problémů

1. Vyberte **možnost Dostupnost a výkon**.

    ![Snímek obrazovky s možnostmi řešení potíží dostupnost a výkon](./media/change-analysis/availability-and-performance.png)

1. Vyberte **položku Změny aplikace**. Ne, že tato funkce je také k dispozici v **chybě aplikace**.

   ![Snímek obrazovky s tlačítkem "Pády aplikace"](./media/change-analysis/application-changes.png)

1. Chcete-li povolit analýzu změn, vyberte **možnost Povolit .**

   ![Snímek obrazovky s možnostmi "Pády aplikace"](./media/change-analysis/enable-changeanalysis.png)

1. Zapněte **analýzu změn** a vyberte **Uložit**. Nástroj zobrazí všechny webové aplikace v rámci plánu služby App Service. Přepínač úrovně plánu můžete použít k zapnutí analýzy změn pro všechny webové aplikace v rámci plánu.

    ![Snímek obrazovky s uživatelským rozhraním Povolit analýzu změn](./media/change-analysis/change-analysis-on.png)


1. Chcete-li získat přístup k analýze změn, vyberte **možnost Diagnostikovat a vyřešit problémy** > **s chybami dostupnosti****a výkonu** > aplikací . Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách. Ve výchozím nastavení se zobrazují změny za posledních 24 hodin, které pomáhají s okamžitými problémy.

     ![Snímek obrazovky s rozdílem zobrazení změn](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Povolit analýzu změn ve velkém měřítku

Pokud vaše předplatné obsahuje mnoho webových aplikací, povolení služby na úrovni webové aplikace by bylo neefektivní. Spusťte následující skript a povolte všechny webové aplikace ve vašem předplatném.

Požadavky:
* Modul Az prostředí PowerShell. Postupujte podle pokynů na [webu Instalace modulu Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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

- Povolte application insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte přehledy aplikací pro [Azure VM a škálovatelné aplikace azure virtuálních počítačů a škálovatelné aplikace](azure-vm-vmss-apps.md)Azure pro virtuální počítače .
- Přečtěte si další informace o [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), který pomáhá power Change Analysis.
