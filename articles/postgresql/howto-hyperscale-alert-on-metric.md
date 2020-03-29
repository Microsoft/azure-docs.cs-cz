---
title: Konfigurace výstrah – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k upozorněním metrik pro Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063146"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Nastavení výstrah pro Azure Database for PostgreSQL – Hyperscale (Citus) pomocí portálu Azure Portal

Tento článek ukazuje, jak nastavit azure databáze pro postgreSQL výstrahy pomocí portálu Azure. Můžete obdržet upozornění na základě metrik monitorování pro vaše [služby](concepts-hyperscale-monitoring.md) Azure.

Nastavíme výstrahu, která se aktivuje, když hodnota zadané metriky překročí prahovou hodnotu. Výstraha se aktivuje, když je podmínka poprvé splněna, a pokračuje v aktivaci později.

Výstrahu můžete nakonfigurovat tak, aby při aktivaci spustila následující akce:
* Odešlete e-mailová oznámení správci služby a spolusprávcům.
* Odešlete e-mail na další e-maily, které zadáte.
* Zavolejte webhook.

Informace o pravidlech výstrah můžete konfigurovat a získat pomocí:
* [Portál Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla výstrahy pro metriku z webu Azure Portal
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte Azure Database for PostgreSQL server, který chcete monitorovat.

2. V části **Monitorování** na postranním panelu vyberte **Výstrahy,** jak je znázorněno:

   ![Vybrat pravidla výstrahy](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Vyberte **Nové pravidlo výstrahy** (+ ikona).

4. Otevře se stránka **Vytvořit pravidlo,** jak je znázorněno níže. Vyplňte požadované informace:

   ![Přidat formulář upozornění na metriku](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. V části **Podmínka** vyberte **Přidat**.

6. Vyberte metriku ze seznamu signálů, na které chcete být upozorněni. V tomto příkladu vyberte "Procento úložiště".
   
   ![Vybrat metriku](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurace logiky výstrah:

    * **Operátor** (např. "Větší než")
    * **Prahová hodnota** (např. 85 procent)
    * **Rozlišovací schopnost agregace** musí být splněna před aktivací výstrahy (např. "Za posledních 30 minut")
    * a **Četnost hodnocení** (např. "1 minuta")
   
   Po dokončení vyberte **Hotovo.**

   ![Vybrat metriku](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. V části **Skupiny akcí** vyberte **Vytvořit nový** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář "Přidat skupinu akcí" s názvem, krátkým názvem, předplatným a skupinou prostředků.

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Konfigurace typu akce **E-mail/SMS/Push/Hlas.**
    
    Zvolte "E-mail role Správce prostředků Azure" chcete-li odesílat oznámení vlastníkům předplatného, přispěvatelům a čtenářům.
   
    Po dokončení vyberte **ok.**

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Zadejte název pravidla výstrahy, popis a závažnost.

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Chcete-li **výstrahu** vytvořit, vyberte vytvořit pravidlo výstrahy.

    Během několika minut je výstraha aktivní a aktivuje se, jak bylo popsáno dříve.

### <a name="managing-alerts"></a>Správa upozornění

Po vytvoření výstrahy ji můžete vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne relevantní pro tuto výstrahu.
* **Upravit** nebo **odstranit** pravidlo výstrahy.
* Pokud chcete výstrahu dočasně zastavit nebo obnovit příjem oznámení, **zakažte** nebo **povolte** výstrahu.

## <a name="suggested-alerts"></a>Navrhovaná upozornění

### <a name="disk-space"></a>Místo na disku

Monitorování a upozorňování je důležité pro každou produkční skupinu serverů Hyperscale (Citus). Podkladová databáze PostgreSQL vyžaduje pro správnou funkci volné místo na disku. Pokud se disk zaplní, uzel databázového serveru přejde do režimu offline a odmítne spustit, dokud nebude k dispozici místo. V tomto okamžiku vyžaduje žádost o podporu společnosti Microsoft k opravě situace.

Doporučujeme nastavit výstrahy místa na disku na každém uzlu v každé skupině serverů, a to i pro neprodukční použití. Výstrahy využití místa na disku poskytují předběžné upozornění potřebné k zásahu a udržení uzlů v pořádku. Nejlepších výsledků dosáhnete, pokud možno vyzkoušejte řadu výstrah s využitím 75 %, 85 % a 95 %. Procenta, která se mají vybrat, závisí na rychlosti přijím dat, protože rychlé přiřazování dat zaplní disk rychleji.

Jak se disk blíží svému limitu místa, vyzkoušejte tyto techniky, abyste získali více volného místa:

* Zkontrolujte zásady uchovávání dat. Pokud je to možné, přesuňte starší data do studeného úložiště.
* Zvažte [přidání uzlů](howto-hyperscale-scaling.md#add-worker-nodes) do skupiny serverů a vyvažování štřepů. Vyvažování distribuuje data mezi více počítačů.
* Zvažte [zvýšení kapacity](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) pracovních uzlů. Každý pracovník může mít až 2 TiB skladování. Přidání uzlů by však mělo být před změnami velikosti uzlů pokusem, protože přidání uzlů se dokončí rychleji.

### <a name="cpu-usage"></a>Využití procesoru

Monitorování využití procesoru je užitečné pro stanovení směrného plánu pro výkon. Můžete si například všimnout, že využití procesoru je obvykle kolem 40-60 %. Pokud se využití procesoru náhle začne vznášet kolem 95 %, můžete rozpoznat anomálii. Využití procesoru může odrážet organický růst, ale může také odhalit zbloudilý dotaz. Při vytváření výstrahy procesoru nastavte dlouhou granularitu agregace, abyste zachytili prodloužené zvýšení a ignorovali momentální špičky.

## <a name="next-steps"></a>Další kroky
* Další informace o [konfiguraci webových háků v výstrahách](../azure-monitor/platform/alerts-webhooks.md).
* Získejte [přehled o kolekci metrik,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) abyste se ujistili, že vaše služba je dostupná a citlivá.
