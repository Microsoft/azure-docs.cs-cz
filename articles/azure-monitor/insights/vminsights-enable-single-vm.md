---
title: Povolit monitorování Azure pro virtuální počítače (preview) pro vyhodnocení | Dokumentace Microsoftu
description: Zjistěte, jak vyhodnotit monitorování Azure pro virtuální počítače na jeden virtuální počítač Azure nebo na škálovací sadu virtuálních počítačů.
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
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122525"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Povolit monitorování Azure pro virtuální počítače (preview) pro vyhodnocení

Můžete vyhodnotit monitorování Azure pro virtuální počítače (preview) na malý počet virtuálních počítačů Azure (VM) nebo na jednom virtuálním počítači nebo virtuálním počítači. Je nejrychlejším a nejjednodušším způsobem povolení monitorování na webu Azure Portal. Vaším cílem je zjistit jakékoli výkonem a dostupností způsobené a monitorování virtuálních počítačů. 

Než začnete, projděte si [požadavky](vminsights-enable-overview.md) a ujistěte se, že vaše předplatné a splňovat požadavky na prostředky.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Zapněte sledování pro jeden virtuální počítač Azure
Povolení monitorování virtuálního počítače Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **virtuálních počítačů**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce virtuální počítač v **monitorování** vyberte **Insights (preview)** .

1. Na **Insights (preview)** stránce **vyzkoušet**.

    ![Povolit monitorování Azure pro virtuální počítače pro virtuální počítač](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Na **připojování Přehled monitorování Azure** stránky, pokud máte existující Log Analytics vyberte pracovní prostor v rámci stejného předplatného, v rozevíracím seznamu.  

    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, kde je virtuální počítač nasadit v rámci předplatného. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics k ukládání dat monitorování z virtuálního počítače, naleznete v tématu [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné ze [podporované oblasti](vminsights-enable-overview.md#log-analytics).

Po povolení monitorování, můžete potřebovat Počkejte asi 10 minut, než můžete zobrazit stav metriky pro virtuální počítač.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Zapněte sledování pro jeden virtuální počítač škálovací sady

Povolení monitorování škálovací sady virtuálních počítačů Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Virtual Machine Scale Sets**.

3. V seznamu vyberte škálovací sadu virtuálních počítačů.

4. Na virtuálním počítači škálovací sady stránce v **monitorování** vyberte **Insights (preview)** .

5. Na **Insights (preview)** stránky, pokud chcete použít existující pracovní prostor Log Analytics, vyberte v rozevíracím seznamu.

    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, který se nasazuje virtuální počítač v rámci předplatného. 

    ![Povolit monitorování Azure pro virtuální počítače pro škálovací sadu virtuálních počítačů](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics k ukládání dat monitorování ze škálovací sady virtuálních počítačů, naleznete v tématu [vytvořit pracovní prostor Log Analytics](../learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné ze [podporované oblasti](vminsights-enable-overview.md#log-analytics).

Po povolení monitorování, můžete potřebovat Počkejte asi 10 minut před zobrazením dat monitorování pro škálovací sady.

>[!NOTE]
>Pokud používáte ruční upgrade modelu pro svou škálovací sadu, upgradujte instance a dokončete instalaci. Můžete začít upgrady z **instance** stránku, **nastavení** oddílu.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Teď, když jste povolili monitorování vaší škálovací sadě virtuálního počítače nebo virtuální počítač, informacím o monitorování je k dispozici pro analýzy ve službě Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další postup

* Další informace o použití funkce stavu, najdete v článku [porozumět stavu vašich virtuálních počítačů Azure Monitor](vminsights-health.md). 
* Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [použití Azure monitoru pro virtuální počítače mapu](vminsights-maps.md). 
* Chcete-li identifikovat problémová místa, celkové využití a výkonu Virtuálních počítačů, naleznete v tématu [virtuálního počítače Azure zobrazení výkonu](vminsights-performance.md).