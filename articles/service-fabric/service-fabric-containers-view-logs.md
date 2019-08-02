---
title: Zobrazení protokolů kontejnerů v Azure Service Fabric | Microsoft Docs
description: Popisuje postup zobrazení protokolů kontejneru pro běžící Service Fabric služby kontejneru pomocí Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: atsenthi
ms.openlocfilehash: 3fa40d794d02da08d29b6cac652edf493977f8e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599724"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Zobrazit protokoly pro službu Service Fabric Container Service
Azure Service Fabric je kontejner Orchestrator a podporuje [kontejnery Linux a Windows](service-fabric-containers-overview.md).  Tento článek popisuje, jak zobrazit protokoly kontejneru běžící služby kontejneru nebo mrtvého kontejneru, abyste mohli diagnostikovat a řešit problémy.

## <a name="access-the-logs-of-a-running-container"></a>Přístup k protokolům spuštěného kontejneru
K protokolům kontejneru se dá dostat pomocí [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Ve webovém prohlížeči otevřete Service Fabric Explorer z koncového bodu správy clusteru, a to tak, že [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)přejdete na.  

Protokoly kontejnerů se nachází na uzlu clusteru, na kterém je instance služby kontejneru spuštěná. Jako příklad Získejte protokoly webového front-endu kontejneru pro [hlasovací ukázkovou aplikaci pro Linux](service-fabric-quickstart-containers-linux.md). Ve stromovém zobrazení rozbalte **Clusterové**>**aplikace**>**VotingType**>**Fabric:/hlasovací/azurevotefront**.  Pak rozbalte oddíl (v tomto příkladu d1aa737e-f22a-e347-be16-eec90be24bc1) a podívejte se, že kontejner je spuštěný v uzlu clusteru *_lnxvm_0*.

Ve stromovém zobrazení Najděte balíček kódu v uzlu *_lnxvm_0* rozbalením **uzlů**> **_lnxvm_0**>**Fabric:/hlasovacího**>kódu**azurevotfrontPkg**>.>**Kód**balíčků.  Pak vyberte možnost **protokoly kontejnerů** a zobrazte tak protokoly kontejnerů.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Přístup k protokolům mrtvého nebo neúspěšného kontejneru
Počínaje verzí v 6.2 můžete také načíst protokoly pro nedoručený nebo chybový kontejner pomocí [rozhraní REST API](/rest/api/servicefabric/sfclient-index) nebo příkazů [Service Fabric CLI (SFCTL)](service-fabric-cli.md) .

### <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru
Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Pokud není zadán atribut **ContainersRetentionCount** , nebudou zachovány žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

Nastavení **RunInteractive** odpovídá Docker `--interactive` a `tty` [Flags](https://docs.docker.com/engine/reference/commandline/run/#options). Pokud je toto nastavení v souboru manifestu nastaveno na hodnotu true, budou tyto příznaky použity ke spuštění kontejneru.  

### <a name="rest"></a>REST
Pomocí operace [získat protokoly kontejneru nasazené v uzlu](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) můžete získat protokoly pro kontejner, u kterého došlo k chybě. Zadejte název uzlu, na kterém byl kontejner spuštěn, název aplikace, název manifestu služby a název balíčku kódu.  Zadejte `&Previous=true`. Odpověď bude obsahovat protokoly kontejneru pro nedoručený kontejner instance balíčku kódu.

Identifikátor URI žádosti má následující tvar:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Příklad požadavku:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

tělo odpovědi 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Pomocí příkazu [sfctl Service Get-Container-logs](service-fabric-sfctl-service.md) načtěte protokoly pro chybnou kontejner.  Zadejte název uzlu, na kterém byl kontejner spuštěn, název aplikace, název manifestu služby a název balíčku kódu. `--previous` Zadejte příznak.  Odpověď bude obsahovat protokoly kontejneru pro nedoručený kontejner instance balíčku kódu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpověď:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Další postup
- Pracujte s [kurzem vytvoření aplikace typu kontejner pro Linux](service-fabric-tutorial-create-container-images.md).
- Další informace o [Service Fabric a kontejnerech](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
