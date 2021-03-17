---
title: Zabezpečená komunikace služby Azure Service Fabric reverzní proxy
description: Nakonfigurujte reverzní proxy tak, aby umožňoval zabezpečenou koncovou komunikaci v aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256131"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Připojení k zabezpečené službě pomocí reverzního proxy serveru

V tomto článku se dozvíte, jak vytvořit zabezpečené připojení mezi reverzním proxy serverem a službami a tím i koncovým zabezpečeným kanálem. Další informace o reverzním proxy serveru najdete [v tématu reverzní proxy v Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> Připojení k zabezpečeným službám se podporuje jenom v případě, že je reverzní proxy nakonfigurované pro naslouchání na HTTPS. V tomto článku se předpokládá, že se jedná o tento případ. Postup konfigurace reverzního proxy serveru v Service Fabric najdete v tématu [Nastavení reverzního proxy serveru v Azure Service Fabric](service-fabric-reverseproxy-setup.md) .

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Zabezpečené vytváření připojení mezi reverzním proxy serverem a službami 

### <a name="reverse-proxy-authenticating-to-services"></a>Reverzní proxy ověřování pro služby:
Reverzní proxy server identifikuje sám sebe se službami pomocí jejího certifikátu. V případě clusterů Azure je certifikát zadaný pomocí vlastnosti ***reverseProxyCertificate*** v [části typ prostředku](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. ServiceFabric/clustery**](/azure/templates/microsoft.servicefabric/clusters) v šabloně správce prostředků. V případě samostatných clusterů je certifikát určen buď pomocí ***ReverseProxyCertificate*** , nebo pomocí vlastnosti ***ReverseProxyCertificateCommonNames*** v části **zabezpečení** ClusterConfig.jszapnuté. Další informace najdete v tématu [Povolení reverzního proxy na samostatných clusterech](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Služby můžou implementovat logiku, která ověří certifikát prezentovaný reverzním proxy serverem. Služby můžou určit podrobnosti přijímaného klientského certifikátu jako konfigurační nastavení v konfiguračním balíčku. To je možné číst za běhu a použít k ověření certifikátu prezentovaného reverzním proxy serverem. Chcete-li přidat nastavení konfigurace, přečtěte si téma [Správa parametrů aplikace](service-fabric-manage-multiple-environment-app-configuration.md) . 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Reverzní proxy server ověřuje identitu služby prostřednictvím certifikátu, který prezentuje služba:
Reverzní proxy server podporuje následující zásady, které provádějí ověřování certifikátů serveru pro certifikáty prezentované službami: None, ServiceCommonNameAndIssuer a ServiceCertificateThumbprints.
Chcete-li vybrat zásadu pro použití reverzního proxy serveru, zadejte **ApplicationCertificateValidationPolicy** do části **ApplicationGateway/http** v části [fabricSettings](service-fabric-cluster-fabric-settings.md).

V další části jsou uvedeny podrobnosti o konfiguraci pro každou z těchto možností.

### <a name="service-certificate-validation-options"></a>Možnosti ověřování certifikátu služby 

- **Žádné**: reverzní proxy přeskočí ověření proxy serveru s proxy serverem a vytvoří zabezpečené připojení. Toto je výchozí chování.
V části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) zadejte **ApplicationCertificateValidationPolicy** s hodnotou **none** .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: reverzní proxy ověří certifikát prezentovaný službou na základě běžného názvu certifikátu a kryptografického otisku bezprostředního vystavitele: zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCommonNameAndIssuer** v části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Pokud chcete zadat seznam běžných názvů služeb a kryptografických otisků vystavitelů, přidejte v oblasti **fabricSettings**oddíl [**ApplicationGateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) , jak je znázorněno níže. Do pole **Parameters** lze přidat více než jednou dvojici společného názvu certifikátu a otisků kryptografických otisků vystavitele. 

   Pokud se připojení ke koncovému bodu proxy serveru zaznamená, vytvoří se kanál protokolu TLS s certifikátem, který obsahuje běžný název a kryptografický otisk vystavitele.
   Po neúspěšném přiřazení podrobností certifikátu selže reverzní proxy požadavek klienta se stavovým kódem 502 (špatným bránou). Stavový řádek protokolu HTTP bude obsahovat také frázi "Neplatný certifikát SSL". 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: reverzní proxy ověří certifikát proxy služby na základě jeho kryptografického otisku. Tuto trasu můžete zvolit, pokud jsou služby nakonfigurované s certifikáty podepsanými svým vlastníkem: v části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCertificateThumbprints** .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Zadejte také kryptografické otisky pomocí položky **ServiceCertificateThumbprints** v části **ApplicationGateway/http** . V poli hodnota lze zadat více kryptografických otisků jako čárkami oddělený seznam, jak je vidět níže:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Pokud je kryptografický otisk certifikátu serveru uvedený v této položce konfigurace, reverzní proxy připojení TLS úspěšně. V opačném případě se připojení ukončí a požadavek klienta selže s 502 (chybnou bránou). Stavový řádek protokolu HTTP bude obsahovat také frázi "Neplatný certifikát SSL".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logika výběru koncového bodu, když služby zveřejňují zabezpečení a také nezabezpečené koncové body
Service Fabric podporuje konfiguraci více koncových bodů pro službu. Další informace najdete v tématu [určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md).

Reverzní proxy vybere jeden z koncových bodů, aby předal požadavek na základě parametru dotazu **naslouchacího procesu** v [identifikátoru URI služby](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Pokud není zadán parametr **naslouchacího** prvku, může reverzní proxy vybrat libovolný koncový bod ze seznamu koncových bodů. V závislosti na koncových bodech nakonfigurovaných pro službu může být vybraný koncový bod HTTP nebo HTTPS. Mohou nastat scénáře nebo požadavky, kde chcete, aby reverzní proxy pracoval v režimu pouze pro zabezpečení. To znamená, že nechcete, aby zabezpečený reverzní proxy přesměroval požadavky na nezabezpečené koncové body. Chcete-li nastavit reverzní proxy do režimu pouze pro zabezpečení, zadejte položku konfigurace **SecureOnlyMode** s hodnotou **true** v části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Při provozu v **SecureOnlyMode**, pokud klient zadal parametr **naslouchacího procesu** odpovídající koncovému bodu http (nezabezpečeného), reverzní proxy server nevrátí požadavek se stavovým kódem HTTP 404 (Nenalezeno).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Nastavení ověřování klientského certifikátu pomocí reverzního proxy serveru
Ukončení protokolu TLS proběhne na reverzním proxy serveru a ztratí se všechna data certifikátu klienta. Aby služby prováděly ověřování klientským certifikátem, zadejte nastavení **ForwardClientCertificate** v části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

1. Pokud je **ForwardClientCertificate** nastavené na **hodnotu false**, reverzní proxy nepožaduje klientský certifikát během své metody handshake TLS s klientem.
Toto je výchozí chování.

2. Pokud je **ForwardClientCertificate** nastaveno na **hodnotu true**, reverzní proxy požádá o certifikát klienta během své metody handshake TLS s klientem.
Pak přepošle data certifikátu klienta ve vlastní hlavičce HTTP s názvem **X-Client-Certificate**. Hodnota hlavičky je formátovací řetězec PEM kódovaný v kódování Base64 certifikátu klienta. Služba může po kontrole dat certifikátu úspěšně nebo neúspěšně požádat o příslušný stavový kód.
Pokud klient neprezentuje certifikát, reverzní proxy předává prázdnou hlavičku a umožňuje, aby služba zavedla případ.

> [!NOTE]
> Reverzní proxy funguje jenom jako předávací služba. Neprovede žádné ověření certifikátu klienta.


## <a name="next-steps"></a>Další kroky
* [Nastavení a konfigurace reverzního proxy serveru v clusteru](service-fabric-reverseproxy-setup.md).
* Další informace o [konfiguraci reverzního proxy serveru pro připojení k zabezpečeným službám](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Podívejte se na příklad komunikace HTTP mezi službami ve [vzorovém projektu na GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Vzdálená volání procedur s Reliable Services Vzdálená komunikace](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, které používá OWIN v Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
