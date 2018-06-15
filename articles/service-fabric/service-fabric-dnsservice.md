---
title: Služba Azure Service Fabric DNS | Microsoft Docs
description: Použijte službu dns Service Fabric pro zjišťování mikroslužeb z v clusteru.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204990"
---
# <a name="dns-service-in-azure-service-fabric"></a>Služba DNS v Azure Service Fabric
Služba DNS je volitelné systému služba, kterou můžete povolit v clusteru pro zjišťování dalších služeb pomocí protokolu DNS. 

Mnoho služeb, zejména kontejnerizované služeb, může mít název existující adresu URL a schopnost vyřešit pomocí standardní protokol DNS (a nikoli protokol služby DNS) je žádoucí, zejména ve scénářích "navýšení a posunutí". Služba DNS umožňuje mapování názvů DNS pro název služby a proto přeložit koncový bod IP adresy. 

Služba DNS mapuje názvy DNS pro názvy služby, které se pak vyřeší služby DNS k vrácení koncového bodu služby. V době vytvoření je zadaný název DNS pro službu.

![Koncové body služby](./media/service-fabric-dnsservice/dns.png)

Dynamické porty nejsou podporovány službou DNS. K rozpoznávání služeb, které jsou zveřejněné na dynamické porty, použijte [reverse služba proxy](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Povolení služby DNS
Při vytváření clusteru pomocí portálu je ve výchozím nastavení povolena služba DNS **služba DNS zahrnují** políčko **konfigurace clusteru** nabídky:

![Povolení služby DNS prostřednictvím portálu][2]

Pokud nepoužíváte portálu k vytvoření clusteru nebo aktualizujete existující cluster, budete muset povolit služba DNS v šabloně:

- Pokud chcete nasadit do nového clusteru, můžete použít [ukázkových šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořte vlastní šablony Resource Manageru. 
- Pokud chcete aktualizovat existující cluster, můžete přejít do skupiny prostředků clusteru na portál a klikněte na **skriptu pro automatizaci** pro práci s šablonu, která odráží aktuální stav clusteru a další prostředky ve skupině. Další informace najdete v tématu [vyexportujte šablonu ze skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Jakmile vytvoříte šablonu, můžete povolit službu DNS pomocí následujících kroků:

1. Zkontrolujte, zda `apiversion` je nastaven na `2017-07-01-preview` nebo novější pro `Microsoft.ServiceFabric/clusters` prostředků a pokud ne, aktualizovat, je znázorněné v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teď povolit službu DNS přidáním následující `addonFeatures` části po `fabricSettings` části, jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Jakmile clusteru šablony aktualizovaly s předchozí změny, je použít a umožní upgradu dokončení. Po dokončení upgradu, služba DNS systému spuštění v clusteru. Název služby je `fabric:/System/DnsService`, a najdete ji v části **systému** služby v Service Fabric explorer části. 


## <a name="setting-the-dns-name-for-your-service"></a>Nastavení názvu DNS pro vaši službu
Jakmile je spuštěna služba DNS v clusteru můžete nastavit název DNS pro vaše služby buď deklarativně pro výchozí služby v `ApplicationManifest.xml` nebo pomocí příkazů prostředí PowerShell.

Název DNS služby je možné přeložit v rámci clusteru. Důrazně doporučujeme použít schéma pojmenování `<ServiceDnsName>.<AppInstanceName>`, například `service1.application1`. Tím zajistíte jedinečnost název DNS v rámci clusteru. Pokud je aplikace nasazena pomocí Docker compose, služby se automaticky přiřazují pomocí toto schéma pojmenování názvy DNS.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Nastavení názvu DNS pro výchozí služba v ApplicationManifest.xml
Otevřete projekt v sadě Visual Studio nebo svém oblíbeném editoru a otevřete `ApplicationManifest.xml` souboru. Přejděte do části výchozí služby a pro každou službu přidat `ServiceDnsName` atribut. Následující příklad ukazuje, jak nastavit název DNS služby `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Jakmile je aplikace nasazená, instance služby v Service Fabric explorer zobrazuje název DNS pro tuto instanci, jak je znázorněno na následujícím obrázku: 

![Koncové body služby][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Nastavení názvu DNS pro službu pomocí prostředí Powershell
Název DNS pro službu, můžete nastavit při vytváření pomocí `New-ServiceFabricService` prostředí Powershell. Následující příklad vytvoří nový bezstavové služby s názvem DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Pomocí DNS v službách
Pokud nasazujete více než jedna služba, můžete najít koncové body jiných služeb, ke komunikaci s pomocí názvu DNS. Služba DNS používá se pouze pro bezstavové služby, protože protokol DNS nemůže komunikovat s stavové služby. Pro stavové služby, můžete použít integrované [reverse služba proxy](./service-fabric-reverseproxy.md) pro volání protokolu http k volání konkrétní službu oddílu. Dynamické porty nejsou podporovány službou DNS. Reverzní proxy server můžete použít k rozpoznávání služeb, které používají dynamické porty.

Následující kód ukazuje, jak volat jiné služby, která je jednoduše volání regulární http, kde zadáte port a všechny cesty jako část adresy URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Další postup
Další informace o komunikaci služby v rámci clusteru s [připojení a komunikovat se službami](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
