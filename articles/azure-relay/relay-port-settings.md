---
title: Nastavení portu Azure Relay | Microsoft Docs
description: Tento článek obsahuje tabulku, která popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85314277"
---
# <a name="azure-relay-port-settings"></a>Nastavení portu Azure Relay

Následující tabulka popisuje požadovanou konfiguraci pro hodnoty portů pro Azure Relay.

## <a name="hybrid-connections"></a>Hybridní připojení

Hybrid Connections používá objekty WebSockets na portu 443 s protokolem TLS jako podkladový přenosový mechanismus, který používá pouze **protokol HTTPS** . 

## <a name="wcf-relays"></a>Přenosy WCF
  
|Vazba|Zabezpečení přenosu|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ano|HTTPS| 
|" |No|HTTP|  
|[BasicHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (služba)|Kteroukoli|9351/HTTP|  
|[NetEventRelayBinding – třída](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetEventRelayBinding – třída](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (služba)|Kteroukoli|9351/HTTP|  
|[NetTcpRelayBinding – třída](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/služba)|Kteroukoli|5671/9352/HTTP (9352/9353 při použití Hybrid)|  
|[NetOnewayRelayBinding – třída](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ano|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetOnewayRelayBinding – třída](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (služba)|Kteroukoli|9351/HTTP|  
|[WebHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ano|HTTPS|  
|" |No|HTTP|  
|[WebHttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (služba)|Kteroukoli|9351/HTTP|  
|[WS2007HttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ano|HTTPS|  
|" |No|HTTP|  
|[WS2007HttpRelayBinding – třída](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (služba)|Kteroukoli|9351/HTTP|

## <a name="next-steps"></a>Další kroky
Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Přenos – nejčastější dotazy](relay-faq.md)