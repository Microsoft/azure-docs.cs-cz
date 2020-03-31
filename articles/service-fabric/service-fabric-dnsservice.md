---
title: Služba DNS azure service fabric
description: Službu DNS služby Service Fabric použijte ke zjišťování mikroslužeb zclusteru.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458026"
---
# <a name="dns-service-in-azure-service-fabric"></a>Služba DNS v Azure Service Fabric
Služba DNS je volitelná systémová služba, kterou můžete v clusteru povolit ke zjišťování dalších služeb pomocí protokolu DNS. 

Mnoho služeb, zejména kontejnerizované služby, jsou adresovatelné prostřednictvím již existující adresy URL. Možnost vyřešit tyto služby pomocí standardního protokolu DNS, nikoli protokolu service fabric naming service service service service, je žádoucí. Služba DNS umožňuje mapovat názvy DNS na název služby a tím předávat adresy IP koncového bodu. Tato funkce udržuje přenositelnost kontejnerizovaných služeb napříč různými platformami a může usnadnit "výtah a posun" scénáře tím, že umožňuje používat existující adresy URL služby, spíše než muset přepsat kód využít pojmenování služby. 

Služba DNS mapuje názvy DNS na názvy služeb, které jsou službou pojmenování vyřešeny tak, aby vrátily koncový bod služby. Název DNS pro službu je k dispozici v době vytvoření. Následující diagram znázorňuje, jak služba DNS funguje pro bezstavové služby.

![koncové body služby](./media/service-fabric-dnsservice/stateless-dns.png)

Počínaje Service Fabric verze 6.3, Service Fabric DNS protokol byl rozšířen o schéma pro adresování dělené stavové služby. Tato rozšíření umožňují vyřešit konkrétní adresy IP oddílů pomocí kombinace názvu DNS stavu stavu služby a názvu oddílu. Podporována jsou všechna tři schémata dělení:

- Pojmenované dělení
- Dělení v rozsahu
- Dělení singletonu

Následující diagram znázorňuje, jak služba DNS funguje pro služby se serverem stavu.

![koncové body stavové služby](./media/service-fabric-dnsservice/stateful-dns.png)

Služba DNS nepodporuje dynamické porty. Chcete-li vyřešit služby vystavené na dynamických portech, použijte [službu reverzní proxy .](./service-fabric-reverseproxy.md)

## <a name="enabling-the-dns-service"></a>Povolení služby DNS
> [!NOTE]
> Služba DNS pro služby Service Fabric ještě není v Linuxu podporována.

Při vytváření clusteru pomocí portálu je služba DNS ve výchozím nastavení povolena v položce **Zahrnout službu DNS** v **nabídce konfigurace clusteru:**

![Povolení služby DNS prostřednictvím portálu](./media/service-fabric-dnsservice/enable-dns-service.png)

Pokud nepoužíváte portál k vytvoření clusteru nebo aktualizujete existující cluster, budete muset povolit službu DNS v šabloně:

- Chcete-li nasadit nový cluster, můžete použít [ukázkové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořit vlastní šablonu Správce prostředků. 
- Chcete-li aktualizovat existující cluster, můžete přejít do skupiny prostředků clusteru na portálu a klepnutím na tlačítko **Automatizační skript** pracovat se šablonou, která odráží aktuální stav clusteru a dalších prostředků ve skupině. Další informace naleznete [v tématu Export šablony ze skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Po vytvoření šablony můžete službu DNS povolit pomocí následujících kroků:

1. Zkontrolujte, `apiversion` zda `2017-07-01-preview` je prostředek `Microsoft.ServiceFabric/clusters` nastaven na prostředek nebo novější, a pokud ne, aktualizujte jej, jak je znázorněno v následujícím příkladu:

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

   - Chcete-li povolit službu DNS s `addonFeatures` výchozím `properties` nastavením, přidejte ji do oddílu uvnitř oddílu, jak je znázorněno v následujícím příkladu:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Chcete-li povolit službu s `DnsService` jiným i `fabricSettings` výchozím `properties` nastavením, přidejte oddíl do oddílu uvnitř oddílu. V takovém případě není nutné přidat službu `addonFeatures`DnsService do služby . Další informace o vlastnostech, které lze nastavit pro službu DNS, naleznete v [tématu Nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Po aktualizaci šablony clusteru se změnami je použijte a nechte upgrade dokončit. Po dokončení upgradu se v clusteru spustí systémová služba DNS. Název služby `fabric:/System/DnsService`je a najdete jej v části **Systémové** služby v průzkumníku Service Fabric. 

> [!NOTE]
> Při upgradu služby DNS ze zakázané na povolenou službu Aplikace Service Fabric Explorer nemusí odrážet nový stav. Chcete-li vyřešit, restartujte uzly úpravou upgradepolicy v šabloně Azure Resource Manager. Další informace naleznete v [odkazu na šablonu service fabric.](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications)

> [!NOTE]
> Povolení služby DNS při vývoji v místním počítači přepíše některá nastavení DNS. Pokud máte problémy s připojením k internetu, zkontrolujte nastavení DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Nastavení názvu DNS pro vaši službu
Název DNS pro své služby můžete nastavit deklarativně pro výchozí služby v souboru ApplicationManifest.xml nebo pomocí příkazů prostředí PowerShell.

Název DNS pro vaši službu je řešitelný v celém clusteru, takže je důležité zajistit jedinečnost názvu DNS v celém clusteru. 

Důrazně doporučujeme použít schéma pojmenování `<ServiceDnsName>.<AppInstanceName>`; například `service1.application1`. Pokud je aplikace nasazena pomocí vytváření Dockeru, služby jsou automaticky přiřazeny názvy DNS pomocí tohoto schématu pojmenování.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Nastavení názvu DNS pro výchozí službu v souboru ApplicationManifest.xml
Otevřete projekt v sadě Visual Studio nebo oblíbeném editoru a otevřete soubor ApplicationManifest.xml. Přejděte do výchozí části služby a `ServiceDnsName` pro každou službu přidejte atribut. Následující příklad ukazuje, jak nastavit název DNS služby tak, aby`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Po nasazení aplikace instance služby v průzkumníku Service Fabric zobrazí název DNS pro tuto instanci, jak je znázorněno na následujícím obrázku: 

![koncové body služby](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

Následující příklad nastaví název DNS pro `statefulsvc.app`stavovou službu na . Služba používá pojmenované schéma dělení. Všimněte si, že názvy oddílů jsou malá písmena. Jedná se o požadavek na oddíly, které budou v dotazech DNS zacíleny. Další informace naleznete v [tématu Vytváření dotazů DNS na oddíl stavové služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Nastavení názvu DNS pro službu pomocí prostředí Powershell
Název DNS pro službu můžete nastavit při `New-ServiceFabricService` jejím vytváření pomocí příkazu Powershell. Následující příklad vytvoří novou bezstavovou službu s názvem DNS`service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Náhled] Vytváření dotazů DNS na oddíl stavové služby
Počínaje service fabric verze 6.3, service fabric DNS služba podporuje dotazy na oddíly služby.

Pro oddíly, které budou použity v dotazech DNS, platí následující omezení pojmenování:

   - Názvy oddílů by měly být kompatibilní se službou DNS.
   - Vícepopisné názvy oddílů (které obsahují tečku, '., v názvu) by neměly být používány.
   - Názvy oddílů by měly být malá písmena.

Dotazy DNS, které cílí na oddíl, jsou formátovány takto:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Kde:

- *First-Label-Of-Partitioned-Service-DNSName* je první část názvu DNS služby.
- *PartitionPrefix* je hodnota, kterou lze nastavit v části DnsService manifestu clusteru nebo prostřednictvím šablony Správce prostředků clusteru. Výchozí hodnota je "--". Další informace naleznete v tématu [Nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Název cílového oddílu* je název oddílu. 
- *PartitionSuffix* je hodnota, kterou lze nastavit v části DnsService manifestu clusteru nebo prostřednictvím šablony Správce prostředků clusteru. Výchozí hodnota je prázdný řetězec. Další informace naleznete v tématu [Nastavení služby DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Remaining-Partitioned-Service-DNSName* je zbývající část názvu DNS služby.

Následující příklady ukazují dotazy DNS pro dělené služby spuštěné v clusteru, který má výchozí nastavení pro `PartitionPrefix` a `PartitionSuffix`: 

- Chcete-li vyřešit oddíl "0" `backendrangedschemesvc.application` služby s názvem DNS, `backendrangedschemesvc-0.application`který používá schéma dělení v rozsahu, použijte .
- Chcete-li vyřešit oddíl "první" `backendnamedschemesvc.application` služby s názvem DNS, který používá pojmenované schéma dělení, použijte `backendnamedschemesvc-first.application`.

Služba DNS vrátí adresu IP primární repliky oddílu. Pokud není zadán žádný oddíl, vrátí služba adresu IP primární repliky náhodně vybraného oddílu.

## <a name="using-dns-in-your-services"></a>Používání služby DNS ve vašich službách
Pokud nasadíte více než jednu službu, můžete najít koncové body jiných služeb pro komunikaci s pomocí názvu DNS. Služba DNS funguje pro bezstavové služby a ve službě Service Fabric verze 6.3 a novější pro stavové služby. Pro stavové služby spuštěné ve verzích Service Fabric před verzí 6.3 můžete použít integrovanou službu [reverzní proxy](./service-fabric-reverseproxy.md) pro volání http k volání určitého oddílu služby. 

Služba DNS nepodporuje dynamické porty. Pomocí služby reverzní proxy můžete vyřešit služby, které používají dynamické porty.

Následující kód ukazuje, jak volat bezstavové služby prostřednictvím DNS. Je to prostě pravidelné http volání, kde zadáte název DNS, port a všechny volitelné cesty jako součást URL.

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

Následující kód ukazuje volání na konkrétní oddíl stavové služby. V tomto případě název DNS obsahuje název oddílu (partition1). Volání předpokládá cluster s výchozími `PartitionPrefix` `PartitionSuffix`hodnotami pro a .

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
* U service fabric verze 6.3 a vyšší, došlo k potížím s DNS vyhledávání pro názvy služeb obsahující pomlčku v názvu DNS. Další informace o tomto problému naleznete v následujícím [problému GitHub .](https://github.com/Azure/service-fabric-issues/issues/1197) Oprava tohoto jevu v příští aktualizaci 6.3. 

* Služba DNS pro služby Service Fabric ještě není v Linuxu podporována. Služba DNS je podporována pro kontejnery v Linuxu. Ruční překlad pomocí prostředků Fabric Client/ServicePartitionResolver je dostupná alternativa.

## <a name="next-steps"></a>Další kroky
Další informace o komunikaci služeb v rámci clusteru pomocí [připojení a komunikace se službami](service-fabric-connect-and-communicate-with-services.md)

