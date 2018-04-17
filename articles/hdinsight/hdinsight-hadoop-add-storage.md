---
title: Přidejte další úložiště Azure účty do HDInsight | Microsoft Docs
description: Naučte se přidávat další úložiště Azure účty do existujícího clusteru HDInsight.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 569a41dfdbf2f9d911e67f283f413130ba7e1f79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidat další účty úložiště do HDInsight

Další informace o použití akce skriptu do HDInsight Pokud chcete přidat další úložiště Azure účty. Kroky v tomto dokumentu přidání účtu úložiště do existujícího clusteru HDInsight se systémem Linux.

> [!IMPORTANT]
> Informace v tomto dokumentu je o přidání dalšího úložiště do clusteru po jeho vytvoření. Informace o přidání účty úložiště při vytváření clusteru najdete v tématu [nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Jak to funguje

Tento skript používá následující parametry:

* __Název účtu úložiště Azure__: název účtu úložiště pro přidání do clusteru HDInsight. Po spuštění skriptu, HDInsight můžete číst a zapisovat data uložená v rámci tohoto účtu úložiště.

* __Klíč účtu úložiště Azure__: klíč, který uděluje přístup k účtu úložiště.

* __-p__ (volitelné):-li zadána, klíč není šifrován a je uložen v souboru core-site.xml jako prostý text.

Během zpracování na skript provede následující akce:

* Pokud účet úložiště už existuje v konfiguraci core-site.xml pro cluster, skript bude ukončen a budou provedeny žádné další akce.

* Ověří, že existuje účet úložiště a je přístupný pomocí klíče.

* Klíč zašifruje pomocí přihlašovacích údajů clusteru.

* Účet úložiště se přidá do souboru core-site.xml.

* Zastaví a restartuje službu Oozie, YARN, MapReduce2 a HDFS. Zastavení a spuštění těchto služeb umožňuje, aby uživatelé používali nový účet úložiště.

> [!WARNING]
> Použití účtu úložiště v jiném umístění než HDInsight cluster není podporováno.

## <a name="the-script"></a>Skript

__Skript umístění__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Požadavky na__:

* Skript se musí použít na __hlavní uzly__.

## <a name="to-use-the-script"></a>Pomocí skriptu

Tento skript lze z portálu Azure, Azure PowerShell nebo Azure CLI 1.0. Další informace najdete v tématu [HDInsight se systémem Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu.

> [!IMPORTANT]
> Pokud používáte podle kroků uvedených v dokumentu přizpůsobení, použijte tyto informace použít tento skript:
>
> * Nahraďte všechny akce skriptu příklad URI identifikátor URI pro tento skript (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Žádné parametry příkladu nahraďte název účtu úložiště Azure a klíč účtu úložiště, který se má přidat do clusteru. Pokud používáte portál Azure, musí být tyto parametry oddělené mezerou.
> * Není potřeba označit tento skript jako __trvalé__, jak přímo aktualizuje Ambari konfiguraci pro daný cluster.

## <a name="known-issues"></a>Známé problémy

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Nezobrazuje se v portálu Azure nebo nástroje pro účty úložiště

Při zobrazení clusteru HDInsight na portálu Azure, vyberete __účty úložiště__ položky v rámci __vlastnosti__ nezobrazí účty úložiště, které jsou přidány prostřednictvím této akce skriptu. Azure PowerShell a rozhraní příkazového řádku Azure nezobrazují účtu další úložiště buď.

Informace o úložiště není zobrazit, protože skript pouze upraví konfiguraci core-site.xml pro cluster. Tyto informace se nepoužívá při načítání informace o clusteru pomocí Azure rozhraní API pro správu.

Chcete-li zobrazit informace o účtu úložiště přidat do clusteru pomocí tohoto skriptu, použijte Ambari REST API. K načtení těchto informací pro váš cluster, použijte následující příkazy:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Nastavit `$clusterName` na název clusteru HDInsight. Nastavit `$storageAccountName` k názvu účtu úložiště. Po zobrazení výzvy zadejte přihlašovací clusteru (správce) a heslo.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Nastavit `$PASSWORD` heslo k účtu přihlášení (správce) clusteru. Nastavit `$CLUSTERNAME` na název clusteru HDInsight. Nastavit `$STORAGEACCOUNTNAME` k názvu účtu úložiště.
>
> Tento příklad používá [curl (http://curl.haxx.se/) ](http://curl.haxx.se/) a [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) načíst a analyzovat JSON data.

Při použití tohoto příkazu, nahraďte __CLUSTERNAME__ s názvem clusteru HDInsight. Nahraďte __heslo__ s heslo pro přihlášení HTTP pro cluster. Nahraďte __STORAGEACCOUNT__ s názvem účtu úložiště přidat pomocí akce skriptu. Informace o vrácená z tohoto příkazu se zobrazí podobná následující text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Tento text je příkladem šifrovaný klíč, který se používá pro přístup k účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Nelze získat přístup k úložišti po změně klíč

Pokud změníte klíč pro účet úložiště, HDInsight mít nadále přístup k účtu úložiště. HDInsight používá kopii klíče uložené v mezipaměti v core-site.xml pro cluster. Tato kopie v mezipaměti musí být aktualizovány tak, aby odpovídaly nový klíč.

Spuštění skriptu akci nemá __není__ aktualizovat klíč, protože skript zkontroluje, zda položka pro účet úložiště již existuje. Pokud položka již existuje, nebyly provedeny žádné změny.

Chcete-li tento problém obejít, je třeba odebrat existující položku pro účet úložiště. Chcete-li odebrat existující položku pomocí následujících kroků:

1. Ve webovém prohlížeči otevřete uživatelské rozhraní Ambari Web pro váš cluster HDInsight. Identifikátor URI je https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ názvem vašeho clusteru.

    Po zobrazení výzvy zadejte HTTP přihlášení uživatele a heslo pro váš cluster.

2. Ze seznamu služeb na levé straně stránky, vyberte __HDFS__. Vyberte __konfigurací__ ve středu stránky.

3. V __filtru...__  pole, zadejte hodnotu __fs.azure.account__. Tento příkaz vrátí položky pro všechny účty dalšího úložiště, které jsou přidané do clusteru. Existují dva typy položek; __keyprovider__ a __klíč__. Oba obsahují název účtu úložiště jako součást název klíče.

    Níže jsou příklady položek pro účet úložiště s názvem __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Po zjištění klíče pro účet úložiště, je třeba odebrat, použijte červený '-' ikony napravo položky ho odstranit. Potom pomocí __Uložit__ tlačítko uložte provedené změny.

5. Po uložení změn, přidat účet úložiště a nová hodnota klíče do clusteru pomocí akce skriptu.

### <a name="poor-performance"></a>Snížený výkon

Pokud účet úložiště v jiné oblasti než clusteru HDInsight se můžete setkat snížený výkon. Přístup k datům v jiné oblasti odešle síťový provoz, mimo místní datového centra Azure a napříč veřejného Internetu, které můžou představovat latence.

> [!WARNING]
> Použití účtu úložiště v jiné oblasti než HDInsight cluster není podporováno.

### <a name="additional-charges"></a>Další poplatky.

Pokud účet úložiště je v jiné oblasti než clusteru HDInsight, můžete si povšimnout další nimi spojeným nákladům na vaši fakturaci Azure. Poplatek za odchozí data se použije v případě, že data ponechá místního datového centra. Tento poplatek za platí i v případě, že se provoz určený pro jiné datové centrum Azure v jiné oblasti.

> [!WARNING]
> Použití účtu úložiště v jiné oblasti než HDInsight cluster není podporováno.

## <a name="next-steps"></a>Další postup

Jste se naučili jak přidat další účty úložiště do existujícího clusteru HDInsight. Další informace o akcí skriptů naleznete v tématu [HDInsight se systémem Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
