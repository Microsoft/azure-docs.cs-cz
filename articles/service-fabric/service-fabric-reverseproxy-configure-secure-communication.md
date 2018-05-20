---
title: Azure Service Fabric reverse zabezpečené komunikace proxy | Microsoft Docs
description: Nakonfigurujte reverzní proxy server pro zajištění zabezpečené komunikace začátku do konce.
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
ms.openlocfilehash: 237a72fd282b29d3032675ccf3fb350f8db59ef7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Připojení na zabezpečené služby s reverzní proxy server

Tento článek vysvětluje, jak k vytvoření zabezpečeného spojení mezi reverzní proxy server a služby, a tak umožňuje zabezpečený kanál začátku do konce.

Připojení k zabezpečení služeb je podporováno pouze v případě, že reverzní proxy server je nakonfigurován pro naslouchání na HTTPS. Zbývající části dokumentu se předpokládá, že je tomu tak.
Odkazovat na [reverzní proxy server v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) reverzní proxy server nakonfigurovat v Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Vytvoření zabezpečeného spojení mezi reverzní proxy server a služby 

### <a name="reverse-proxy-authenticating-to-services"></a>Ověřování služby reverzní proxy server:
Reverzní proxy server se identifikuje ke službám pomocí jeho certifikát zadaný u ***reverseProxyCertificate*** vlastnost **clusteru** [části Typ prostředku](../azure-resource-manager/resource-group-authoring-templates.md). Služby můžete implementovat logiku ověření certifikát předložený reverzní proxy server. Služby můžete zadat podrobnosti o certifikát přijatý klienta jako nastavení konfigurace v konfigurační balíček. To můžete přečíst v době běhu a slouží k ověření certifikát předložený reverzní proxy server. Odkazovat na [spravovat aplikace parametry](service-fabric-manage-multiple-environment-app-configuration.md) přidat nastavení konfigurace. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Ověření identity služby prostřednictvím certifikát předložený službu reverzní proxy server:
K provedení ověřování certifikátu serveru certifikátů předložený služeb, reverzní proxy server podporuje jednu z následujících možností: None, ServiceCommonNameAndIssuer a ServiceCertificateThumbprints.
Vyberte jednu z těchto tří možností, zadejte **ApplicationCertificateValidationPolicy** v sekci parametrů elementu ApplicationGateway/Http pod [fabricSettings](service-fabric-cluster-fabric-settings.md).

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

Informace naleznete v sekci Další podrobnosti o další konfiguraci pro každý z těchto možností.

### <a name="service-certificate-validation-options"></a>Možnosti ověřování certifikátu služby 

- **Žádný**: reverzní proxy server přeskočí ověření certifikátu směrovány přes proxy server služby a navázat zabezpečené připojení. Toto je výchozí chování.
Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **žádné** v sekci parametrů elementu ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: reverzní proxy server ověří certifikát předložený služby založené na běžný název certifikátu a kryptografický otisk vystavitelů okamžitou: Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCommonNameAndIssuer** v sekci parametrů elementu ApplicationGateway/Http.

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

Pokud chcete určit seznam běžný název služby a vystavitele kryptografické otisky, přidejte **ApplicationGateway/Http/ServiceCommonNameAndIssuer** prvek v rámci fabricSettings, jak je uvedeno níže. V elementu pole Parametry lze přidat více párů kryptografický otisk vystavitelů a běžný název certifikátu. 

Pokud koncový bod reverzní proxy server se připojuje k uvede certifikát, který je běžný název a vystavitele kryptografický otisk odpovídá některému z hodnoty zadané v tomto poli, je vytvořit kanál SSL. Při selhání tak, aby odpovídaly podrobnosti certifikátu se nezdaří reverzní proxy server požadavek klienta s 502 stavový kód (chybný brána). Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL." 

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


- **ServiceCertificateThumbprints**: reverzní proxy server ověří podle jeho kryptografický otisk certifikátu směrovány přes proxy server služby. Můžete přejít, tato trasa při služby jsou nakonfigurovány s vlastní podepsané certifikáty: Zadejte **ApplicationCertificateValidationPolicy** s hodnotou **ServiceCertificateThumbprints** v sekci parametrů elementu ApplicationGateway/Http.

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

Také zadat kryptografické otisky s **ServiceCertificateThumbprints** položky v části parametry elementu ApplicationGateway/Http. Více kryptografické otisky lze zadat jako seznam oddělený čárkami v poli hodnota, jak je uvedeno níže:

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

Pokud kryptografický otisk certifikátu serveru je uveden v této položce Konfigurace, reverzní proxy server úspěšné připojení protokolem SSL. V opačném případě ukončí připojení a požadavek klienta s 502 (chybný brána) se nezdaří. Stavový řádek HTTP bude také obsahovat frázi "Neplatný certifikát SSL."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Koncový bod výběr logika při vystavení zabezpečené, jakož i zabezpečená koncové body služby
Služba fabric podporuje konfiguraci více koncových bodů pro službu. Viz [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md).

Reverzní proxy server vybere jeden z koncových bodů pro předávání na základě žádosti **ListenerName** parametr dotazu. Pokud není zadáno, můžete vybrat libovolný koncový bod, ze seznamu koncových bodů. Teď to může být koncový bod HTTP nebo HTTPS. Mohou existovat scénáře nebo požadavky na místo, kam chcete reverzní proxy server pracovat v "zabezpečit pouze režim", jednofaktorovému nechcete, aby zabezpečené reverzní proxy server pro předávání požadavků na zabezpečená koncové body. Toho lze dosáhnout zadáním **SecureOnlyMode** položku konfigurace s hodnotou **true** v sekci parametrů elementu ApplicationGateway/Http.   

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

> 
> Při provozu v **SecureOnlyMode**, pokud má zadanou klienta **ListenerName** odpovídající koncový bod HTTP(unsecured), reverzní proxy server selže požadavek s 404 stavový kód HTTP (není nalezena).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Nastavení ověřování pomocí certifikátu klienta přes reverzní proxy server
Probíhá ukončení protokolu SSL na reverzní proxy server a dojde ke ztrátě všech dat certifikát klienta. Pro služby, aby prováděla ověřování certifikátu klienta, nastavte **ForwardClientCertificate** nastavení v sekci parametrů elementu ApplicationGateway/Http.

1. Při **ForwardClientCertificate** je nastaven na **false**, reverzní proxy server nebude žádost o certifikát klienta během jeho metody handshake SSL s klientem.
Toto je výchozí chování.

2. Když **ForwardClientCertificate** je nastaven na **true**, reverse proxy žádosti o certifikát klienta během jeho metody handshake SSL s klientem.
Bude poté předávat klienta data certifikátu v vlastní hlavičku HTTP s názvem **certifikát klienta X**. Hodnota hlavičky je řetězec formátu PEM kódováním base64 z certifikátu klienta. Službu můžete úspěšné nebo selhání požadavek s odpovídající stavový kód po kontrole data certifikátu.
Pokud klient nemá k dispozici certifikát, reverzní proxy server předává hlavičku prázdný a nechat službu zpracovávat tento případ.

> Reverzní proxy server je pouhé předávání. Ho nebude provádět žádné ověření certifikátu klienta.


## <a name="next-steps"></a>Další postup
* Odkazovat na [konfigurovat reverzní proxy server pro připojení k zabezpečení služeb](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) pro Azure Resource Manager šablony ukázky konfigurace zabezpečení reverzní proxy server s jinou službu certifikátem možnosti ověřování.
* Zobrazit příklad komunikaci pomocí protokolu HTTP mezi službami v [ukázkového projektu na Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Volání vzdálených procedur s vzdálenou komunikaci spolehlivé služby](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, která používá OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md)
* [Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
