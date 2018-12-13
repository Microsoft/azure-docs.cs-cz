---
title: Jak monitorovat účet služby Azure Storage | Dokumentace Microsoftu
description: Další informace o monitorování účtu úložiště v Azure pomocí webu Azure portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: f7b73fa4d1f596e0221c2cec3c6c7417ceb767a4
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275682"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorování účtu úložiště na webu Azure Portal

[Azure Storage Analytics](../storage-analytics.md) poskytuje metriky pro všechny služby storage a protokoly pro objekty BLOB, fronty a tabulky. Můžete použít [webu Azure portal](https://portal.azure.com) ke konfiguraci, protokoly a metriky, které jsou zaznamenány pro svůj účet a konfigurace grafů, které poskytují vizuální reprezentace vašich dat metriky.

> [!NOTE]
> Existují náklady spojené s zkoumání dat monitorování na webu Azure Portal. Další informace najdete v tématu [analytika úložiště a fakturace](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Služba soubory Azure aktuálně podporuje metrik Storage Analytics, ale zatím nepodporuje protokolování.
> 
> Podrobný průvodce k identifikaci, diagnostice a řešit problémy související s Azure Storage pomocí Storage Analytics a další nástroje, najdete v části [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurace monitorování pro účet úložiště

1. V [webu Azure portal](https://portal.azure.com)vyberte **účty úložiště**, klikněte název účtu úložiště otevřete řídicí panel účtu.
1. Vyberte **diagnostiky** v **monitorování** nabídky okna.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Vyberte **typ** dat metrik pro každou **služby** chcete monitorovat a **zásady uchovávání informací** pro data. Můžete také zakázat monitorování tak, že nastavíte **stav** k **vypnout**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Chcete-li nastavit zásady uchovávání dat, přesuňte **uchování (dny)** posuvník nebo zadat počet dnů od data, pokud chcete zachovat, od 1 do 365. Výchozí pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud není žádná zásada uchovávání informací, je jenom na vás odstranit data sledování.

   > [!WARNING]
   > Bude se vám účtovat, když ručně odstraňte data metrik. Zastaralé analytických dat (data starší než zásady uchovávání informací) je odstraněn systémem bez poplatků. Doporučujeme nastavit zásady uchovávání informací podle jak dlouho chcete uchovávat analytická data úložiště pro váš účet. Zobrazit [co poplatky se vám účtovat potřeba povolit metriky úložiště?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) Další informace.
   >

1. Jakmile dokončíte konfiguraci monitorování, vyberte **Uložit**.

Výchozí sada metriky se zobrazí v grafech v okně účtu úložiště, stejně jako okna konkrétní službu (objektu blob, fronty, tabulky a souboru). Po povolení metriky pro službu, může trvat až hodinu, se zobrazí v grafech jeho data. Můžete vybrat **upravit** na libovolný graf metrik pro [konfigurace, které metriky](#how-to-customize-metrics-charts) jsou zobrazeny v grafu.

Shromažďování metrik a protokolování lze zakázat nastavením **stav** k **vypnout**.

> [!NOTE]
> Azure Storage využívá [tabulky úložiště](../common/storage-introduction.md#table-storage) ukládat metriky pro účet úložiště a úložiště metriky v tabulkách ve vašem účtu. Další informace najdete v tématu. [Jak jsou uloženy metriky](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Přizpůsobení grafů metrik

Pomocí následujícího postupu vyberte, které metriky úložiště, chcete-li zobrazit v grafu metrik. 

1. Začněte tím, že zobrazení grafu metrik úložiště na webu Azure Portal. Grafy můžete najít na **okno účet úložiště** a **metriky** okna pro jednotlivé služby (objekt blob, fronty, tabulky, souboru).

   V tomto příkladu používá následující graf, který se zobrazí na **okno účet úložiště**:

   ![Výběr grafu na webu Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klikněte na libovolné místo v grafu můžete upravit graf.

1. V dalším kroku vyberte **časový rozsah** metrik pro zobrazení v grafu a **služby** (objekt blob, fronty, tabulky, souboru) jehož metriky, které chcete zobrazit. Tady jsou vybrané metriky minulý týden má být zobrazen pro službu blob service:

   ![Výběr časového rozsahu a služby v okně upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Vyberte jednotlivé **metriky** měly jako zobrazených v grafu, pak klikněte na tlačítko **OK**.

   ![Výběr jednotlivých metrik v okně upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Nastavení grafu nemají vliv na kolekce, agregace nebo úložiště dat v účtu úložiště monitorování.

### <a name="metrics-availability-in-charts"></a>Metriky dostupnosti v grafech

Seznam dostupných metrik změn podle služby, které jste zvolili v rozevíracího seznamu a typ jednotky grafu upravujete. Například můžete vybrat procento metriky, jako je *PercentNetworkError* a *PercentThrottlingError* pouze v případě, že upravujete graf, který zobrazuje jednotky v procentech:

![Žádost o chybě procento grafu na webu Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metriky řešení

Metriky, které jste vybrali v **diagnostiky** určuje řešení, která jsou k dispozici pro váš účet:

* **Agregační** monitorování poskytuje metriky, jako je procentuální podíl příchozí a odchozí transakce, dostupnosti, latence a úspěch. Tyto metriky se agregují z objektu blob, tabulky, souboru a služba front.
* **Rozhraní API** poskytuje lepší řešení s metrikami, které jsou k dispozici pro úložiště pro jednotlivé operace navíc agregace úrovně služeb.

## <a name="configure-metrics-alerts"></a>Konfigurace upozornění metriky

Můžete vytvářet výstrahy, které vás upozorní, pokud bylo dosaženo prahové hodnoty pro metriky prostředků úložiště.

1. Chcete-li otevřít **okno pravidla upozornění**, přejděte dolů k položce **monitorování** část **okně s nabídkou** a vyberte **upozornění (klasická)**.
2. Vyberte **přidat upozornění metriky (klasické)** otevřít **přidání pravidla výstrahy** okno
3. Zadejte **název** a **popis** pro nové pravidlo výstrah.
4. Vyberte **metrika** pro který chcete přidat upozornění, oznámení **podmínku**a **prahová hodnota**. Prahová hodnota jednotku zadejte mění v závislosti na metriku, kterou jste zvolili. Například "počet" je typ jednotky pro *ContainerCount*, zatímco jednotky pro *PercentNetworkError* metrika je procento.
5. Vyberte **období**. Metriky, které od nebo prahovou hodnotu překročit v období nebo aktivovat upozornění.
6. (Volitelné) Konfigurace **e-mailu** a **Webhooku** oznámení. Další informace o webhooků najdete v tématu [konfigurace webhooku v upozornění Azure metriky](../../azure-monitor/platform/alerts-webhooks.md). Pokud nenakonfigurujete oznámení e-mailu nebo webhooku, upozornění se zobrazí pouze na webu Azure portal.

!['Přidání pravidla výstrahy' okna na webu Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Přidat grafů metrik k řídicímu panelu portálu

Grafy metrik Azure Storage pro všechny vaše účty úložiště můžete přidat na řídicí panel portálu.

1. Kliknutím vyberte **upravit řídicí panel** během zobrazení řídicího panelu ve [webu Azure portal](https://portal.azure.com).
1. V **Galerie dlaždic**vyberte **najít dlaždice podle** > **typ**.
1. Vyberte **typ** > **účty úložiště**.
1. V **prostředky**, vyberte účet úložiště, jehož metriky, které chcete přidat na řídicí panel.
1. Vyberte **kategorie** > **monitorování**.
1. Přetáhněte myší na graf dlaždici na řídicím panelu pro metriku, kterou chcete zobrazit. Opakujte pro všechny metriky, které byste chtěli zobrazeny v řídicím panelu. Na následujícím obrázku grafu "Objekty BLOB – celkový počet požadavků" je zvýrazněn jako příklad, ale všechny grafy jsou k dispozici pro umístění na řídicím panelu.

   ![Galerie dlaždic na webu Azure portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Vyberte **přizpůsobení dokončeno** v horní části řídicího panelu po dokončení přidávání grafů.

Po přidání grafů na řídicí panel, můžete dále přizpůsobit jejich jak je popsáno v [přizpůsobení grafů metrik](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Konfigurace protokolování

Můžete dát pokyn uložit diagnostické protokoly pro čtení, zápisu a odstranit zastaralé požadavky pro objekt blob, tabulky a fronty služby Azure Storage. Zásad uchovávání dat, které nastavíte, platí také pro tyto protokoly.

> [!NOTE]
> Služba soubory Azure aktuálně podporuje metrik Storage Analytics, ale zatím nepodporuje protokolování.
>

1. V [webu Azure portal](https://portal.azure.com)vyberte **účty úložiště**, klikněte název účtu úložiště a otevřete tak okno účet úložiště.
1. Vyberte **diagnostiky** v **monitorování** nabídky okna.

    ![Položky nabídky Diagnostika v části monitorování na webu Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)
    
1. Zajištění **stav** je nastavena na **na**a vyberte **služby** pro které chcete povolit protokolování.

    ![Konfigurace protokolování na portálu Azure portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klikněte na **Uložit**.

Diagnostické protokoly se ukládají do kontejneru objektů blob s názvem *$logs* ve vašem účtu úložiště. Můžete zobrazit pomocí Průzkumníka služby storage jako data protokolu [Microsoft Storage Explorer](http://storageexplorer.com), nebo prostřednictvím kódu programu pomocí klientské knihovny pro úložiště nebo Powershellu.

Informace o přístupu k $logs kontejneru najdete v tématu [povolení protokolování úložiště a přístup k datům protokolů](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Další postup

* Najít další podrobnosti o [metriky, protokolování a fakturaci](../storage-analytics.md) pro Storage Analytics.
* [Povolení služby Azure Storage metriky a zobrazení dat metrik](../storage-enable-and-view-metrics.md) pomocí prostředí PowerShell a programově pomocí jazyka C#.
