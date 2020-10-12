---
title: Monitorování a Správa Hadoop pomocí Ambari REST API – Azure HDInsight
description: Naučte se používat Ambari k monitorování a správě clusterů Hadoop ve službě Azure HDInsight. V tomto dokumentu se dozvíte, jak používat REST API Ambari, která je součástí clusterů HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 45b9c158aca85d62b02d65282876d5e40129878f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081062"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Správa clusterů HDInsight pomocí REST API Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Naučte se používat REST API Apache Ambari ke správě a monitorování clusterů Apache Hadoop ve službě Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Co je Apache Ambari

Apache Ambari zjednodušuje správu a monitorování clusterů Hadoop tím, že poskytuje jednoduché webové uživatelské rozhraní, které je zajištěno pomocí [rozhraní REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari se ve výchozím nastavení poskytuje u clusterů HDInsight se systémem Linux.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash na Ubuntu ve Windows 10.  V příkladech v tomto článku se používá prostředí bash ve Windows 10. Pokyny k instalaci najdete v tématu [Instalační příručka k systému Windows pro Linux pro systém Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) .  Budou fungovat i další [prostředí UNIX](https://www.gnu.org/software/bash/) .  Příklady s některými drobnými úpravami mohou fungovat na příkazovém řádku systému Windows.  Případně můžete použít prostředí Windows PowerShell.

* JQ, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Prostředí Windows PowerShell.  Nebo můžete použít bash.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Základní identifikátor prostředku pro rozhraní Ambari REST API

 Základní identifikátor URI (Uniform Resource Identifier) pro Ambari REST API v HDInsight je `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , kde `CLUSTERNAME` je název vašeho clusteru.  Názvy clusterů v identifikátorech URI rozlišují **velká a malá písmena**.  I když název clusteru v části plně kvalifikovaného názvu domény (FQDN) v identifikátoru URI () rozlišuje velká a malá písmena `CLUSTERNAME.azurehdinsight.net` , jiné výskyty v identifikátoru URI rozlišují velká a malá písmena.

## <a name="authentication"></a>Authentication

Připojení k Ambari v HDInsight vyžaduje protokol HTTPS. Použijte název účtu správce (výchozí nastavení je **admin**) a heslo, které jste zadali při vytváření clusteru.

V případě Balíček zabezpečení podniku clusterů místo `admin` použijte plně kvalifikované uživatelské jméno, jako je `username@domain.onmicrosoft.com` .

## <a name="examples"></a>Příklady

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat přihlašovací údaje)

Zachovejte přihlašovací údaje, abyste je nemuseli znovu zadávat pro každý příklad.  Název clusteru se zachová v samostatném kroku.

**A. bash**  
Následující skript upravte tak, že nahradíte `PASSWORD` vlastní heslo.  Pak zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifikace správného názvu clusteru použita

Skutečná velikost písmen názvu clusteru může být jiná, než očekáváte.  V těchto krocích se zobrazí skutečná velikost písmen a pak se uloží do proměnné pro všechny další příklady.

Úpravou následujících skriptů nahraďte `CLUSTERNAME` název vašeho clusteru. Pak zadejte příkaz. (Název clusteru pro plně kvalifikovaný název domény nerozlišuje velká a malá písmena.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
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

Následující příklad používá [JQ](https://stedolan.github.io/jq/) nebo [ConvertFrom-JSON](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) k analýze dokumentu odpovědi JSON a zobrazení pouze `health_report` informací z výsledků.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Získání plně kvalifikovaného názvu domény uzlů clusteru

Možná budete muset znát plně kvalifikovaný název domény (FQDN) uzlu clusteru. Plně kvalifikovaný název domény pro různé uzly v clusteru můžete snadno načíst pomocí následujících příkladů:

**Všechny uzly**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Uzly Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Získat interní IP adresu uzlů clusteru

IP adresy vrácené příklady v této části nejsou přímo přístupné prostřednictvím Internetu. Jsou přístupné jenom v rámci Azure Virtual Network, který obsahuje cluster HDInsight.

Další informace o práci s HDInsight a virtuálními sítěmi najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Pokud chcete najít IP adresu, musíte znát interní plně kvalifikovaný název domény (FQDN) uzlů clusteru. Jakmile budete mít plně kvalifikovaný název domény, můžete získat IP adresu hostitele. V následujících příkladech se první dotaz Ambari pro plně kvalifikovaný název domény všech hostitelských uzlů. Pak se dotazuje Ambari na IP adresu každého hostitele.

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

### <a name="get-the-default-storage"></a>Získat výchozí úložiště

Clustery HDInsight musí jako výchozí úložiště používat účet Azure Storage nebo Data Lake Storage. Po vytvoření clusteru můžete tyto informace načíst pomocí Ambari. Například pokud chcete číst/zapisovat data do kontejneru mimo HDInsight.

Následující příklady načtou z clusteru výchozí konfiguraci úložiště:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Tyto příklady vrátí první konfiguraci použitou pro server ( `service_config_version=1` ), který obsahuje tyto informace. Pokud načtete hodnotu, která byla upravena po vytvoření clusteru, bude pravděpodobně nutné zobrazit seznam verzí konfigurace a načíst nejnovější z nich.

Vrácená hodnota je podobná jednomu z následujících příkladů:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Tato hodnota označuje, že cluster používá pro výchozí úložiště účet Azure Storage. `ACCOUNTNAME`Hodnota je název účtu úložiště. `CONTAINER`Část je název kontejneru objektů BLOB v účtu úložiště. Kontejner je kořenem úložiště kompatibilního se systémem HDFS pro daný cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen2 pro výchozí úložiště. `ACCOUNTNAME`Hodnoty a `CONTAINER` mají stejný význam jako Azure Storage výše zmíněné.

* `adl://home` – Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen1 pro výchozí úložiště.

    Chcete-li najít Data Lake Storage název účtu, použijte následující příklady:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Vrácená hodnota je podobná `ACCOUNTNAME.azuredatalakestore.net` , kde `ACCOUNTNAME` je název účtu Data Lake Storage.

    Pokud chcete najít adresář v rámci Data Lake Storage, který obsahuje úložiště pro cluster, použijte následující příklady:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Vrácená hodnota je podobná `/clusters/CLUSTERNAME/` . Tato hodnota je cesta v rámci Data Lake Storage účtu. Tato cesta je kořenem systému souborů kompatibilního s HDFS pro daný cluster.  

> [!NOTE]  
> Rutina [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) , kterou poskytuje [Azure PowerShell](/powershell/azure/) , vrátí taky informace o úložišti pro daný cluster.

### <a name="get-all-configurations"></a>Získat všechny konfigurace

Získejte konfigurace, které jsou k dispozici pro váš cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Tento příklad vrátí dokument JSON obsahující aktuální konfiguraci pro nainstalované součásti. Podívejte se na hodnotu *značky* . Následující příklad je výpis z dat vrácených z typu clusteru Spark.

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

Získejte konfiguraci pro komponentu, které vás zajímá. V následujícím příkladu nahraďte `INITIAL` hodnotou značky vrácenou z předchozího požadavku.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Tento příklad vrátí dokument JSON obsahující aktuální konfiguraci pro `livy2-conf` komponentu.

### <a name="update-configuration"></a>Aktualizovat konfiguraci

1. Vytvořit `newconfig.json` .  
   Upravte a potom zadejte následující příkazy:

   * Nahraďte `livy2-conf` novou součástí.
   * Nahraďte `INITIAL` skutečnou hodnotou získanou `tag` pro [získání všech konfigurací](#get-all-configurations).

     **A. bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShellový skript používá [JQ](https://stedolan.github.io/jq/).  `C:\HD\jq\jq-win64`Níže upravte, aby odrážela vaši skutečnou cestu a verzi [JQ](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ se používá k zapnutí dat načtených z HDInsight do nové šablony konfigurace. Konkrétně tyto příklady provedou následující akce:

   * Vytvoří jedinečnou hodnotu obsahující řetězec "Version" a datum, které je uloženo v `newtag` .

   * Vytvoří kořenový dokument pro novou konfiguraci.

   * Získá obsah `.items[]` pole a přidá jej pod **desired_config** element.

   * Odstraní `href` prvky, `version` a `Config` , protože tyto prvky nejsou nutné k odeslání nové konfigurace.

   * Přidá `tag` prvek s hodnotou `version#################` . Číselná část je založena na aktuálním datu. Každá konfigurace musí mít jedinečnou značku.

     Nakonec se data uloží do `newconfig.json` dokumentu. Struktura dokumentu by měla vypadat podobně jako v následujícím příkladu:

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

2. Upravit `newconfig.json` .  
   Otevřete `newconfig.json` dokument a upravte nebo přidejte hodnoty v `properties` objektu. Následující příklad změní hodnotu `"livy.server.csrf_protection.enabled"` z `"true"` na `"false"` .

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Až provedete úpravy, soubor uložte.

3. Odeslat `newconfig.json` .  
   K odeslání aktualizované konfigurace do Ambari použijte následující příkazy.

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

    Tyto příkazy odesílají obsah **newconfig.jsv** souboru do clusteru jako novou konfiguraci. Požadavek vrátí dokument JSON. Element **versionTag** v tomto dokumentu by se měl shodovat s verzí, kterou jste odeslali, a objekt **config** obsahuje změny konfigurace, které jste požadovali.

### <a name="restart-a-service-component"></a>Restartovat součást služby

V tomto okamžiku webové uživatelské rozhraní Ambari označuje, že je potřeba restartovat službu Spark, aby se nová konfigurace mohla projevit. K restartování služby použijte následující postup.

1. Pro povolení režimu údržby pro službu Spark2 použijte následující postup:

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
    ```

2. Ověřit režim údržby  

    Tyto příkazy odesílají dokument JSON na server, který se zapne v režimu údržby. Pomocí této žádosti můžete ověřit, že je služba teď v režimu údržby:

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

    Vrácená hodnota je `ON` .

3. Dále pomocí následujícího políčka vypněte službu Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > `href`Hodnota vrácená tímto identifikátorem URI používá interní IP adresu uzlu clusteru. Pokud ho chcete použít mimo cluster, nahraďte jeho `10.0.0.18:8080` část plně kvalifikovaným názvem domény clusteru.  

4. Ověřte požadavek.  
    Níže uvedený příkaz upravte nahrazením `29` skutečnou hodnotou `id` vrácenou z předchozího kroku.  Následující příkazy načtou stav žádosti:

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

    Odpověď `COMPLETED` indikuje, že žádost byla dokončena.

5. Po dokončení předchozí žádosti použijte následující příkaz a spusťte službu Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Služba teď používá novou konfiguraci.

6. Nakonec pomocí následujícího příkazu vypněte režim údržby.

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

## <a name="next-steps"></a>Další kroky

Úplný odkaz na REST API najdete v tématu [rozhraní Apache Ambari API reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Viz také, [autorizace uživatelů pro zobrazení Apache Ambari](./hdinsight-authorize-users-to-ambari.md)
