---
title: Zobrazení protokolů kontejnerů ve službě Azure Fabric
description: Popisuje, jak zobrazit protokoly kontejnerů pro spuštěné služby kontejneru Service Fabric pomocí aplikace Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458179"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Zobrazit protokoly pro službu kontejneru Service Fabric
Azure Service Fabric je kontejner orchestrator a podporuje [linuxové i windows kontejnery](service-fabric-containers-overview.md).  Tento článek popisuje, jak zobrazit protokoly kontejnerů spuštěné služby kontejneru nebo mrtvý kontejner, takže můžete diagnostikovat a řešit problémy.

## <a name="access-the-logs-of-a-running-container"></a>Přístup k protokolům spuštěného kontejneru
Protokoly kontejnerů lze přistupovat pomocí [aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Ve webovém prohlížeči otevřete aplikaci Service Fabric Explorer z `http://mycluster.region.cloudapp.azure.com:19080/Explorer`koncového bodu správy clusteru tak, že přejdete na .  

Protokoly kontejnerů jsou umístěny v uzlu clusteru, na který je spuštěna instance služby kontejneru. Jako příklad získáte protokoly webového front-endkontejneru [ukázkové aplikace Linux Voting](service-fabric-quickstart-containers-linux.md). Ve stromovém zobrazení rozbalte **položku Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**.  Potom rozbalte oddíl (d1aa737e-f22a-e347-be16-eec90be24bc1, v tomto příkladu) a uvidíte, že kontejner je spuštěn na uzlu clusteru *_lnxvm_0*.

Ve stromovém zobrazení najděte balíček kódu v *uzlu _lnxvm_0* rozbalením **uzlů**>**_lnxvm_0**>**prostředků infrastruktury:/Voting**>**azurevotfrontPkg**>**Code Packages**>**kódu**.  Pak vyberte možnost **Protokoly kontejnerů,** chcete-li zobrazit protokoly kontejnerů.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Přístup k protokolům mrtvého nebo havarovaného kontejneru
Počínaje v6.2, můžete také načíst protokoly pro mrtvý nebo havarovaný kontejner pomocí [rest API](/rest/api/servicefabric/sfclient-index) nebo service [fabric CLI (SFCTL)](service-fabric-cli.md) příkazy.

### <a name="set-container-retention-policy"></a>Nastavení zásad uchovávání informací kontejneru
Jako pomoc s diagnostikou selhání spuštění kontejneru Service Fabric (verze 6.1 nebo vyšší) podporuje zachování kontejnerů, které se ukončily nebo které se nepovedlo spustit. Tuto zásadu je možné nastavit v souboru **ApplicationManifest.xml**, jak ukazuje následující fragment kódu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Nastavení **ContainersRetentionCount** určuje počet kontejnerů, které se při svém selhání zachovají. Pokud je zadaná hodnota záporná, zachovají se všechny kontejnery, které selhaly. Pokud není zadán atribut **ContainersRetentionCount,** nebudou zachovány žádné kontejnery. Atribut **ContainersRetentionCount** také podporuje parametry aplikace, takže uživatelé mohou zadat různé hodnoty pro testovací a produkční clustery. Při použití této funkce použijte omezení umístění, aby služba kontejneru cílila na konkrétní uzel. Zabrání se tak přesunu služby kontejneru na jiné uzly. Všechny kontejnery zachované pomocí této funkce je nutné ručně odebrat.

Nastavení **RunInteractive** odpovídá Dockeru `--interactive` a `tty` [příznaky](https://docs.docker.com/engine/reference/commandline/run/#options). Pokud je toto nastavení nastaveno na hodnotu true v souboru manifestu, tyto příznaky se používají ke spuštění kontejneru.  

### <a name="rest"></a>REST
Pomocí operace [Získat protokoly kontejnerů nasazených na uzel](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) získat protokoly pro havarovaný kontejner. Zadejte název uzlu, na který byl kontejner spuštěn, název aplikace, název manifestu služby a název balíčku kódu.  Zadejte `&Previous=true`. Odpověď bude obsahovat protokoly kontejneru pro mrtvý kontejner instance balíčku kódu.

Identifikátor URI požadavku má následující formulář:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Příklad požadavku:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Tělo odezvy:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Servisní tkanina (SFCTL)
Pomocí příkazu [get-container-logs služby sfctl](service-fabric-sfctl-service.md) načíst protokoly pro havarovaný kontejner.  Zadejte název uzlu, na který byl kontejner spuštěn, název aplikace, název manifestu služby a název balíčku kódu. Zadejte `--previous` příznak.  Odpověď bude obsahovat protokoly kontejneru pro mrtvý kontejner instance balíčku kódu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpověď:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Další kroky
- Práce prostřednictvím [vytvořit linuxový kontejner aplikace tutorial](service-fabric-tutorial-create-container-images.md).
- Další informace o [service fabric a kontejnerech](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
