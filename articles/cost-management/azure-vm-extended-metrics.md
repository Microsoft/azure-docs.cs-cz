---
title: Přidání rozšířené metriky pro virtuální počítače Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže povolit a nakonfigurovat rozšířené diagnostických metrik pro virtuální počítače Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/12/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b7e4665dc3579f357ce1e28bf34be35c931736bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642913"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Přidání rozšířené metriky pro virtuální počítače Azure

Služba Cost Management využívá Azure metriky data z vašich virtuálních počítačů Azure zobrazíte podrobné informace o jejich prostředky. Data metriky, čítače výkonu, nazývané také používá ke generování sestav Cost Management. Ale Cost Management není shromažďovat automaticky všech dat metriky Azure z virtuálních počítačů hosta, je nutné povolit shromažďování metrik. Tento článek vám pomůže povolit a nakonfigurovat dalších diagnostických metrik pro virtuální počítače Azure.

Po povolení shromažďování metrik můžete:

- Vědět, když vaše virtuální počítače jsou tam dostupné pro jejich omezení procesoru, disku a paměti.
- Detekujte trendy využití a anomálie.
- Řídit své náklady podle velikosti podle využití.
- Získání nákladů efektivní velikosti optimalizace doporučení Cost Management.

Například můžete chtít sledovat % procesoru a paměti % virtuální počítače Azure. Metriky virtuálního počítače Azure odpovídají _[hostitele] využití CPU_ a _procento paměti [hosta]_.

> [!NOTE]
> Kolekce rozšířených dat metriky se podporuje jenom s využitím Azure monitoru úrovni hosta. Služba Cost Management není kompatibilní s rozšířením Log Analytics VM.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Ověřte, že jsou na virtuálních počítačích povolené metriky

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. V části **virtuálních počítačů**, vyberte virtuální počítač a potom v části **monitorování**vyberte **metriky**. Se zobrazí seznam dostupných metrik.
3. Vyberte několik metrik a graf zobrazí data pro ně.  
    ![Příklad metrika – využití CPU na hostiteli](./media/azure-vm-extended-metrics/metric01.png)

V předchozím příkladu omezenou sadu standardní metriky jsou k dispozici pro hostitele, ale nejsou paměti metriky. Metriky paměti jsou součástí rozšířené metriky. Musíte provést další kroky k povolení rozšířené metriky. Následující informace vám pomůže povolovat.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Povolit rozšířené metriky na webu Azure Portal

Standardní metriky jsou metriky hostitele počítače. _Využití CPU na [hostitele]_ metrika je jedním z příkladů. Existují také základní metriky pro hostované virtuální počítače a také jsou volány rozšířené metriky. Příklady rozšířené metriky _procento paměti [hosta]_ a _dostupné paměti [hosta]_.

Povolení rozšířené metriky je jednoduché. Pro každý virtuální počítač povolte monitorování na úrovni hosta. Když povolíte monitorování na úrovni hosta, instalaci agenta Azure diagnostics na virtuálním počítači. Následující postup je stejný pro virtuální počítače classic a běžné a stejný pro Windows a virtuální počítače s Linuxem.

Uvědomte si, že Azure a Linuxu monitorování na úrovni hosta se vyžaduje účet úložiště. Když povolíte monitorování na úrovni hosta, pokud se rozhodnete nemáte existující účet úložiště, pak je jedna vytvořená za vás.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Povolit monitorování na úrovni hosta na stávajících virtuálních počítačů

1. V **virtuálních počítačů**, zobrazit seznam vašich virtuálních počítačů a potom vyberte virtuální počítač.
2. V části **monitorování**vyberte **metriky**.
3. Klikněte na tlačítko **nastavení diagnostiky**.
4. Na stránce nastavení diagnostiky, klikněte na tlačítko **povolit monitorování na úrovni hosta**.  
    ![Povolit monitorování na úrovni hosta](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Po několika minutách se nainstaluje agent diagnostiky Azure na virtuálním počítači. Aktualizujte stránku a metrik hosta se aktualizuje seznam dostupných metrik.  
    ![Rozšířené metriky](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Povolit monitorování na úrovni hosta na nové virtuální počítače

Při vytváření nové virtuální počítače, ujistěte se, že vyberete **Diagnostika hostovaného operačního systému**.

![Povolte diagnostiku hostovaného operačního systému](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Přihlašovací údaje správce prostředků

Po povolení rozšířené metriky, ujistěte se, že Cost Management má přístup k vaší [Resource Manageru přihlašovacími údaji](activate-subs-accounts.md). Vaše přihlašovací údaje jsou požadovány pro Cost Management ke shromáždění a zobrazení dat výkonu pro virtuální počítače. Také slouží k vytvoření doporučení pro optimalizaci nákladů. Služba Cost Management potřebuje aspoň tři dny údaje o výkonu z instance k určení, zda je mezi kandidáty pro doporučení downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Zapnutí metrik virtuálního počítače pomocí skriptu

Metriky virtuálního počítače pomocí skriptů prostředí Azure PowerShell můžete povolit. Pokud máte mnoho virtuálních počítačů, které chcete k zapnutí metrik na, můžete použít skript k automatizaci procesu. Příklady skriptů najdete na Githubu v [povolení diagnostiky Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobrazit metriku výkonu Azure

Pokud chcete zobrazit metriky výkonu na vaše instance Azure na portálu Cloudyn, přejděte na **prostředky** > **Compute** > **instanci Průzkumníka**. V seznamu instancí virtuálních počítačů rozbalte instanci a potom rozbalte prostředků k zobrazení podrobností.

![Instance Průzkumníka](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nepovolili přístup přes rozhraní API Azure Resource Manageru pro vaše účty, pokračujte [účtů a předplatných Azure aktivovat](activate-subs-accounts.md).
