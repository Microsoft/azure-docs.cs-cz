---
title: Použití analýzy změn aplikace v Azure Monitor k nalezení problémů s webovými aplikacemi | Microsoft Docs
description: Pomocí analýzy změn aplikace v Azure Monitor můžete řešit problémy s aplikacemi na živých webech na Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836738"
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

## <a name="data-sources"></a>Zdroje dat

Dotazy analýzy změn aplikace pro Azure Resource Manager sledované vlastnosti, konfigurace proxy a změny v hostu webové aplikace. Kromě toho služba sleduje změny závislostí prostředků, aby bylo možné diagnostikovat a monitorovat aplikaci od začátku do konce.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Změny sledovaných vlastností Azure Resource Manager

Pomocí [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview)vám analýza změn poskytuje historický záznam o tom, jak se prostředky Azure, které hostují vaši aplikaci, v průběhu času změnily. Je možné zjistit sledovaná nastavení, jako jsou spravované identity, upgrade operačního systému platformy a názvy hostitelů.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Změny nastavení Azure Resource Manager proxy

Nastavení, jako je pravidlo konfigurace protokolu IP, nastavení TLS a verze rozšíření, ještě nejsou k dispozici v Azure Resource graphu, proto změňte dotazy na analýzu a tyto změny proveďte zabezpečeně, aby poskytovaly další podrobnosti v aplikaci, která se změnila.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Změny v nasazení a konfiguraci webové aplikace (změny v hostu)

Změna analýz zachytí stav nasazení a konfigurace aplikace každé 4 hodiny. Může zjistit například změny v proměnných prostředí aplikace. Nástroj počítá rozdíly a prezentuje, co se změnilo. Na rozdíl od Správce prostředků změn nemusí být informace o změně nasazení kódu v nástroji k dispozici okamžitě. Chcete-li zobrazit nejnovější změny v analýze změn, vyberte **aktualizovat**.

![Snímek obrazovky s tlačítkem "Vyhledat změny nyní"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Změny závislosti

Změny závislostí prostředků mohou také způsobovat problémy ve webové aplikaci. Například pokud webová aplikace volá do mezipaměti Redis, může být SKU Redis Cache ovlivněn výkon webové aplikace. Pokud chcete zjistit změny v závislostech, změňte analýzu na záznam DNS webové aplikace. Tímto způsobem identifikuje změny ve všech součástech aplikace, které by mohly způsobovat problémy.
V současné době jsou podporovány následující závislosti:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="application-change-analysis-service"></a>Služba Analysis Services pro změny aplikace

Služba Analysis Services pro změny aplikace počítá a agreguje data změny ze zdrojů dat uvedených výše. Poskytuje sadu analýz pro uživatele, kteří můžou snadno procházet všemi změnami prostředků a určit, která změna je relevantní v kontextu řešení potíží nebo monitorování.
Poskytovatel prostředků "Microsoft. ChangeAnalysis" musí být zaregistrován v rámci předplatného pro Azure Resource Manager sledované vlastnosti a data změny nastavení proxy, aby byla dostupná. Když zadáte nástroj Diagnostika a řešení problémů pro webovou aplikaci nebo spustíte kartu Change Analysis Standalone, tento poskytovatel prostředků se automaticky zaregistruje. Pro vaše předplatné nemá žádné implementace výkonu ani nákladů. Pokud povolíte analýzu změn pro webové aplikace (nebo povolíte nástroj Diagnostika a řešení problémů), bude mít zanedbatelný dopad na výkon webové aplikace a žádné fakturační náklady.
V případě změn v hostu webové aplikace je potřeba samostatné povolení ke skenování souborů kódu v rámci webové aplikace. Další informace najdete v části o [změně analýzy v nástroji Diagnostika a řešení problémů](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) dále v tomto článku.

## <a name="visualizations-for-application-change-analysis"></a>Vizualizace pro analýzu změn aplikace

### <a name="standalone-ui"></a>Samostatné uživatelské rozhraní

V Azure Monitor je k dispozici samostatné podokno pro analýzu změn pro zobrazení všech změn s přehledem o závislostech a prostředcích aplikací.

Pokud chcete spustit prostředí, vyhledejte na panelu hledání možnost analýza změn Azure Portal.

![Snímek obrazovky s vyhledáváním analýzy změn v Azure Portal](./media/change-analysis/search-change-analysis.png)

Všechny prostředky v rámci vybraného předplatného se zobrazí se změnami za posledních 24 hodin. Pro optimalizaci výkonu načítání stránek služba zobrazuje 10 prostředků najednou. Kliknutím na další stránky zobrazíte další prostředky. Pracujeme na odebrání tohoto omezení.

![Snímek obrazovky okna pro změnu analýzy v Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Kliknutím na prostředek zobrazíte všechny jeho změny. V případě potřeby přejděte k podrobnostem o změně a zobrazte si podrobnosti o změně formátu JSON a přehledy.

![Snímek obrazovky s podrobnostmi o změně](./media/change-analysis/change-details.png)

U jakékoli zpětné vazby použijte tlačítko Odeslat názor v okně nebo e-mailu changeanalysisteam@microsoft.com.

![Snímek obrazovky s tlačítkem zpětné vazby v okně pro změnu analýzy](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnostika a řešení problémů webové aplikace

V Azure Monitor je analýza změn integrovaná i v prostředí pro samoobslužnou **diagnostiku a řešení problémů** . K tomuto prostředí se dostanete ze stránky **Přehled** vaší aplikace App Service.

![Snímek obrazovky s tlačítkem "Přehled" a "diagnostikovat a řešit problémy"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analýza změn aplikace v nástroji Diagnostika a řešení problémů

Analýza změn aplikace je samostatný detektor ve webové aplikaci diagnostikuje a řeší problémy nástroje. Je také agregovaná v případě **chyb aplikací** a **detektorů pro webové aplikace**. Když zadáte nástroj Diagnostika a řešení problémů, poskytovatel prostředků **Microsoft. ChangeAnalysis** se automaticky zaregistruje. Postupujte podle těchto pokynů a povolte sledování změn v hostovaném webovém aplikaci.

1. Vyberte **dostupnost a výkon**.

    ![Snímek možností Poradce při potížích s dostupností a výkonem](./media/change-analysis/availability-and-performance.png)

2. Vyberte **změny aplikace**. Funkce je také k dispozici v případě **chyb aplikace**.

   ![Snímek obrazovky s tlačítkem pro zhroucení aplikace](./media/change-analysis/application-changes.png)

3. Pokud chcete povolit analýzu změn, vyberte **Povolit nyní**.

   ![Snímek obrazovky s možnostmi zhroucení aplikací](./media/change-analysis/enable-changeanalysis.png)

4. Zapněte **analýzu změn** a vyberte **Uložit**. Tento nástroj zobrazí všechny webové aplikace v rámci plánu App Service. Pomocí přepínače úroveň plánu můžete zapnout analýzu změn pro všechny webové aplikace v rámci plánu.

    ![Snímek obrazovky s uživatelským rozhraním povolit analýzu změn](./media/change-analysis/change-analysis-on.png)

5. Chcete-li získat přístup k analýze změn, vyberte možnost **Diagnostika a řešení problémů s** > **dostupností a výkonem** > **aplikace**výkonu. Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách. Ve výchozím nastavení se zobrazí změny za posledních 24 hodin, které vám pomůžou s okamžitými problémy.

     ![Snímek obrazovky se zobrazením rozdílů změn](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Povolit škálovatelnou analýzu změn

Pokud vaše předplatné obsahuje mnoho webových aplikací, povolení služby na úrovni webové aplikace bude neefektivní. Spuštěním následujícího skriptu povolte všechny webové aplikace v rámci vašeho předplatného.

Požadavky:

- PowerShell AZ Module. Postupujte podle pokynů v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) .

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostika a řešení problémů s virtuálním počítačem

Přejít na nástroj Diagnostika a řešení problémů pro virtuální počítač.  Přejděte na **Nástroje pro řešení potíží**, přejděte na stránku a vyberte **analyzovat poslední změny** a zobrazte změny na virtuálním počítači.

![Snímek obrazovky s diagnostikou a řešením problémů s virtuálním počítačem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Snímek obrazovky s diagnostikou a řešením problémů s virtuálním počítačem](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Další kroky

- Povolí Application Insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte Application Insights pro virtuální počítače [Azure a Azure Virtual Machine Scale set pro aplikace hostované službou IIS](azure-vm-vmss-apps.md).
- Přečtěte si další informace o [Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/overview), který pomáhá analyzovat změny napájení.
