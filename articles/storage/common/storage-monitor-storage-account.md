---
title: Jak monitorovat účet Azure Storage na webu Azure Portal | Dokumenty společnosti Microsoft
description: Zjistěte, jak monitorovat účet úložiště v Azure pomocí portálu Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268285"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorování účtu úložiště na webu Azure Portal

[Azure Storage Analytics](storage-analytics.md) poskytuje metriky pro všechny služby úložiště a protokoly pro objekty BLOB, fronty a tabulky. Na webu [Azure Portal](https://portal.azure.com) můžete nakonfigurovat metriky a protokoly, které se zaznamenávají pro váš účet, a nakonfigurovat grafy, které poskytují vizuální znázornění dat metrik. 

Doporučujeme zkontrolovat [Azure Monitor pro úložiště](../../azure-monitor/insights/storage-insights-overview.md) (preview). Jedná se o funkci Azure Monitoru, která nabízí komplexní monitorování vašich účtů Azure Storage tím, že poskytuje jednotné zobrazení výkonu, kapacity a dostupnosti služeb Azure Storage. Nevyžaduje, abyste nic povolovali nebo nakonfigurovali, a tyto metriky můžete okamžitě zobrazit z předdefinovaných interaktivních grafů a dalších vizualizací.

> [!NOTE]
> Existují náklady spojené s zkoumáním dat monitorování na webu Azure Portal. Další informace naleznete v [tématu Storage Analytics](storage-analytics.md).
>
> Azure Files aktuálně podporuje metriky Storage Analytics, ale zatím nepodporuje protokolování.
>
> Účty úložiště objektů blob s prémiovým blokem výkonu nepodporují metriky Storage Analytic, ale podporují protokolování. Protokolování můžete povolit programově prostřednictvím rozhraní REST API nebo klientské knihovny. Pokud chcete zobrazit metriky s účty úložiště objektů blob s virtuálním objektem výkonu s výkonem, zvažte použití [metrik úložiště Azure ve službě Azure Monitor](storage-metrics-in-azure-monitor.md).
>
> Podrobný průvodce používáním služby Storage Analytics a dalších nástrojů k identifikaci, diagnostice a řešení problémů souvisejících s úložištěm Azure najdete v tématu [Sledování, diagnostika a řešení potíží s úložištěm Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurace monitorování pro účet úložiště

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Účty úložiště**a pak název účtu úložiště otevřete řídicí panel účtu.
1. V části **MONITOROVÁNÍ** v okně nabídky vyberte **Diagnostika.**

    ![Možnosti monitorování](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Vyberte **typ** dat metrik pro každou **službu,** kterou chcete sledovat, a **zásady uchovávání dat.** Monitorování můžete také zakázat nastavením **funkce Stav** na **Vypnuto**.

    ![Možnosti monitorování](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Chcete-li nastavit zásady uchovávání dat, přesuňte posuvník **Uchovávání informací (dny)** nebo zadejte počet dní, po které chcete data uchovat, od 1 do 365. Výchozí hodnota pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nulu. Pokud neexistuje žádné zásady uchovávání informací, je na vás odstranit data monitorování.

   > [!WARNING]
   > Poplatky se vám účtují při ručním odstranění dat metrik. Zastaralá analytická data (data starší než vaše zásady uchovávání informací) jsou systémem zdarma smazána. Doporučujeme nastavit zásady uchovávání informací na základě toho, jak dlouho chcete uchovávat analytická data úložiště pro svůj účet. Další informace najdete [v tématu Fakturace na metrikách úložiště.](storage-analytics-metrics.md#billing-on-storage-metrics)
   >

1. Po dokončení konfigurace monitorování vyberte **uložit**.

Výchozí sada metrik se zobrazí v grafech v okně účtu úložiště a také v jednotlivých okněch služby (objekt blob, fronta, tabulka a soubor). Jakmile aktivujete metriky pro službu, může trvat až hodinu, než se data zobrazí v grafech. Můžete vybrat **upravit** na libovolném metrickém grafu a nakonfigurovat, které metriky se v grafu zobrazí.

Shromažďování metrik a protokolování můžete zakázat nastavením **funkce Stav** na **Vypnuto**.

> [!NOTE]
> Azure Storage používá [úložiště tabulek](storage-introduction.md#table-storage) k ukládání metrik pro váš účet úložiště a ukládá metriky v tabulkách ve vašem účtu. Další informace najdete v tématech: [Jak jsou metriky uloženy](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Přizpůsobení grafů metrik

Pomocí následujícího postupu zvolte, které metriky úložiště chcete zobrazit v grafu metrik.

1. Začněte zobrazením grafu metriky úložiště na webu Azure Portal. Grafy najdete v **okně účtu úložiště** a v okně **Metriky** pro jednotlivé služby (objekt blob, fronta, tabulka, soubor).

   V tomto příkladu použije následující graf, který se zobrazí v **okně účtu úložiště**:

   ![Výběr grafu na webu Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Chcete-li graf upravit, klepněte na libovolné místo v grafu.

1. Dále vyberte **časový rozsah** metrik, které se mají zobrazit v grafu, a **službu** (objekt blob, fronta, tabulka, soubor), jejíž metriky chcete zobrazit. Metriky z minulého týdne jsou vybrány k zobrazení pro službu objektů blob:

   ![Časový rozsah a výběr služeb v okně Upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Vyberte jednotlivé **metriky, které** chcete zobrazit v grafu, a klikněte na **OK**.

   ![Výběr jednotlivých metrik v okně Upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Nastavení grafu nemají vliv na shromažďování, agregaci nebo ukládání dat monitorování v účtu úložiště.

### <a name="metrics-availability-in-charts"></a>Dostupnost metrik v grafech

Seznam dostupných metrik se mění na základě služby, kterou jste v rozevíracím seznamu zvolili, a podle typu jednotky upravovaného grafu. Můžete například vybrat procentuální metriky, jako *jsou PercentNetworkError* a *PercentThrottlingError,* pouze pokud upravujete graf, který zobrazuje jednotky v procentech:

![Žádost o graf procenta chyb na webu Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Rozlišení metrik

Metriky, které jste vybrali v **části Diagnostika,** určují rozlišení metrik, které jsou k dispozici pro váš účet:

* **Souhrnné** monitorování poskytuje metriky, jako je příchozí přenos dat/odchozí přenos dat, dostupnost, latence a procento úspěšnosti. Tyto metriky jsou agregovány ze služeb objektů blob, tabulky, souborů a fronty.
* **Na rozhraní API** poskytuje jemnější rozlišení, s metriky k dispozici pro jednotlivé operace úložiště, kromě agregace na úrovni služeb.

## <a name="configure-metrics-alerts"></a>Konfigurace upozornění na metriky

Můžete vytvořit výstrahy, které vás upozorní na dosažení prahových hodnot pro metriky prostředků úložiště.

1. Chcete-li otevřít **okno Pravidla výstrahy**, přejděte dolů do části **MONITOROVÁNÍ** v **okně Menu** a vyberte **Výstrahy (klasické).**
2. Výběrem **možnosti Přidat upozornění na metriku (klasické)** otevřete okno **Přidat pravidlo výstrahy.**
3. Zadejte **název** a **popis** nového pravidla výstrahy.
4. Vyberte **metriku,** pro kterou chcete přidat výstrahu, **podmínku**výstrahy a **prahovou hodnotu**. Typ jednotky prahová hodnota se mění v závislosti na zvolené metrice. Například "count" je typ jednotky pro *ContainerCount*, zatímco jednotka pro metriku *PercentNetworkError* je procento.
5. Vyberte **období**. Metriky, které během období dosáhnou nebo překročí prahovou hodnotu, spustí výstrahu.
6. (Nepovinné) Konfigurace **e-mailových** a **webhookových** oznámení. Další informace o webhooky, [najdete v tématu Konfigurace webhooku na upozornění metriky Azure](../../azure-monitor/platform/alerts-webhooks.md). Pokud nenakonfigurujete e-mailová oznámení nebo oznámení webhooku, výstrahy se zobrazí jenom na webu Azure Portal.

![Okno "Přidat pravidlo výstrahy" na webu Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Přidání grafů metrik na řídicí panel portálu

Na řídicí panel portálu můžete přidat grafy metrik Azure Storage pro libovolné účty úložiště.

1. Při zobrazení řídicího **panelu** na [webu Azure Portal](https://portal.azure.com)vyberte klikněte na Upravit řídicí panel .
1. V **galerii dlaždic**vyberte **Najít dlaždice podle** > **typu**.
1. Vyberte **typ** > **úložiště účtů**.
1. V **části Zdroje**vyberte účet úložiště, jehož metriky chcete přidat na řídicí panel.
1. Vyberte**možnost Sledování** **kategorií** > .
1. Přetáhněte dlaždici grafu na řídicí panel pro metriku, kterou chcete zobrazit. Tento postup opakujte pro všechny metriky, které chcete zobrazit na řídicím panelu. Na následujícím obrázku je jako příklad zvýrazněn graf "Objekty blobů – celkový počet požadavků", ale všechny grafy jsou k dispozici pro umístění na řídicím panelu.

   ![Galerie dlaždic na webu Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Až dopřidáváte grafy, vyberte **Hotovo přizpůsobení** v horní části řídicího panelu.

Po přidání grafů na řídicí panel je můžete dále přizpůsobit, jak je popsáno v tématu Přizpůsobení grafů metrik.

## <a name="configure-logging"></a>Konfigurace protokolování

Azure Storage můžete dát pokyn k uložení protokolů diagnostiky pro čtení, zápis a odstranění požadavků na služby objektů blob, tabulky a fronty. Zásady uchovávání dat, které nastavíte, platí také pro tyto protokoly.

> [!NOTE]
> Azure Files aktuálně podporuje metriky Storage Analytics, ale zatím nepodporuje protokolování.
>

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Účty úložiště**a pak název účtu úložiště otevřete okno účtu úložiště.
1. V yberte **Nastavení diagnostiky (klasické)** v části **Monitorování (klasické)** v okně nabídky.

    ![Diagnostika položky nabídky v části MONITOROVÁNÍ na webu Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Ujistěte **se,** že je stav nastaven **na Zapnuto**, a vyberte **služby,** pro které chcete protokolování povolit.

    ![Nakonfigurujte protokolování na webu Azure Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klikněte na **Uložit**.

Protokoly diagnostiky jsou uloženy v kontejneru objektů blob s názvem *$logs* v účtu úložiště. Data protokolu můžete zobrazit pomocí průzkumníka úložiště, jako je [Průzkumník úložiště Microsoft](https://storageexplorer.com), nebo programově pomocí knihovny klienta úložiště nebo prostředí PowerShell.

Informace o přístupu ke kontejneru $logs naleznete v [tématu Protokolování analýzy úložiště](storage-analytics-logging.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další podrobnosti o [metrikách, protokolování a fakturaci](storage-analytics.md) pro Storage Analytics.
