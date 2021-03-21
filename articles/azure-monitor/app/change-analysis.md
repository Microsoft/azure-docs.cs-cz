---
title: Použití analýzy změn aplikace v Azure Monitor k nalezení problémů s webovými aplikacemi | Microsoft Docs
description: Pomocí analýzy změn aplikace v Azure Monitor můžete řešit problémy s aplikacemi na živých webech na Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 43ece2cb0f5cb9428d8d73f769018e9fe2408ab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655801"
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

## <a name="supported-resource-types"></a>Podporované typy prostředků

Služba analýza změn aplikace podporuje změny na úrovni vlastností prostředků ve všech typech prostředků Azure, včetně běžných prostředků, jako jsou:
- Virtuální počítač
- Škálovací sada virtuálních počítačů
- App Service
- Služba Azure Kubernetes
- Funkce Azure
- Síťové prostředky: Skupina zabezpečení sítě, Virtual Network, Application Gateway atd.
- Datové služby: Storage, SQL, Redis Cache, Cosmos DB atd.

## <a name="data-sources"></a>Zdroje dat

Dotazy analýzy změn aplikace pro Azure Resource Manager sledované vlastnosti, konfigurace proxy a změny v hostu webové aplikace. Kromě toho služba sleduje změny závislostí prostředků, aby bylo možné diagnostikovat a monitorovat aplikaci od začátku do konce.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Změny sledovaných vlastností Azure Resource Manager

Pomocí [Azure Resource graphu](../../governance/resource-graph/overview.md)vám analýza změn poskytuje historický záznam o tom, jak se prostředky Azure, které hostují vaši aplikaci, v průběhu času změnily. Je možné zjistit sledovaná nastavení, jako jsou spravované identity, upgrade operačního systému platformy a názvy hostitelů.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Změny nastavení Azure Resource Manager proxy

Nastavení, jako je pravidlo konfigurace protokolu IP, nastavení TLS a verze rozšíření, ještě nejsou k dispozici v Azure Resource graphu, proto změňte dotazy na analýzu a tyto změny proveďte zabezpečeně, aby poskytovaly další podrobnosti v aplikaci, která se změnila.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Změny v nasazení a konfiguraci webové aplikace (změny v hostu)

Změna analýz zachytí stav nasazení a konfigurace aplikace každé 4 hodiny. Může zjistit například změny v proměnných prostředí aplikace. Nástroj počítá rozdíly a prezentuje, co se změnilo. Na rozdíl od Správce prostředků změn nemusí být informace o změně nasazení kódu v nástroji k dispozici okamžitě. Chcete-li zobrazit nejnovější změny v analýze změn, vyberte **aktualizovat**.

![Snímek obrazovky s tlačítkem "Vyhledat změny nyní"](./media/change-analysis/scan-changes.png)

V současné době jsou podporovány všechny textové soubory v **kořenu** kořenového adresáře lokality s následujícími příponami:
- *. config
- *. XML
- *. JSON
- *. gem
- *. yml
- *. txt
- *. ini
- *. env

### <a name="dependency-changes"></a>Změny závislosti

Změny závislostí prostředků mohou také způsobovat problémy v prostředku. Například pokud webová aplikace volá do mezipaměti Redis, může být SKU Redis Cache ovlivněn výkon webové aplikace. Dalším příkladem je, že je port 22 uzavřený ve skupině zabezpečení sítě virtuálního počítače, způsobí chyby připojení.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Diagnostika a řešení problémů v aplikaci Web App (Preview)

Pokud chcete zjistit změny v závislostech, změňte analýzu na záznam DNS webové aplikace. Tímto způsobem identifikuje změny ve všech součástech aplikace, které by mohly způsobovat problémy.
V současné době jsou v **diagnostice a řešení problémů v rámci webové aplikace podporovány následující závislosti | Navigátor (Preview)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>Související prostředky

Analýza změny aplikace detekuje související prostředky. Běžnými příklady jsou skupiny zabezpečení sítě, Virtual Network, Application Gateway a Load Balancer související s virtuálním počítačem.
Síťové prostředky se obvykle automaticky zřídí ve stejné skupině prostředků jako prostředky, které ji používají, takže filtrování změn podle skupiny prostředků zobrazí všechny změny pro virtuální počítač a související síťové prostředky.

![Snímek obrazovky se změnami sítě](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Povolení služby analýza změn aplikace

Služba Analysis Services pro změny aplikace počítá a agreguje data změny ze zdrojů dat uvedených výše. Poskytuje sadu analýz pro uživatele, kteří můžou snadno procházet všemi změnami prostředků a určit, která změna je relevantní v kontextu řešení potíží nebo monitorování.
Poskytovatel prostředků "Microsoft. ChangeAnalysis" musí být zaregistrován v rámci předplatného pro Azure Resource Manager sledované vlastnosti a data změny nastavení proxy, aby byla dostupná. Když zadáte nástroj Diagnostika a řešení problémů pro webovou aplikaci nebo spustíte kartu Change Analysis Standalone, tento poskytovatel prostředků se automaticky zaregistruje.
V případě změn v hostu webové aplikace je potřeba samostatné povolení ke skenování souborů kódu v rámci webové aplikace. Další informace najdete v části o [změně analýzy v nástroji Diagnostika a řešení problémů](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) dále v tomto článku.

## <a name="cost"></a>Náklady
Analýza změn aplikací je bezplatná služba – neúčtují se vám žádné fakturační náklady na předplatná, která jsou povolená. Služba také nemá žádný vliv na výkon při kontrole změn vlastností prostředků Azure. Povolíte-li analýzu změn pro webové aplikace změny souborů v hostovi (nebo povolíte nástroj Diagnostika a řešení problémů), bude mít zanedbatelný dopad na výkon webové aplikace a žádné fakturační náklady.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Povolit škálování změn ve velkém měřítku pro soubor a změny proměnných prostředí webové aplikace

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

## <a name="next-steps"></a>Další kroky

- Další informace o [vizualizacích v analýze změn](change-analysis-visualizations.md)
- Naučte se [řešit problémy při změně analýzy](change-analysis-troubleshoot.md) .
- Povolí Application Insights pro [aplikace Azure App Services](azure-web-apps.md).
- Povolte Application Insights pro virtuální počítače [Azure a Azure Virtual Machine Scale set pro aplikace hostované službou IIS](azure-vm-vmss-apps.md).
