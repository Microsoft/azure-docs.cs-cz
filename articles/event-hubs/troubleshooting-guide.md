---
title: Řešení potíží s připojením – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o řešení problémů s připojením k Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012676"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Řešení potíží s připojením – Azure Event Hubs
K dispozici jsou různé důvody pro klientské aplikace, které se nemohou připojit k centru událostí. Problémy s připojením mohou být trvalé nebo přechodné. Pokud se problém projeví i po celou dobu (trvalý), možná budete chtít ověřit připojovací řetězec, nastavení brány firewall vaší organizace, nastavení brány firewall protokolu IP, nastavení zabezpečení sítě (koncové body služby, privátní koncové body atd.) a další. Pro přechodné problémy, upgrade na nejnovější verzi sady SDK, spuštění příkazů pro kontrolu zrušených paketů a získání trasování sítě může pomáhat při řešení problémů. 

Tento článek popisuje tipy pro řešení potíží s připojením pomocí Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Řešení potíží s trvalým připojením
Pokud se aplikace nemůže připojit k centru událostí vůbec, postupujte podle kroků v této části a odstraňte problém. 

### <a name="check-if-there-is-a-service-outage"></a>Zkontroluje, jestli nedochází k výpadku služby.
Podívejte se na výpadek služby Azure Event Hubs v [lokalitě stav služby Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Ověření připojovacího řetězce 
Ověřte, zda je připojovací řetězec, který používáte, správný. Viz [získání připojovacího řetězce](event-hubs-get-connection-string.md) pro získání připojovacího řetězce pomocí Azure Portal, CLI nebo PowerShellu. 

U klientů Kafka ověřte, že jsou správně nakonfigurované producer.config nebo consumer.config soubory. Další informace najdete v tématu [posílání a přijímání zpráv pomocí Kafka v Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Ověřte, že ve skupinách zabezpečení sítě je povolený tag služby AzureEventGrid.
Pokud je vaše aplikace spuštěná v podsíti a je přidružená skupina zabezpečení sítě, ověřte, jestli je povolený internetový odchozí nebo že je povolená značka služby AzureEventGrid. Podívejte se na [značky služby virtuální sítě](../virtual-network/service-tags-overview.md) a vyhledejte `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Ověřte, jestli aplikace potřebuje běžet v konkrétní podsíti virtuální sítě.
Potvrďte, že vaše aplikace běží v podsíti virtuální sítě, která má přístup k oboru názvů. Pokud není, spusťte aplikaci v podsíti, která má přístup k oboru názvů, nebo přidejte IP adresu počítače, na kterém je spuštěná aplikace, do [brány firewall protokolu IP](event-hubs-ip-filtering.md). 

Při vytváření koncového bodu služby virtuální sítě pro obor názvů centra událostí obor názvů akceptuje provoz pouze z podsítě, která je svázána s koncovým bodem služby. K tomuto chování existuje výjimka. Můžete přidat konkrétní IP adresy v bráně firewall protokolu IP a povolit tak přístup ke veřejnému koncovému bodu centra událostí. Další informace najdete v tématu [koncové body síťové služby](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Ověřte nastavení brány firewall protokolu IP pro váš obor názvů.
Ověřte, že veřejná IP adresa počítače, na kterém je spuštěná aplikace, není blokovaná bránou firewall protokolu IP.  

Ve výchozím nastavení jsou Event Hubs obory názvů přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů Event Hubs. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Event Hubs, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Další informace najdete v tématu [Konfigurace pravidel brány firewall protokolu IP pro obor názvů Azure Event Hubs](event-hubs-ip-filtering.md). Pokud chcete zjistit, jestli máte problémy s filtrováním IP adres, virtuální sítí nebo řetězem certifikátů, přečtěte si téma [řešení potíží souvisejících se sítí](#troubleshoot-network-related-issues).

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Ověřte, jestli se k oboru názvů dá přistup jenom pomocí privátního koncového bodu.
Pokud je obor názvů Event Hubs nakonfigurovaný jenom přes privátní koncový bod, zkontrolujte, jestli klientská aplikace přistupuje k oboru názvů přes soukromý koncový bod. 

[Služba privátního propojení Azure](../private-link/private-link-overview.md) umožňuje přístup k Azure Event Hubs přes **privátní koncový bod** ve vaší virtuální síti. Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [Konfigurace privátních koncových bodů](private-link-service.md). Podívejte se na část **ověření, jestli připojení privátního koncového bodu funguje** , abyste zkontrolovali, že se používá privátní koncový bod. 

### <a name="troubleshoot-network-related-issues"></a>Řešení potíží souvisejících se sítí
Pokud chcete řešit problémy související se sítí pomocí Event Hubs, postupujte podle těchto kroků: 

Přejděte na nebo [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Pomáhá s kontrolou, jestli máte problémy s filtrováním IP adres nebo virtuální sítí nebo s řetězem certifikátů (nejběžnější při použití sady Java SDK).

Příklad **úspěšné zprávy**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Příklad **chybové zprávy o selhání**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Řešení potíží s přechodným připojením
Pokud se setkáváte s přerušovanými problémy s připojením, přečtěte si následující části, kde najdete tipy k odstraňování potíží. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Použijte nejnovější verzi klientské sady SDK.
Některé problémy s přechodným připojením se mohly vyřešit v novějších verzích sady SDK, než jaké používáte. Ujistěte se, že používáte nejnovější verzi sady SDK klienta v aplikacích. Sady SDK se průběžně zdokonalují pomocí nových/aktualizovaných funkcí a oprav chyb, takže se vždycky testují pomocí nejnovějšího balíčku. V poznámkách k verzi najdete vyřešené problémy a přidané nebo aktualizované funkce. 

Informace o klientských sadách SDK najdete v článku sady [SDK pro Azure Event Hubs-Client](sdks.md) . 

### <a name="run-the-command-to-check-dropped-packets"></a>Spuštění příkazu pro kontrolu zrušených paketů
Pokud dochází k problémům s přerušovaným připojením, spusťte následující příkaz, který zkontroluje, jestli nedošlo k vyřazeným paketům. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každé 1 sekundy ke službě. Pak můžete zjistit, kolik z nich bylo úspěšné/neúspěšné, a také zobrazit latenci připojení TCP. Nástroj si můžete stáhnout `psping` [tady](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Ekvivalentní příkazy můžete použít, pokud používáte jiné nástroje `tnc` , například, `ping` a tak dále. 

Získejte trasování sítě, pokud předchozí kroky neumožňují a neanalyzují ho pomocí nástrojů, jako je třeba [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [Podpora Microsoftu](https://support.microsoft.com/) . 

### <a name="service-upgradesrestarts"></a>Upgrady nebo restarty služby
Problémy s přechodným připojením mohou nastat kvůli upgradům a restartům služby back-end. Když k nim dojde, může se zobrazit následující příznaky: 

- Je možné, že v příchozích zprávách nebo požadavcích dojde k přerušení.
- Soubor protokolu může obsahovat chybové zprávy.
- Aplikace se můžou po několik sekund odpojit od služby.
- Žádosti můžou být v neomezeném omezení.

Pokud kód aplikace využívá sadu SDK, zásady opakování jsou již vytvořeny a aktivní. Aplikace se znovu připojí bez významného dopadu na aplikaci nebo pracovní postup. Zachycení těchto přechodných chyb, zálohování a následné opakování volání zajistí, že váš kód bude odolný vůči těmto přechodným problémům.

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Řešení potíží s ověřováním a autorizací](troubleshoot-authentication-authorization.md)
