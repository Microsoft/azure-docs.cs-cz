---
title: Použití analýzy změn aplikace v Azure Monitor k nalezení problémů s webovými aplikacemi | Microsoft Docs
description: Pomocí analýzy změn aplikace v Azure Monitor můžete řešit problémy s aplikacemi na živých webech na Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 6a5df4f6a20a9f7061f56dac507a474f7bda6100
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992878"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Použití analýzy změn aplikace (Preview) v Azure Monitor

Když dojde k problému nebo výpadku živého webu, rychle určit hlavní příčinu je kritická. Standardní řešení monitorování vás můžou upozornit na problém. Můžou dokonce označovat, která součást selhává. Tato výstraha ale nebude vždycky okamžitě vysvětlit příčinu selhání. Věděli jste, že váš web pracoval před pěti minutami a teď je porušený. Co se změnilo za posledních pět minut? Jedná se o otázku, kterou analýza změn aplikace je navržená tak, aby odpovídala v Azure Monitor.

Při sestavování výkonného [grafu prostředků Azure](../../governance/resource-graph/overview.md)vám analýza změn poskytuje přehledy o změnách aplikací Azure, aby se zvýšila pozorování a snižovala MTTR (což znamená, jak dlouho se má opravit).

> [!IMPORTANT]
> Změna analýzy je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Tato verze se nedoporučuje pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="overview"></a>Přehled

Změna analýzy detekuje různé typy změn, od vrstvy infrastruktury po nasazení aplikace. Jedná se o poskytovatele prostředků Azure na úrovni předplatného, který kontroluje změny prostředků v předplatném. Změna analýz poskytuje data různých diagnostických nástrojů, které uživatelům pomůžou pochopit, jaké změny mohly způsobovat problémy.

Následující diagram znázorňuje architekturu analýzy změn:

![Diagram architektury, jak analýza změn získává data změny a poskytuje je klientským nástrojům](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Zdroje dat

Dotazy analýzy změn aplikace pro Azure Resource Manager sledované vlastnosti, konfigurace proxy a změny v hostu webové aplikace. Kromě toho služba sleduje změny závislostí prostředků, aby bylo možné diagnostikovat a monitorovat aplikaci od začátku do konce.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Změny sledovaných vlastností Azure Resource Manager

Pomocí [Azure Resource graphu](../../governance/resource-graph/overview.md)vám analýza změn poskytuje historický záznam o tom, jak se prostředky Azure, které hostují vaši aplikaci, v průběhu času změnily. Je možné zjistit sledovaná nastavení, jako jsou spravované identity, upgrade operačního systému platformy a názvy hostitelů.

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
V případě změn v hostu webové aplikace je potřeba samostatné povolení ke skenování souborů kódu v rámci webové aplikace. Další informace najdete v části o [změně analýzy v nástroji Diagnostika a řešení problémů](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) dále v tomto článku.

## <a name="visualizations-for-application-change-analysis"></a>Vizualizace pro analýzu změn aplikace

### <a name="standalone-ui"></a>Samostatné uživatelské rozhraní

V Azure Monitor je k dispozici samostatné podokno pro analýzu změn pro zobrazení všech změn s přehledem o závislostech a prostředcích aplikací.

Pokud chcete spustit prostředí, vyhledejte na panelu hledání možnost analýza změn Azure Portal.

![Snímek obrazovky s vyhledáváním analýzy změn v Azure Portal](./media/change-analysis/search-change-analysis.png)

Všechny prostředky v rámci vybraného předplatného se zobrazí se změnami za posledních 24 hodin. Pro optimalizaci výkonu načítání stránek služba zobrazuje 10 prostředků najednou. Kliknutím na další stránky zobrazíte další prostředky. Pracujeme na odebrání tohoto omezení.

![Snímek obrazovky okna pro změnu analýzy v Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Kliknutím na prostředek zobrazíte všechny jeho změny. V případě potřeby přejděte k podrobnostem o změně a zobrazte si podrobnosti o změně formátu JSON a přehledy.

![Snímek obrazovky s podrobnostmi o změně](./media/change-analysis/change-details.png)

U jakékoli zpětné vazby použijte tlačítko Odeslat názor v okně nebo e-mailu changeanalysisteam@microsoft.com .

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

3. Odkaz vede k Aalysis uživatelského rozhraní změny aplikace na rozsah webové aplikace. Pokud není zapnuté sledování změn ve webové aplikaci, použijte k získání změn nastavení souborů a aplikací informační zprávu.

   ![Snímek obrazovky s možnostmi zhroucení aplikací](./media/change-analysis/enable-changeanalysis.png)

4. Zapněte **analýzu změn** a vyberte **Uložit**. Tento nástroj zobrazí všechny webové aplikace v rámci plánu App Service. Pomocí přepínače úroveň plánu můžete zapnout analýzu změn pro všechny webové aplikace v rámci plánu.

    ![Snímek obrazovky s uživatelským rozhraním povolit analýzu změn](./media/change-analysis/change-analysis-on.png)

5. Možnost změnit data je také k dispozici v rozevíracích selektorech pro výběr **webové aplikace** a při **selhání aplikace** . Zobrazí se graf, který shrnuje typ změn v průběhu času spolu s podrobnostmi o těchto změnách. Ve výchozím nastavení se zobrazí změny za posledních 24 hodin, které vám pomůžou s okamžitými problémy.

     ![Snímek obrazovky se zobrazením rozdílů změn](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostika a řešení problémů s virtuálním počítačem

Přejít na nástroj Diagnostika a řešení problémů pro virtuální počítač.  Přejděte na **Nástroje pro řešení potíží**, přejděte na stránku a vyberte **analyzovat poslední změny** a zobrazte změny na virtuálním počítači.

![Snímek obrazovky s diagnostikou a řešením problémů s virtuálním počítačem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analyzátor změn v nástrojích pro řešení potíží](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Historie změn protokolu aktivit
Funkce [Zobrazit historii změn](../platform/activity-log.md#view-change-history) v protokolu aktivit volá back-end služby Analysis Service v aplikaci, aby se získaly změny přidružené k operaci. **Historii změn** , která se používá k přímému volání [Azure Resource graphu](../../governance/resource-graph/overview.md) , ale přeměnila back-end na volání analýzy změn aplikace, takže vrácené změny budou zahrnovat změny úrovně prostředků z [Azure Resource graphu](../../governance/resource-graph/overview.md), vlastnosti prostředku z [Azure Resource Manager](../../azure-resource-manager/management/overview.md)a změny v hostu z PaaS Services, jako je App Services webová aplikace. Aby mohla služba Analysis Services pro změny aplikace kontrolovat změny v předplatných uživatelů, musí být zaregistrovaný poskytovatel prostředků. Při prvním zadání karty **historie změn** se nástroj automaticky spustí, aby zaregistroval poskytovatele prostředků **Microsoft. ChangeAnalysis** . Po registraci budou změny z **Azure Resource Graph** k dispozici okamžitě a budou zahrnovat posledních 14 dní. Změny z jiných zdrojů budou k dispozici po přibližně 4 hodinách po zprovoznění předplatného.

![Integrace historie změn protokolu aktivit](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>Integrace se službou VM Insights
Uživatelé s povoleným [virtuálním počítačem Insights](../insights/vminsights-overview.md) můžou zobrazit, co se změnilo ve svých virtuálních počítačích, které by mohly způsobit jakékoli špičky v grafu metrik, jako je například procesor nebo paměť, a taky to, jestli to způsobilo. Data změny jsou integrovaná v navigačním panelu na straně virtuálních počítačů Insights. Uživatel může zobrazit, jestli na virtuálním počítači došlo ke změnám, a kliknout na **prozkoumat změny** a zobrazit podrobnosti o změně v uživatelském rozhraní pro analýzu změn aplikace.

[![Integrace se službou VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>Povolit škálovatelnou analýzu změn

Pokud vaše předplatné obsahuje mnoho webových aplikací, povolení služby na úrovni webové aplikace bude neefektivní. Spuštěním následujícího skriptu povolte všechny webové aplikace v rámci vašeho předplatného.

Požadavky:

- PowerShell AZ Module. Postupujte podle pokynů v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .

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

## <a name="troubleshoot"></a>Řešení potíží

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>Nedaří se zaregistrovat poskytovatele prostředků Microsoft. Change Analysis Provider z karty historie změn
Pokud při prvním zobrazení historie změn po integraci s analýzou změn aplikace vidíte historii změn, uvidíte, že se automaticky zaregistruje poskytovatel prostředků **Microsoft. ChangeAnalysis**. Ve výjimečných případech může selhání selhat z následujících důvodů:

- Nemáte **dostatečná oprávnění k registraci poskytovatele prostředků Microsoft. ChangeAnalysis**. Tato chybová zpráva znamená, že vaše role v aktuálním předplatném nemá přiřazený obor **Microsoft. support/registr/Action** . K tomu může dojít v případě, že nejste vlastníkem předplatného a máte oprávnění ke sdíleným přístupům prostřednictvím spolupracovníka. To znamená, že se zobrazí přístup ke skupině prostředků. Chcete-li tento problém vyřešit, obraťte se na vlastníka předplatného, abyste mohli zaregistrovat poskytovatele prostředků **Microsoft. ChangeAnalysis** . To se dá udělat v Azure Portal prostřednictvím **předplatných | Poskytovatelé prostředků** a vyhledejte ```Microsoft.ChangeAnalysis``` a zaregistrujte se v uživatelském rozhraní nebo prostřednictvím Azure PowerShell nebo rozhraní příkazového řádku Azure.

    Registrovat poskytovatele prostředků prostřednictvím PowerShellu: 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Nepovedlo se zaregistrovat poskytovatele prostředků Microsoft. ChangeAnalysis**. Tato zpráva znamená, že se něco nezdařilo hned po odeslání žádosti o registraci poskytovatele prostředků do uživatelského rozhraní a není v souvislosti s problémem s oprávněním. Pravděpodobně se může jednat o dočasný problém s připojením k Internetu. Zkuste aktualizovat stránku a zkontrolovat připojení k Internetu. Pokud chyba přetrvává, obraťte se na changeanalysishelp@microsoft.com

- **Trvá to déle, než se čekalo**. Tato zpráva znamená, že registrace trvá déle než 2 minuty. To je neobvyklé, ale nemusí nutně znamenat, že se něco pokazilo. Můžete přejít k **předplatným | Poskytovatel prostředků** pro kontrolu stavu registrace poskytovatele prostředků **Microsoft. ChangeAnalysis** . Můžete zkusit použít uživatelské rozhraní pro zrušení registrace, opětovného registraci nebo obnovení, abyste viděli, jestli vám to pomůže. Pokud se problém opakuje, obraťte se changeanalysishelp@microsoft.com na podporu.
    ![Řešení potíží s registrací RP trvá příliš dlouho](./media/change-analysis/troubleshoot-registration-taking-too-long.png)



## <a name="next-steps"></a>Další kroky

- Povolí Application Insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte Application Insights pro virtuální počítače [Azure a Azure Virtual Machine Scale set pro aplikace hostované službou IIS](azure-vm-vmss-apps.md).
- Přečtěte si další informace o [Azure Resource graphu](../../governance/resource-graph/overview.md), který pomáhá analyzovat změny napájení.