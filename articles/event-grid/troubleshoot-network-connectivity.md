---
title: Řešení potíží s připojením k síti – Azure Event Grid | Microsoft Docs
description: Tento článek poskytuje informace o řešení potíží s připojením k síti pomocí Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339741"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Řešení potíží s připojením – Azure Event Grid

K dispozici jsou různé důvody pro klientské aplikace, které se nemohou připojit k Event Gridmu tématu nebo doméně. Problémy s připojením mohou být trvalé nebo přechodné. Pokud se problém projeví i po celou dobu (trvalý), možná budete chtít ověřit nastavení brány firewall vaší organizace, nastavení brány firewall protokolu IP, značky služeb, privátní koncové body a další. V případě přechodných problémů se spouštěním příkazů pro kontrolu zrušených paketů a získání trasování sítě může pomáhat při řešení problémů.

Tento článek poskytuje tipy pro řešení potíží s připojením pomocí Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Řešení potíží s trvalým připojením

Pokud se aplikace nebude moci připojit ke službě Event Grid vůbec, postupujte podle kroků v této části a odstraňte problém.

### <a name="check-if-theres-a-service-outage"></a>Podívejte se, jestli nedochází k výpadku služby.

Podívejte se na výpadek služby Azure Event Grid v [lokalitě stav služby Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Ověřte, jestli nejsou porty vyžadované pro komunikaci s Event Grid blokované bránou firewall organizace.

Ověřte, že porty používané při komunikaci s Azure Event Grid nejsou blokované v bráně firewall vaší organizace. V následující tabulce najdete Odchozí porty, které musíte otevřít ke komunikaci s Azure Event Grid.

| Protokol | Porty |
| -------- | ----- |
| HTTPS    | 443   |

Tady je ukázkový příkaz, který zkontroluje, jestli je port 443 blokovaný.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

V systému Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Ověřte, jestli jsou ve vaší podnikové bráně povolené IP adresy.

Když pracujete s Azure, někdy je potřeba, abyste v podnikové bráně firewall nebo proxy povolili konkrétní rozsahy IP adres nebo adresy URL pro přístup ke všem službám Azure, které používáte nebo se pokoušíte použít. Ověřte, jestli je povolený provoz na IP adresách, které používá Event Grid. Pro IP adresy, které používá Azure Event Grid: Přečtěte si téma [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519) a [značka služby – AzureEventGrid](network-security.md#service-tags).

V dokumentu [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519) uvádí také IP adresy **podle oblasti**. Rozsahy adres pro **oblast tématu** a **spárované oblasti** ve vaší podnikové bráně firewall nebo proxy serveru můžete použít. V případě spárované oblasti pro oblast si přečtěte téma [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Do značky služby AzureEventGrid se přidaly nové IP adresy, i když to není obvyklé. Proto je vhodné provést týdenní kontrolu značek služeb.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Ověřte, že ve skupinách zabezpečení sítě je povolený tag služby AzureEventGrid.

Pokud je vaše aplikace spuštěná v podsíti a má přidruženou skupinu zabezpečení sítě, zkontrolujte, jestli je povolený buď internetový odchozí, nebo je povolená značka služby AzureEventGrid. Zobrazit [značky služeb](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Ověřte nastavení brány firewall protokolu IP pro vaše téma nebo doménu.

Ověřte, že veřejná IP adresa počítače, na kterém je aplikace spuštěná, není blokovaná EventGridým tématem nebo bránou firewall pro IP adresy domény.

Ve výchozím nastavení jsou Event Grid tématy/doménami přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Pravidla brány firewall protokolu IP se používají na úrovni Event Gridho tématu nebo domény. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v Event Gridm tématu nebo doméně, jsou odmítnuté jako zakázané. Odpověď nezmiňuje pravidlo protokolu IP.

Další informace najdete v tématu [Konfigurace pravidel brány firewall protokolu IP pro Azure Event Grid téma/doménu](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Najít IP adresy blokované bránou firewall protokolu IP

Povolit diagnostické protokoly pro Event Grid téma/doména [Povolit diagnostické protokoly](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Zobrazí se IP adresa pro připojení, které bylo odepřeno.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Ověřte, jestli se k tématu nebo doméně EventGrid dá přistup jenom pomocí privátního koncového bodu.

Pokud je v Event Grid téma/doména dostupná jenom přes privátní koncový bod, zkontrolujte, jestli klientská aplikace přistupuje k tématu nebo doméně přes soukromý koncový bod. Pokud ho chcete potvrdit, zkontrolujte, jestli je klientská aplikace spuštěná v podsíti a jestli je v této podsíti privátní koncový bod pro Event Grid téma nebo doménu.

[Služba privátního propojení Azure](../private-link/private-link-overview.md) umožňuje přístup k Azure Event Grid přes **privátní koncový bod** ve vaší virtuální síti. Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [Konfigurace privátních koncových bodů](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Řešení potíží s přechodným připojením

Pokud máte občasné problémy s připojením, přečtěte si následující části Tipy pro řešení potíží.

### <a name="run-the-command-to-check-dropped-packets"></a>Spuštění příkazu pro kontrolu zrušených paketů

Pokud dochází k problémům s přerušovaným připojením, spusťte následující příkaz, který zkontroluje, jestli nedošlo k vyřazeným paketům. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každé 1 sekundy ke službě. Pak můžete zjistit, kolik z nich bylo úspěšné/neúspěšné, a také zobrazit latenci připojení TCP. Nástroj si můžete stáhnout `psping` [tady](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Ekvivalentní příkazy můžete použít, pokud používáte jiné nástroje, například `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Získejte trasování sítě, pokud předchozí kroky neumožňují a neanalyzují ho pomocí nástrojů, jako je třeba [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [Podpora Microsoftu](https://support.microsoft.com/) .

### <a name="service-upgradesrestarts"></a>Upgrady nebo restarty služby

Problémy s přechodným připojením mohou nastat kvůli upgradům a restartům služby back-end. Když k nim dojde, může se zobrazit následující příznaky:

- Je možné, že v příchozích zprávách nebo požadavcích dojde k přerušení.
- Soubor protokolu může obsahovat chybové zprávy.
- Aplikace se můžou po několik sekund odpojit od služby.
- Žádosti můžou být v neomezeném omezení.

Zachycení těchto přechodných chyb, zálohování a následné opakování volání zajistí, že váš kód bude odolný vůči těmto přechodným problémům.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/).