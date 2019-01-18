---
title: Zobrazit protokoly kontejnerů v Azure Service Fabric | Dokumentace Microsoftu
description: Popisuje postup zobrazení protokolů kontejneru pro spuštěné služby container Service Fabric pomocí Service Fabric Exploreru.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: twhitney
ms.openlocfilehash: d66e27d860d18a37ffd9c6355b8d769116f26d73
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391240"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Zobrazit protokoly pro Service Fabric container service
Azure Service Fabric je orchestrátor kontejnerů a podporuje obě [kontejnerů Linuxu a Windows](service-fabric-containers-overview.md).  Tento článek popisuje postup zobrazení protokolů kontejneru spuštěných služby kontejneru nebo dead kontejneru, takže můžete diagnostikovat a řešit problémy.

## <a name="access-the-logs-of-a-running-container"></a>Přístup k protokolům spuštěný kontejner
Protokoly kontejneru můžete získat přístup pomocí [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Ve webovém prohlížeči otevřete Service Fabric Explorer z koncového bodu správy clusteru tak, že přejdete do [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Protokoly kontejneru jsou umístěny na uzlu clusteru, na kterém běží instance služby kontejneru na. Třeba získat protokoly webové front-end kontejneru [ukázkovou aplikaci Linux Voting](service-fabric-quickstart-containers-linux.md). Ve stromovém zobrazení rozbalte **clusteru**>**aplikací**>**VotingType**>**fabric: / Voting / azurevotefront**.  Pak rozbalte oddíl (d1aa737e-f22a-e347-be16-eec90be24bc1, v tomto příkladu) a zobrazit, že je kontejner spuštěný v uzlu clusteru *_lnxvm_0*.

Ve stromovém zobrazení, vyhledejte balíček kódu na *_lnxvm_0* uzlu tak, že rozbalíte **uzly**>**_lnxvm_0**>**fabric: / Voting**  > **azurevotfrontPkg**>**balíčky kódu**>**kód**.  Vyberte **protokoly kontejneru** možnosti se zobrazí protokoly kontejneru.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Přístup k protokolům dead nebo zhroucené kontejneru
Počínaje v6.2, můžete také načíst protokoly pro neaktivní nebo zhroucené kontejneru pomocí [rozhraní REST API](/rest/api/servicefabric/sfclient-index) nebo [Service Fabric CLI (SFCTL)](service-fabric-cli.md) příkazy.

### <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru
Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Když **ContainersRetentionCount** atribut není zadán, zachovají se žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

Nastavení **RunInteractive** odpovídá Docker `--interactive` a `tty` [příznaky](https://docs.docker.com/engine/reference/commandline/run/#options). Pokud toto nastavení nastavena na hodnotu true v souboru manifestu, tyto příznaky se používá ke spuštění kontejneru.  

### <a name="rest"></a>REST
Použití [získat protokoly nasazení na uzel kontejneru](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operaci získat protokoly pro kontejner chyb. Zadejte název uzlu, který byl kontejner spuštěný, název aplikace, název manifestu služby a název balíčku kódu.  Zadejte `&Previous=true`. Odpověď bude obsahovat protokoly kontejneru pro neaktivní kontejner instance balíček kódu.

Identifikátor URI požadavku má následující formát:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Příklad žádosti:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Text odpovědi 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Použití [sfctl get-container protokoly služby](service-fabric-sfctl-service.md) příkaz pro načtení v protokolech chyb kontejneru.  Zadejte název uzlu, který byl kontejner spuštěný, název aplikace, název manifestu služby a název balíčku kódu. Zadejte `--previous` příznak.  Odpověď bude obsahovat protokoly kontejneru pro neaktivní kontejner instance balíček kódu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpověď:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Další postup
- Seznámení se základními [vytvořit kurz vývoje aplikace kontejneru s Linuxem](service-fabric-tutorial-create-container-images.md).
- Další informace o [Service Fabric a kontejnery](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
