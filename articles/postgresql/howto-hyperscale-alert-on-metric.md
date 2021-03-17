---
title: Konfigurace výstrah – Citus (-Scale) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for PostgreSQL-Citus (škálování na více procesorů).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577084"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Použití Azure Portal k nastavení výstrah pro metriky pro Azure Database for PostgreSQL – Citus (škálování)

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for PostgreSQL pomocí Azure Portal. Můžete obdržet upozornění na základě [metrik monitorování](concepts-hyperscale-monitoring.md) vašich služeb Azure.

Nastavíme výstrahu, která se aktivuje, když hodnota zadané metriky přechází z prahové hodnoty. Výstraha se aktivuje při prvním splnění podmínky a dále se aktivuje.

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:
* Odesílat e-mailová oznámení správcům služeb a spolusprávcům.
* Odešlete e-mail na další e-maily, které zadáte.
* Zavolejte Webhook.

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla upozornění na metriku na webu Azure Portal
1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL, který chcete monitorovat.

2. V části **monitorování** na bočním panelu vyberte **výstrahy** , jak je znázorněno níže:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Vybrat pravidla výstrah":::

3. Vyberte **nové pravidlo výstrahy** (+ ikona).

4. Otevře se stránka **vytvořit pravidlo** , jak je znázorněno níže. Vyplňte požadované informace:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Přidat formulář upozornění metriky":::

5. V části **Podmínka** vyberte **Přidat**.

6. Vyberte metriku ze seznamu signálů, na kterých se má upozornit. V tomto příkladu vyberte "úložiště v procentech".
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Snímek obrazovky se zobrazí na stránce Konfigurovat logiku signálu, kde můžete zobrazit několik signálů.":::

7. Konfigurace logiky výstrah:

    * – **Operátor** (ex. "Větší než")
    * **Prahová hodnota** (např. 85 procent)
    * Časový rozsah **členitosti agregace** , po kterou musí být splněno pravidlo metriky před triggery výstrahy (např. "Za posledních 30 minut")
    * a **frekvence vyhodnocení** (např. 1 minuta)
   
   Po dokončení vyberte **Hotovo** .

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Snímek obrazovky se zobrazí v podokně, kde můžete nakonfigurovat logiku výstrah.":::

8. V části **skupiny akcí** vyberte **vytvořit novou** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář přidat skupinu akcí s názvem, krátkým názvem, předplatným a skupinou prostředků.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Snímek obrazovky se zobrazí ve formuláři přidat skupinu akcí, kde můžete zadat popsané hodnoty.":::

10. Nakonfigurujte typ akce **e-mail/SMS/Push/Voice** .
    
    Pokud chcete odesílat oznámení vlastníkům, přispěvatelům a čtenářům předplatného, vyberte Azure Resource Manager role e-mailu.
   
    Po dokončení vyberte **OK** .

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Snímek obrazovky se zobrazí v podokně E-mail/S M/s/hlasové oznámení/hlas.":::

11. Zadejte název, popis a závažnost pravidla výstrahy.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Snímek obrazovky se zobrazí v podokně Podrobnosti výstrahy."::: 

12. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

### <a name="managing-alerts"></a>Správa upozornění

Po vytvoření výstrahy ji můžete vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.

## <a name="suggested-alerts"></a>Navrhované výstrahy

### <a name="disk-space"></a>Místo na disku

Monitorování a upozorňování je důležité pro každou skupinu serverů Citus (produkčního) na úrovni služby. Podkladová databáze PostgreSQL vyžaduje pro správné fungování volné místo na disku. Pokud se disk zaplní, uzel databázového serveru přejde do režimu offline a zamítne spustit, dokud nebude dostupné místo. V tomto okamžiku vyžaduje, aby žádost o podporu společnosti Microsoft tuto situaci opravila.

Doporučujeme nastavit upozornění na místo na disku pro každý uzel v každé skupině serverů, a to i v případě neprodukčního využití. Výstrahy využití místa na disku poskytují předběžné upozornění potřebné k tomu, aby byly uzly v dobrém stavu. Nejlepších výsledků dosáhnete, když si vyzkoušíte řadu výstrah na 75%, 85% a 95% využití. Procentuální hodnoty, které se mají zvolit, závisí na rychlosti příjmu dat, protože rychlé přijímání dat vyplní disk rychleji.

Vzhledem k dosažení limitu místa na disku Vyzkoušejte tyto techniky, abyste získali více volného místa:

* Zkontrolujte zásady uchovávání dat. Pokud je to možné, přesuňte starší data do chladírenského úložiště.
* Zvažte [Přidání uzlů](howto-hyperscale-scale-grow.md#add-worker-nodes) do skupiny serverů a nové vyrovnávání horizontálních oddílů. Nové vyrovnávání distribuuje data napříč více počítači.
* Zvažte [větší kapacitu](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) pracovních uzlů. Každý pracovní proces může mít až 2 TiB úložiště. Před změnou velikosti uzlů by se ale mělo provést pokus o přidání uzlů, protože přidávání uzlů se dokončí rychleji.

### <a name="cpu-usage"></a>Využití procesoru

Monitorování využití procesoru je užitečné k navázání standardních hodnot výkonu. Můžete si například všimnout, že využití CPU je obvykle přibližně 40-60%. Pokud využití CPU náhle začne najeďte kolem 95%, můžete rozpoznat anomálii. Využití CPU může odrážet ekologický růst, ale může také odhalit neosamocený dotaz. Při vytváření upozornění na procesor nastavte členitost agregované členitosti na průběžné zvyšování úrovně a ignorovat momentické špičky.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/alerts/alerts-webhooks.md).
* Získejte [Přehled o kolekci metrik](../azure-monitor/data-platform.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.
