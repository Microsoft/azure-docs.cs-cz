---
title: Sledování a správě Hadoop pomocí Ambari REST API – Azure HDInsight | Microsoft Docs
description: Další informace o použití Ambari ke sledování a správě clusterů systému Hadoop v prostředí Azure HDInsight. V tomto dokumentu se dozvíte, jak pomocí Ambari REST API, která je součástí clusterů HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 55017b139ce89fa74a8105da05792024ecee86b2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177950"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Správa clusterů HDInsight pomocí Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Naučte se používat rozhraní Ambari REST API pro správu a sledování clusterů systému Hadoop v prostředí Azure HDInsight.

Apache Ambari zjednodušuje správu a sledování clusteru Hadoop tím, že poskytuje snadno použít webového uživatelského rozhraní a rozhraní REST API. Ambari je obsažena v clusterech HDInsight, které používají operační systém Linux. Ambari slouží ke sledování clusteru a udělat změny konfigurace.

## <a id="whatis"></a>Co je Ambari

[Apache Ambari](http://ambari.apache.org) poskytuje webové uživatelské rozhraní, které lze použít ke správě a sledování clusterů systému Hadoop. Vývojářům můžete integrovat tyto funkce do svých aplikací pomocí [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari je dostupné ve výchozím nastavení s clustery HDInsight se systémem Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Jak používat Ambari REST API

> [!IMPORTANT]
> Informace a příklady v tomto dokumentu vyžadují clusteru služby HDInsight, který používá operační systém Linux. Další informace najdete v tématu [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Příklady v tomto dokumentu jsou uvedeny pro prostředí Bourne (bash) a prostředí PowerShell. Příklady bash byly testovány s GNU bash verze 4.3.11, ale by měly spolupracovat s další součásti pro Unix. Příklady prostředí PowerShell byly testovány s PowerShell 5.0, ale by měla fungovat s prostředí PowerShell 3.0 nebo vyšší.

Pokud se používá __Bourne prostředí__ (Bash), musíte mít nainstalované tyto položky:

* [cURL](http://curl.haxx.se/): cURL je nástroj, který slouží k práci s rozhraními API REST z příkazového řádku. V tomto dokumentu se používá ke komunikaci s Ambari REST API.

Jestli používáte Bash nebo prostředí PowerShell, musí také mít [jq](https://stedolan.github.io/jq/) nainstalována. Jq je nástroj pro práci s dokumenty JSON. Používá se v **všechny** příklady Bash a **jeden** příklady prostředí PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Základní identifikátor URI pro Ambari Rest API

Základní identifikátor URI pro Ambari REST API v HDInsight https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, kde **CLUSTERNAME** je název clusteru.

> [!IMPORTANT]
> Sice velká a malá písmena název clusteru v části název (FQDN) plně kvalifikované domény identifikátor URI (CLUSTERNAME.azurehdinsight.net), jsou ostatní události v identifikátoru URI malá a velká písmena. Například pokud je název clusteru `MyCluster`, platné identifikátory URI jsou následující:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Následující identifikátory URI vrátí chybovou zprávu, protože název druhého výskytu není správnou velikost.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

Připojení k Ambari v HDInsight vyžaduje protokol HTTPS. Použijte název účtu správce (výchozí hodnota je **správce**) a heslo, které jste zadali při vytváření clusteru.

## <a name="examples-authentication-and-parsing-json"></a>Příklady: Ověřování a analýza JSON

Následující příklady ukazují, jak vytvořit požadavek GET na základní Ambari REST API:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Příklady Bash v tomto dokumentu provést následující předpoklady:
>
> * Výchozí hodnota je přihlašovací jméno pro cluster `admin`.
> * `$CLUSTERNAME` obsahuje název clusteru. Tuto hodnotu můžete nastavit pomocí `set CLUSTERNAME='clustername'`
> * Po zobrazení výzvy zadejte heslo pro přihlášení clusteru (správce).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Příklady prostředí PowerShell v tomto dokumentu provést následující předpoklady:
>
> * `$creds` je objekt přihlašovacích údajů, který obsahuje přihlašovací jméno správce a heslo pro cluster. Tuto hodnotu můžete nastavit pomocí `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` a poskytování pověření při zobrazení výzvy.
> * `$clusterName` je řetězec, který obsahuje název clusteru. Tuto hodnotu můžete nastavit pomocí `$clusterName="clustername"`.

Oba příklady vrátit dokument JSON, který začíná informace podobně jako v následujícím příkladu:

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>Analýza dat JSON

Následující příklad používá `jq` analyzovat odpověď dokumentu JSON a zobrazuje pouze `health_report` informace z výsledků.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

Prostředí PowerShell 3.0 a vyšší poskytuje `ConvertFrom-Json` rutiny, která převádí dokumentu JSON na objekt, který je snazší s ním pracovat z prostředí PowerShell. Následující příklad používá `ConvertFrom-Json` lze zobrazit pouze `health_report` informace z výsledků.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Při většina příklady v tomto dokumentu `ConvertFrom-Json` zobrazíte elementy z dokumentu odpovědi [Ambari aktualizace konfigurace](#example-update-ambari-configuration) příklad používá jq. Jq se používá v tomto příkladu můžete vytvořit novou šablonu z dokumentu JSON odpovědi.

Úplný přehled rozhraní REST API, najdete v části [Ambari API odkaz V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Příklad: Získat plně kvalifikovaný název domény uzlů clusteru

Při práci s HDInsight, musíte znát název plně kvalifikované domény (FQDN) uzlu clusteru. Můžete snadno získat plně kvalifikovaný název domény pro různé uzly v clusteru pomocí následující příklady:

* **Všechny uzly**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **hlavních uzlech**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Pracovní uzly**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Uzly zookeeper**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Příklad: Získáte interní IP adresu z uzlů clusteru

> [!IMPORTANT]
> IP adresy vrácené v příkladech v této části nejsou přímo přístupné přes internet. Jsou dostupné v rámci virtuální sítě Azure, která obsahuje clusteru HDInsight.
>
> Další informace o práci s HDInsight a virtuální sítě najdete v tématu [možnosti rozšíření HDInsight pomocí vlastních Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

Chcete-li najít IP adresu, musíte znát interní plně kvalifikovaný název domény (FQDN) uzlů clusteru. Jakmile máte plně kvalifikovaný název domény, pak můžete získat IP adresu hostitele. Následující příklady nejprve dotaz Ambari pro všechny uzly hostitelského plně kvalifikovaný název domény, a poté dotaz Ambari pro IP adresu každého hostitele.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Předchozí příklady výzvu k zadání hesla. Tento příklad spustí `curl` příkaz vícekrát, aby heslo je k dispozici jako `$PASSWORD` více výzev.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Příklad: Získat výchozí úložiště

Při vytváření clusteru služby HDInsight, musíte použít účet úložiště Azure nebo Data Lake Store jako výchozí úložiště pro cluster. Ambari slouží k načtení těchto informací po vytvoření clusteru. Například pokud chcete pro čtení a zápis dat do kontejneru mimo HDInsight.

Následující příklady načíst výchozí konfigurace úložiště z clusteru:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Tyto příklady vrátí první konfigurace platí na serveru (`service_config_version=1`) obsahující tyto informace. Pokud je načíst hodnotu, která byla změněna po vytvoření clusteru, musíte do seznamu verze konfigurace a načíst nejnovější.

Vrácená hodnota je podobný jedné z následujících příkladech:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Tato hodnota určuje, zda cluster používá účet úložiště Azure pro výchozí úložiště. `ACCOUNTNAME` Hodnota je název účtu úložiště. `CONTAINER` Část je název kontejneru objektů blob v účtu úložiště. Kontejner je kořenem HDFS kompatibilní úložiště pro cluster.

* `adl://home` – Tato hodnota určuje, jestli cluster používá Azure Data Lake Store pro výchozí úložiště.

    Pokud chcete najít název účtu Data Lake Store, použijte následující příklady:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Vrácená hodnota je podobná `ACCOUNTNAME.azuredatalakestore.net`, kde `ACCOUNTNAME` je název účtu Data Lake Store.

    Pokud chcete najít adresář v Data Lake Store, který obsahuje úložiště pro cluster, použijte následující příklady:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Vrácená hodnota je podobná `/clusters/CLUSTERNAME/`. Tato hodnota je cestu v rámci účtu Data Lake Store. Tato cesta je kořenový adresář systému HDFS kompatibilní souborů pro cluster. 

> [!NOTE]
> `Get-AzureRmHDInsightCluster` Rutiny poskytované [prostředí Azure PowerShell](/powershell/azure/overview) také vrátí informace o úložiště pro cluster.


## <a name="example-get-configuration"></a>Příklad: Get konfigurace

1. Získání konfigurace, které jsou k dispozici pro váš cluster.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Tento příklad vrátí dokumentu JSON, který obsahuje aktuální konfiguraci (identifikovaný *značky* hodnotu) pro součásti nainstalované v clusteru. Následující příklad je výňatek ze s daty vrácenými z typu clusteru Spark.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. Získáte konfiguraci pro součást, která vás zajímá. V následujícím příkladu nahraďte `INITIAL` s hodnota značky vrácená z předchozí požadavek.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Tento příklad vrátí dokumentu JSON, který obsahuje aktuální konfiguraci `core-site` součásti.

## <a name="example-update-configuration"></a>Příklad: Aktualizace konfigurace

1. Získejte aktuální konfiguraci, která Ambari ukládá jako "požadovanou konfiguraci":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Tento příklad vrátí dokumentu JSON, který obsahuje aktuální konfiguraci (identifikovaný *značky* hodnotu) pro součásti nainstalované v clusteru. Následující příklad je výňatek ze s daty vrácenými z typu clusteru Spark.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Z tohoto seznamu, je nutné zkopírovat název součásti (například **spark\_thrift\_sparkconf** a **značky** hodnotu.

2. Načíst konfiguraci pro součást a značky pomocí následujících příkazů:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Nahraďte **spark thrift-sparkconf** a **počáteční** pomocí součásti a značky, který chcete načíst konfiguraci pro.
   
    Jq slouží k zapnutí data načtená z HDInsight do nové šablony konfigurace. Tyto příklady konkrétně, proveďte následující akce:
   
    * Vytvoří jedinečnou hodnotu obsahující řetězec "verze" a data, která je uložena v `newtag`.

    * Vytvoří dokument kořenové pro nové požadované konfigurace.

    * Získá obsah `.items[]` pole a přidá ho **desired_config** element.

    * Odstraní `href`, `version`, a `Config` prvky, jako tyto prvky nejsou potřebné odeslat novou konfiguraci.

    * Přidá `tag` element s hodnotou `version#################`. Číselnou část je založena na aktuální datum. Každá konfigurace musí mít jedinečný kód.
     
    Nakonec k uložení dat `newconfig.json` dokumentu. Struktura dokumentu by měla vypadat podobně jako v následujícím příkladu:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Otevřete `newconfig.json` dokumentu a změnit nebo přidat hodnoty v `properties` objektu. Následující příklad změní hodnotu `"spark.yarn.am.memory"` z `"1g"` k `"3g"`. Přidává také `"spark.kryoserializer.buffer.max"` s hodnotou `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Jakmile dokončíte provedení změny, uložte soubor.

4. Použijte následující příkazy k odeslání do Ambari aktualizovanou konfiguraci.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Tyto příkazy odeslat obsah **newconfig.json** souboru do clusteru jako nový požadované konfigurace. Požadavek vrátí dokumentu JSON. **VersionTag** element v tomto dokumentu by měl shodovat s verzí, které jste odeslali, a **konfigurací** objekt obsahuje změny konfigurace, které jste požádali.

### <a name="example-restart-a-service-component"></a>Příklad: Restartovat součást služby

Nyní když se podíváte na webovému uživatelskému rozhraní Ambari, službu Spark označuje, že je nutné restartovat předtím, než se projeví se nová konfigurace. Restartujte službu pomocí následujících kroků.

1. Pokud chcete povolit režim údržby pro službu Spark, použijte následující:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Tyto příkazy poslat dokument JSON na server, který zapne režimu údržby. Můžete ověřit, že služba je nyní v režimu údržby pomocí následující žádosti o:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    Vrácená hodnota je `ON`.

2. Chcete-li vypnout službu vedle, použijte následující:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    Odpověď bude podobná jako v následujícím příkladu:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > `href` Hodnoty vrácené tento identifikátor URI používá interní IP adresu uzlu clusteru. Pokud chcete použít z mimo cluster, nahraďte část '10.0.0.18:8080' plně kvalifikovaný název domény clusteru. 
    
    Následující příkazy načíst stav žádosti:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Odpověď z `COMPLETED` označuje, že žádost byla dokončena.

3. Po dokončení předchozí požadavek, použijte následující spuštění služby.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Služba teď používá nová konfigurace.

4. Nakonec použijte následující vypnutí režimu údržby.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Další postup

Úplný přehled rozhraní REST API, najdete v části [Ambari API odkaz V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

