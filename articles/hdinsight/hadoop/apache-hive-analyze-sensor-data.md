---
title: Analýza dat snímačů pomocí Hive a Hadoop - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak analyzovat data snímačů pomocí konzole dotaz Hive s HDInsight (Hadoop) a potom vizualizaci dat v aplikaci Microsoft Excel s PowerView.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb3dc93d7cb741a8a3099abe13d00f40c9639705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analýza dat snímačů v konzole dotaz Hive na Hadoop v HDInsight

Zjistěte, jak analyzovat data snímačů pomocí konzole dotaz Hive s HDInsight (Hadoop) a potom vizualizovat data v aplikaci Microsoft Excel pomocí Power View.

> [!IMPORTANT]
> Kroky v tomto dokumentu pracovat pouze s clustery HDInsight se systémem Windows. HDInsight je k dispozici pouze v systému Windows verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


V této ukázce použijete Hive ke zpracování historických dat a identifikovat problémy s vytápění a klimatizace systémy. Konkrétně určit systémy, nejsou schopna spolehlivě udržovat nastavenou teplotu provedením následujících úloh:

* Vytváření tabulek HIVE k dotazování na data uložená v souborech s oddělovači (CSV).
* Vytváření dotazů HIVE analyzovat data.
* Pokud chcete načíst analyzovaná data, aplikaci Microsoft Excel pro připojení k HDInsight.
* K vizualizaci dat, použijte Power View.

![Diagram architektury řešení](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Požadavky

* Cluster služby HDInsight (Hadoop): najdete v části [vytvoření Hadoop clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) informace o vytvoření clusteru.
* Microsoft Excel 2013

  > [!NOTE]
  > Aplikace Microsoft Excel se používá pro vizualizaci dat s [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Ovladač ODBC Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Ke spuštění ukázky

1. Z webového prohlížeče přejděte na následující adresu URL: 

         https://<clustername>.azurehdinsight.net

    Parametr `<clustername>` nahraďte názvem vašeho clusteru HDInsight.

    Po zobrazení výzvy ověřování pomocí správce uživatelské jméno a heslo, které jste použili při zřizování tento cluster.

2. Z webové stránky, které se otevře, klikněte na tlačítko **postupem Začínáme Galerie** kartě a potom v části **řešení s ukázkovými daty** kategorie, klikněte na tlačítko **analýza dat snímačů** ukázka.

    ![Získávání Začínáme Galerie image](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Postupujte podle pokynů na webové stránce ukončíte vzorku.
