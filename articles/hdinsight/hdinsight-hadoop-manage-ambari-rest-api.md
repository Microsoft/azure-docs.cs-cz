---
title: Monitorování a správa systému Hadoop pomocí rozhraní Ambari REST API – Azure HDInsight
description: Zjistěte, jak použít Ambari ke sledování a Správa clusterů Hadoop v Azure HDInsight. V tomto dokumentu se dozvíte, jak pomocí rozhraní Ambari REST API je součástí clusterů HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 38d845e133b5ae1a091fd9fee698b2bf801135e1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880588"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Zjistěte, jak použít rozhraní Apache Ambari REST API ke správě a monitorování clusterů systému Apache Hadoop v Azure HDInsight.

## <a id="whatis"></a>Co je Apache Ambari
[Apache Ambari](https://ambari.apache.org) zjednodušuje správu a monitorování clusterů systému Hadoop tím, že poskytuje snadno použitelné webové uživatelské rozhraní se opírá o jeho [rozhraní REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari je dostupné ve výchozím nastavení s clustery HDInsight založené na Linuxu.

## <a name="prerequisites"></a>Požadavky
* **Cluster Hadoop v HDInsight**. Zobrazit [Začínáme s HDInsight v Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash na Ubuntu ve Windows 10**.  V příkladech v tomto článku se používá prostředí Bash ve Windows 10. Zobrazit [subsystém Windows pro Linux Instalační příručka pro Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) kroky instalace.  Další [prostředí Unix](https://www.gnu.org/software/bash/) fungují stejně.  Příklady s drobné úpravy, můžete pracovat na příkazovém řádku Windows.  Alternativně můžete použít prostředí Windows PowerShell.

* **jq**, příkazového řádku procesoru JSON.  Zobrazit [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Alternativně můžete použít [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Základní identifikátor URI pro Ambari Rest API

 Základní identifikátor URI pro rozhraní Ambari REST API na HDInsight je `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, kde `CLUSTERNAME` je název vašeho clusteru.  Názvy clusterů v identifikátorech URI **malá a velká písmena**.  Název clusteru v části plně kvalifikovaný název (FQDN) identifikátoru URI (CLUSTERNAME.azurehdinsight.net) je velká a malá písmena, další výskyty v identifikátoru URI jsou malá a velká písmena.

## <a name="authentication"></a>Authentication

Připojení k Ambari na HDInsight vyžaduje protokol HTTPS. Použijte název účtu správce (výchozí hodnota je **správce**) a heslo, které jste zadali při vytváření clusteru.

## <a name="examples"></a>Příklady

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat přihlašovací údaje)
Zachovat svoje přihlašovací údaje, aby se zabránilo pokuste pro každý příklad.  Název clusteru budou zachovány v samostatný krok.

**A. Bash**  
Níže uvedený skript upravte tak, že nahradíte `PASSWORD` s vlastní heslo.  Potom zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Zjištění názvu správně malá a velká použita clusteru
Skutečné malých a velkých písmen na název clusteru může být jiný než byste očekávali v závislosti na způsobu vytvoření clusteru.  Zde uvedené kroky se zobrazí skutečné velká a malá písmena a uložte ho do proměnné pro všechny další příklady.

Upravit skripty níže nahraďte `CLUSTERNAME` názvem vašeho clusteru. Potom zadejte příkaz. (Název clusteru plně kvalifikovaného názvu domény není malá a velká písmena.)

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLSUTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analýza dat JSON

Následující příklad používá [jq](https://stedolan.github.io/jq/) nebo [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) analyzovat odpověď dokumentu JSON a zobrazit pouze `health_report` informace z výsledků.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Získat plně kvalifikovaný název domény uzlů clusteru

Při práci s HDInsight, budete muset vědět, plně kvalifikovaný název domény (FQDN) uzlu clusteru. Můžete snadno získat plně kvalifikovaný název domény pro různé uzly v clusteru pomocí následujících příkladech:

**Všechny uzly**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Hlavní uzly**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Pracovní uzly**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Uzly zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Získání interní IP adresy uzlů clusteru

IP adresy vrácených příklady v této části nejsou přímo přístupné přes internet. Jsou dostupné v rámci virtuální sítě Azure, která obsahuje HDInsight cluster.

Další informace o práci s HDInsight a virtuálními sítěmi, najdete v části [možnosti rozšíření HDInsight s použitím vlastní virtuální sítě Azure](hdinsight-extend-hadoop-virtual-network.md).

Pokud chcete zjistit IP adresu, musíte znát interní plně kvalifikovaný název domény (FQDN) uzlů clusteru. Jakmile budete mít plně kvalifikovaný název domény, pak můžete získat IP adresu hostitele. Následující příklady nejprve Ambari dotázat na plně kvalifikovaný název domény všech uzlů hostitele a pak Ambari dotázat na IP adresu každého hostitele.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Získání výchozí úložiště

Při vytváření clusteru služby HDInsight, musí použít účet služby Azure Storage nebo Data Lake Storage jako výchozí úložiště clusteru. Ambari slouží k načtení těchto informací po vytvoření clusteru. Pokud třeba chcete pro čtení a zápisu dat do kontejneru mimo HDInsight.

Následující příklady načíst výchozí konfigurace úložiště v clusteru:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Tyto příklady vrátí první konfigurace pro server (`service_config_version=1`) obsahující tyto informace. Pokud načtete hodnotu, která byla změněna po vytvoření clusteru, budete muset seznamu verze konfigurace a načíst nejnovější.

Vrácená hodnota je podobná následující příklady:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Tato hodnota označuje, že cluster používá účet Azure Storage pro výchozí úložiště. `ACCOUNTNAME` Hodnota je název účtu úložiště. `CONTAINER` Část je název kontejneru objektů blob v účtu úložiště. Kontejner je kořenový adresář HDFS kompatibilní úložiště pro cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen2 pro výchozí úložiště. `ACCOUNTNAME` a `CONTAINER` hodnoty mají stejný význam jako pro Azure Storage, již bylo zmíněno dříve.

* `adl://home` – Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen1 pro výchozí úložiště.

    Pokud chcete zjistit název účtu Data Lake Storage, použijte následující příklady:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Vrácená hodnota je podobný `ACCOUNTNAME.azuredatalakestore.net`, kde `ACCOUNTNAME` je název účtu Data Lake Storage.

    Pokud chcete najít adresář Data Lake Storage, který obsahuje úložiště pro cluster, použijte následující příklady:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Vrácená hodnota je podobný `/clusters/CLUSTERNAME/`. Tato hodnota je cesta v rámci účtu Data Lake Storage. Tato cesta je kořenový systému HDFS kompatibilní souborů pro cluster.  

> [!NOTE]  
> [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) rutiny poskytované [prostředí Azure PowerShell](/powershell/azure/overview) také vrátí informace o úložiště pro cluster.


### <a name="get-all-configurations"></a> Získat všechny konfigurace

Získání konfigurace, které jsou k dispozici pro váš cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

V tomto příkladu vrátí dokument JSON obsahující aktuální konfiguraci (identifikovaný *značka* hodnotu) pro součásti nainstalované v clusteru. Následující příklad je výpisem z data vrácená z typu clusteru Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Získat konfiguraci pro konkrétní součást

Získáte konfiguraci pro součást, která vás zajímají. V následujícím příkladu nahraďte `INITIAL` hodnotou značky vrácená z předchozí žádosti.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

V tomto příkladu vrátí dokumentu JSON, který obsahuje aktuální konfiguraci `livy2-conf` komponenty.

### <a name="update-configuration"></a>Konfigurace aktualizace

1. Vytvoření `newconfig.json`.  
   Upravte a potom zadejte následující příkazy:

   * Nahraďte `livy2-conf` pomocí požadované součásti.
   * Nahraďte `INITIAL` se skutečnou hodnotou načtené pro `tag` z [získat všechny konfigurace](#Get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **B. prostředí powershell**  
     Tento skript Powershellu využívá [jq](https://stedolan.github.io/jq/).  Upravit `C:\HD\jq\jq-win64` níže tak, aby odrážely skutečné cesty a verze [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

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
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Upravit `newconfig.json`.  
   Otevřít `newconfig.json` v dokumentu a upravit nebo přidat hodnoty `properties` objektu. Následující příklad změní hodnotu `"livy.server.csrf_protection.enabled"` z `"true"` k `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Po dokončení úpravami, které provedete, uložte soubor.

3. Odeslat `newconfig.json`.  
   K odeslání aktualizovanou konfiguraci na Ambari použijte následující příkazy.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Tyto příkazy odeslat obsah **newconfig.json** souboru do clusteru jako novou požadovanou konfiguraci. Požadavek vrátí dokument JSON. **VersionTag** element v tomto dokumentu by měla odpovídat verzi jste odeslali a **configs** objekt obsahuje změny konfigurace, které jste požádali.

### <a name="restart-a-service-component"></a>Restartovat součást služby

V tomto okamžiku Pokud podíváte na webové uživatelské rozhraní Ambari, službu Sparku znamená, že je nutné restartovat předtím, než se nová konfigurace projevila. Pomocí následujících kroků restartujte službu.

1. Pomocí následujícího postupu povolte režim údržby pro službu Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Ověření režimu údržby  

    Tyto příkazy odeslání dokumentu JSON na serveru, na kterém Zapne režim údržby. Můžete ověřit, že služba je nyní v režimu údržby pomocí následující žádosti:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Vrácená hodnota je `ON`.

3. Chcete-li vypnout službu Spark2 v dalším kroku použijte následující:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
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

4. Ověření požadavku.  
    Upravte následující příkaz tak, že nahradíte `29` se skutečnou hodnotou `id` vrácená z předchozího kroku.  Následující příkazy načtou stav požadavku:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Odpověď `COMPLETED` označuje, že žádost byla dokončena.

5. Po dokončení předchozího požadavku, použijte následující postup ke spuštění služby Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Služba teď používá novou konfiguraci.

6. Nakonec použijte následující postup k vypnutí možnosti režimu údržby.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Další postup

Úplný popis rozhraní REST API najdete v části [Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

