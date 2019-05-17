---
title: Povolit monitorování Azure pro virtuální počítače (preview) pro vyhodnocení | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro škálovací sady pro účely vyhodnocení virtuálních počítačů a jednoho virtuálního počítače Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524082"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Povolit monitorování Azure pro virtuální počítače (preview) pro vyhodnocení

K vyhodnocení monitorování Azure pro virtuální počítače (preview) na malý počet virtuálních počítačů Azure nebo jeden virtuální počítač nebo škálovací sady virtuálních počítačů, je nejrychlejším a nejjednodušším přístupem k povolení monitorování na webu Azure Portal. Na konci tohoto procesu se úspěšně začali jste je monitorování a další, pokud se vyskytly problémy s výkonem nebo dostupností. 

Před Začínáme pracovat, nezapomeňte se podívat [požadavky](vminsights-enable-overview.md) a ověřte vaše předplatné a prostředky splňují požadavky.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Zapněte sledování pro jeden virtuální počítač Azure
Pokud chcete povolit monitorování virtuálního počítače Azure na webu Azure Portal, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **virtuálních počítačů**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce virtuální počítač v **monitorování** vyberte **Insights (preview)**.

1. Na **Insights (preview)** stránce **vyzkoušet**.

    ![Povolit monitorování Azure pro virtuální počítače pro virtuální počítač](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Na **připojování Přehled monitorování Azure** stránky, pokud máte existující Log Analytics vyberte pracovní prostor v rámci stejného předplatného, v rozevíracím seznamu.  

    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, který se nasazuje virtuální počítač v rámci předplatného. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z virtuálního počítače, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) v jednom z podporovaných oblastech uvedených [tady](vminsights-enable-overview.md#log-analytics).

Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav metriky pro virtuální počítač.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Zapněte sledování pro jeden virtuální počítač škálovací sady

Chcete-li povolit monitorování virtuálních počítačů Azure škálovací sady na webu Azure Portal, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Virtual Machine Scale Sets**.

3. V seznamu vyberte škálovací sadu virtuálních počítačů.

4. Na virtuálním počítači škálovací sady stránce v **monitorování** vyberte **Insights (preview)**.

5. Na **Insights (preview)** stránky, pokud máte existující pracovní prostor Log Analytics můžete chcete použít, vyberte v rozevíracím seznamu.

    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, který se nasazuje virtuální počítač v rámci předplatného. 

    ![Povolit monitorování Azure pro virtuální počítače pro škálovací sadu virtuálních počítačů](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z virtuálního počítače, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../learn/quick-create-workspace.md) v jednom z podporovaných oblastech uvedených [tady](vminsights-enable-overview.md#log-analytics).

Po povolení sledování, může trvat přibližně 10 minut, než může zobrazení dat monitorování pro škálovací sadu.

>[!NOTE]
>Pokud používáte ruční upgrade modelu pro svou škálovací sadu, budete muset upgradovat instance a dokončete instalaci.  To můžete udělat z instance stránky v části **nastavení** oddílu.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Další postup

Teď, když je zapnuté monitorování pro virtuální počítač nebo škálovací sady virtuálních počítačů, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). Pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).