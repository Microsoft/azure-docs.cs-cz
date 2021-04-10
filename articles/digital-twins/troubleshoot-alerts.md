---
title: Nastavení výstrah
titleSuffix: Azure Digital Twins
description: Podívejte se, jak povolit výstrahy pro metriky digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100594862"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Řešení potíží se službou Azure Digital vlákna: výstrahy

Digitální vlákna Azure shromažďuje [metriky](troubleshoot-metrics.md) pro vaši instanci služby, která poskytuje informace o stavu vašich prostředků. Tyto metriky můžete použít k vyhodnocení celkového stavu služby digitálních vláken Azure a prostředků, které jsou k ní připojené.

**Výstrahy** proaktivně upozorňují na to, kdy jsou v datech metriky zjištěny důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Můžete si přečíst další informace o výstrahách v tématu [*Přehled výstrah v Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md).

## <a name="turn-on-alerts"></a>Zapnout výstrahy

Tady je postup, jak povolit upozornění pro instanci digitálních vláken Azure:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

3. Na následující stránce *vytvořit pravidlo výstrahy* můžete podle pokynů definovat podmínky, akce, které se mají aktivovat, a podrobnosti výstrahy.     
    * Podrobnosti **oboru** by měly být automaticky vyplněny podrobnostmi pro vaši instanci.
    * Určíte podrobnosti o **podmínkách** a **skupině akcí** pro přizpůsobení aktivačních událostí a odpovědí.
    * V části **Podrobnosti pravidla výstrahy** zadejte název a volitelný popis pravidla. Pokud chcete, aby se výstraha stala aktivní, jakmile se vytvoří, můžete zaškrtnout políčko _Povolit pravidlo výstrahy po vytvoření_ .
        - Tady můžete vybrat _skupinu prostředků_ a úroveň _závažnosti_ .

4. Kliknutím na tlačítko _vytvořit pravidlo výstrahy_ vytvořte pravidlo upozornění.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Snímek obrazovky se stránkou vytvořit pravidlo výstrahy s oddíly pro podrobnosti oboru, podmínky, skupiny akcí a pravidla výstrahy" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Návod k vyplnění těchto polí najdete v tématu [*Přehled výstrah v Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md). Níže jsou uvedeny některé příklady, jak budou tyto kroky vypadat jako u digitálních vláken Azure.

### <a name="select-conditions"></a>Vybrat podmínky

Tady je výpis z procesu *podmínky výběru* , který ilustruje, jaké typy signálů upozornění jsou k dispozici pro digitální vlákna Azure. Na této stránce můžete filtrovat typ signálu a vybrat požadovaný signál ze seznamu.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Snímek obrazovky, na které se zobrazuje první stránka konfigurovat logiku signálu V poli typ signálu se zvýrazní Výběr metrik nebo protokolů aktivit a seznam metrik, které se dají vybrat.":::

Po výběru signálu budete požádáni o konfiguraci logiky výstrahy. Můžete filtrovat dimenzi, nastavit prahovou hodnotu pro výstrahu a nastavit četnost kontrol pro podmínku. Tady je příklad nastavení výstrahy pro, když průměrná metrika četnosti selhání směrování překročí 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Snímek obrazovky, na které se zobrazuje druhá stránka konfigurace logiky signálů Na stránce se zobrazuje historie metrik, která obsahuje oblast pro filtrování podle dimenze, jako je například operace Event Grid, a sekce pro definování logiky výstrah, jako je Average, je větší než 5.":::

### <a name="verify-success"></a>Ověřit úspěch

Po nastavení výstrah se zobrazí zpátky na stránce s *upozorněními* pro vaši instanci.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Snímek obrazovky se stránkou s výstrahami a tlačítkem, který se má přidat Je nakonfigurovaná jedna výstraha." lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o výstrahách s Azure Monitor najdete v tématu [*Přehled výstrah v Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [*řešení potíží: zobrazení metrik pomocí Azure monitor*](troubleshoot-metrics.md).
* Pokud chcete zjistit, jak povolit protokolování diagnostiky pro metriky, přečtěte si téma [*řešení potíží: nastavení diagnostiky*](troubleshoot-diagnostics.md).
