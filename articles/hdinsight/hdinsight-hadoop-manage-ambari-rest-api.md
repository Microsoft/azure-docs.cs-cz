---
title: Monitorování a správa systému Hadoop pomocí rozhraní Ambari REST API – Azure HDInsight
description: Zjistěte, jak použít Ambari ke sledování a Správa clusterů Hadoop v Azure HDInsight. V tomto dokumentu se dozvíte, jak pomocí rozhraní Ambari REST API je součástí clusterů HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: d6e2bdba7e3536404f087dc468a0895d0be0c2a0
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106015"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Správa clusterů HDInsight pomocí rozhraní Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Další informace o použití rozhraní Ambari REST API ke správě a monitorování clusterů systému Hadoop v Azure HDInsight.

Apache Ambari zjednodušuje správu a monitorování clusteru Hadoop tím, že poskytuje snadno pomocí webového uživatelského rozhraní a rozhraní REST API. Ambari je zahrnuta v clusterech HDInsight, které používají operační systém Linux. Ambari slouží k monitorování clusteru a provést změny konfigurace.

## <a id="whatis"></a>Co je Ambari

[Apache Ambari](http://ambari.apache.org) poskytuje webové uživatelské rozhraní, které lze použít ke správě a monitorování clusterů systému Hadoop. Vývojářům můžete integrovat tyto funkce do svých aplikací s použitím [rozhraní Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari je dostupné ve výchozím nastavení s clustery HDInsight založené na Linuxu.

## <a name="how-to-use-the-ambari-rest-api"></a>Použití rozhraní Ambari REST API

> [!IMPORTANT]
> Informace a příklady v tomto dokumentu vyžadují clusteru služby HDInsight, který používá operační systém platformy Linux. Další informace najdete v tématu [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

V příkladech v tomto dokumentu jsou k dispozici pro prostředí Bourne (bash) a prostředí PowerShell. Příklady bash byly testovány s verzí bash GNU 4.3.11, ale by měla fungovat s další prostředí Unix. Příklady prostředí PowerShell byly testovány pomocí prostředí PowerShell 5.0, ale by měla fungovat s Powershellem 3.0 nebo vyšší.

Pokud se používá __Bourne prostředí__ (Bash), musíte mít nainstalované tyto položky:

* [cURL](http://curl.haxx.se/): cURL je nástroj, který slouží k práci s rozhraními REST API z příkazového řádku. V tomto dokumentu se používá ke komunikaci s rozhraní Ambari REST API.

Ať už pomocí Bashe nebo Powershellu, musíte mít také [jq](https://stedolan.github.io/jq/) nainstalované. Jq je nástroj pro práci s dokumenty JSON. Používá se v **všechny** příklady Bash a **jeden** příklady prostředí PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Základní identifikátor URI pro Ambari Rest API

Základní identifikátor URI pro rozhraní Ambari REST API na HDInsight je https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, kde **CLUSTERNAME** je název vašeho clusteru.

> [!IMPORTANT]
> Název clusteru v části plně kvalifikovaný název (FQDN) identifikátoru URI (CLUSTERNAME.azurehdinsight.net) je velká a malá písmena, další výskyty v identifikátoru URI jsou malá a velká písmena. Například, pokud je název vašeho clusteru `MyCluster`, platné identifikátory URI jsou následující:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Následující identifikátory URI vrátí chybu, protože druhým výskytem název není správnou velikost.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

Připojení k Ambari na HDInsight vyžaduje protokol HTTPS. Použijte název účtu správce (výchozí hodnota je **správce**) a heslo, které jste zadali při vytváření clusteru.

## <a name="examples-authentication-and-parsing-json"></a>Příklady: Ověření a parsování formátu JSON

Následující příklady ukazují, jak vytvořit žádost o získání základní rozhraní Ambari REST API:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Bash příklady v tomto dokumentu vytvořit následující předpoklady:
>
> * Výchozí hodnota je přihlašovací jméno pro cluster `admin`.
> * `$CLUSTERNAME` obsahuje název clusteru. Tuto hodnotu lze nastavit pomocí `set CLUSTERNAME='clustername'`
> * Po zobrazení výzvy zadejte heslo pro přihlášení ke clusteru (admin).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Příklady prostředí PowerShell v tomto dokumentu vytvořit následující předpoklady:
>
> * `$creds` je objekt přihlašovacích údajů, který obsahuje přihlašovací jméno správce a heslo pro cluster. Tuto hodnotu lze nastavit pomocí `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` a po zobrazení výzvy přihlašovacích údajů.
> * `$clusterName` je řetězec, který obsahuje název clusteru. Tuto hodnotu lze nastavit pomocí `$clusterName="clustername"`.

Oba příklady vrátí dokument JSON, který začíná s informacemi, podobně jako v následujícím příkladu:

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

Následující příklad používá `jq` analyzovat odpověď dokumentu JSON a zobrazit pouze `health_report` informace z výsledků.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

Poskytuje prostředí PowerShell 3.0 a vyšší `ConvertFrom-Json` rutiny, která převede dokument JSON na objekt, který je snadnější pracovat z Powershellu. Následující příklad používá `ConvertFrom-Json` lze zobrazit pouze `health_report` informace z výsledků.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Přestože většina příkladů v tomto dokumentu využívají `ConvertFrom-Json` zobrazíte prvky z dokumentu odpovědi [Ambari aktualizace konfigurace](#example-update-ambari-configuration) příkladu jq. Jq v tomto příkladu slouží k vytvoření nové šablony z dokumentu JSON odpovědi.

Úplný popis rozhraní REST API najdete v části [V1 referenční dokumentace rozhraní API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Příklad: Získejte plně kvalifikovaný název domény uzlů clusteru

Při práci s HDInsight, budete muset vědět, plně kvalifikovaný název domény (FQDN) uzlu clusteru. Můžete snadno získat plně kvalifikovaný název domény pro různé uzly v clusteru pomocí následujících příkladech:

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

* **Hlavní uzly**

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

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Příklad: Získání interní IP adresy uzlů clusteru

> [!IMPORTANT]
> IP adresy vrácených příklady v této části nejsou přímo přístupné přes internet. Jsou dostupné v rámci virtuální sítě Azure, která obsahuje HDInsight cluster.
>
> Další informace o práci s HDInsight a virtuálními sítěmi, najdete v části [možnosti rozšíření HDInsight s použitím vlastní virtuální sítě Azure](hdinsight-extend-hadoop-virtual-network.md).

Pokud chcete zjistit IP adresu, musíte znát interní plně kvalifikovaný název domény (FQDN) uzlů clusteru. Jakmile budete mít plně kvalifikovaný název domény, pak můžete získat IP adresu hostitele. Následující příklady nejprve Ambari dotázat na plně kvalifikovaný název domény všech uzlů hostitele a pak Ambari dotázat na IP adresu každého hostitele.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Předchozí příklady výzvu k zadání hesla. Tento příklad se spouští `curl` příkazu více než jednou, takže heslo je k dispozici jako `$PASSWORD` více výzev.

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

## <a name="example-get-the-default-storage"></a>Příklad: Získání výchozí úložiště

Při vytváření clusteru služby HDInsight, musíte použít účet služby Azure Storage nebo Data Lake Store jako výchozím úložištěm clusteru. Ambari slouží k načtení těchto informací po vytvoření clusteru. Pokud třeba chcete pro čtení a zápisu dat do kontejneru mimo HDInsight.

Následující příklady načíst výchozí konfigurace úložiště v clusteru:

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
> Tyto příklady vrátí první konfigurace pro server (`service_config_version=1`) obsahující tyto informace. Pokud načtete hodnotu, která byla změněna po vytvoření clusteru, budete muset seznamu verze konfigurace a načíst nejnovější.

Vrácená hodnota je podobná následující příklady:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Tato hodnota označuje, že cluster používá účet Azure Storage pro výchozí úložiště. `ACCOUNTNAME` Hodnota je název účtu úložiště. `CONTAINER` Část je název kontejneru objektů blob v účtu úložiště. Kontejner je kořenový adresář HDFS kompatibilní úložiště pro cluster.

* `adl://home` – Tato hodnota označuje, že cluster používá Azure Data Lake Store pro výchozí úložiště.

    Pokud chcete zjistit název účtu Data Lake Store, použijte následující příklady:

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

    Vrácená hodnota je podobný `ACCOUNTNAME.azuredatalakestore.net`, kde `ACCOUNTNAME` je název účtu Data Lake Store.

    Pokud chcete zjistit adresáři v Data Lake Store, obsahující úložiště pro cluster, použijte následující příklady:

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

    Vrácená hodnota je podobný `/clusters/CLUSTERNAME/`. Tato hodnota je cesta v rámci účtu Data Lake Store. Tato cesta je kořenový systému HDFS kompatibilní souborů pro cluster. 

> [!NOTE]
> `Get-AzureRmHDInsightCluster` Rutiny poskytované [prostředí Azure PowerShell](/powershell/azure/overview) také vrátí informace o úložiště pro cluster.


## <a name="example-get-configuration"></a>Příklad: Získat konfiguraci

1. Získání konfigurace, které jsou k dispozici pro váš cluster.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    V tomto příkladu vrátí dokument JSON obsahující aktuální konfiguraci (identifikovaný *značka* hodnotu) pro součásti nainstalované v clusteru. Následující příklad je výpisem z data vrácená z typu clusteru Spark.
   
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

2. Získáte konfiguraci pro součást, která vás zajímají. V následujícím příkladu nahraďte `INITIAL` hodnotou značky vrácená z předchozí žádosti.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    V tomto příkladu vrátí dokumentu JSON, který obsahuje aktuální konfiguraci `core-site` komponenty.

## <a name="example-update-configuration"></a>Příklad: Aktualizace konfigurace

1. Získejte aktuální konfiguraci, která Ambari ukládá jako "požadovanou konfiguraci":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    V tomto příkladu vrátí dokument JSON obsahující aktuální konfiguraci (identifikovaný *značka* hodnotu) pro součásti nainstalované v clusteru. Následující příklad je výpisem z data vrácená z typu clusteru Spark.
   
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
   
    Z tohoto seznamu, je nutné zkopírovat název součásti (například **spark\_thrift\_sparkconf** a **značka** hodnotu.

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
    > Nahraďte **spark thrift-sparkconf** a **počáteční** pomocí součásti a značku, kterou chcete načíst konfiguraci.
   
    Chcete-li data načtená z HDInsight do nové šablony konfigurace se používá Jq. Tyto příklady konkrétně provést následující akce:
   
    * Vytvoří jedinečnou hodnotu obsahující řetězec "verze" a data, která je uložena v `newtag`.

    * Vytvoří kořenový dokument pro nové požadované konfigurace.

    * Získá obsah `.items[]` pole a přidá ho pod **desired_config** elementu.

    * Odstraní `href`, `version`, a `Config` prvky, jako tyto prvky nejsou potřeba odeslat novou konfiguraci.

    * Přidá `tag` element s hodnotou `version#################`. Číselnou část je založen na aktuální datum. Každá konfigurace musí mít jedinečný značku.
     
    A konečně data uložená `newconfig.json` dokumentu. Strukturu dokumentu by měl vypadat podobně jako v následujícím příkladu:
     
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

3. Otevřít `newconfig.json` v dokumentu a upravit nebo přidat hodnoty `properties` objektu. Následující příklad změní hodnotu `"spark.yarn.am.memory"` z `"1g"` k `"3g"`. Také přidá `"spark.kryoserializer.buffer.max"` s hodnotou `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Po dokončení úpravami, které provedete, uložte soubor.

4. K odeslání aktualizovanou konfiguraci na Ambari použijte následující příkazy.
   
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
   
    Tyto příkazy odeslat obsah **newconfig.json** souboru do clusteru jako novou požadovanou konfiguraci. Požadavek vrátí dokument JSON. **VersionTag** element v tomto dokumentu by měla odpovídat verzi jste odeslali a **configs** objekt obsahuje změny konfigurace, které jste požádali.

### <a name="example-restart-a-service-component"></a>Příklad: Restartovat součást služby

V tomto okamžiku Pokud podíváte na webové uživatelské rozhraní Ambari, službu Sparku znamená, že je nutné restartovat předtím, než se nová konfigurace projevila. Pomocí následujících kroků restartujte službu.

1. Pomocí následujícího postupu povolte režim údržby pro službu Sparku:

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
   
    Tyto příkazy odeslání dokumentu JSON na serveru, na kterém Zapne režim údržby. Můžete ověřit, že služba je nyní v režimu údržby pomocí následující žádosti:
   
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

2. Chcete-li vypnout službu v dalším kroku použijte následující:

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
    > `href` Hodnoty vrácené pomocí tohoto identifikátoru URI používá interní IP adresa uzlu clusteru. Pro použití z mimo cluster, nahraďte část "10.0.0.18:8080" plně kvalifikovaný název domény clusteru. 
    
    Následující příkazy načtou stav požadavku:

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

    Odpověď `COMPLETED` označuje, že žádost byla dokončena.

3. Po dokončení předchozího požadavku, použijte následující postup ke spuštění služby.
   
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
    Služba teď používá novou konfiguraci.

4. Nakonec použijte následující postup k vypnutí možnosti režimu údržby.
   
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

Úplný popis rozhraní REST API najdete v části [V1 referenční dokumentace rozhraní API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

