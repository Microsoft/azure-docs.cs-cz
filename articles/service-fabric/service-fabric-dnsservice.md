---
title: Služba DNS pro Azure Service Fabric
description: Použijte službu DNS Service Fabric pro zjišťování mikroslužeb v rámci clusteru.
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91268039"
---
# <a name="dns-service-in-azure-service-fabric"></a>Služba DNS v Azure Service Fabric
Služba DNS je volitelná systémová služba, kterou můžete v clusteru povolit a zjišťovat tak další služby pomocí protokolu DNS. 

Mnohé služby, zejména služby s využitím kontejnerů, jsou adresovatelné prostřednictvím již existující adresy URL. Je možné tyto služby vyřešit pomocí standardního protokolu DNS, ale nemusíte používat protokol Service Fabric Naming Service. Služba DNS umožňuje mapovat názvy DNS na název služby, a to proto, aby vyřešila IP adresy koncového bodu. Tyto funkce zachovává přenositelnost kontejnerových služeb napříč různými platformami a můžou zjednodušit scénáře "navýšení a posun" tím, že vám umožní použít stávající adresy URL služby a nemusíte přepisovat kód, abyste mohli využít Naming Service. 

Služba DNS mapuje názvy DNS na názvy služeb, které jsou zase vyřešeny Naming Service pro vrácení koncového bodu služby. Název DNS pro službu je k dispozici v době vytváření. Následující diagram znázorňuje, jak služba DNS funguje pro bezstavové služby.

![Diagram znázorňující, jak jsou názvy DNS mapovány na názvy služeb služby DNS pro bezstavové služby.](./media/service-fabric-dnsservice/stateless-dns.png)

Od verze Service Fabric 6,3 byl rozšířen protokol DNS Service Fabric, aby obsahoval schéma pro adresování přístavových služeb s oddíly. Tato rozšíření umožňují vyřešit konkrétní IP adresy oddílu pomocí kombinace názvu DNS stavové služby a názvu oddílu. Všechna tři schémata dělení na oddíly jsou podporovaná:

- Pojmenované dělení
- Dělení v rozsahu
- Dělení singleton

Následující diagram znázorňuje, jak služba DNS funguje pro rozdělené stavové služby.

![Diagram znázorňující, jak jsou názvy DNS mapovány na názvy služeb služby DNS pro bezstavové služby bez oddílů.](./media/service-fabric-dnsservice/stateful-dns.png)

Služba DNS nepodporuje dynamické porty. Chcete-li vyřešit služby vystavené dynamickými porty, použijte [službu reverzního proxy serveru](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Povolení služby DNS
> [!NOTE]
> Služba DNS pro službu Service Fabric Services ještě není v systému Linux podporována.

Když vytvoříte cluster pomocí portálu, služba DNS je ve výchozím nastavení povolená v poli **zahrnout službu DNS** v nabídce **Konfigurace clusteru** :

![Povolení služby DNS prostřednictvím portálu](./media/service-fabric-dnsservice/enable-dns-service.png)

Pokud nepoužíváte portál k vytvoření clusteru nebo pokud aktualizujete existující cluster, budete muset povolit službu DNS v šabloně:

- K nasazení nového clusteru můžete použít [ukázkové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořit vlastní šablonu správce prostředků. 
- Pokud chcete aktualizovat existující cluster, můžete přejít na skupinu prostředků clusteru na portálu a kliknout na **skript Automation** pro práci se šablonou, která odráží aktuální stav clusteru a dalších prostředků ve skupině. Další informace najdete v tématu [Export šablony ze skupiny prostředků](../azure-resource-manager/templates/export-template-portal.md).

Po vytvoření šablony můžete službu DNS povolit pomocí následujících kroků:

1. Ověřte, že `apiversion` je u prostředku nastavené na `2017-07-01-preview` nebo novější `Microsoft.ServiceFabric/clusters` , a pokud ne, aktualizujte ho tak, jak je znázorněno v následujícím příkladu:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nyní povolte službu DNS jedním z následujících způsobů:

   - Pokud chcete službu DNS povolit s výchozím nastavením, přidejte ji do `addonFeatures` oddílu v části, `properties` jak je znázorněno v následujícím příkladu:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Chcete-li povolit službu s jiným než výchozím nastavením, přidejte `DnsService` do části oddíl `fabricSettings` dovnitř oddílu `properties` . V takovém případě nemusíte přidávat služba DNSservice do `addonFeatures` . Další informace o vlastnostech, které lze nastavit pro službu DNS, najdete v tématu [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Jakmile aktualizujete šablonu clusteru o provedené změny, použijte ji a nechte upgrade dokončeno. Po dokončení upgradu začne služba systému DNS běžet ve vašem clusteru. Název služby je `fabric:/System/DnsService` a můžete ho najít v části **Systémová** služba v Service Fabric Exploreru. 

> [!NOTE]
> Při upgradu serveru DNS z zakázaného na povoleno se Service Fabric Explorer nemusí odrážet nový stav. Chcete-li řešení vyřešit, restartujte uzly úpravou UpgradePolicy v šabloně Azure Resource Manager. Další informace najdete v [referenčních informacích k šabloně Service Fabric](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) .

> [!NOTE]
> Povolení služby DNS při vývoji na místním počítači přepíše některá nastavení DNS. Pokud se setkáte s připojením k Internetu, ověřte nastavení DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Nastavení názvu DNS pro vaši službu
Můžete nastavit název DNS pro vaše služby, a to buď deklarativně pro výchozí služby, v souboru ApplicationManifest.xml nebo pomocí příkazů PowerShellu.

Název DNS pro vaši službu je v rámci clusteru přeložitelný, takže je důležité zajistit jedinečnost názvu DNS v rámci clusteru. 

Důrazně doporučujeme použít schéma pojmenování `<ServiceDnsName>.<AppInstanceName>` , například `service1.application1` . Pokud je aplikace nasazená pomocí nástroje Docker, služba automaticky přiřazuje názvy DNS pomocí tohoto schématu pojmenovávání.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Nastavení názvu DNS pro výchozí službu v ApplicationManifest.xml
Otevřete projekt v aplikaci Visual Studio nebo v oblíbeném editoru a otevřete soubor ApplicationManifest.xml. Přejít na oddíl výchozí služby a pro každou službu přidejte `ServiceDnsName` atribut. Následující příklad ukazuje, jak nastavit název DNS služby na `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Po nasazení aplikace instance služby v Průzkumníkovi Service Fabric zobrazuje název DNS pro tuto instanci, jak je znázorněno na následujícím obrázku: 

![koncové body služby](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Následující příklad nastaví název DNS stavové služby na `statefulsvc.app` . Služba používá schéma pojmenovaného dělení. Všimněte si, že názvy oddílů jsou malými písmeny. Toto je požadavek na oddíly, které budou cílené na dotazy DNS. Další informace najdete v tématu [vytváření dotazů DNS na oddílu stavové služby](#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Nastavení názvu DNS pro službu pomocí prostředí PowerShell
Název DNS pro službu můžete nastavit při jejím vytváření pomocí `New-ServiceFabricService` příkazu PowerShellu. Následující příklad vytvoří novou bezstavovou službu s názvem DNS. `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Tisk Vytváření dotazů DNS na oddílu stavové služby
Od verze Service Fabric 6,3 služba DNS Service Fabric podporuje dotazy na oddíly služeb.

Pro oddíly, které se budou používat v dotazech DNS, platí následující omezení pojmenování:

   - Názvy oddílů by měly odpovídat rozhraní DNS.
   - Názvy oddílů s více štítky (včetně teček, "." v názvu) by neměly být použity.
   - Názvy oddílů by měly být malými písmeny.

Dotazy DNS, které cílí na oddíl, jsou formátovány takto:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Kde:

- *First-Label-* --partition-------------------------
- *PartitionPrefix* je hodnota, kterou lze nastavit v oddílu služba DNSservice manifestu clusteru nebo v šabloně správce prostředků clusteru. Výchozí hodnota je "--". Další informace najdete v tématu věnovaném  [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-partition-Name* je název oddílu. 
- *PartitionSuffix* je hodnota, kterou lze nastavit v oddílu služba DNSservice manifestu clusteru nebo v šabloně správce prostředků clusteru. Výchozí hodnota je prázdný řetězec. Další informace najdete v tématu věnovaném  [nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Zbytek-partition-Service-DnsName* je zbývající část názvu DNS služby.

Následující příklady ukazují dotazy DNS na dělené služby spuštěné v clusteru, který má výchozí nastavení pro `PartitionPrefix` a `PartitionSuffix` : 

- Chcete-li vyřešit oddíl "0" služby s názvem DNS `backendrangedschemesvc.application` , který používá schéma dělení v rozsahu, použijte `backendrangedschemesvc-0.application` .
- Chcete-li vyřešit oddíl "First" služby s názvem DNS `backendnamedschemesvc.application` , který používá schéma pojmenovaného oddílu, použijte `backendnamedschemesvc-first.application` .

Služba DNS vrátí IP adresu primární repliky oddílu. Pokud není zadaný žádný oddíl, služba vrátí IP adresu primární repliky náhodně vybraného oddílu.

## <a name="using-dns-in-your-services"></a>Použití DNS ve vašich službách
Pokud nasazujete více než jednu službu, můžete najít koncové body dalších služeb, se kterými budete komunikovat pomocí názvu DNS. Služba DNS funguje u bezstavových služeb a v Service Fabric verze 6,3 a novější pro stavové služby. U stavových služeb běžících ve verzích Service Fabric starších než 6,3 můžete použít integrovanou [službu reverzního proxy serveru](./service-fabric-reverseproxy.md) pro volání http pro volání konkrétního oddílu služby. 

Služba DNS nepodporuje dynamické porty. Službu reverzního proxy serveru můžete použít k překladu služeb, které používají dynamické porty.

Následující kód ukazuje, jak volat bezstavovou službu prostřednictvím DNS. Jedná se o běžné volání http, kde jako součást adresy URL zadáte název DNS, port a libovolnou volitelnou cestu.

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

Následující kód ukazuje volání na konkrétní oddíl stavové služby. V tomto případě název DNS obsahuje název oddílu (partition1). Volání předpokládá cluster s výchozími hodnotami pro `PartitionPrefix` a `PartitionSuffix` .

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
* Pro Service Fabric verze 6,3 a vyšší existuje problém s vyhledáváním DNS pro názvy služeb obsahující spojovník v názvu DNS. Další informace o tomto problému najdete v následujícím [problému GitHubu](https://github.com/Azure/service-fabric-issues/issues/1197). Oprava pro tuto chybu přichází v další aktualizaci 6,3. 

* Služba DNS pro službu Service Fabric Services ještě není v systému Linux podporována. Služba DNS je podporovaná pro kontejnery v systému Linux. K dispozici je ruční řešení pomocí nástroje Fabric Client/ServicePartitionResolver.

## <a name="next-steps"></a>Další kroky
Další informace o komunikaci služby v rámci clusteru s  [připojením a komunikace se službami](service-fabric-connect-and-communicate-with-services.md)
