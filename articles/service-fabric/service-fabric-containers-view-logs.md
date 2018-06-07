---
title: Zobrazit protokoly kontejnerů v Azure Service Fabric | Microsoft Docs
description: Popisuje, jak zobrazit protokoly kontejner pro spuštěné služby kontejneru Service Fabric pomocí Service Fabric Exploreru.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: c8b6bc791700e6811f5681ee70329e4d2ac05991
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824607"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Zobrazit protokoly pro službu kontejneru Service Fabric
Azure Service Fabric je kontejner orchestrator a podporuje obě [Linux a Windows kontejnery](service-fabric-containers-overview.md).  Tento článek popisuje, jak zobrazit protokoly kontejneru spuštěnou službu kontejneru nebo kontejner neaktivní, aby mohli diagnostice a řešení problémů.

## <a name="access-the-logs-of-a-running-container"></a>Přístup k protokolům spuštěné kontejneru
Kontejner protokoly lze přistupovat pomocí [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Ve webovém prohlížeči, otevřete Service Fabric Explorer z koncového bodu správy clusteru tak, že přejdete na [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Kontejner protokoly jsou umístěny na uzlu clusteru, který běží instance služby kontejneru na. Jako příklad získání protokolů kontejneru front-endu webové služby [Linux hlasování ukázkovou aplikaci](service-fabric-quickstart-containers-linux.md). Ve stromovém zobrazení, rozbalte položku **clusteru**>**aplikace**>**VotingType**>**fabric: / Voting / azurevotefront**.  Potom rozbalte oddíl (d1aa737e-f22a-e347-be16-eec90be24bc1, v tomto příkladu) a zda kontejner je spuštěn na uzlu clusteru *_lnxvm_0*.

Ve stromovém zobrazení najít balíček kódu na *_lnxvm_0* uzlu rozšířením **uzly**>**_lnxvm_0**>**fabric: / hlasování**  > **azurevotfrontPkg**>**kód balíčky**>**kód**.  Vyberte **kontejneru protokoly** možnosti se zobrazí v protokolech kontejneru.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Přístup k protokolům neaktivní nebo zhroucené kontejneru
Počínaje v6.2, může také načíst protokoly pro kontejner neaktivní nebo zhroucené pomocí [rozhraní REST API](/rest/api/servicefabric/sfclient-index) nebo [Service Fabric rozhraní příkazového řádku (SFCTL)](service-fabric-cli.md) příkazy.

### <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru
Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Když **ContainersRetentionCount** atribut nezadáte, budou se uchovávat žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

### <a name="rest"></a>REST
Použití [získat nasadit kontejner protokoly na uzlu](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operace k získání protokoly pro kontejner zhroucené. Zadejte název tohoto uzlu, spuštěné v kontejneru, název aplikace, manifest název služby a název balíčku kódu.  Zadejte `&Previous=true`. Odpověď bude obsahovat protokoly kontejner pro neaktivní kontejner instance balíček kódu.

Identifikátoru URI požadavku má následující formát:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Příklad žádost:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Text odpovědi 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Použití [sfctl služby get kontejneru protokoly](service-fabric-sfctl-service.md) příkaz k načtení protokoly pro kontejner zhroucené.  Zadejte název tohoto uzlu, spuštěné v kontejneru, název aplikace, manifest název služby a název balíčku kódu. Zadejte `-previous` příznak.  Odpověď bude obsahovat protokoly kontejner pro neaktivní kontejner instance balíček kódu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Odpověď:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Další postup
- Fungovat prostřednictvím [vytvořit kurz Linux kontejneru aplikace](service-fabric-tutorial-create-container-images.md).
- Další informace o [Service Fabric a kontejnery](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
