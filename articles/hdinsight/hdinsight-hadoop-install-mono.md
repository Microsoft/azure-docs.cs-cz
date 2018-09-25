---
title: Instalace nebo aktualizace Mono na HDInsight – Azure
description: Zjistěte, jak použít konkrétní verzi nástroje Mono s clusterem HDInsight. Mono se používá ke spouštění aplikací .NET v clusterech HDInsight založených na Linuxu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.custom: hdinsightactive
ms.openlocfilehash: 54e5a5b72627dc9cd2d842ccb24d10e2f9ab9dce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957172"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Instalace nebo aktualizace Mono v HDInsight

Zjistěte, jak nainstalovat určitou verzi [Mono](https://www.mono-project.com) na HDInsight 3.4 a vyšší.

Mono je nainstalován na HDInsight 3.4 a vyšší a slouží ke spouštění aplikací .NET. Informace o verzi Mono zahrnuty s jednotlivými verzemi HDInsight najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md). K instalaci na jinou verzi v clusteru, použijte akci skriptu v tomto dokumentu. 

## <a name="how-it-works"></a>Jak to funguje

Tento skript je možné zadat následující parametr:

* __Číslo verze mono__: verze Mono k instalaci. Musí být k dispozici od verze [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Tento skript nainstaluje následující Mono balíčky:

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>Skript

__Umístění skriptu__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Požadavky na__:

* Skript se musí použít na __hlavním uzlům__ a __pracovní uzly__.

## <a name="to-use-the-script"></a>Pomocí skriptu

Informace o tom, jak pomocí tohoto skriptu s HDInsight, najdete v článku [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu. Můžete použít skript prostřednictvím webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure Classic.

Během provádění akce dokument skriptu, použijte následující identifikátor URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Pokud chcete nastavit Mono verze nainstalovaná, použijte číslo verze v __parametry__ pole. Zadejte například `5.4` instalace Mono 5.4.

> [!NOTE]
> Při konfiguraci HDInsight pomocí tohoto skriptu, označí skript jako __trvalé__. Toto nastavení umožňuje HDInsight skript vyrovnat pracovní uzly přidané prostřednictvím operací škálování.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak upgrade nebo instalace konkrétní verze architektury mono na HDInsight. Další informace o použití aplikace .NET v Mono na HDInsight najdete v následujících dokumentech:

* [Použití .NET pro streamování MapReduce v HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Použití .NET s Hivem a Pig v HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Vývoj řešení C# se Stormem v HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migrace řešení .NET do HDInsight založených na Linuxu](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Další informace o použití akce skriptu, naleznete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
