---
title: Místní brána dat pro Azure Analysis Services | Microsoft Docs
description: Místní brána je nutná v případě, že se Server Analysis Services v Azure připojí k místním zdrojům dat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fed184c349789dc38f12f62567acc0d0500ca94c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016089"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Připojení k místním zdrojům dat s místní bránou dat

Místní brána dat zajišťuje zabezpečený přenos dat mezi místními zdroji dat a servery Azure Analysis Services v cloudu. Kromě práce s více Azure Analysis Services servery ve stejné oblasti funguje nejnovější verze brány také s Azure Logic Apps, Power BI, Power Apps a Power Automate. Brána, kterou nainstalujete, je stejná pro všechny tyto služby, Azure Analysis Services a Logic Apps mít ještě nějaké další kroky.

Zde uvedené informace jsou specifické pro to, jak Azure Analysis Services fungují s místní bránou dat. Další informace o bráně obecně a o tom, jak funguje s dalšími službami, najdete v tématu [co je místní brána dat?](/data-integration/gateway/service-gateway-onprem).

V případě Azure Analysis Services se při prvním procesu čtyř částí získá instalační program s bránou.

- **Stažení a spuštění instalačního programu** – tento krok nainstaluje službu brány do počítače ve vaší organizaci. Přihlásíte se také k Azure pomocí účtu v Azure AD vašeho [tenanta](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Účty Azure B2B (Guest) se nepodporují.

- **Registrace brány** – v tomto kroku zadáte název a obnovovací klíč pro bránu a vyberete oblast a zaregistrujete bránu do cloudové služby brány. Prostředek brány je možné zaregistrovat v libovolné oblasti, ale doporučuje se ho ve stejné oblasti jako servery Analysis Services. 

- **Vytvoření prostředku brány v Azure** – v tomto kroku vytvoříte prostředek brány v Azure.

- **Připojte prostředek brány k serverům** – Jakmile máte prostředek brány, můžete k němu začít připojovat servery. Můžete propojit víc serverů a dalších prostředků, pokud jsou ve stejné oblasti.



## <a name="how-it-works"></a>Jak to funguje
Bránu, kterou nainstalujete do počítače ve vaší organizaci, se spouští jako služba systému Windows, místní **Brána dat**. Tato místní služba je zaregistrovaná v cloudové službě brány přes Azure Service Bus. Pak vytvoříte prostředek místní brány dat pro předplatné Azure. Vaše Azure Analysis Services servery se pak připojí k vašemu prostředku brány Azure. Když se modely na vašem serveru potřebují připojovat k místním zdrojům dat pro dotazy nebo zpracování, dotaz a tok dat procházejí prostředek brány, Azure Service Bus, místní služby místní brány dat a zdroji dat. 

![Jak to funguje](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Tok dotazů a dat:

1. Dotaz vytvoří cloudová služba se zašifrovanými přihlašovacími údaji pro místní zdroj dat. Pak se odešle do fronty ke zpracování bránou.
2. Cloudová služba brány dotaz analyzuje a odešle požadavek do [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Místní brána dat se služby Azure Service Bus dotáže na čekající žádosti.
4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se pomocí nich ke zdroji dat.
5. Brána odešle dotaz do zdroje dat k provedení.
6. Výsledky se pošlou ze zdroje dat zpět do brány a pak do cloudové služby a na váš server.

## <a name="installing"></a>Instalace

Při instalaci pro prostředí Azure Analysis Services je důležité postupovat podle kroků popsaných v tématu [instalace a konfigurace místní brány dat pro Azure Analysis Services](analysis-services-gateway-install.md). Tento článek je určený pro Azure Analysis Services. Obsahuje další kroky potřebné k nastavení místního prostředku brány dat v Azure a připojení serveru Azure Analysis Services k prostředku.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Připojení k prostředku brány v jiném předplatném

Doporučuje se vytvořit prostředek brány Azure ve stejném předplatném jako váš server. Servery ale můžete nakonfigurovat tak, aby se připojovaly k prostředku brány v jiném předplatném. Připojení k prostředku brány v jiném předplatném není podporované při konfiguraci stávajícího nastavení serveru nebo při vytváření nového serveru na portálu, ale dá se nakonfigurovat pomocí PowerShellu. Další informace najdete v tématu [připojení prostředku brány k serveru](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Nastavení portů a komunikace

Brána vytvoří odchozí připojení k Azure Service Bus. Komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354.  Příchozí porty brána nevyžaduje.

Možná budete muset v bráně firewall zahrnout IP adresy pro vaši oblast dat. [Seznam IP adres datových center Microsoft Azure si můžete stáhnout.](https://www.microsoft.com/download/details.aspx?id=56519) Tento seznam se každý týden aktualizuje. IP adresy v seznamu IP adres datacentra Azure jsou uvedené v zápisu CIDR. Další informace najdete v tématu [směrování Inter-Domain bez třídy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Níže jsou uvedené plně kvalifikované názvy domény používané bránou.

| Názvy domén | Odchozí porty | Description |
| --- | --- | --- |
| *.powerbi.com |80 |Ke stažení instalačního programu se používá HTTP. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671–5672 |Rozšířený protokol řízení front zpráv (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Naslouchací procesy u služby Service Bus Relay přes TCP (vyžaduje port 443 pro získání tokenu služby Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Používá se k otestování připojení k internetu v případě, že je brána nedostupná pro službu Power BI. |
| *.microsoftonline-p.com |443 |Používá se k ověření v závislosti na konfiguraci. |
| dc.services.visualstudio.com    |443 |Používá se v AppInsights ke shromáždění telemetrie. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Vynucení komunikace přes protokol HTTPS s Azure Service Busem

Bráně můžete vynutit komunikaci s Azure Service Bus pomocí protokolu HTTPS místo přímého protokolu TCP; Nicméně to může významně snížit výkon. Soubor *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* můžete upravit změnou hodnoty z `AutoDetect` na `Https` . Tento soubor se obvykle nachází v adresáři *C:\Program Files\On-premises data Gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Další kroky 

V části místní brána dat je součástí obecného obsahu, který se vztahuje na všechny služby, které brána podporuje:

* [Místní brána dat – nejčastější dotazy](/data-integration/gateway/service-gateway-onprem-faq)   
* [Použití aplikace místní brány dat](/data-integration/gateway/service-gateway-app)   
* [Správa na úrovni tenantů](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurace nastavení proxy serveru](/data-integration/gateway/service-gateway-proxy)   
* [Úprava nastavení komunikace](/data-integration/gateway/service-gateway-communication)   
* [Konfigurace souborů protokolu](/data-integration/gateway/service-gateway-log-files)   
* [Řešení potíží](/data-integration/gateway/service-gateway-tshoot)
* [Monitorování a optimalizace výkonu brány](/data-integration/gateway/service-gateway-performance)