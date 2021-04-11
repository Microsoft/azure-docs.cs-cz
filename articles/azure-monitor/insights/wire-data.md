---
title: Řešení pro přenos dat v Azure Monitor | Microsoft Docs
description: Data o kabelech jsou konsolidovaná síťová a výkonná data z počítačů s agenty Log Analytics. Kvůli pomoci při korelaci dat se síťová data kombinují s daty protokolu.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732019"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Řešení Wire Data 2.0 (Preview) v Azure Monitor (vyřazeno)

![Symbol Wire Data](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>Řešení pro přenos dat se nahradilo pomocí řešení [Cloud Insights](../vm/vminsights-overview.md) a [Service map](../vm/service-map.md).  Pomocí agenta Log Analytics a agenta závislostí se shromažďují data síťového připojení do Azure Monitor.
>
>Podpora řešení pro přenos dat bude ukončena **31. března 2022**.  Až do data vyřazení můžou stávající zákazníci, kteří používají Wire Data 2.0 (Preview), i nadále používat řešení.
>
>Noví a stávající zákazníci by si měli nainstalovat řešení [VM Insights](../vm/vminsights-enable-overview.md) nebo [Service map](../vm/service-map.md).  Datová sada map, kterou shromažďují, je srovnatelná s datovou sadou Wire Data 2.0 (Preview).  Funkce VM Insights zahrnuje sadu dat Service Map spolu s dalšími údaji o výkonu a funkcemi pro analýzu. Obě nabídky mají [připojení s ověřovacími službami Azure](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

Data o kabelech jsou konsolidovaná data o síti a výkonu shromážděná z počítačů připojených k Windows a Linuxem připojeným pomocí agenta Log Analytics, včetně těch, které monitoruje Operations Manager ve vašem prostředí. Kvůli pomoci při korelaci dat se síťová data kombinují s jinými daty protokolu.

Kromě agenta Log Analytics používá řešení pro přenos dat také agenty závislostí od Microsoftu, které nainstalujete do počítačů v infrastruktuře IT. Závislí agenti monitorují síťová data odesílaná do a z počítačů na úrovních sítě 2–3 v [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), a to včetně různých použitých protokolů a portů. Data se pak odesílají Azure Monitor pomocí agentů.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrace na Azure Monitor přehledy virtuálních počítačů nebo Service Map

V mnoha případech vidíme, že zákazníci mají často na jednom virtuálním počítači povolený Wire Data 2.0 (Preview) i řešení  [Cloud Insights](../vm/vminsights-overview.md) nebo [Service map](../vm/service-map.md) .  To znamená, že ve vašem VIRTUÁLNÍm počítači máte povolenou nabídku nahrazení.  [Řešení Wire data 2.0 (Preview) můžete jednoduše odebrat z pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Pokud máte virtuální počítače, na kterých je povolená jenom Wire Data 2.0 (Preview), můžete virtuální počítače připojit k [virtuálnímu počítači](../vm/vminsights-enable-overview.md) nebo [řešení Service map](../vm/service-map.md) a pak [řešení Wire data 2.0 (Preview) odebrat z pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migrace dotazů do tabulky VMConnection z Azure Monitor pro virtuální počítač Insights

### <a name="agents-providing-data"></a>Agenti poskytující data

#### <a name="wire-data-20-query"></a>Wire Data 2.0 dotaz

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>Dotazy k VIRTUÁLNÍm počítačům a Service Map

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>IP adresy agentů poskytujících data

#### <a name="wire-data-20-query"></a>Wire Data 2.0 dotaz

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>Dotazy k VIRTUÁLNÍm počítačům a Service Map

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Veškerá odchozí komunikace podle vzdálené IP adresy

#### <a name="wire-data-20-query"></a>Wire Data 2.0 dotaz

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>Dotazy k VIRTUÁLNÍm počítačům a Service Map

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Počet přijatých bajtů podle názvu protokolu

#### <a name="wire-data-20-query"></a>Wire Data 2.0 dotaz

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>Dotazy k VIRTUÁLNÍm počítačům a Service Map

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Množství síťového provozu (v bajtech) podle procesu

#### <a name="wire-data-20-query"></a>Wire Data 2.0 dotaz

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>Dotazy k VIRTUÁLNÍm počítačům a Service Map

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Další příklady dotazů

Další příklady dotazů najdete v [dokumentaci pro vyhledávání protokolů VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) a v [dokumentaci k virtuálním počítačům s výstrahami](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) .

## <a name="uninstall-wire-data-20-solution"></a>Odinstalace řešení Wire Data 2.0

Pokud chcete odinstalovat Wire Data 2.0 stačí odebrat řešení z Log Analytics pracovních prostorů.  Výsledkem bude následující:

* z virtuálních počítačů, které jsou připojené k pracovnímu prostoru, se odebírá síťový balíček Správa dat. 
* datový typ dat o přenosech dat se už ve vašem pracovním prostoru nezobrazuje.

Pomocí [těchto pokynů](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) odeberte řešení pro přenos dat.

>[!NOTE]
>Pokud máte ve svém pracovním prostoru řešení Service Map nebo VM Insights, Management Pack se neodebere, protože tato řešení také používají tento Management Pack.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 Management packy

Když se řešení Wire Data aktivuje v pracovním prostoru služby Log Analytics, odešle se do všech serverů Windows v tomto pracovním prostoru sada Management Pack o velikosti 300 kB. Pokud používáte agenty nástroje System Center Operations Manager v [připojené skupině pro správu](../agents/om-agents.md), nasadí se sada Management Pack monitoru závislostí z nástroje System Center Operations Manager. Pokud jsou agenti přímo připojeni, Azure Monitor doručí Management Pack.

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Uloží se sem: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Sada Management Pack používá tento zdroj dat: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Odinstalace agenta závislostí

>[!NOTE]
>Pokud plánujete nahradit data ze sítě pomocí Service Map nebo sady VM Insights, neměli byste agenta závislostí odebírat.

Následující části vám pomůžou odebrat agenta závislostí.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalace agenta závislostí v systému Windows

Správce může odinstalovat agenta závislostí pro systém Windows prostřednictvím ovládacích panelů.

Správce může také spustit závislost%Programfiles%\Microsoft Agent\Uninstall.exe pro odinstalaci agenta závislostí.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalace agenta závislostí v systému Linux

Chcete-li úplně odinstalovat agenta závislostí ze systému Linux, je nutné odebrat samotného agenta a konektor, který je nainstalován automaticky s agentem. Obojí najednou odinstalujete následujícím jediným příkazem:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Použití řešení Wire Data 2.0

Na stránce **Přehled** pracovního prostoru služby Log Analytics na Azure Portalu otevřete kliknutím na **Wire Data 2.0** řídicí panel Wire Data. Tento řídicí panel obsahuje okna popsaná v následující tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Kliknutím na **Zobrazit vše** v dolní části okna nebo na záhlaví okna můžete spustit hledání v protokolu, které vrátí všechny záznamy.

| **Okno** | **Popis** |
| --- | --- |
| Agenti zachytávající síťový přenos | Zobrazuje počet agentů, kteří zachytávají síťový provoz, a seznam 10 hlavních počítačů, které zachytávají provoz. Kliknutím na počet můžete v protokolu vyhledat <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kliknutím na počítač v seznamu vyhledáte v protokolu celkový počet zachycených bajtů. |
| Místní podsítě | Zobrazuje počet místních podsítí zjištěných agenty.  Kliknutím na počet vyhledáte v protokolu <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> a zobrazíte seznam všech podsítí s počtem bajtů odeslaných přes každou z nich. Kliknutím na podsíť v seznamu vyhledáte v protokolu celkový počet bajtů odeslaných přes tuto podsíť. |
| Protokoly na úrovni aplikace | Zobrazuje počet používaných protokolů na úrovni aplikace zjištěných agenty. Kliknutím na počet můžete v protokolu vyhledat <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kliknutím na protokol vyhledáte v protokolu celkový počet bajtů odeslaných pomocí tohoto protokolu. |

![Řídicí panel Wire Data](./media/wire-data/wire-data-dash.png)

V okně **Agenti zachytávající síťový přenos** můžete zjistit, kolik šířky pásma sítě spotřebovávají jednotlivé počítače. Toto okno vám pomůže snadno najít počítač s _největší komunikací_ ve vašem prostředí. Takové počítače mohou být přetížené, chovat se abnormálně nebo používat více síťových prostředků než normálně.

![Snímek obrazovky okna agenti zachytávající síťový provoz na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná jednotlivými počítači.](./media/wire-data/log-search-example01.png)

V okně **Místní podsítě** můžete podobně zjistit, jak velký síťový provoz probíhá přes vaše podsítě. Uživatelé často definují podsítě pro kritické oblasti svých aplikací. Toto okno vám umožní tyto oblasti zobrazit.

![Snímek obrazovky okna místní podsítě na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná jednotlivými LocalSubnet.](./media/wire-data/log-search-example02.png)

V okně **Protokoly na úrovni aplikace** můžete zjistit, jaké protokoly se používají. Můžete například očekávat, že se ve vašem síťovém prostředí nepoužívá protokol SSH. Prohlédnutím informací dostupných v tomto okně můžete tento předpoklad rychle potvrdit nebo vyvrátit.

![Snímek obrazovky okna protokoly na úrovni aplikace na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná každým protokolem.](./media/wire-data/log-search-example03.png)

Je také užitečné vědět, jestli se provoz přes protokol časem zvyšuje nebo snižuje. Pokud se například množství dat přenášených nějakou aplikací zvyšuje, může se jednat o něco, o čem byste měli vědět nebo co může stát za povšimnutí.

## <a name="input-data"></a>Vstupní data

Řešení Wire Data shromažďuje metadata o síťovém provozu pomocí agentů, které jste povolili. Každý agent odesílá data zhruba po 15 sekundách.

## <a name="output-data"></a>Výstupní data

Pro každý typ vstupních dat se vytvoří záznam typu _WireData_. Záznamy WireData mají vlastnosti uvedené v následující tabulce:

| Vlastnost | Popis |
|---|---|
| Počítač | Název počítače, na kterém byla data shromážděna |
| TimeGenerated | Čas záznamu |
| LocalIP | IP adresa místního počítače |
| SessionState | Připojeno nebo Odpojeno |
| ReceivedBytes | Množství přijatých bajtů |
| ProtocolName | Název použitého síťového protokolu |
| IPVersion | Verze protokolu IP |
| Směr | Příchozí nebo odchozí |
| MaliciousIP | IP adresa známého škodlivého zdroje |
| Závažnost | Závažnost podezřelého malwaru |
| RemoteIPCountry | Země nebo oblast vzdálené IP adresy |
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager |
| SourceSystem | Zdroj, kde byla data shromážděna |
| SessionStartTime | Čas zahájení relace |
| SessionEndTime | Čas ukončení relace |
| LocalSubnet | Podsíť, kde byla data shromážděna |
| LocalPortNumber | Číslo místního portu |
| RemoteIP | Vzdálená IP adresa použitá vzdáleným počítačem |
| RemotePortNumber | Číslo portu použité vzdálenou IP adresou |
| SessionID | Jedinečná hodnota, která identifikuje komunikační relaci mezi dvěma IP adresami |
| SentBytes | Počet odeslaných bajtů |
| TotalBytes | Celkový počet bajtů odeslaných během relace |
| ApplicationProtocol | Typ použitého síťového protokolu   |
| ProcessID | ID procesu Windows |
| ProcessName | Cesta a název souboru procesu |
| RemoteIPLongitude | Zeměpisná délka IP adresy |
| RemoteIPLatitude | Zeměpisná šířka IP adresy |

## <a name="next-steps"></a>Další kroky

- Další požadavky a metody, které umožňují monitorovat virtuální počítače, najdete v tématu [nasazení přehledů virtuálních počítačů](./vminsights-enable-overview.md) .
- Podrobné záznamy hledání dat kabelové sítě zobrazíte [prohledáním protokolů](../logs/log-query-overview.md).
