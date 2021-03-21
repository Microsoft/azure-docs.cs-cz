---
title: Řešení potíží s Azure VPN Gateway pomocí diagnostických protokolů
description: Řešení potíží s Azure VPN Gateway pomocí diagnostických protokolů
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 4e65dcd448a2ab2fad635cab12f41d858416becf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726051"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Řešení potíží s Azure VPN Gateway pomocí diagnostických protokolů

Tento článek vám pomůže pochopit různé protokoly, které jsou dostupné pro VPN Gateway diagnostiku, a jak je použít k efektivnímu řešení potíží s bránou VPN Gateway.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

V Azure jsou k dispozici následující protokoly:

|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Obsahuje protokoly diagnostiky pro události konfigurace brány, primární změny a události údržby. |
|**TunnelDiagnosticLog** | Obsahuje události změny stavu tunelu. Události připojení a odpojení tunelu mají souhrnný důvod změny stavu, pokud je to možné. |
|**RouteDiagnosticLog** | Zaznamená změny do statických tras a událostí protokolu BGP, ke kterým dojde v bráně. |
|**IKEDiagnosticLog** | Protokoluje zprávy řízení IKE a události v bráně. |
|**P2SDiagnosticLog** | Protokoluje zprávy a události řízení Point-to-site v bráně. |

Všimněte si, že v těchto tabulkách je dostupných několik sloupců. V tomto článku prezentujeme jenom ty nejdůležitější pro snazší využití protokolu.

## <a name="set-up-logging"></a><a name="setup"></a>Nastavení protokolování

Informace o tom, jak nastavit události diagnostického protokolu z Azure VPN Gateway pomocí Azure Log Analytics, najdete v tématu [Nastavení výstrah pro události diagnostického protokolu z VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Změny konfigurace se auditují v tabulce **GatewayDiagnosticLog** . Všimněte si, že může trvat několik minut, než se změny, které spustíte, projeví v protokolech.

Tady máte vzorový dotaz jako referenci.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Tento dotaz na **GatewayDiagnosticLog** zobrazí více sloupců.

|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**TimeGenerated** | časové razítko každé události, v časovém pásmu UTC|
|**OperationName** |událost, ke které došlo. Může to být buď *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration* .|
|**Zpráva** | Podrobnosti o tom, jaká operace se děje, a zobrazí seznam úspěšných nebo neúspěšných výsledků.|

Následující příklad ukazuje aktivitu zaznamenanou při použití nové konfigurace:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Nastavte příklad operace brány.":::


Všimněte si, že SetGatewayConfiguration se protokoluje při každém změně konfigurace v VPN Gateway nebo bráně místní sítě.
Křížové odkazy na výsledky z tabulky **GatewayDiagnosticLog** s těmito tabulkami **TunnelDiagnosticLog** vám pomůžou určit, jestli se chyba připojení tunelového připojení spustila ve stejnou dobu, kdy se změnila konfigurace, nebo když došlo k údržbě. Pokud ano, máme skvělý ukazatel na možnou hlavní příčinu.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

Tabulka **TunnelDiagnosticLog** je velmi užitečná pro kontrolu historických stavů připojení tunelu.

Tady máte vzorový dotaz jako referenci.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Tento dotaz na **TunnelDiagnosticLog** zobrazí více sloupců.


|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**TimeGenerated** | časové razítko každé události, v časovém pásmu UTC|
|**OperationName** | událost, ke které došlo. Může to být buď *TunnelConnected* nebo *TunnelDisconnected*.|
| **Instance \_ s** | instance role brány, která aktivovala událost. Může to být buď GatewayTenantWorker \_ \_ 0, nebo GatewayTenantWorker \_ v \_ 1, což jsou názvy dvou instancí brány.|
| **Prostředek** | označuje název brány sítě VPN. |
| **ResourceGroup** | označuje skupinu prostředků, ve které je brána.|


Příklad výstupu:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Příklad události připojené k tunelu":::


**TunnelDiagnosticLog** je velmi užitečné k odstraňování potíží s minulými událostmi týkajícími se neočekávaných odpojení sítě VPN. Jeho odlehčená povaha nabízí možnost analýzy velkých časových rozsahů za několik dní s malým úsilím.
Pouze po identifikaci časového razítka odpojení můžete přejít na podrobnější analýzu tabulky **IKEdiagnosticLog** , aby se dig hlouběji do příčiny odpojení, která se týkají protokolu IPSec.


Rady pro odstraňování potíží:
- Pokud se na jedné instanci brány zobrazuje událost odpojení, za několik sekund následuje událost připojení k **jiné** instanci brány a prohlížíte při převzetí služeb při selhání brány. Obvykle se jedná o očekávané chování z důvodu údržby instance brány. Další informace o tomto chování najdete v tématu [o redundanci Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Stejné chování se projeví, pokud úmyslně spustíte resetování brány na Azure, což způsobí restartování instance aktivní brány. Další informace o tomto chování najdete v tématu [resetování VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic) .
- Pokud se u jedné instance brány zobrazuje událost odpojení, po několika sekundách událost připojení ke **stejné** instanci brány, můžete se podívat na poruchau sítě, která způsobuje časový limit DPD, nebo odpojení chybně odeslané místním zařízením.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

Tabulka **RouteDiagnosticLog** sleduje aktivitu pro staticky upravené trasy nebo trasy přijaté prostřednictvím protokolu BGP.

Tady máte vzorový dotaz jako referenci.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Tento dotaz na **RouteDiagnosticLog** zobrazí více sloupců.

|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**TimeGenerated** | časové razítko každé události, v časovém pásmu UTC|
|**OperationName** | událost, ke které došlo. Může být buď *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*|
| **Zpráva** | Podrobnosti o tom, jaká operace se děje.|

Výstup bude zobrazovat užitečné informace o připojených nebo odpojených partnerských vztazích protokolu BGP a vyměňovaných trasách.

Příklad:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Příklady tras protokolu BGP.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

Tabulka **IKEDiagnosticLog** nabízí podrobné protokolování ladění pro protokol IKE/IPSec. To je velmi užitečné při řešení potíží při odpojování nebo při připojování scénářů sítě VPN.

Tady máte vzorový dotaz jako referenci.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Tento dotaz na **IKEDiagnosticLog** zobrazí více sloupců.


|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**TimeGenerated** | časové razítko každé události, v časovém pásmu UTC|
| **RemoteIP** | IP adresa místního zařízení VPN. V reálných scénářích je užitečné filtrovat podle IP adresy relevantního místního zařízení, které má více než jednu. |
|**LocalIP** | IP adresa VPN Gateway řešíme. V reálných scénářích je užitečné filtrovat podle IP adresy příslušné brány VPN, ale v předplatném je víc než jedna. |
|**Událost** | obsahuje diagnostickou zprávu užitečnou pro řešení potíží. Obvykle začínají klíčovým slovem a odkazují na akce prováděné pomocí Azure Gateway **\[ Send \]** indikuje událost, která je způsobená paketem IPSec odesílaným **\[ obdrženou \]** bránou Azure. indikuje, že událost v důsledku přijetí paketu přijatého z **\[ místního \]** zařízení znamená, že brána Azure zavedla akci místně. |


Všimněte si, že RemoteIP, LocalIP a sloupce událostí nejsou k dispozici v původním seznamu sloupců v databázi AzureDiagnostics, ale přidají se do dotazu analýzou výstupu sloupce "zpráva", aby se zjednodušila jeho analýza.

Tipy pro řešení potíží:

- Aby bylo možné identifikovat začátek vyjednávání protokolu IPSec, je nutné najít počáteční \_ zprávu o INICIALIZACI SA. Takovou zprávu může odeslat kterákoli strana tunelu. Ten, kdo pošle první paket, se v terminologii protokolu IPsec nazývá "iniciátor", zatímco druhá strana se stala "respondér". První zpráva o \_ INICIALIZACI SA je vždy ta, kde rCookie = 0.

- Pokud se nepovede tunelové propojení IPsec navázat, Azure se bude pokoušet opakovat každých několik sekund. Z tohoto důvodu problémy s řešením "VPN down" je velmi praktické na IKEdiagnosticLog, protože nemusíte čekat na určitou dobu k reprodukování problému. Selhání se také bude teoreticky vždycky opakovat pokaždé, když se pokusíme, abyste mohli kdykoliv přiblížit jedno "ukázkové" "pokusy o neúspěšné vyjednávání.

- Inicializace SA \_ obsahuje parametry protokolu IPSec, které chce partner použít pro toto vyjednávání protokolu IPSec. Oficiální dokument   
[Výchozí parametry protokolu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) obsahují seznam parametrů protokolu IPSec podporovaných bránou Azure s výchozími nastaveními.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

Poslední dostupná tabulka pro diagnostiku VPN je **P2SDiagnosticLog**. Tato aktivita sleduje aktivitu pro Point-to-site.

Tady máte vzorový dotaz jako referenci.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Tento dotaz na **P2SDiagnosticLog** zobrazí více sloupců.

|***Název** _ | _ *_Popis_** |
|---        | ---               |
|**TimeGenerated** | časové razítko každé události, v časovém pásmu UTC|
|**OperationName** | událost, ke které došlo. Bude *P2SLogEvent*|
| **Zpráva** | Podrobnosti o tom, jaká operace se děje.|

Ve výstupu se zobrazí všechna místa na nastaveních lokality, která brána používá, a také zásady protokolu IPsec.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Příklad P2S protokolů.":::

V případě, že se klient připojí přes IKEv2 nebo OpenVPN Point do lokality, bude v tabulce protokolovat aktivitu paketů, konverzace EAP/RADIUS a úspěšné nebo neúspěšné výsledky uživatele.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Příklad protokolů EAP.":::

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci výstrah v protokolech prostředků tunelu najdete v tématu [Nastavení výstrah v VPN Gatewaych protokolech prostředků](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

