---
title: Použijte Hive s Hadoop pro analýzu protokolu Web - Azure HDInsight | Microsoft Docs
description: Další informace o použití Hive s HDInsight k analýze webových protokolů. Budete používat soubor protokolu jako vstup do tabulky HDInsight a použít HiveQL k dotazování data.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 53a0560d3bc5a52069d5829b9c3bd353e0c37ef3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398153"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Použijte Hive s HDInsight se systémem Windows k analýze protokolů z webů
Zjistěte, jak použít HiveQL v prostředí HDInsight k analýze protokolů z webu. Analýza protokolu webu můžete použít, segmentovat cílovou skupinu podle podobné aktivity, kategorizace návštěvníky podle demografické údaje a zjistit, obsah se zobrazení, webů, které pocházejí z a tak dále.

> [!IMPORTANT]
> Kroky v tomto dokumentu pracovat pouze s clustery HDInsight se systémem Windows. HDInsight je k dispozici pouze v systému Windows verze nižší než HDInsight 3.4. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

V této ukázce použijete clusteru služby HDInsight k analýze webu soubory protokolu pro získání přehledu o četnosti návštěv k webu z externích webových stránek za den. Můžete také vygenerovat souhrn chyb na webových stránkách, kterými se uživatelé setkávají. Získáte informace o těchto tématech:

* Připojte k Azure Blob storage, který obsahuje soubory protokolu webové stránky.
* Vytváření tabulek HIVE k dotazování tyto protokoly.
* Vytváření dotazů HIVE analyzovat data.
* V aplikaci Microsoft Excel pro připojení k HDInsight (pomocí připojení k databázi otevřít (ODBC) pro načtení analyzovaná data.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Požadavky
* Musí být zřízená clusteru Hadoop v Azure HDInsight. Pokyny najdete v tématu [zřizování clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Musíte mít aplikaci Microsoft Excel 2013 nebo nainstalovat aplikaci Excel 2010.
* Musíte mít [ovladače ODBC Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886) k importu dat z podregistru do aplikace Excel.

## <a name="to-run-the-sample"></a>Ke spuštění ukázky
1. Z [portál Azure](https://portal.azure.com/), z úvodního panelu (Pokud je připnutý clusteru existuje), klikněte na dlaždici clusteru, na kterém chcete spustit ukázku.
2. V okně clusteru pod **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**a potom z **řídicí panel clusteru** okně klikněte na tlačítko **řídicí panel clusteru HDInsight**. Alternativně můžete přímo otevře řídicí panel pomocí následující adresu URL:

         https://<clustername>.azurehdinsight.net

    Po zobrazení výzvy ověřování pomocí správce uživatelské jméno a heslo, které jste použili při zřizování clusteru.
3. Z webové stránky, které se otevře, klikněte na tlačítko **postupem Začínáme Galerie** kartě a potom v části **řešení s ukázkovými daty** kategorie, klikněte na tlačítko **analýza webového protokolu** ukázka.
4. Postupujte podle pokynů na webové stránce ukončíte vzorku.

## <a name="next-steps"></a>Další postup
Zkuste následující ukázka: [analýza dat snímače používání Hive s HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
