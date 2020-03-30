---
title: Místní datová brána pro Azure Analysis Services | Dokumenty společnosti Microsoft
description: Místní brána je nezbytná, pokud se server Analysis Services v Azure připojí k místním zdrojům dat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310148"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Připojení k místním zdrojům dat pomocí místní brány dat

Místní brána dat zajišťuje zabezpečený přenos dat mezi místními zdroji dat a servery Služby Azure Analysis Services v cloudu. Kromě práce s několika servery Azure Analysis Services ve stejné oblasti funguje nejnovější verze brány také s Azure Logic Apps, Power BI, Power Apps a Power Automate. Zatímco brána, kterou nainstalujete, je stejná ve všech těchto službách, Azure Analysis Services a Logic Apps mají některé další kroky.

Zde uvedené informace jsou specifické pro fungování služby Azure Analysis Services s místní bránou dat. Další informace o bráně obecně a o tom, jak funguje s jinými službami, najdete v [tématu Co je místní brána dat?](/data-integration/gateway/service-gateway-onprem).

Pro Azure Analysis Services, získání nastavení s bránou poprvé je čtyřdílný proces:

- **Stáhnout a spustit instalační program** – Tento krok nainstaluje službu brány do počítače ve vaší organizaci. K Azure se taky přihlásíte pomocí účtu ve službě Azure AD [vašeho tenanta.](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Účty Azure B2B (guest) nejsou podporované.

- **Registrace brány** – v tomto kroku zadáte název a obnovovací klíč pro bránu a vyberete oblast a zaregistrujete bránu pomocí cloudové služby Gateway. Prostředek brány lze zaregistrovat v libovolné oblasti, ale doporučuje se, aby byl ve stejné oblasti jako servery Služby Analysis Services. 

- **Vytvoření prostředku brány v Azure** – v tomto kroku vytvoříte prostředek brány v Azure.

- **Připojte servery k prostředku brány** – jakmile budete mít prostředek brány, můžete k němu začít připojovat servery. Můžete připojit více serverů a dalších prostředků za předpokladu, že jsou ve stejné oblasti.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Jak to funguje
Brána, kterou nainstalujete do počítače ve vaší organizaci, běží jako služba **Windows, místní brána dat**. Tato místní služba je zaregistrovaná v cloudové službě brány přes Azure Service Bus. Potom vytvoříte prostředek místní brány dat pro vaše předplatné Azure. Vaše servery Azure Analysis Services se pak připojí k prostředku brány Azure. Když se modely na serveru potřebují připojit k místním zdrojům dat pro dotazy nebo zpracování, dotaz a tok dat prochází prostředek brány, Azure Service Bus, místní místní služba datových bran a vaše zdroje dat. 

![Jak to funguje](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Tok dotazů a dat:

1. Dotaz vytvoří cloudová služba se zašifrovanými přihlašovacími údaji pro místní zdroj dat. Pak se odešle do fronty ke zpracování bránou.
2. Cloudová služba brány analyzuje dotaz a odešle požadavek na [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Místní brána dat se služby Azure Service Bus dotáže na čekající žádosti.
4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se pomocí nich ke zdroji dat.
5. Brána odešle dotaz do zdroje dat k provedení.
6. Výsledky se pošlou ze zdroje dat zpět do brány a pak do cloudové služby a na váš server.

## <a name="installing"></a>Installing

Při instalaci pro prostředí Azure Analysis Services je důležité postupovat podle kroků popsaných v [části Instalace a konfigurace místní brány dat pro Službu Azure Analysis Services](analysis-services-gateway-install.md). Tento článek je specifický pro Azure Analysis Services. Obsahuje další kroky potřebné k nastavení prostředku místní datové brány v Azure a připojení serveru Azure Analysis Services k prostředku.

## <a name="ports-and-communication-settings"></a>Porty a nastavení komunikace

Brána vytvoří odchozí připojení k Azure Service Bus. Komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354.  Příchozí porty brána nevyžaduje.

Do brány firewall může být nutné zahrnout IP adresy pro oblast dat. [Seznam IP adres datových center Microsoft Azure si můžete stáhnout.](https://www.microsoft.com/download/details.aspx?id=56519) Tento seznam se každý týden aktualizuje. IP adresy v seznamu IP adres datacentra Azure jsou uvedené v zápisu CIDR. Další informace naleznete v [tématu Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Níže jsou plně kvalifikované názvy domén používané bránou.

| Názvy domén | Odchozí porty | Popis |
| --- | --- | --- |
| *.powerbi.com |80 |Ke stažení instalačního programu se používá HTTP. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671–5672 |Rozšířený protokol řízení front zpráv (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Naslouchací procesy u služby Service Bus Relay přes TCP (vyžaduje port 443 pro získání tokenu služby Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Používá se k otestování připojení k internetu v případě, že je brána nedostupná pro službu Power BI. |
| *.microsoftonline-p.com |443 |Používá se k ověření v závislosti na konfiguraci. |
| dc.services.visualstudio.com  |443 |Používá AppInsights ke shromažďování telemetrie. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>Vynucení komunikace přes protokol HTTPS s Azure Service Busem

Můžete vynutit bránu ke komunikaci s Azure Service Bus pomocí protokolu HTTPS namísto přímého TCP; to však může výrazně snížit výkon. Soubor *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* můžete upravit změnou `AutoDetect` hodnoty `Https`z na . Tento soubor je obvykle umístěn v *c:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Další kroky 

Následující články jsou zahrnuty v obecném obsahu místní brány dat, který se vztahuje na všechny služby, které brána podporuje:

* [Nejčastější dotazy k místní datové bráně](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Použití aplikace místní brány dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Správa na úrovni tenantů](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurace nastavení serveru proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Úprava nastavení komunikace](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurace souborů protokolu](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Řešení potíží](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorování a optimalizace výkonu brány](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
