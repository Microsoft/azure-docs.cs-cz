---
title: Konfigurace výstrah a práce s metrikami v řešení Azure VMware
description: Naučte se používat výstrahy pro příjem oznámení. Naučíte se také, jak pracovat s metrikami a získat hlubší přehled o privátním cloudu řešení Azure VMware.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 486f25eba017b2d4e37c0796909a0d26adee6ba8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309624"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Konfigurace výstrah Azure v řešení Azure VMware 

V tomto článku se dozvíte, jak nakonfigurovat [skupiny akcí Azure](/azure/azure-monitor/alerts/action-groups) v [Microsoft Azure výstrahy](/azure/azure-monitor/alerts/alerts-overview) na přijímání oznámení o aktivovaných událostech, které definujete. Naučíte se také, jak pomocí [Azure monitor metrik](/azure/azure-monitor/essentials/data-platform-metrics) získat hlubší přehled o privátním cloudu řešení Azure VMware.


## <a name="supported-metrics-and-activities"></a>Podporované metriky a aktivity

Následující metriky jsou viditelné prostřednictvím Azure Monitor metriky.

| **Název signálu**                                                         | **Typ signálu** | **Monitorovat službu** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Celková kapacita disku úložiště dat                                           | Metric          | Platforma            |
| Procento využitého disku úložiště dat                                          | Metric          | Platforma            |
| Procento CPU                                                          | Metric          | Platforma            |
| Průměrná efektivní paměť                                                | Metric          | Platforma            |
| Průměrná režie paměti                                                 | Metric          | Platforma            |
| Průměrná celková paměť                                                    | Metric          | Platforma            |
| Průměrné využití paměti                                                    | Metric          | Platforma            |
| Využitý disk úložiště dat                                                     | Metric          | Platforma            |
| Všechny operace správy                                           | Protokol aktivit    | Správcovské      |
| Zaregistrujte poskytovatele prostředků Microsoft. AVS. (Microsoft. AVS/privateClouds) | Protokol aktivit    | Správcovské      |
| Vytvoří nebo aktualizuje PrivateCloud. (Microsoft. AVS/privateClouds)          | Protokol aktivit    | Správcovské      |
| Odstraní PrivateCloud. (Microsoft. AVS/privateClouds)                    | Protokol aktivit    | Správcovské      |

## <a name="configure-an-alert-rule"></a>Konfigurace pravidla výstrahy
1. V privátním cloudu řešení Azure VMware vyberte   >  **výstrahy** monitorování a pak **nové pravidlo výstrahy**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Snímek obrazovky, který ukazuje, kde nakonfigurovat pravidlo výstrahy v privátním cloudu řešení Azure VMware." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Otevře se nová obrazovka konfigurace, kde můžete:
   - Definování oboru
   - Konfigurace podmínky
   - Nastavení skupiny akcí
   - Definování podrobností pravidla výstrahy
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Snímek obrazovky, který ukazuje okno vytvořit pravidlo výstrahy." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. V části **obor** vyberte cílový prostředek, který chcete monitorovat. Ve výchozím nastavení je privátní cloud řešení Azure VMware z místa, kde jste otevřeli nabídku výstrahy, definovaný.

1. V části **Podmínka** vyberte **Přidat podmínku** a v okně, které se otevře, vyberte signál, který chcete vytvořit pro pravidlo výstrahy. 

   V našem příkladu jsme vybrali **procento využitého disku úložiště dat**, které je relevantní z perspektivy [smlouvy SLA pro řešení Azure VMware](https://aka.ms/avs/sla) . 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Snímek obrazovky, který zobrazuje okno Konfigurovat logiku signálu s předdefinovanými názvy signálů."::: 

1. Definujte logiku, která aktivuje výstrahu, a potom vyberte **Hotovo**. 

   V našem příkladu byla upravena pouze **prahová hodnota** a **frekvence vyhodnocení** . 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Snímek obrazovky, který zobrazuje informace o vybrané logice signálu."::: 

1. V části **Akce** vyberte **Přidat skupiny akcí**. Skupina akcí definuje *způsob* přijetí *oznámení a jeho* přijetí.   Oznámení můžete přijímat prostřednictvím e-mailu, SMS, [nabízeného oznámení mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) nebo hlasové zprávy.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Snímek obrazovky zobrazující existující skupiny akcí a umístění, kde se má vytvořit nová skupina akcí":::

1. V okně, které se otevře, vyberte **vytvořit skupinu akcí**.

   >[!TIP]
   > Můžete také použít existující skupinu akcí.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Snímek obrazovky, který zobrazuje skupiny akcí, které se mají pro výstrahu vybrat."::: 

 

 
1. V okně, které se otevře, zadejte na kartě **základy** název skupiny akcí a název a zobrazované jméno.

1. Vyberte kartu **oznámení** , vyberte typ a **název** **oznámení** . Pak vyberte **OK**.

   Náš příklad vychází z e-mailových oznámení.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Snímek obrazovky, který zobrazuje nastavení e-mailu, zprávy SMS, nabízeného oznámení a hlasu pro výstrahu." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Volitelné Nakonfigurujte **Akce** , pokud chcete provést proaktivní akce a obdržet oznámení o události. Vyberte dostupný **typ akce** a potom vyberte **zkontrolovat + vytvořit**. 
   - Runbooky služeb automatizace
   - Azure Functions – pro provádění vlastního kódu řízeného událostmi bez serveru
   - ITSM – pro integraci s poskytovatelem služeb, jako je ServiceNow, vytvoření lístku
   - Aplikace logiky – pro složitější orchestraci pracovního postupu
   - Webhooky – pro aktivaci procesu v jiné službě

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Snímek obrazovky, který zobrazuje okno vytvořit skupinu akcí s fokusem v rozevíracím seznamu typ akce." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. V části **Podrobnosti pravidla výstrahy** zadejte název, popis, skupinu prostředků, do které chcete uložit pravidlo výstrahy, závažnost. Pak vyberte **vytvořit pravidlo výstrahy**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Snímek obrazovky, který zobrazuje podrobnosti pro pravidlo výstrahy."::: 
 
   Pravidlo výstrahy je viditelné a lze ho spravovat z Azure Portal.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Snímek obrazovky, který zobrazuje nové pravidlo upozornění v okně pravidla" lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Jakmile metrika dosáhne prahové hodnoty definované v pravidle výstrahy, je nabídka **výstrahy** aktualizována a zobrazena.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Snímek obrazovky, který zobrazuje výstrahu po dosažení definované prahové hodnoty." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   V závislosti na konfigurované skupině akcí obdržíte prostřednictvím nakonfigurovaného média oznámení. V našem příkladu jsme nakonfigurovali e-mail.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Snímek obrazovky Azure Monitor výstrahy s řetězcem chyby a událost data a času byla aktivována."::: 

## <a name="work-with-metrics"></a>Práce s metrikami

1. Z privátního cloudu řešení Azure VMware vyberte   >  **metriky** monitorování. Pak v rozevíracím seznamu vyberte požadovanou metriku.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Snímek obrazovky zobrazující okno metrik a zaměření na rozevírací seznam metriky" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Můžete změnit parametry diagramu, například **časový rozsah** nebo **časové rozlišení**. 

   Další možnosti jsou:
   - **Přechod k podrobnostem protokolů** a dotazování na data v souvisejícím pracovním prostoru Log Analytics
   - **Připnout tento diagram** na řídicí panel Azure pro usnadnění práce.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Snímek obrazovky zobrazující možnosti časového rozsahu a časové rozlišení pro metriku" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali pravidlo výstrahy pro privátní cloud vašeho řešení Azure VMware, budete možná chtít získat další informace o:
- [Metriky Azure Monitoru](/azure/azure-monitor/essentials/data-platform-metrics)
- [Upozornění služby Azure Monitor](/azure/azure-monitor/alerts/alerts-overview)
- [Skupiny akcí Azure](/azure/azure-monitor/alerts/action-groups)

Můžete také pokračovat v některém z dalších průvodců [Řešení Azure VMware](index.yml) .





