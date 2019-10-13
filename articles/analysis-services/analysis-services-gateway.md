---
title: Místní brána dat pro Azure Analysis Services | Microsoft Docs
description: Místní brána je nutná v případě, že se Server Analysis Services v Azure připojí k místním zdrojům dat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8d9df32070ff252dff791650788888d1d9a6ce84
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294935"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Připojení k místním zdrojům dat s místní bránou dat

Místní brána dat zajišťuje zabezpečený přenos dat mezi místními zdroji dat a servery Azure Analysis Services v cloudu. Kromě práce s více Azure Analysis Services servery ve stejné oblasti funguje nejnovější verze brány také s Azure Logic Apps, Power BI, Power Apps a Microsoft Flow. K jedné bráně můžete přidružit více služeb ve stejném předplatném a stejné oblasti. Brána, kterou nainstalujete, je stejná pro všechny tyto služby, Azure Analysis Services a Logic Apps mít ještě nějaké další kroky.

V případě Azure Analysis Services se při prvním procesu čtyř částí získá instalační program s bránou.

- **Stažení a spuštění instalačního programu** – tento krok nainstaluje službu brány do počítače ve vaší organizaci. Přihlásíte se také k Azure pomocí účtu v Azure AD vašeho [tenanta](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Účty Azure B2B (Guest) se nepodporují.

- **Registrace brány** – v tomto kroku zadáte název a obnovovací klíč pro bránu a vyberete oblast a zaregistrujete bránu do cloudové služby brány. Prostředek brány je možné zaregistrovat v libovolné oblasti, ale doporučujeme, aby byl ve stejné oblasti jako servery Analysis Services. 

- **Vytvoření prostředku brány v Azure** – v tomto kroku vytvoříte prostředek brány ve svém předplatném Azure.

- **Připojte své servery k prostředku brány** – Jakmile máte prostředek brány v předplatném, můžete k němu začít připojit své servery. Můžete propojit víc serverů a dalších prostředků, pokud jsou ve stejném předplatném a stejné oblasti.

## <a name="how-it-works"> </a>Jak to funguje
Bránu, kterou nainstalujete do počítače ve vaší organizaci, se spouští jako služba systému Windows, místní **Brána dat**. Tato místní služba je zaregistrovaná v cloudové službě brány prostřednictvím Azure Service Bus. Pak vytvoříte prostředek místní brány dat pro vaše předplatné Azure. Vaše Azure Analysis Services servery se pak připojí k vašemu prostředku brány Azure. Když se modely na vašem serveru potřebují připojovat k místním zdrojům dat pro dotazy nebo zpracování, dotaz a tok dat procházejí prostředek brány, Azure Service Bus, místní služby místní brány dat a zdroji dat. 

![Jak to funguje](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Dotazy a tok dat:

1. Cloudová služba vytvoří dotaz s šifrovanými přihlašovacími údaji pro místní zdroj dat. Pak se pošle do fronty, kterou brána zpracuje.
2. Cloudová služba brány dotaz analyzuje a odešle požadavek do [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Místní brána dat se dotáže Azure Service Bus na nevyřízené žádosti.
4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se ke zdrojům dat pomocí těchto přihlašovacích údajů.
5. Brána odešle dotaz do zdroje dat ke spuštění.
6. Výsledky se odešlou ze zdroje dat zpátky do brány a potom do cloudové služby a vašeho serveru.

## <a name="installing"></a>Instalují

Při instalaci pro prostředí Azure Analysis Services je důležité postupovat podle kroků popsaných v tématu [instalace a konfigurace místní brány dat pro Azure Analysis Services](analysis-services-gateway-install.md). Tento článek je určený pro Azure Analysis Services. Obsahuje další kroky potřebné k nastavení místního prostředku brány dat v Azure a připojení serveru Azure Analysis Services k prostředku.

## <a name="ports-and-communication-settings"></a>Nastavení portů a komunikace

Brána vytvoří odchozí připojení k Azure Service Bus. Komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354.  Brána nevyžaduje příchozí porty.

Možná budete muset v bráně firewall povolit IP adresy pro vaši oblast dat. [Seznam IP adres Microsoft Azure datacentra](https://www.microsoft.com/download/details.aspx?id=41653)si můžete stáhnout. Tento seznam se každý týden aktualizuje. IP adresy, které jsou uvedené v seznamu IP adres datového centra Azure, jsou v notaci CIDR. Další informace najdete v tématu [směrování mezi doménami bez třídy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Níže jsou uvedené plně kvalifikované názvy domény používané bránou.

| Názvy domén | Odchozí porty | Popis |
| --- | --- | --- |
| *. powerbi.com |80 |Protokol HTTP použitý ke stažení instalačního programu. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Rozšířený protokol řízení front zpráv (AMQP) (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Naslouchací procesy v Service Bus Relay přes TCP (vyžaduje 443 pro získání tokenu Access Control) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Používá se k otestování připojení k Internetu, pokud je brána nedosažitelná služba Power BI. |
| *. microsoftonline-p.com |443 |Používá se pro ověřování v závislosti na konfiguraci. |
| dc.services.visualstudio.com  |443 |Používá se v AppInsights ke shromáždění telemetrie. |

### <a name="force-https"></a>Vynucení komunikace pomocí protokolu HTTPS s Azure Service Bus

Bráně můžete vynutit komunikaci s Azure Service Bus pomocí protokolu HTTPS místo přímého protokolu TCP; Nicméně to může významně snížit výkon. Soubor *Microsoft. PowerBI. datamove. Pipeline. GatewayCore. dll. config* můžete upravit změnou hodnoty z `AutoDetect` na `Https`. Tento soubor se obvykle nachází v adresáři *C:\Program Files\On-premises data Gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Další kroky 

V části místní brána dat je součástí obecného obsahu, který se vztahuje na všechny služby, které brána podporuje:

* [Nejčastější dotazy k místní bráně dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Použití aplikace místní brány dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Správa na úrovni tenanta](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurace nastavení proxy serveru](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Upravit nastavení komunikace](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurace souborů protokolu](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Řešení problémů](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorování a optimalizace výkonu brány](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
