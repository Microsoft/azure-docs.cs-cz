---
title: Služba Azure Service Fabric DNS | Dokumentace Microsoftu
description: Pomocí Service Fabric dns služby pro zjišťování mikroslužeb v rámci clusteru.
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
ms.date: 7/20/2018
ms.author: msfussell
ms.openlocfilehash: 3c8eac98414fa43213136940fb4c91694a78a2c1
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397522"
---
# <a name="dns-service-in-azure-service-fabric"></a>Služba DNS v Azure Service Fabric
Služba DNS je volitelná systémová služba, kterou můžete povolit ve vašem clusteru na zjišťovat další služby pomocí protokolu DNS. 

Mnoho služeb, zejména kontejnerizovaných služeb, jsou adresovatelné prostřednictvím předem stávající adresy URL. Schopnost vyřešit těmito službami prostřednictvím standardního protokolu DNS, nikoli protokol služba pojmenování Service Fabric není žádoucí. Služba DNS umožňuje mapovat názvy DNS na název služby a proto přeložit IP adresy koncového bodu. Tato funkce udržuje přenositelnost kontejnerizovaných služeb napříč různými platformami a mohl provádět "metodou lift and shift" scénáře jednodušší, protože můžete použít existující službu adresy URL a nemusíte přepisovat kód využít službu pojmenování. 

Služba DNS mapuje názvy DNS na názvy služeb, které pak jsou vyřešeny pojmenování Service k vrácení koncového bodu služby. V době vytvoření je zadaný název DNS pro službu. Následující diagram ukazuje, jak funguje služba DNS pro bezstavové služby.

![Koncové body služby](./media/service-fabric-dnsservice/stateless-dns.png)

Od verze Service Fabric verze 6.3, protokol Service Fabric DNS se prodloužila zahrnout schéma adresování dělené stavové služby. Tato rozšíření umožňují přeložit IP adresy konkrétního oddílu pomocí kombinace stavové služby název DNS a název oddílu. Podporují se všechny tři schémata dělení:

- S názvem rozdělení do oddílů
- Rozsahové dělení
- Vytváření oddílů singleton

Následující diagram ukazuje, jak funguje služba DNS pro dělené stavové služby.

![Koncové body stavové služby](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamické porty nejsou podporovány službou DNS. K rozpoznávání služeb, které jsou zveřejněné na dynamické porty, použijte [reverzní proxy service](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Povolení služby DNS
Při vytváření clusteru pomocí portálu, služba DNS je povolena ve výchozím nastavení **služba DNS zahrnují** zaškrtávací políčko na **konfigurace clusteru** nabídky:

![Povolení služby DNS na portálu](./media/service-fabric-dnsservice/enable-dns-service.png)

Pokud nepoužíváte k vytvoření clusteru na portálu nebo pokud aktualizujete existující cluster, bude nutné povolit službu DNS v šabloně:

- Pokud chcete nasadit nový cluster, můžete použít [ukázkových šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořit vlastní šablonu Resource Manageru. 
- Pokud chcete aktualizovat existující cluster, můžete přejít do skupiny prostředků clusteru na portálu a klikněte na **automatizační skript** pracovat se šablonou, která odráží aktuální stav clusteru a dalších prostředků ve skupině. Další informace najdete v tématu [vyexportujte šablonu ze skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Jakmile budete mít šablony, můžete povolit službu DNS pomocí následujících kroků:

1. Zkontrolujte, že `apiversion` je nastavena na `2017-07-01-preview` nebo vyšší `Microsoft.ServiceFabric/clusters` prostředků a pokud ne, aktualizovat, ji jak je znázorněno v následujícím příkladu:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Služba DNS je teď možné povolte v jednom z následujících způsobů:

   - Povolit službu DNS s výchozím nastavením, přidejte ji tak `addonFeatures` sekci `properties` části, jak je znázorněno v následujícím příkladu:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Chcete-li povolit službu pomocí jiného než výchozího nastavení, přidejte `DnsService` části `fabricSettings` sekci `properties` oddílu. V takovém případě nepotřebujete přidat služba DnsService k `addonFeatures`. Další informace o vlastnosti, které lze nastavit pro službu DNS, najdete v článku [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. Po aktualizaci clusteru šablony s provedenými změnami, je použít a umožní inovace dokončení. Po dokončení upgradu systému služba DNS se spustí ve vašem clusteru. Název služby je `fabric:/System/DnsService`, a najdete ho v části **systému** služby v Service Fabric explorer části. 


## <a name="setting-the-dns-name-for-your-service"></a>Nastavení názvu DNS pro vaši službu
Název DNS pro služby můžete nastavit pomocí deklarace pro výchozí služby v souboru ApplicationManifest.xml nebo prostřednictvím příkazů prostředí PowerShell.

Název DNS pro vaši službu je možné přeložit v rámci clusteru, takže je důležité, abychom zajistili jedinečnost názvu DNS napříč clusterem. 

Důrazně doporučujeme použít schéma pojmenování `<ServiceDnsName>.<AppInstanceName>`; například `service1.application1`. Pokud je aplikace nasazená pomocí Docker compose, služby se automaticky přiřazují pomocí toto schéma pojmenování názvy DNS.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Nastavení názvu DNS pro výchozí služby v souboru ApplicationManifest.xml
Otevřete projekt v sadě Visual Studio nebo svém oblíbeném editoru a otevřete soubor ApplicationManifest.xml. Přejděte do části výchozí služby a pro každou službu přidat `ServiceDnsName` atribut. Následující příklad ukazuje, jak nastavit název DNS služby `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Jakmile aplikace nasazena, instance služby v Service Fabric explorer zobrazí název DNS pro tuto instanci, jak je znázorněno na následujícím obrázku: 

![Koncové body služby](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Následující příklad nastaví název DNS pro stavové služby k `statefulsvc.app`. Služba používá pojmenované schéma dělení. Všimněte si, že názvy oddílů jsou malá. To je požadavek na oddíly, které budou cílem v dotazech DNS; Další informace najdete v tématu [oddílu služby DNS provádění dotazů na stavový](#making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Nastavení názvu DNS pro službu pomocí Powershellu
Název DNS pro služby můžete nastavit při vytváření pomocí `New-ServiceFabricService` příkaz prostředí Powershell. Následující příklad vytvoří nový bezstavovou službu s názvem DNS `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Preview] Vytváření dotazů DNS na oddíl se stavovou službou
Od verze Service Fabric verze 6.3, služba Service Fabric DNS podporuje dotazy pro oddíly služby.

Pro oddíly, které se použijí dotazy DNS platí následující omezení pojmenování:

   - Názvy oddílů musí být kompatibilní s DNS.
   - Názvy oddílů více popisek (obsahující tečku, ".", v názvu) by se neměly.
   - Názvy oddílů musí být malá.

Dotazy DNS, které se zaměřují oddíl se formátují následujícím způsobem:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Kde:

- *First-Label-Of-Partitioned-Service-DNSName* je první část názvu DNS služby.
- *PartitionPrefix* je hodnota, která je možné nastavit v části služba DnsService manifestu clusteru, nebo pomocí šablony Resource Manageru clusteru. Výchozí hodnota je "-". Další informace najdete v tématu [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Název cílového oddílu* je název oddílu. 
- *PartitionSuffix* je hodnota, která je možné nastavit v části služba DnsService manifestu clusteru, nebo pomocí šablony Resource Manageru clusteru. Výchozí hodnota je prázdný řetězec. Další informace najdete v tématu [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Zbývající-rozdělit na oddíly-Service-DNSName* je zbývající část názvu DNS služby.

Následující příklady ukazují, dotazy DNS pro dělené služby spuštěné v clusteru, který má výchozí nastavení pro `PartitionPrefix` a `PartitionSuffix`: 

- Chcete-li vyřešit oddíl "0" service s názvem DNS `backendrangedschemesvc.application` , která používá ranged schéma dělení, použijte `backendrangedschemesvc-0.application`.
- Chcete-li vyřešit oddílu služby s názvem DNS "first" `backendnamedschemesvc.application` , která používá pojmenované schéma dělení, použijte `backendnamedschemesvc-first.application`.

Služba DNS vrátí IP adresu primární repliky oddílu. Pokud není zadán žádný oddíl, vrátí služba IP adresu primární repliky namátkou vybraného oddílu.

## <a name="using-dns-in-your-services"></a>Pomocí DNS ve vašich službách
Pokud provádíte nasazení více služeb, můžete najít koncové body jiných služeb ke komunikaci s pomocí názvu DNS. Služba DNS funguje pro bezstavové služby a v Service Fabric verze 6.3 a novějších systémů pro stavové služby. Pro stavové služby spuštěné v Service Fabric verze před 6.3, můžete použít předdefinované [reverzní proxy service](./service-fabric-reverseproxy.md) pro volání http pro volání oddíl konkrétní službu. 

Dynamické porty nejsou podporovány službou DNS. Reverzní proxy server služby slouží k rozpoznávání služeb, které používají dynamické porty.

Následující kód ukazuje, jak volat bezstavovou službu prostřednictvím DNS. Je jednoduše regulární http volání ve kterém zadáte název DNS, port a žádné volitelné cestu jako část adresy URL.

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

Následující kód ukazuje volání na konkrétní oddíl stavové služby. V takovém případě obsahuje název DNS název oddílu (Oddíl1). Volání předpokládá clusteru s použitím výchozích hodnot pro `PartitionPrefix` a `PartitionSuffix`.

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>Známé problémy
* Pro Service Fabric verze 6.3 a vyšší dojde k problému s vyhledávání DNS pro názvy služeb, které obsahuje pomlčky v názvu DNS. Další informace o tomto problému, sledovat následující [problém Githubu](https://github.com/Azure/service-fabric-issues/issues/1197). V dalším 6.3 aktualizace Připravujeme opravu pro tento. 

## <a name="next-steps"></a>Další postup
Další informace o komunikaci služby v rámci clusteru pomocí [připojení a komunikace se službami](service-fabric-connect-and-communicate-with-services.md)

