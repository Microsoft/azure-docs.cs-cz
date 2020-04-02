---
title: Nastavení portu Azure Relay | Dokumenty společnosti Microsoft
description: Tento článek obsahuje tabulku, která popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529115"
---
# <a name="azure-relay-port-settings"></a>Nastavení portu Azure Relay

Následující tabulka popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.

## <a name="hybrid-connections"></a>Hybridní připojení

Hybridní připojení používá WebSockets na portu 443 s TLS jako základní mechanismus přenosu, který používá pouze **protokol HTTPS.** 

## <a name="wcf-relays"></a>Přenosy WCF
  
|Vazba|Zabezpečení přenosu|Port|  
|-------------|------------------------|----------|  
|[Základní třída httprelaybinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ano|HTTPS| 
|" |Ne|HTTP|  
|[Základní třída httprelaybinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[Třída NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/služba)|Buď|5671/9352/HTTP (9352/9353, pokud používáte hybridní)|  
|[Třída NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |Ne|9350/HTTP|  
|[Třída NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[Třída WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (služba)|Buď|9351/HTTP|  
|[Třída WS2007HttpRelayBinding (klient)](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)|Ano|HTTPS|  
|" |Ne|HTTP|  
|[Třída WS2007HttpRelayBinding (služba)](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)|Buď|9351/HTTP|

## <a name="next-steps"></a>Další kroky
Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)