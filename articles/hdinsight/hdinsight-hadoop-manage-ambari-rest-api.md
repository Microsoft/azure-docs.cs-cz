---
title: Sledování a správa Hadoopu pomocí rozhraní Ambari REST API – Azure HDInsight
description: Přečtěte si, jak pomocí Ambari monitorovat a spravovat clustery Hadoop v Azure HDInsight. V tomto dokumentu se dozvíte, jak používat rozhraní API Ambari REST, které je součástí clusterů HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381388"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Přečtěte si, jak pomocí rozhraní APACHE Ambari REST API spravovat a monitorovat clustery Apache Hadoop v Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Co je Apache Ambari

[Apache Ambari](https://ambari.apache.org) zjednodušuje správu a monitorování clusterů Hadoop tím, že poskytuje snadno použitelné webové uživatelské prostředí podporované jeho [REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari je k dispozici ve výchozím nastavení s linuxovými clustery HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash na Ubuntu na Windows 10.  Příklady v tomto článku používají prostředí Bash ve Windows 10. Postup instalace najdete v [příručce k instalaci podsystému Windows pro Linux pro Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Ostatní [unixové granáty](https://www.gnu.org/software/bash/) budou fungovat také.  Příklady s některými drobnými úpravami mohou fungovat na příkazovém řádku systému Windows.  Nebo můžete použít prostředí Windows PowerShell.

* jq, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Prostředí Windows PowerShell.  Nebo můžete použít [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Základní identifikátor jednotného prostředku pro rozhraní API ambari rest API

 Základní identifikátor URI (Uniform Resource Identifier) pro rozhraní API `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`Ambari REST na rozhraní HDInsight je , kde `CLUSTERNAME` je název vašeho clusteru.  Názvy clusterů v identifikátorech URI **rozlišují malá a velká písmena**.  Zatímco název clusteru v plně kvalifikované části názvu domény (Plně`CLUSTERNAME.azurehdinsight.net`kvalifikovaný název domény) uri ( ) je malá a velká písmena, ostatní výskyty v URI jsou malá a velká písmena.

## <a name="authentication"></a>Authentication

Připojení k Ambari na HDInsight vyžaduje HTTPS. Použijte název účtu správce (výchozí je **správce)** a heslo, které jste zadali při vytváření clusteru.

Pro clustery balíčků podnikového `admin`zabezpečení použijte místo něho plně kvalifikované uživatelské jméno, jako je `username@domain.onmicrosoft.com`.

## <a name="examples"></a>Příklady

### <a name="setup-preserve-credentials"></a>Nastavení (zachovat pověření)

Zachovejte pověření, abyste je pro každý příklad nezadali znovu.  Název clusteru bude zachován v samostatném kroku.

**A. Bash**  
Upravte níže uvedený `PASSWORD` skript nahrazením skutečným heslem.  Pak zadejte příkaz.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Správně identifikovat název clusteru s velikostmi písmen

Skutečné velikosti písmen názvu clusteru se mohou lišit od očekávání.  Kroky zde zobrazí skutečné velikostě písmen a pak jej uložte do proměnné pro všechny pozdější příklady.

Upravte níže uvedené `CLUSTERNAME` skripty a nahraďte je názvem clusteru. Pak zadejte příkaz. (Název clusteru pro hlavní název sítě se nerozlišují malá a velká písmena.)

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

Následující příklad používá [jq](https://stedolan.github.io/jq/) nebo [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) analyzovat dokument odpovědi JSON a zobrazit pouze `health_report` informace z výsledků.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Získání hlavního název sítě clusteru uzlů

Možná budete potřebovat znát plně kvalifikovaný název domény (FQDN) uzlu clusteru. Hlavní název sítě pro různé uzly v clusteru můžete snadno načíst pomocí následujících příkladů:

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

**Zookeeper uzly**

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

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Získání interní IP adresy uzlů clusteru

IP adresy vrácené příklady v této části nejsou přímo přístupné přes internet. Jsou přístupné jenom v rámci virtuální sítě Azure, která obsahuje cluster HDInsight.

Další informace o práci s HDInsight a virtuálními sítěmi najdete [v tématu Plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Chcete-li najít adresu IP, musíte znát interní plně kvalifikovaný název domény (FQDN) uzlů clusteru. Jakmile budete mít hlavní název souboru, můžete získat IP adresu hostitele. Následující příklady první dotaz Ambari pro reqdn všech uzlů hostitele. Pak se dotazuje Ambari na IP adresu každého hostitele.

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

### <a name="get-the-default-storage"></a>Získání výchozího úložiště

Clustery HDInsight musí jako výchozí úložiště používat účet úložiště Azure nebo úložiště datových jezer. Ambari můžete použít k načtení těchto informací po vytvoření clusteru. Například pokud chcete číst a zapisovat data do kontejneru mimo HDInsight.

Následující příklady načítají výchozí konfiguraci úložiště z clusteru:

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
> Tyto příklady vrátí první konfiguraci`service_config_version=1`použitou na serveru ( ), který obsahuje tyto informace. Pokud načtete hodnotu, která byla změněna po vytvoření clusteru, bude pravděpodobně nutné uvést seznam verzí konfigurace a načíst nejnovější.

Vrácená hodnota je podobná jednomu z následujících příkladů:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Tato hodnota označuje, že cluster používá účet úložiště Azure pro výchozí úložiště. Hodnota `ACCOUNTNAME` je název účtu úložiště. Část `CONTAINER` je název kontejneru objektů blob v účtu úložiště. Kontejner je kořenúložiště kompatibilního úložiště HDFS pro cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen2 pro výchozí úložiště. Hodnoty `ACCOUNTNAME` `CONTAINER` a mají stejný význam jako pro Azure Storage uvedené výše.

* `adl://home`- Tato hodnota označuje, že cluster používá Azure Data Lake Storage Gen1 pro výchozí úložiště.

    Chcete-li najít název účtu Úložiště datového jezera, použijte následující příklady:

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

    Vrácená hodnota je `ACCOUNTNAME.azuredatalakestore.net`podobná `ACCOUNTNAME` , kde je název účtu úložiště datového jezera.

    Chcete-li najít adresář v úložišti datového jezera, který obsahuje úložiště pro cluster, použijte následující příklady:

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

    Vrácená hodnota je `/clusters/CLUSTERNAME/`podobná . Tato hodnota je cesta v rámci účtu úložiště datového jezera. Tato cesta je kořenem systému souborů kompatibilního s HDFS pro cluster.  

> [!NOTE]  
> Rutina [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) poskytovaná [prostředím Azure PowerShell](/powershell/azure/overview) také vrací informace o úložišti pro cluster.

### <a name="get-all-configurations"></a>Získejte všechny konfigurace

Získejte konfigurace, které jsou k dispozici pro váš cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Tento příklad vrátí dokument JSON obsahující aktuální konfiguraci pro nainstalované součásti. Viz hodnota *značky.* Následující příklad je výňatek z dat vrácených z typu clusteru Spark.

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

Získejte konfiguraci pro komponentu, která vás zajímá. V následujícím příkladu `INITIAL` nahraďte hodnotou značky vrácenou z předchozího požadavku.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Tento příklad vrátí dokument JSON obsahující aktuální `livy2-conf` konfiguraci komponenty.

### <a name="update-configuration"></a>Aktualizovat konfiguraci

1. Vytvořit `newconfig.json`.  
   Upravte a zadejte níže uvedené příkazy:

   * Nahraďte `livy2-conf` novou komponentou.
   * Nahraďte `INITIAL` skutečnou `tag` hodnotou načtenou z [možnosti Získat všechny konfigurace](#get-all-configurations).

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     Skript Prostředí PowerShell používá [jq](https://stedolan.github.io/jq/).  Upravte `C:\HD\jq\jq-win64` níže, aby odrážely vaše aktuální cestu a verzi [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq se používá k přeměně dat načtených z HDInsight na novou konfigurační šablonu. Konkrétně tyto příklady provést následující akce:

   * Vytvoří jedinečnou hodnotu obsahující řetězec "verze" a datum, které je uloženo v aplikaci `newtag`.

   * Vytvoří kořenový dokument pro novou konfiguraci.

   * Získá obsah `.items[]` pole a přidá jej pod **desired_config** element.

   * Odstraní `href`prvky `version`, `Config` a, protože tyto prvky nejsou potřebné k odeslání nové konfigurace.

   * Přidá `tag` prvek s hodnotou `version#################`. Číselná část je založena na aktuálním datu. Každá konfigurace musí mít jedinečnou značku.

     Nakonec jsou data uložena `newconfig.json` do dokumentu. Struktura dokumentu by měla vypadat podobně jako v následujícím příkladu:

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
   Otevřete `newconfig.json` dokument a upravte `properties` nebo přidejte hodnoty v objektu. Následující příklad změní hodnotu `"true"` `"false"`z `"livy.server.csrf_protection.enabled"` na .

        "livy.server.csrf_protection.enabled": "false",

    Po dokončení úprav soubor uložte.

3. Odeslat `newconfig.json`.  
   K odeslání aktualizované konfigurace do ambari použijte následující příkazy.

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

    Tyto příkazy odešlí obsah souboru **newconfig.json** do clusteru jako novou konfiguraci. Požadavek vrátí dokument JSON. Prvek **versionTag** v tomto dokumentu by měl odpovídat verzi, kterou jste odeslali, a objekt **configs** obsahuje požadované změny konfigurace.

### <a name="restart-a-service-component"></a>Restartování součásti služby

V tomto okamžiku webové uživatelské uživatelské nastavení Ambari označuje, že služba Spark musí být restartována, než se nová konfigurace projeví. K restartování služby použijte následující kroky.

1. K povolení režimu údržby služby Spark2 použijte následující:

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

2. Ověření režimu údržby  

    Tyto příkazy odešlou dokument JSON na server, který zapne režim údržby. Pomocí následujícího požadavku můžete ověřit, zda je služba nyní v režimu údržby:

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

    Vrácená hodnota `ON`je .

3. Dále vypněte službu Spark2 pomocí následujícího:

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
    > Hodnota `href` vrácená tímto identifikátorem URI používá interní adresu IP uzlu clusteru. Chcete-li jej použít mimo `10.0.0.18:8080` cluster, nahraďte část kvalifikovaným kvalifikovaným názevem clusteru.  

4. Ověřte požadavek.  
    Upravte níže uvedený `29` příkaz nahrazením `id` skutečnou hodnotou vrácené z předchozího kroku.  Stav požadavku načítají následující příkazy:

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

    Odpověď označuje, `COMPLETED` že požadavek byl dokončen.

5. Po dokončení předchozího požadavku spusťte službu Spark2 pomocí následujícího příkazu.

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

    Služba nyní používá novou konfiguraci.

6. Nakonec režim údržby vypněte pomocí následujícího příkazu.

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

Úplný odkaz na rozhraní REST API naleznete v [tématu Apache Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Viz také [Autorizovat uživatele pro zobrazení Apache Ambari](./hdinsight-authorize-users-to-ambari.md)
