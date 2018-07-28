---
title: Azure Service Fabric reverzního proxy zabezpečené komunikace | Dokumentace Microsoftu
description: Konfigurace reverzního proxy serveru pro zajištění zabezpečené komunikace začátku do konce.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 0558a5647267dda26890ba3a6dc1af326fae94f6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308159"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Připojení ke službě zabezpečené pomocí reverzního proxy serveru

Tento článek vysvětluje, jak vytvořit zabezpečené připojení mezi reverzního proxy serveru a služby, což umožní zabezpečený kanál začátku do konce.

Připojení k zabezpečení služeb je podporováno pouze v případě, že reverzního proxy je nakonfigurovaná k naslouchání na adrese HTTPS. Zbývající části dokumentu se předpokládá, že jde o tento případ.
Odkazovat na [reverzní proxy server v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) reverzní proxy server nakonfigurovat v Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Navazování zabezpečeného připojení mezi reverzního proxy serveru a služby 

### <a name="reverse-proxy-authenticating-to-services"></a>Reverzní proxy server ověřování do služby:
Reverzní proxy server identifikuje ke službám pomocí jeho certifikát zadaný u ***reverseProxyCertificate*** vlastnost **clusteru** [části typu prostředku](../azure-resource-manager/resource-group-authoring-templates.md). Služby můžete implementovat logiku pro ověření certifikátu uváděnému reverzního proxy serveru. Služby můžete zadat podrobnosti o certifikátu přijaté klienta jako nastavení konfigurace v balíčku pro konfiguraci. To může číst za běhu a slouží k ověření certifikátu uváděnému reverzního proxy serveru. Odkazovat na [Správa parametrů aplikací](service-fabric-manage-multiple-environment-app-configuration.md) přidat nastavení konfigurace. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Reverzní proxy server ověření identity služby prostřednictvím certifikát předložený službou:
Reverzní proxy server podporuje tyto zásady k provedení ověření certifikátu serveru certifikátů předložený služby: None, ServiceCommonNameAndIssuer a ServiceCertificateThumbprints.
Vyberte zásadu pro reverzní proxy server používat, zadejte **ApplicationCertificateValidationPolicy** v **ApplicationGateway/Http** části [nastavení fabricSettings](service-fabric-cluster-fabric-settings.md).

V další části se zobrazí podrobnosti o konfiguraci pro každý z těchto možností.

### <a name="service-certificate-validation-options"></a>Možnosti ověřování certifikátu služby 

- **Žádný**: přeskočí ověření certifikátu směrovány přes proxy server služby reverzní proxy server a vytvoří zabezpečené připojení. Toto je výchozí chování.
Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **žádný** v [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) oddílu.

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

- **ServiceCommonNameAndIssuer**: reverzní proxy server ověří certifikát předložený službou na základě běžný název certifikátu a kryptografický otisk vystavitele okamžité: Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCommonNameAndIssuer** v [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) oddílu.

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

   Chcete-li zadat seznam běžný název služby a kryptografické otisky vystavitele, přidejte [ **ApplicationGateway/Http/ServiceCommonNameAndIssuer** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) části **nastavení fabricSettings**, jak vidíte níže. Lze přidat více certifikátů běžné dvojice název a vystavitele kryptografický otisk v **parametry** pole. 

   Pokud koncový bod reverzní proxy server se připojuje k představuje certifikát, který je běžné kryptografického otisku název a vystavitele odpovídá některému z hodnoty zadané tady, je vytvořit kanál SSL. 
   Reverzní proxy server při selhání tak, aby odpovídaly podrobnosti o certifikátu, selže požadavek klienta 502 kód stavu (Chybná brána). Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL." 

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

- **ServiceCertificateThumbprints**: reverzní proxy server ověří, jestli certifikát směrovány přes proxy server služby založené na jeho kryptografický otisk. Můžete také přejít tato trasa při služeb jsou nakonfigurovány s vlastní podepsané certifikáty: Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCertificateThumbprints** v [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) oddílu.

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

   Také určit, že kryptografické otisky s **ServiceCertificateThumbprints** položku **ApplicationGateway/Http** oddílu. Více kryptografických otisků lze zadat jako seznam oddělený čárkami v poli hodnota, jak je znázorněno níže:

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

   Pokud kryptografický otisk certifikátu serveru je uvedený v této položce Konfigurace, reverzní proxy server úspěšné připojení protokolem SSL. V opačném případě ukončí připojení a selže požadavek klienta s 502 (Chybná brána). Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Koncový bod výběru logiky Pokud služby vystaví zabezpečené, jakož i nezabezpečené koncových bodů
Service fabric podporuje konfiguraci více koncových bodů služby. Další informace najdete v tématu [určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md).

Reverzní proxy server vybere jeden z koncových bodů k předání požadavku na základě **ListenerName** parametr v dotazu [identifikátor URI služby](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Pokud **ListenerName** parametr není zadán, reverzní proxy server můžete vybrat libovolný koncový bod v seznamu koncových bodů. V závislosti na koncové body konfigurované pro službu může být vybraný koncový bod koncový bod HTTP nebo HTTPS. Může existovat scénáře nebo požadavky na místo, kam chcete reverzní proxy server k provozu v "jen pro zabezpečený režim"; To znamená, že nechcete zabezpečené reverzní proxy server pro předávání požadavků na nezabezpečenou koncových bodů. Reverzní proxy server nastavit do režimu jen pro zabezpečení, zadejte **SecureOnlyMode** položku konfigurace s hodnotou **true** v [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) oddílu.   

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
> Při fungování v **SecureOnlyMode**, pokud má zadaná klienta **ListenerName** odpovídající HTTP(unsecured) koncový bod, reverzní proxy server selže požadavek se stavovým kódem 404 (Nenalezeno) protokolu HTTP.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Nastavení ověřování pomocí certifikátu klienta prostřednictvím reverzního proxy serveru
Ukončení protokolu SSL se odehrává na reverzní proxy server a dojde ke ztrátě všech dat certifikátu klienta. Pro služby k provedení ověření klientského certifikátu, zadejte **ForwardClientCertificate** nastavení [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) části.

1. Při **ForwardClientCertificate** je nastavena na **false**, reverzní proxy server nebude vyžadovat certifikát klienta během jeho metody handshake SSL s klientem.
Toto je výchozí chování.

2. Když **ForwardClientCertificate** je nastavena na **true**, reverzní proxy požaduje certifikát klienta při jeho metody handshake SSL u klienta.
Pak předá klienta data certifikátu ve vlastní hlavičku HTTP s názvem **certifikát klienta pro X**. Hodnota hlavičky je řetězec formátu PEM kódovanou jako base64 z certifikátu klienta. Službu můžete úspěšné/selhání požadavku odpovídající stavovým kódem až po kontrole data certifikátu.
Pokud klient není k dispozici certifikát, reverzní proxy server předává prázdnou hlavičku a nechat službu zpracování případu.

> [!NOTE]
> Reverzní proxy server je i server pro předávání. Neprovede žádné ověření certifikátu klienta.


## <a name="next-steps"></a>Další postup
* Odkazovat na [konfigurace reverzního proxy serveru pro připojení k zabezpečení služeb](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) pro Azure Resource Manager zabezpečení ukázkové šablony konfigurace reverzního proxy serveru s jinou službu certifikátem možnosti ověřování.
* Podívejte se příklad komunikaci pomocí protokolu HTTP mezi službami v [ukázkového projektu na Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Volání vzdálených procedur pomocí vzdálené komunikace modelu Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, která používá OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
