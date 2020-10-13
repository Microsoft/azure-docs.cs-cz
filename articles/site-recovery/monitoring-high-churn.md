---
title: Monitorování vzorů změn na virtuálních počítačích
description: Naučte se monitorovat vzorce změn v Virtual Machines chráněných pomocí Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: 51f45b5c0b8a2729f64eb376e06875719474cbfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663890"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Monitorování vzorů změn na virtuálních počítačích

Tento článek poskytuje přehled různých nástrojů, které se dají použít k monitorování vzorů změn na virtuálním počítači. Pomocí správných nástrojů je snadné zjistit přesně to, která aplikace způsobuje vysoké změny, a pak může být provedena další akce pro tuto aplikaci.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Pro virtuální počítače Azure (Windows nebo Linux)

Pokud je váš počítač hostovaný v Azure a používá spravovaný nebo nespravovaný disk pro úložiště, můžete snadno sledovat výkon sledováním metrik disků. To vám umožní pečlivě monitorovat a nastavit správný výběr disku tak, aby vyhovoval vašemu vzoru použití aplikace. Můžete ji také použít k vytvoření výstrah, diagnostiky a automatizace sestavení. [Další informace](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Po ochraně počítačů pomocí Azure Site Recovery můžete monitorovat počítače pomocí protokolů Azure Monitor a Log Analytics. [Další informace](https://docs.microsoft.com/azure/site-recovery/monitor-log-analytics).

K dispozici jsou také některé nástroje specifické pro operační systém, které můžete použít.

## <a name="for-windows-machines"></a>Počítače s Windows

V případě, že máte počítač, je místní nebo ne, na kterém běží operační systém Windows, je k dispozici několik dalších nástrojů.

Kromě kontroly využití disku ve Správci úloh můžete vždycky odkázat na **Sledování prostředků** a **sledování výkonu**. Tyto nástroje jsou již přítomny na počítačích s Windows.

### <a name="resource-monitor"></a>Sledování prostředků

**Sledování prostředků** zobrazí informace o používání hardwarových a softwarových prostředků v reálném čase. Chcete-li spustit Sledování prostředků v počítači s Windows, postupujte podle následujících kroků:

1. Stiskněte Win + R a zadejte _ResMon_.
1. Jakmile se ResMon, to znamená, Sledování prostředků otevře okno, přepne na kartu disk. Poskytuje následující zobrazení –

    ![Karta Sledování prostředků disku](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Tato karta se musí nepřetržitě monitorovat, aby se získal jasný obrázek. V předchozím příkladu vidíte, že _wmiprv.exe_ je velká.

Jakmile zjistíte, že aplikace způsobují vysoké změny v počítači, můžete podniknout potřebné akce, abyste se převzali do souvislosti s těmito aplikacemi.

### <a name="performance-monitor"></a>Monitorování výkonu

**Sledování výkonu** monitoruje různé aktivity v počítači, jako je využití procesoru nebo paměti. Chcete-li spustit nástroj sledování výkonu na počítači s Windows, postupujte podle následujících kroků:

1. Stiskněte Win + R a napište _perfmon_.
1. Po zobrazení PerfMon se zobrazí okno sledování výkonu a zobrazí se následující zobrazení:

    ![Krok 1 sledování výkonu](./media/monitoring-high-churn/perfmon-step1.png)

1. Rozbalte složku **Nástroje pro sledování** na pravé straně a klikněte na sledování výkonu. Otevře se vám následující zobrazení, které vám poskytne informace o aktuálním výkonu.

    ![STEP2 sledování výkonu](./media/monitoring-high-churn/perfmon-step1.png)

1. Tento graf aktuálně sleduje pouze jedno monitorování, tedy '% času procesoru ' – jak je uvedeno v tabulce vpravo pod grafem. Kliknutím na **+** v horní části nástroje můžete přidat další položky pro monitorování.
1. Níže je znázorněno, jak může monitor výkonu po přidání dalších čítačů do něj vypadat –

    ![Step3 sledování výkonu](./media/monitoring-high-churn/perfmon-step3.png)

Další informace o sledování výkonu [najdete tady](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>Počítače s Linuxem

V případě, že máte počítač, je místní nebo ne, na kterém běží operační systém Linux, je k dispozici několik dalších nástrojů pro monitorování vzorů změn.

### <a name="iotop"></a>Iotop

Jedním z nejčastěji používaných nástrojů je _iotop_. Je to nástroj pro zobrazení aktivity disku v reálném čase. Může vypsat procesy, které provádějí vstupně-výstupní operace, spolu se šířkou pásma disku, kterou používají.

Otevřete příkazový řádek a spusťte příkaz `iotop` .

### <a name="iostat"></a>IoStat

IoStat je jednoduchý nástroj, který bude shromažďovat a zobrazovat statistiku systémového vstupu a výstupu úložného zařízení. Tento nástroj se často používá ke sledování problémů s výkonem zařízení úložiště, včetně zařízení, místních disků a vzdálených disků.

Otevřete příkazový řádek a spusťte příkaz `iostat` .

## <a name="next-steps"></a>Další kroky

Naučte se monitorovat pomocí [Azure monitor](monitor-log-analytics.md).
