---
title: Jak získat názvy hostitelů uzlů clusteru Azure HDInsight
description: Přečtěte si, jak získat názvy hostitelů a plně kvalifikovaný název domény pro uzly clusteru Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111440"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Vyhledání názvů hostitelů uzlů clusteru

Cluster HDInsight se vytvoří pomocí veřejné služby DNS clustername.azurehdinsight.net. Při použití SSH na jednotlivých uzlech nebo při nastavování připojení k uzlům clusteru ve stejné vlastní virtuální síti je třeba použít název hostitele nebo plně kvalifikované názvy domény (FQDN) uzlů clusteru.

V tomto článku se dozvíte, jak získat názvy hostitelů uzlů clusteru. Můžete ji získat ručně prostřednictvím webového uživatelského rozhraní Ambari nebo automaticky prostřednictvím Ambari REST API.

> [!WARNING]
> Použijte prosím následující doporučené přístupy k načtení názvů hostitelů uzlů clusteru. Čísla v názvu hostitele nejsou zaručená v sekvenci a HDInsight může změnit formát názvu hostitele tak, aby byl zarovnaný k virtuálním počítačům s aktualizací Release. Neprovádějte závislost na všech určitých konvencích pojmenování, které dnes existují. 
>

Názvy hostitelů můžete získat prostřednictvím uživatelského rozhraní Ambari nebo Ambari REST API. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Získání názvů hostitelů z webového uživatelského rozhraní Ambari
Můžete použít webové uživatelské rozhraní Ambari k získání názvů hostitelů při použití SSH pro uzel. Zobrazení hostitelé webového uživatelského rozhraní Ambari je k dispozici v clusteru HDInsight v `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

![Get-Host-Names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Získat názvy hostitelů z Ambari REST API
Při vytváření skriptů pro automatizaci můžete pomocí REST API Ambari získat názvy hostitelů před tím, než provedete připojení k hostitelům. Čísla v názvu hostitele nejsou zaručená v sekvenci a HDInsight může změnit formát názvu hostitele tak, aby byl zarovnaný k virtuálním počítačům s aktualizací Release. Neprovádějte závislost na všech určitých konvencích pojmenování, které dnes existují. 

Tady je několik příkladů, jak načíst plně kvalifikovaný název domény pro uzly v clusteru. Další informace o Ambari REST API najdete v tématu [Správa clusterů HDInsight pomocí REST API Apache Ambari](.\hdinsight-hadoop-manage-ambari-rest-api.md) .

Následující příklad používá [JQ](https://stedolan.github.io/jq/) nebo [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) k analýze dokumentu odpovědi JSON a zobrazení pouze názvů hostitelů.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```