---
title: Jak monitorovat účet Azure Storage v Azure Portal | Microsoft Docs
description: Naučte se monitorovat účet úložiště v Azure pomocí Azure Portal a Analýza úložiště Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 4c78938e2f70342822a4009ed9195196ca486fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034664"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorování účtu úložiště na webu Azure Portal

[Analýza úložiště Azure](storage-analytics.md) poskytuje metriky pro všechny služby úložiště a protokoly objektů blob, front a tabulek. Pomocí [Azure Portal](https://portal.azure.com) můžete nakonfigurovat, které metriky a protokoly se budou zaznamenávat pro váš účet, a nakonfigurovat grafy, které poskytují vizuální reprezentace dat metrik. 

Doporučujeme, abyste zkontrolovali [Azure monitor pro úložiště](../../azure-monitor/insights/storage-insights-overview.md) (Preview). Jedná se o funkci Azure Monitor, která nabízí ucelený přehled o vašich Azure Storagech účtech tím, že poskytuje jednotný přehled o výkonu, kapacitě a dostupnosti služby Azure Storage Services. Nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tyto metriky můžete okamžitě zobrazit z předem definovaných interaktivních grafů a dalších vizualizací, které jsou v ní obsažené.

> [!NOTE]
> Existují náklady spojené s zkoumáním dat monitorování v Azure Portal. Další informace najdete v tématu [Analýza úložiště](storage-analytics.md).
>
> Soubory Azure aktuálně podporuje Analýza úložiště metriky, ale zatím nepodporují protokolování.
>
> Účty úložiště blob bloku úrovně Premium nepodporují analytické metriky úložiště, ale podporují protokolování. Protokolování můžete povolit programově prostřednictvím REST API nebo klientské knihovny. Pokud chcete zobrazit metriky s účty služby Blob Storage BLOB služby Premium Performance, zvažte použití [Azure Storage metriky v Azure monitor](storage-metrics-in-azure-monitor.md).
>
> Podrobný návod k používání Analýza úložiště a dalších nástrojů k identifikaci, diagnostice a odstraňování potíží souvisejících s Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurace monitorování pro účet úložiště

1. V [Azure Portal](https://portal.azure.com)vyberte **účty úložiště**a potom název účtu úložiště otevřete řídicí panel účtu.
1. V části **monitorování** okna nabídky vyberte **Diagnostika** .

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Vyberte **typ** dat metriky pro každou **službu** , kterou chcete monitorovat, a **zásady uchovávání** dat. Monitorování můžete také zakázat nastavením **stav** na **vypnuto**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Chcete-li nastavit zásady uchovávání dat, přesuňte posuvník **Doba uchovávání (dny)** nebo zadejte počet dní, po které se mají data uchovávat, od 1 do 365. Výchozí hodnota pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud neexistují žádné zásady uchovávání informací, je třeba odstranit data monitorování.

   > [!WARNING]
   > Účtují se vám poplatky za ruční odstranění dat metriky. Data zastaralých analýz (data starší než vaše zásady uchovávání) se systémem odpouštějí bez jakýchkoli nákladů. Doporučujeme nastavit zásady uchovávání informací v závislosti na tom, jak dlouho chcete uchovávat data analýzy úložiště pro váš účet. Další informace najdete v tématu [fakturace na metrikách úložiště](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

1. Po dokončení konfigurace monitorování vyberte **Uložit**.

Výchozí sada metrik se zobrazí v grafech v okně účet úložiště a také na jednotlivých oknech služby (objekt blob, fronta, tabulka a soubor). Jakmile u služby povolíte metriky, může trvat až hodinu, než se data zobrazí ve svých grafech. Můžete vybrat **Upravit** v libovolném grafu metriky a nakonfigurovat, které metriky se zobrazí v grafu.

Shromažďování a protokolování metrik můžete zakázat nastavením **stav** na **vypnuto**.

> [!NOTE]
> Azure Storage využívá [úložiště tabulek](storage-introduction.md#table-storage) k ukládání metrik pro váš účet úložiště a ukládá metriky v tabulkách ve vašem účtu. Další informace najdete v tématech: [Jak jsou uloženy metriky](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Přizpůsobení grafů metrik

Pomocí následujícího postupu můžete zvolit metriky úložiště, které se mají zobrazit v grafu metrik.

1. Začněte zobrazením grafu metriky úložiště v Azure Portal. Grafy můžete najít v okně **účet úložiště** a v okně **metriky** pro jednotlivé služby (objekt blob, fronta, tabulka, soubor).

   V tomto příkladu používáme následující graf, který se zobrazí v okně **účet úložiště**:

   ![Výběr grafu v Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Graf upravíte Kliknutím kamkoli v grafu.

1. V dalším kroku vyberte **časový rozsah** metrik, který se má zobrazit v grafu, a **službu** (objekt blob, frontu, tabulku, soubor), jejíž metriky chcete zobrazit. Tady se vyberou metriky v minulém týdnu, které se mají zobrazit pro službu BLOB Service:

   ![Časový rozsah a výběr služby v okně Upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Vyberte jednotlivé **metriky** , které chcete zobrazit v grafu, a pak klikněte na **OK**.

   ![Výběr jednotlivých metrik v okně Upravit graf](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Nastavení grafu nemá vliv na shromažďování, agregaci ani ukládání dat monitorování v účtu úložiště.

### <a name="metrics-availability-in-charts"></a>Dostupnost metrik v grafech

Seznam dostupných metrik se mění podle toho, kterou službu jste zvolili v rozevíracím seznamu, a typem jednotky pro graf, který upravujete. Můžete například vybrat procentuální metriky jako *PercentNetworkError* a *PercentThrottlingError* pouze v případě, že upravujete graf zobrazující jednotky v procentech:

![Graf procento chyb žádosti v Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Řešení metrik

Metriky, které jste vybrali v části **Diagnostika** , určují řešení metrik, které jsou k dispozici pro váš účet:

* **Agregované** monitorování poskytuje metriky, jako je příchozí/odchozí, dostupnost, latence a procento úspěšnosti. Tyto metriky jsou agregované ze služeb blob, Table, File a Queue.
* **Každé rozhraní API** poskytuje přesnější řešení s metrikami, které jsou dostupné pro jednotlivé operace úložiště, a navíc k agregacím na úrovni služby.

## <a name="configure-metrics-alerts"></a>Konfigurace výstrah metrik

Můžete vytvářet výstrahy, které vás upozorní, když budou dosaženy prahové hodnoty pro metriky prostředků úložiště.

1. Chcete-li otevřít okno **pravidla výstrah**, přejděte dolů k části **monitorování** v okně **nabídky** a vyberte možnost **výstrahy (klasické)**.
2. Výběrem **Přidat upozornění metriky (Classic)** otevřete okno **Přidat pravidlo výstrahy** .
3. Zadejte **název** a **Popis** nového pravidla výstrahy.
4. Vyberte **metriku** , pro kterou chcete přidat výstrahu, **podmínku**upozornění a **prahovou hodnotu**. Typ prahové jednotky se změní v závislosti na zvolené metrikě. Například "Count" je typ jednotky pro *ContainerCount*, zatímco jednotka pro metriku *PercentNetworkError* je procento.
5. Vyberte **období**. Metrika, která dosahuje nebo překračuje prahovou hodnotu v období aktivuje výstrahu.
6. Volitelné Konfigurace oznámení **e-mailem** a **webhookem** Další informace o webhookech najdete v tématu [Konfigurace Webhooku na upozornění metriky Azure](../../azure-monitor/platform/alerts-webhooks.md). Pokud neprovedete konfiguraci oznámení e-mailu nebo Webhooku, výstrahy se zobrazí jenom v Azure Portal.

![Okno Přidat pravidlo výstrahy do Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Přidání grafů metrik na řídicí panel portálu

Do řídicího panelu portálu můžete přidat grafy Azure Storage metriky pro libovolný účet úložiště.

1. Vyberte možnost **Upravit řídicí panel** při prohlížení řídicího panelu v [Azure Portal](https://portal.azure.com).
1. V **galerii dlaždic**vyberte **najít dlaždice podle**  >  **typu**.
1. Vyberte **typ**  >  **účty úložiště**.
1. V části **prostředky**vyberte účet úložiště, jehož metriky chcete přidat na řídicí panel.
1. Vyberte **Categories**možnost  >  **monitorování**kategorií.
1. Přetáhněte dlaždici grafu na řídicí panel, aby se zobrazila metrika, kterou chcete zobrazit. Opakujte pro všechny metriky, které byste chtěli zobrazit na řídicím panelu. Na následujícím obrázku je jako příklad zvýrazněný graf objekty blob – celkový počet požadavků, ale všechny grafy jsou k dispozici pro umístění na řídicím panelu.

   ![Galerie dlaždic v Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Až skončíte s přidáváním grafů, vyberte **Hotovo přizpůsobení** u horní části řídicího panelu.

Po přidání grafů na řídicí panel je můžete dále upravit, jak je popsáno v tématu přizpůsobení grafů metrik.

## <a name="configure-logging"></a>Konfigurovat protokolování

Můžete dát Azure Storage k uložení protokolů diagnostiky pro požadavky na čtení, zápis a odstranění pro služby blob, Table a Queue. Zásady uchovávání dat, které nastavíte, se vztahují také na tyto protokoly.

> [!NOTE]
> Soubory Azure aktuálně podporuje Analýza úložiště metriky, ale zatím nepodporují protokolování.
>

1. V [Azure Portal](https://portal.azure.com)vyberte **účty úložiště**a potom název účtu úložiště otevřete okno účtu úložiště.
1. V části **monitorování (Classic)** v okně nabídky vyberte **nastavení diagnostiky (Classic** ).

    ![Položka nabídky Diagnostika v části sledování v Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Zajistěte, aby byl **stav** nastaven **na zapnuto**, a vyberte **služby** , pro které chcete povolit protokolování.

    ![Nakonfigurujte protokolování v Azure Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klikněte na **Uložit**.

Diagnostické protokoly se ukládají do kontejneru objektů BLOB s názvem *$logs* ve vašem účtu úložiště. Data protokolu můžete zobrazit pomocí Průzkumníka služby Storage, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com), nebo programově pomocí klientské knihovny pro úložiště nebo PowerShellu.

Informace o přístupu k kontejneru $logs najdete v tématu [protokolování služby Storage Analytics](storage-analytics-logging.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [metrikách, protokolování a fakturaci](storage-analytics.md) pro analýza úložiště.
