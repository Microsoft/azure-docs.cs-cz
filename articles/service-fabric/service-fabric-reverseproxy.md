---
title: Azure Service Fabric reverse proxy | Microsoft Docs
description: Pro komunikaci mikroslužeb z uvnitř i vně clusteru pomocí Service Fabric reverzní proxy server.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 21e1e3041d7b1f4dc205355f6c0b8d4fd2e82775
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212271"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Reverzní proxy server v Azure Service Fabric
Reverzní proxy server, které jsou součástí Azure Service Fabric pomáhá mikroslužeb spuštění v clusteru Service Fabric zjistit a komunikovat s jinými službami, které mají koncových bodů protokolu http.

## <a name="microservices-communication-model"></a>Mikroslužeb komunikační model
Mikroslužeb v Service Fabric v podmnožině uzlů v clusteru spustit a provést migraci mezi uzly z různých důvodů. V důsledku toho jsou koncové body pro mikroslužeb můžete změnit dynamicky. Ke zjištění a komunikovat s jinými službami v clusteru, mikroslužbu musí projít následující kroky:

1. Určení umístění služby prostřednictvím pojmenování služby.
2. Připojte ke službě.
3. Zabalení předchozích kroků ve smyčce, který implementuje řešení služby a opakujte zásady pro použití na selhání připojení

Další informace najdete v tématu [připojit a komunikovat se službami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikaci pomocí reverzní proxy server
Reverzní proxy server je služba, která běží na všech uzlech a zpracovává koncový bod řešení, automatické opakování a dalších chyb připojení jménem klienta služby. Můžete přiřadit různé zásady jako zpracovává požadavky od klientů služby lze nakonfigurovat reverzní proxy server. Pomocí reverzní proxy server umožňuje klienta služby pro používání knihovny komunikace HTTP žádné straně klienta a není vyžadují speciální řešení a opakujte logiky ve službě. 

Reverzní proxy server vystavuje jeden nebo více koncových bodů na místní uzel pro klienta služby pro odesílání požadavků k jiným službám.

![Interní komunikaci][1]

> [!NOTE]
> **Podporované platformy**
>
> Reverzní proxy server v Service Fabric aktuálně podporuje tyto platformy
> * *Windows Cluster*: Windows 8 a novější nebo Windows Server 2012 a novější
> * *Cluster s Linuxem*: Reverse Proxy není aktuálně k dispozici u clusterů systému Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Dosažení mikroslužeb z mimo cluster
Výchozí model externí komunikace pro mikroslužeb je model opt-in, kde každé služby nelze přistupovat přímo z externích klientů. [Azure nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md), což je hranici sítě mezi mikroslužeb a externími klienty, provádí překlad síťových adres a předává externí požadavky do koncových bodů interní IP: port. Chcete-li koncový bod mikroslužbu přímo přístupné pro externími klienty, musíte nejdřív nakonfigurovat nástroj pro vyrovnávání zatížení pro přenos dat na každý port, který služba používá v clusteru. Kromě toho většina mikroslužeb, zejména stavová mikroslužeb, nemáte live na všech uzlech clusteru. Mikroslužeb můžete přesouvat mezi uzly na převzetí služeb při selhání. V takových případech nástroj pro vyrovnávání zatížení nemůže zjistit efektivně umístění cílový uzel replik, na které by měla předávat provoz.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Dosažení mikroslužeb přes reverzní proxy server z mimo cluster
Namísto konfigurace port jednotlivé služby nástroji pro vyrovnávání zatížení, můžete nakonfigurovat pouze port reverzní proxy server nástroji pro vyrovnávání zatížení. Tato konfigurace umožňuje klientům mimo cluster používat služby v clusteru pomocí reverzní proxy server bez další konfigurace.

![Externí komunikace][0]

> [!WARNING]
> Při konfiguraci portu reverzní proxy server v nástroj pro vyrovnávání zatížení, všechny mikroslužeb v clusteru, které zveřejňují koncový bod protokolu HTTP jsou adresovatelné z mimo cluster.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formát identifikátoru URI pro adresování služby pomocí reverzní proxy server
Reverzní proxy server používá formát identifikátor URI konkrétní URI k identifikaci oddílu služby, ke kterému má být příchozí žádost předána:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** reverzní proxy server lze nakonfigurovat, aby přijímal provoz protokolu HTTP nebo HTTPS. Předávání protokolu HTTPS, najdete v části [připojení k službě zabezpečené pomocí reverzní proxy server](service-fabric-reverseproxy-configure-secure-communication.md) až budete mít instalace reverzní proxy server tak, aby naslouchala na HTTPS.
* **Cluster plně kvalifikovaný název domény (FQDN) | interní IP:** pro externí klienty můžete nakonfigurovat reverzní proxy server tak, aby byla dostupná prostřednictvím doména clusteru, jako je například mycluster.eastus.cloudapp.azure.com. Ve výchozím nastavení spouští reverzní proxy server na každý uzel. Pro interní provoz reverzní proxy server dostupný na místního hostitele nebo na všechny IP adresy pro interní uzlu, například 10.0.0.1.
* **Port:** je to port, jako je například 19081, který byl zadaný pro reverzní proxy server.
* **ServiceInstanceName:** Toto je plně kvalifikovaný název instance nasazené služby, který se pokoušíte získat přístup bez "fabric: /" schéma. Například pro přístup *fabric: / myapp/Moje_služba/* využije služby, *myapp/Moje_služba*.

    Název instance služby je malá a velká písmena. Pomocí velká a malá písmena jinou pro název instance služby v adrese URL způsobí, že žádosti, které chcete selhat s 404 (není nalezena).
* **Přípona cesty:** jde skutečné cesty URL, například *myapi/hodnoty/přidat/3*, služby, který chcete připojit k.
* **Klíč oddílu:** oddílů služby, jedná se o počítaný oddíl klíč oddílu, který chcete připojit. Všimněte si, že toto je *není* identifikátoru GUID ID oddílu. Tento parametr není vyžadována pro služby, které používají schéma oddílu typu singleton.
* **PartitionKind:** Toto je schéma oddílu služby. To může být 'Int64Range' nebo "S názvem". Tento parametr není vyžadována pro služby, které používají schéma oddílu typu singleton.
* **ListenerName** koncových bodů ze služby jsou ve formátu {"Koncové body": {"Listener1": "Koncovém bodě 1", "Listener2": "Endpoint2"...}}. Pokud je služba poskytuje více koncových bodů, tato identifikuje koncového bodu, který požadavek klienta by měl být předán. To lze vynechat, pokud služba obsahuje pouze jeden naslouchací proces.
* **TargetReplicaSelector** určuje jak měla by být vybrána cíl replik nebo instancí.
  * Po stavová cílovou službu TargetReplicaSelector může být jedna z následujících: 'PrimaryReplica', 'RandomSecondaryReplica' nebo 'RandomReplica'. Pokud není tento parametr zadán, výchozí hodnota je 'PrimaryReplica'.
  * Po bezstavové Cílová služba reverzní proxy server vybere náhodných instance oddílu služby k předání požadavku.
* **Časový limit:** Určuje časový limit pro požadavek HTTP, které jsou vytvořené reverzní proxy server služby jménem žádost klienta. Výchozí hodnota je 60 sekund. Toto je volitelný parametr.

### <a name="example-usage"></a>Příklad použití
Jako příklad Podívejme *fabric: / MyApp/Moje_služba* služby, které se otevře naslouchací proces protokolu HTTP na následující adresu URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Prostředky pro službu jsou následující:

* `/index.html`
* `/api/users/<userId>`

Pokud služba používá schéma, vytváření oddílů singleton *PartitionKey* a *PartitionKind* parametrů řetězce dotazu nejsou vyžadovány, a službu lze získat přístup pomocí brány jako:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Interně: `http://localhost:19081/MyApp/MyService`

Pokud služba používá schéma rozdělení oddílů Uniform Int64 *PartitionKey* a *PartitionKind* parametrů řetězce dotazu, musí být použité k dosažení oddílu služby:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Interně: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

K dosažení prostředky, které poskytuje službu, jednoduše umístíte cesta prostředku po názvu služby v adrese URL:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Interně: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Brána pak předá tyto požadavky na adresu URL služby:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Zvláštní zpracování pro sdílení portů služby
Service Fabric reverzní proxy server se pokusí znovu vyřešte adresu služby a opakovat žádost, pokud služba není dostupný. Obecně platí, pokud služba není dostupný, instance služby ani repliky se přesunul na jiný uzel jako součást životního cyklu normální. V takovém případě reverzní proxy server může dojít k chybě připojení sítě označující, že koncový bod je již otevřen v původně převedenou adresu.

Ale replik nebo instancí služby můžete sdílet hostitelský proces a může také sdílet port při hostované na základě ovladače http.sys webového serveru, včetně:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

V takovém případě je pravděpodobné, že webový server je k dispozici v procesu hostitele a reagovat na požadavky, ale instance přeložit služby nebo replika již není dostupný na hostiteli. V takovém případě brány se zobrazí odpověď HTTP 404 z webového serveru. Odpověď HTTP 404 proto může mít dva odlišné významy:

- Případ #1: Adresa služby je správný, ale prostředek, který uživatel si vyžádal, neexistuje.
- Případ #2: Adresa služby jsou nesprávné a prostředek, který uživatel si vyžádal, může existovat na jiný uzel.

Prvním případě je normální HTTP 404, která je považována za k chybě uživatele. V druhém případě však uživatel požadoval na prostředek, který neexistuje. Reverzní proxy server se nepodařilo najít, protože samotnou službu přesunula. Reverzní proxy server je potřeba znovu překlad adresy a opakujte žádost.

Reverzní proxy server proto musí být k rozlišení mezi těmito dvěma případy. Chcete-li tento rozdíl, je vyžadován nápovědu ze serveru.

* Ve výchozím nastavení reverzní proxy server předpokládá – případ #2 a pokusí se vyřešit a vydejte žádost znovu.
* K označení velikosti písmen #1 reverzní proxy server, služba by měla vrátit následující hlavičku HTTP odpovědi:

  `X-ServiceFabric : ResourceNotFound`

Tuto hlavičku HTTP odpovědi označuje normální HTTP 404 situaci, ve kterém požadovaný prostředek neexistuje, a reverzní proxy server se nepokusí pro překlad adres služby znovu.

## <a name="setup-and-configuration"></a>Instalace a konfigurace

### <a name="enable-reverse-proxy-via-azure-portal"></a>Povolit reverzní proxy server prostřednictvím portálu Azure

Portál Azure poskytuje možnost povolit reverzní proxy server při vytváření nového clusteru Service Fabric.
V části **cluster Service Fabric vytvořit**, krok 2: Konfigurace clusteru, konfigurace typu uzlu, výběrem zaškrtávacího políčka "Povolit reverzní proxy".
Pro konfiguraci zabezpečené reverzní proxy server, můžete zadat certifikát SSL v kroku 3: zabezpečení, konfigurovat nastavení zabezpečení clusteru, zaškrtněte políčko "Zahrnout certifikát SSL pro reverzní proxy server" a zadejte podrobnosti o certifikátu.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Povolit reverzní proxy server pomocí šablon Azure Resource Manageru

Můžete použít [šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md) povolit reverzní proxy server v Service Fabric pro cluster.

Odkazovat na [konfigurace HTTPS reverzní proxy server v clusteru s podporou zabezpečení](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) pro Azure Resource Manager šablony ukázky konfigurace zabezpečeného reverse proxy s certifikáty vyměnit certifikát a zpracování.

Nejdřív získat šablonu pro cluster, který chcete nasadit. Můžete použít ukázkové šablony, nebo vytvořit vlastní šablony Resource Manageru. Potom můžete povolit reverzní proxy server pomocí následujících kroků:

1. Zadejte port pro reverzní proxy server v [oddílu parametry](../azure-resource-manager/resource-group-authoring-templates.md) šablony.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Zadejte port pro každý typ uzlu objektů **clusteru** [části Typ prostředku](../azure-resource-manager/resource-group-authoring-templates.md).

    Port je určený podle názvu parametru, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Chcete-li vyřešit reverzní proxy server z mimo Azure cluster, nastavte pravidla pro vyrovnávání zatížení Azure pro port, který jste zadali v kroku 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Pokud chcete nakonfigurovat certifikáty SSL na portu pro reverzní proxy server, přidejte certifikát, který chcete ***reverseProxyCertificate*** vlastnost **clusteru** [části Typ prostředku](../resource-group-authoring-templates.md) .

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Podpora certifikát reverzní proxy server, který se liší od certifikátu clusteru
 Pokud certifikát reverzní proxy server se liší od certifikátu, který zabezpečuje clusteru, pak dříve zadaný certifikát musí být nainstalovaný na virtuálním počítači a přidat do seznamu řízení přístupu (ACL), se kterým můžete Service Fabric. To lze provést **virtualMachineScaleSets** [části Typ prostředku](../resource-group-authoring-templates.md). Pro instalaci přidejte do osProfile certifikátu. Rozšíření část šablony, můžete aktualizovat certifikát v seznamu ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Pokud používáte certifikáty, které se liší od clusteru certifikát, který chcete povolit reverzní proxy server na existující cluster, nainstalujte certifikát reverzní proxy server a aktualizace seznamu řízení přístupu v clusteru, než povolíte reverzní proxy server. Dokončení [šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md) nasazení s použitím nastavení uvedeno dříve než zahájíte nasazení povolit reverzní proxy server v kroky 1 – 4.

## <a name="next-steps"></a>Další postup
* Zobrazit příklad komunikaci pomocí protokolu HTTP mezi službami v [ukázkového projektu na Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Předávání služby Zabezpečené HTTP s reverzní proxy server](service-fabric-reverseproxy-configure-secure-communication.md)
* [Volání vzdálených procedur s vzdálenou komunikaci spolehlivé služby](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, která používá OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md)
* [Komunikace WCF pomocí spolehlivé služby](service-fabric-reliable-services-communication-wcf.md)
* Možnosti konfigurace další reverzní proxy server, najdete v části ApplicationGateway/Http [nastavení clusteru Service Fabric přizpůsobit](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
