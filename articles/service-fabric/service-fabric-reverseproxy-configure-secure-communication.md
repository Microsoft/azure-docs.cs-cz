---
title: Azure Service Fabric reverzní proxy zabezpečená komunikace
description: Nakonfigurujte reverzní proxy server tak, aby umožňoval zabezpečenou komunikaci mezi koncovými místy v aplikaci Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282247"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Připojení k zabezpečené službě pomocí reverzního proxy serveru

Tento článek vysvětluje, jak vytvořit zabezpečené připojení mezi reverzní proxy server a služby, což umožňuje ukončení zabezpečeného kanálu. Další informace o reverzním proxy serveru najdete [v tématu Reverzní proxy server ve službě Azure Service Fabric](service-fabric-reverseproxy.md)

Připojení k zabezpečeným službám je podporováno pouze v případě, že je reverzní proxy server nakonfigurován tak, aby naslouchal protokolu HTTPS. Tento článek předpokládá, že se jedná o tento případ.
Chcete-li nakonfigurovat reverzní proxy server ve službě Service Fabric služby [Azure Fabric,](service-fabric-reverseproxy-setup.md) nawebute reverzní proxy server ve službě Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Bezpečné navázání spojení mezi reverzním proxy serverem a službami 

### <a name="reverse-proxy-authenticating-to-services"></a>Ověření reverzního proxy serveru pro služby:
Reverzní proxy server identifikuje sám služby pomocí svého certifikátu. Pro clustery Azure je certifikát určen vlastností ***reverseProxyCertificate*** v části [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Typ prostředku](../azure-resource-manager/templates/template-syntax.md) šablony Správce prostředků. U samostatných clusterů je certifikát určen buď pomocí ***reverseproxycertificatecertificate,*** nebo vlastností ***ReverseProxyCertificateCommonNames*** v části **Zabezpečení** clusteru ClusterConfig.json. Další informace naleznete v [tématu Povolení reverzního proxy serveru v samostatných clusterech](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Služby mohou implementovat logiku k ověření certifikátu předloženého reverzní proxy server. Služby mohou určit podrobnosti o přijatém klientském certifikátu jako nastavení konfigurace v konfiguračním balíčku. To lze číst za běhu a slouží k ověření certifikátu předloženého reverzní proxy server. Chcete-li přidat nastavení konfigurace, přečtěte si odkaz [na spravovat parametry aplikace.](service-fabric-manage-multiple-environment-app-configuration.md) 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Reverzní proxy server ověřující identitu služby prostřednictvím certifikátu předloženého službou:
Reverzní proxy server podporuje následující zásady pro ověření certifikátů serveru certifikátů prezentovaných službami: None, ServiceCommonNameAndIssuer a ServiceCertificateThumbprints.
Chcete-li vybrat zásadu pro reverzní proxy server, který chcete použít, zadejte **zásadu ApplicationCertificateValidationPolicy** v části **ApplicationGateway/Http** v části [fabricSettings](service-fabric-cluster-fabric-settings.md).

V další části jsou uvedeny podrobnosti konfigurace pro každou z těchto možností.

### <a name="service-certificate-validation-options"></a>Možnosti ověření certifikátu služby 

- **Žádné**: Reverzní proxy přeskočí ověření certifikátu proxied služby a naváže zabezpečené připojení. Toto je výchozí chování.
V části [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) zadejte **zásadu ApplicationCertificateValidationPolicy** s hodnotou **None.**

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

- **ServiceCommonNameAndIssuer**: Reverzní proxy ověří certifikát předložený službou na základě běžného názvu certifikátu a kryptografického náhledu okamžitého vystavitnamu: Zadejte **zásadu ApplicationCertificateValidationPolicy** s hodnotou **ServiceCommonNameAndIssuer** v části [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Chcete-li zadat seznam běžnýnázev služby a vystavitel thumbprints, přidejte [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) části pod **fabricSettings**, jak je znázorněno níže. Do pole **parametrů** lze přidat více společných názvů certifikátů a dvojic kryptografickýotisk vystavithonu. 

   Pokud koncový bod reverzní proxy se připojuje k představuje certifikát, jehož běžný název a vystavitel kryptografický otisk odpovídá některé z hodnot uvedených zde, ssl kanál je vytvořen. 
   Po selhání odpovídající podrobnosti certifikátu reverzní proxy nezdaří požadavek klienta s 502 (Bad Gateway) stavový kód. Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL". 

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

- **ServiceCertificateThumbprints**: Reverzní proxy server ověří certifikát služby proxied na základě jeho kryptografického otisku. Tuto trasu můžete zvolit, pokud jsou služby konfigurovány s certifikáty podepsanými svým držitelem: Zadejte **zásadu ApplicationCertificateValidationPolicy** s hodnotou **ServiceCertificateThumbprints** v části [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Zadejte také kryptografické otisky s položkou **ServiceCertificateThumbprints** v části **ApplicationGateway/Http.** Více kryptografických otisků lze zadat jako seznam oddělený čárkami v poli hodnoty, jak je znázorněno níže:

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

   Pokud je kryptografický otisk certifikátu serveru uveden v této položce konfigurace, reverzní proxy server uspěje s připojením SSL. V opačném případě ukončí připojení a nezdaří požadavek klienta s 502 (Bad Gateway). Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logika výběru koncového bodu, když služby zveřejňují zabezpečené i nezabezpečené koncové body
Infrastruktura služeb podporuje konfiguraci více koncových bodů pro službu. Další informace naleznete [v tématu Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md).

Reverzní proxy server vybere jeden z koncových bodů pro předání požadavku na základě parametru dotazu **ListenerName** v [identifikátoru URI služby](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Pokud není zadán parametr **ListenerName,** reverzní proxy server může vybrat libovolný koncový bod ze seznamu koncových bodů. V závislosti na koncových bodech nakonfigurovaných pro službu může být vybraným koncovým bodem koncový bod HTTP nebo HTTPS. Mohou existovat scénáře nebo požadavky, kde chcete, aby reverzní proxy pracovat v režimu pouze pro bezpečné; to znamená, že nechcete, aby zabezpečený reverzní proxy server přesměrovávat požadavky na nezabezpečené koncové body. Chcete-li nastavit reverzní proxy server do zabezpečeného režimu pouze pro zabezpečení, zadejte položku konfigurace **SecureOnlyMode** s hodnotou **true** v části [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)   

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
> Pokud klient při práci v **režimu SecureOnlyMode**zadal **název listenername** odpovídající koncovému bodu HTTP(nezabezpečené), reverzní proxy server neprojde požadavkem se stavovým kódem HTTP 404 (Nebyl nalezen).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Nastavení ověřování klientského certifikátu prostřednictvím reverzního proxy serveru
Ukončení SSL probíhá na reverzním proxy serveru a dojde ke ztrátě všech dat klientského certifikátu. Chcete-li, aby služby prováděly ověřování klientského **certifikátu,** zadejte nastavení ForwardClientCertificate v části [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

1. Pokud je **forwardclientcertificate** nastaven na **hodnotu false**, reverzní proxy server nebude během handshake s klientem požadovat klientský certifikát.
Toto je výchozí chování.

2. Pokud je **forwardclientcertificate** nastavenna **na hodnotu true**, reverzní proxy požaduje certifikát klienta během jeho ssl handshake s klientem.
Poté předá data klientského certifikátu ve vlastní hlavičce HTTP s názvem **X-Client-Certificate**. Hodnota záhlaví je řetězec formátu PEM kódu je base64 certifikátu klienta. Služba může úspěšně/nepodaří požadavek s příslušným stavovým kódem po kontrole dat certifikátu.
Pokud klient nepředloží certifikát, obrátí proxy předává prázdnou hlavičku a nechá službu zpracovávat případ.

> [!NOTE]
> Reverzní proxy je pouhý server pro předávání. Neprovede žádné ověření certifikátu klienta.


## <a name="next-steps"></a>Další kroky
* [Nastavte a nakonfigurujte reverzní proxy server v clusteru](service-fabric-reverseproxy-setup.md).
* Odkazovat na [Konfigurace reverzního proxy serveru pro připojení k zabezpečeným službám](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Podívejte se na příklad http komunikace mezi službami v [ukázkovém projektu na GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Vzdálená volání procedur pomocí vzdálené komunikace spolehlivých služeb](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, které používá OWIN ve spolehlivých službách](service-fabric-reliable-services-communication-webapi.md)
* [Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
