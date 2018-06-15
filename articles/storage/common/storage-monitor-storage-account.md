---
title: Postup sledování účtu Azure Storage | Microsoft Docs
description: Naučte se monitorovat účet úložiště v Azure pomocí portálu Azure.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: ffc7d46bbfa4db47a47e416c395efdfc451cadc1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322909"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorování účtu úložiště na portálu Azure

[Azure Storage Analytics](../storage-analytics.md) poskytuje metriky pro všechny služby úložiště a protokoly pro objekty BLOB, fronty a tabulky. Můžete použít [portál Azure](https://portal.azure.com) ke konfiguraci, protokoly a metriky, které se zaznamenávají pro váš účet a konfigurace grafů, které poskytují vizuální reprezentace vašich dat metriky.

> [!NOTE]
> Existují náklady spojené s zkoumání dat monitorování na portálu Azure. Další informace najdete v tématu [Storage Analytics a fakturace](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Soubory Azure aktuálně podporuje metriky analytika úložiště, ale zatím nepodporuje protokolování.
> 
> Podrobné informace týkající se použití analytika úložiště a dalších nástrojů pro identifikovat, diagnostikovat a řešit problémy související s Azure Storage najdete v tématu [monitorování, Diagnostika a řešení Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Nakonfigurujte monitorování účtu úložiště

1. V [portál Azure](https://portal.azure.com), vyberte **účty úložiště**, pak název účtu úložiště otevřete řídící panel účtu.
1. Vyberte **diagnostiky** v **monitorování** části nabídky okna.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Vyberte **typ** metriky dat pro každou **služby** chcete monitorovat a **zásady uchovávání informací** pro data. Můžete také zakázat monitorování nastavením **stav** k **vypnout**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Existují dva typy metrik, které můžete povolit u každé služby, které jsou povolené ve výchozím nastavení pro nové účty úložiště:

   * **Agregační**: shromažďuje metriky například procenta vstupní/výstupní, dostupnosti, latence a úspěch. Tyto metriky se agregují pro služby objektů blob, front, tabulek a souborů.
   * **Na rozhraní API**: kromě agregovaná metrika shromažďuje stejnou sadu metriky pro každé operace úložiště v rozhraní API služby Azure Storage.

   Chcete-li nastavit zásady uchovávání dat, přesunout **uchovávání dat (dny)** posuvníku nebo zadejte počet dnů od 1 do 365 uchovávání dat. Výchozí pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud nejsou žádné zásady uchovávání informací, je na vás odstranit data sledování.

   > [!WARNING]
   > Budou se vám účtovat, když ručně odstranit data metriky. V systému bez nákladů je odstranit zastaralé analytická data (data starší než vaše zásady uchovávání informací). Doporučujeme vám, nastavení zásady uchovávání informací založené na tom, jak dlouho chcete zachovat úložiště analytická data pro váš účet. V tématu [co poplatky, proveďte nimž dochází při povolení úložiště metriky?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) Další informace.
   >

1. Po dokončení konfigurace sledování, vyberte **Uložit**.

Výchozí sadu metriky se zobrazí v grafech v okně účtu úložiště, jakož i jednotlivé služby oken (objekt blob, fronty, tabulky a soubor). Po povolení metrik pro službu, může trvat až jednu hodinu pro data zobrazí v jeho grafy. Můžete vybrat **upravit** na jakékoli metriky grafu pro [konfigurace metriky, které](#how-to-customize-metrics-charts) se zobrazí v grafu.

Shromažďování metrik a protokolování můžete zakázat nastavením **stav** k **vypnout**.

> [!NOTE]
> Azure Storage používá [tabulky úložiště](../common/storage-introduction.md#table-storage) k uložení metriky pro váš účet úložiště a ukládá metriky v tabulkách ve vašem účtu. Další informace najdete v tématu. [Ukládání metriky](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Přizpůsobení metriky grafy

Pomocí následujících kroků vyberte které úložiště metriky, chcete-li zobrazit v grafu metriky. 

1. Spusťte zobrazením grafu metriky úložiště na portálu Azure. Grafy můžete najít na **okně účtu úložiště** a v **metriky** okno pro jednotlivé služby (objekt blob, fronty, tabulce, soubor).

   V tomto příkladu budeme pracovat s následující graf, který se zobrazí na **okně účtu úložiště**:

   ![Výběr grafu na portálu Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klikněte na tlačítko kdekoli v rámci graf a otevřete **metrika** okno. Vyberte **upravit graf** otevřete **upravit graf** okno.

   ![Upravit graf tlačítka v okně grafu](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. Na **upravit graf** okně, vyberte **časový rozsah** metrik, které chcete zobrazit v grafu a **služby** (objektů blob, fronty, tabulky, soubor) jejichž metriky, které chcete zobrazit. Zde jsme vybrali zobrazíte minulého týdne metriky pro služby objektů blob:

   ![Výběr časového rozsahu a služby v okně upravit graf](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Vyberte jednotlivých **metriky** jako měl zobrazit v grafu, pak klikněte na tlačítko **OK**. Například Zde jsme rozhodli jste se zobrazit *ContainerCount* a *ObjectCount* metriky:

   ![Jednotlivé metriky výběr v okně upravit graf](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Graf nastavení neovlivní kolekce, úložiště dat v účtu úložiště, zobrazování dat metrik monitorování nebo agregace.

### <a name="metrics-availability-in-charts"></a>Metriky dostupnosti v diagramech

Seznam dostupné metriky změny podle služby, která jste vybrali v rozevíracím seznamu a typ jednotky grafu, který upravujete. Například můžete vybrat metriky procento jako *PercentNetworkError* a *PercentThrottlingError* pouze v případě, že upravujete graf, který zobrazuje jednotky v procentech:

![Žádost o chybě procento grafu na portálu Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Metriky řešení

Metriky, které jste vybrali v Diagnostika Určuje rozlišení metriky, které jsou k dispozici pro váš účet:

* **Agregační** monitorování poskytuje metriky, jako je například procenta vstupní/výstupní, dostupnosti, latence a úspěch. Tyto metriky se shromažďují z objektu blob, table, souboru a fronty služby.
* **Na rozhraní API** poskytuje lepší řešení s metriky, které jsou k dispozici pro operace jednotlivých úložiště navíc agregace úrovni služby.

## <a name="configure-metrics-alerts"></a>Konfigurace metrik výstrah

Můžete vytvořit oznámení upozornění v případě, že bylo dosaženo prahové hodnoty pro metrika prostředků úložiště.

1. Otevřete **okno pravidla výstrah**, přejděte dolů k položce **monitorování** části **nabídky okno** a vyberte **výstrah pravidla**.
1. Vyberte **přidat upozornění** otevřete **přidání pravidla výstrahy** okno
1. Vyberte **prostředků** (objektů blob, soubor, fronty, tabulka) z rozevíracího seznamu a zadejte **název** a **popis** pro nové pravidlo výstrahy.
1. Vyberte **metrika** pro který chcete přidat oznámení, výstrahu **podmínku**a **prahová hodnota**. Prahová hodnota jednotky zadejte změny v závislosti na metriku, které jste zvolili. Například "count" je typ jednotky pro *ContainerCount*, při jednotku pro *PercentNetworkError* metrika je procento.
1. Vyberte **období**. Metriky, které dosáhnout nebo prahovou hodnotu překročit v období spustí výstrahu.
1. (Volitelné) Konfigurace **e-mailu** a **Webhooku** oznámení. Další informace o webhooků, najdete v části [konfigurace webhook, jehož na výstrahu Azure metriky](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Pokud neprovedete konfiguraci e-mailu nebo webhooku oznámení, zobrazí se výstrahy pouze na portálu Azure.

!['Přidejte pravidlo výstrahy, okno na portálu Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metriky grafy přidáte na řídicím panelu portálu

Grafy metrik Azure Storage pro všechny účty úložiště můžete přidat do řídicího panelu portálu.

1. Kliknutím vyberte **úprava řídicího panelu** při zobrazení řídicího panelu v [portál Azure](https://portal.azure.com).
1. V **dlaždice Galerie**, vyberte **najít dlaždice podle** > **typu**.
1. Vyberte **typ** > **účty úložiště**.
1. V **prostředky**, vyberte účet úložiště, jejichž metriky, které chcete přidat do řídicího panelu.
1. Vyberte **kategorie** > **monitorování**.
1. Přetažení myší graf dlaždice do řídicího panelu pro metriku, které chcete zobrazit. Opakujte pro všechny metriky, které si přejete zobrazené na řídicím panelu. Na následujícím obrázku grafu "Objekty BLOB - celkový počet požadavků" je označený jako příklad, ale všechny grafy jsou k dispozici pro umístění na řídicím panelu.

   ![Galerie dlaždice na portálu Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Vyberte **provádí přizpůsobení** v horní části řídicího panelu po dokončení přidávání grafy.

Po přidání grafy na řídicí panel, můžete dále přizpůsobit je popsaný v [přizpůsobení metriky grafy](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Konfigurace protokolování

Můžete určit, aby k ukládání protokolů diagnostiky pro čtení, zápisu a odstranit požadavky pro objekt blob, table a fronty služby Azure Storage. Zásady uchovávání dat, které nastavíte, platí také pro tyto protokoly.

> [!NOTE]
> Soubory Azure aktuálně podporuje metriky analytika úložiště, ale zatím nepodporuje protokolování.
>

1. V [portál Azure](https://portal.azure.com), vyberte **účty úložiště**, potom na název účtu úložiště, otevřete okno účtu úložiště.
1. Vyberte **diagnostiky** v **monitorování** části nabídky okna.

    ![Diagnostika položku nabídky v části sledování na portálu Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Ujistěte se, **stav** je nastaven na **na**a vyberte **služby** pro které chcete povolit protokolování.

    ![Konfigurovat protokolování na portálu Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Klikněte na **Uložit**.

Diagnostické protokoly jsou ukládány v kontejneru objektů blob s názvem $logs ve vašem účtu úložiště. Můžete zobrazit pomocí Průzkumníka úložiště jako data protokolu [Microsoft Storage Explorer](http://storageexplorer.com), nebo programově pomocí klientské knihovny pro úložiště nebo prostředí PowerShell.

Informace o Přistupování do kontejneru $logs najdete v tématu [povolení protokolování úložiště a přístup k datům protokolu](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Další postup

* Najít další podrobnosti o [metriky, protokolování a fakturace](../storage-analytics.md) pro analytika úložiště.
* [Povolit daty metrik Azure Storage metriky a zobrazení](../storage-enable-and-view-metrics.md) pomocí prostředí PowerShell a programově pomocí C#.
