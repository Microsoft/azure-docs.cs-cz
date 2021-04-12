---
title: Správa spuštění/zastavení virtuálních počítačů v2 (Preview)
description: Tento článek popisuje, jak monitorovat stav virtuálních počítačů Azure spravovaných funkcí spustit nebo zastavit virtuální počítače v2 (Preview) a provádět další úlohy správy.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111714"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Jak spravovat spouštění a zastavování virtuálních počítačů v2 (Preview)

## <a name="azure-dashboard"></a>Řídicí panel Azure

Spuštění/zastavení virtuálních počítačů v2 (Preview) obsahuje [řídicí panel](../../azure-monitor/visualizations.md#azure-dashboards) , který vám pomůže pochopit rozsah správy a poslední operace s vašimi virtuálními počítači. Je to rychlý a snadný způsob, jak ověřit stav každé operace prováděné na vašich virtuálních počítačích Azure. Vizualizace v každé dlaždici je založena na dotazu protokolu a chcete-li zobrazit dotaz, vyberte v pravém horním rohu dlaždice možnost **otevřít v** protokolu. Tím se otevře nástroj [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) v Azure Portal a odsud můžete dotaz vyhodnotit a upravit tak, aby podporoval vaše potřeby, například vlastní [výstrahy protokolu](../../azure-monitor/alerts/alerts-log.md), vlastní [sešit](../../azure-monitor/visualize/workbooks-overview.md)atd.

Data protokolu každé dlaždice na řídicím panelu se zobrazují každou hodinu, s možností ruční aktualizace na vyžádání kliknutím na ikonu **aktualizace** v dané vizualizaci nebo aktualizací úplného řídicího panelu.

Další informace o práci s řídicím panelem založeným na protokolu najdete v následujícím [kurzu](../../azure-monitor/visualize/tutorial-logs-dashboards.md).

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Pokud chcete změnit e-mailová oznámení po nasazení spustit nebo zastavit virtuální počítače v2 (Preview), můžete upravit skupinu akcí vytvořenou během nasazování.

1. V Azure Portal přejděte na **monitorování** a pak na **výstrahy**. Vyberte **Spravovat akce**.

1. Na stránce **Správa akcí** vyberte skupinu akcí s názvem **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Snímek obrazovky se stránkou skupin akcí":::

1. Na stránce **StartStopV2_VM_Notification** můžete upravit možnosti e-mailu, oznámení pro odesílání a doručování. Přidejte další akce nebo aktualizujte stávající konfiguraci na tuto skupinu akcí a potom kliknutím na tlačítko **OK** uložte změny.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Snímek obrazovky se stránkou E-mail/SMS/Push/Voice ukazující ukázkovou e-mailovou adresu.":::

    Další informace o skupinách akcí najdete v tématu [skupiny akcí](../../azure-monitor/alerts/action-groups.md) .

Následující snímek obrazovky je příklad e-mailu, který se pošle, když funkce vypne virtuální počítače.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Snímek obrazovky s ukázkovým e-mailem odeslaným, když funkce vypne virtuální počítače":::

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy při správě virtuálních počítačů, přečtěte si téma [řešení potíží s spuštěním nebo zastavením virtuálních počítačů v2](troubleshoot.md) (Preview).