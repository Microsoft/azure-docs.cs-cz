---
title: Přidat další Azure účty úložiště pro HDInsight
description: Zjistěte, jak přidat další služby Azure storage účty do existujícího clusteru HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a86a965a746ed659b73c359ee44fb9be250aae97
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714279"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidání dalších účtů úložiště pro HDInsight

Zjistěte, jak přidat další služby Azure storage účty do HDInsight pomocí skriptových akcí. Kroky v tomto dokumentu přidání účtu úložiště do existujícího clusteru HDInsight se systémem Linux.

> [!IMPORTANT]  
> Informace v tomto dokumentu je o přidání dalšího úložiště do clusteru po jeho vytvoření. Informace o přidání účtů úložiště při vytváření clusteru najdete v tématu [nastavení clusterů v HDInsight se Apache Hadoop, Apache Spark, Apache Kafka a další](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Jak to funguje

Tento skript používá následující parametry:

* __Název účtu služby Azure storage__: Název účtu úložiště, které chcete přidat do clusteru HDInsight. Po spuštění skriptu, může HDInsight číst a zapisovat data uložená v tomto účtu úložiště.

* __Klíč účtu úložiště Azure__: Klíč, který uděluje přístup k účtu úložiště.

* __-p__ (volitelné): -Li zadán, klíče není zašifrovaný a je uložen v souboru core-site.xml jako prostý text.

Během zpracování skript provede následující akce:

* Pokud účet úložiště už existuje v core-site.xml konfiguraci clusteru, ukončí skriptu a jsou prováděny žádné další akce.

* Ověřuje, že účet úložiště existuje a je přístupný pomocí klíče.

* Klíč zašifruje pomocí přihlašovacích údajů clusteru.

* Účet úložiště se přidá do souboru core-site.xml.

* Zastaví a restartuje [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), a [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) služby. Zastavení a spuštění těchto služeb umožňuje je, aby používaly nový účet úložiště.

> [!WARNING]  
> Použití účtu úložiště v jiném umístění než HDInsight cluster se nepodporuje.

## <a name="the-script"></a>Skript

__Umístění skriptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Požadavky na__:

* Skript se musí použít na __hlavním uzlům__.

## <a name="to-use-the-script"></a>Pomocí skriptu

Tento skript je možné z webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure Classic. Další informace najdete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentu.

> [!IMPORTANT]  
> Při použití kroků uvedených v dokumentu přizpůsobení, chcete-li použít tento skript použijte následující informace:
>
> * Nahraďte všechny akce skriptu příklad URI identifikátor URI pro tento skript (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Žádné parametry příkladu nahraďte název účtu služby Azure storage a klíč účtu úložiště, které chcete přidat do clusteru. Pokud pomocí webu Azure portal, tyto parametry musí být odděleny mezerou.
> * Nepotřebujete označte tento skript jako __trvalé__, jak přímo aktualizuje konfiguraci Ambari clusteru.

## <a name="known-issues"></a>Známé problémy

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Účty úložiště se nezobrazuje v portálu Azure portal nebo nástrojů

Při prohlížení clusteru HDInsight na webu Azure Portal, vyberte __účty úložiště__ položku __vlastnosti__ nezobrazuje úložišti účty přidané prostřednictvím tuto akci se skripty. Azure PowerShell a rozhraní příkazového řádku Azure Classic se nezobrazují dalšího účtu úložiště buď.

Informace o úložiště není zobrazit, protože skript upravuje pouze core-site.xml konfiguraci clusteru. Tyto informace nepoužívá k načítání informace o clusteru pomocí rozhraní API pro správu Azure.

Chcete-li zobrazit informace o účtu úložiště do clusteru pomocí tohoto skriptu, použijte rozhraní Ambari REST API. Použijte následující příkazy pro načtení těchto informací pro váš cluster:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]  
> Nastavte `$clusterName` na název clusteru HDInsight. Nastavte `$storageAccountName` k názvu účtu úložiště. Po zobrazení výzvy zadejte přihlašovací jméno clusteru (admin) a heslo.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]  
> Nastavte `$PASSWORD` heslo účtu (správce) clusteru. Nastavte `$CLUSTERNAME` na název clusteru HDInsight. Nastavte `$STORAGEACCOUNTNAME` k názvu účtu úložiště.
>
> Tento příklad používá [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) a [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) načíst a analyzovat JSON data.

Při použití tohoto příkazu nahraďte __CLUSTERNAME__ s názvem clusteru HDInsight. Nahraďte __heslo__ s HTTP přihlašovací heslo clusteru. Nahraďte __STORAGEACCOUNT__ s názvem účtu úložiště, které jsou přidány pomocí skriptových akcí. Podobně jako tento text se zobrazí informace vrácené z tohoto příkazu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Tento text je příkladem šifrovaný klíč, který se používá pro přístup k účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Nelze získat přístup k úložišti po změně klíče

Pokud změníte klíč pro účet úložiště HDInsight už mít přístup k účtu úložiště. HDInsight používá kopii klíče uložené v mezipaměti v core-site.xml pro cluster. Tato kopie v mezipaměti musí být aktualizovány tak, aby odpovídaly novým klíčem.

Spuštění skriptu znovu akce nemá __není__ aktualizovat klíč, protože skript zkontroluje, jestli obsahuje položku pro účet úložiště už existuje. Pokud položka již existuje, neprovede žádné změny.

Chcete-li tento problém vyřešit, musíte odebrat existující položku pro účet úložiště. Použijte následující kroky pro odebrání existující položky:

1. Ve webovém prohlížeči otevřete webové uživatelské rozhraní Ambari pro váš cluster HDInsight. Identifikátor URI je https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ názvem vašeho clusteru.

    Po zobrazení výzvy zadejte přihlašovací jméno uživatele HTTP a heslo pro váš cluster.

2. V seznamu služeb na levé straně stránky vyberte __HDFS__. Vyberte __Configs__ kartu uprostřed stránky.

3. V __filtr...__  pole, zadejte hodnotu __fs.azure.account__. Vrátí záznamy pro všechny další účty úložiště, které jsou přidané do clusteru. Existují dva typy položek. __keyprovider__ a __klíč__. Oba obsahují název účtu úložiště jako součást názvu klíče.

    Tady jsou ukázkové položky pro účet úložiště s názvem __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Jakmile zjistíte, klíče pro účet úložiště, budete muset odebrat, použijte na červenou '-' ikony napravo od položky k jeho odstranění. Potom použijte __Uložit__ tlačítko uložte provedené změny.

5. Po uložení změn, použití akce skriptu k přidání účtu úložiště a nová hodnota klíče do clusteru.

### <a name="poor-performance"></a>Nízký výkon

Pokud je účet úložiště v jiné oblasti než HDInsight cluster, můžete se setkat nízký výkon. Přístup k datům v jiné oblasti automaticky odesílá síťový provoz, mimo Azure datovým centrem a přes veřejný internet, které můžou představovat latence.

> [!WARNING]  
> Použití účtu úložiště v jiné oblasti než HDInsight cluster se nepodporuje.

### <a name="additional-charges"></a>Další poplatky

Pokud je účet úložiště v jiné oblasti než HDInsight cluster, můžete si všimnout poplatky za odchozí přenos dalších na fakturaci Azure. Poplatek za výchozí přenos dat se používá při opustí datovým centrem. Tato sazba platí i v případě, že se provoz určený pro jiného datového centra Azure v jiné oblasti.

> [!WARNING]  
> Použití účtu úložiště v jiné oblasti než HDInsight cluster se nepodporuje.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak přidat další účty úložiště do existujícího clusteru HDInsight. Další informace o akcí skriptů najdete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
