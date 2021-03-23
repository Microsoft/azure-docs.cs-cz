---
title: Vyžádat data z Azure Data IoT do ADX
description: V tomto kurzu se naučíte, jak načíst IIoT data do ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787658"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Kurz: získání dat z Azure Data IoT do ADX

Platforma Azure průmyslový IoT (IIoT) kombinuje hraniční moduly a cloudové mikroslužby s řadou služeb Azure PaaS, které poskytují funkce pro průmyslové zjišťování prostředků a shromažďují data z těchto assetů pomocí OPC UA. [Azure Průzkumník dat (ADX)](https://docs.microsoft.com/azure/data-explorer) je přirozený cíl pro IIoT data s funkcemi analýzy dat, které umožňují spouštět flexibilní dotazy na ingestovaná data ze serverů OPC UA připojených k IoT Hub prostřednictvím vydavatele OPC. I když cluster ADX může ingestovat data přímo z IoT Hub, platforma IIoT dále zpracovává data, aby byla lépe užitečná před jejich vložením do centra událostí poskytnutého při použití úplného nasazení mikroslužeb (viz Architektura platformy IIoT).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření tabulky v ADX
> * Připojení centra událostí ke clusteru ADX
> * Analýza dat v ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Jak zpřístupnit data v clusteru ADX pro efektivní dotazování 

Pokud se podíváme na formát zprávy z centra událostí (jak je definováno třídou Microsoft. Azure. IIoT. OpcUa. subscriber. Models. MonitoredItemMessageModel), můžeme zobrazit nápovědu ke struktuře, kterou potřebujeme pro schéma tabulky ADX.

![Struktura](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Níže jsou uvedené kroky, které budeme potřebovat k zpřístupnění dat v clusteru ADX a k efektivnímu dotazování na data.  
1. Vytvořte cluster ADX. Pokud ještě nemáte cluster ADX zřízený s platformou IIoT, nebo pokud chcete použít jiný cluster, postupujte podle kroků uvedených [tady](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Povolte přijímání streamování v clusteru ADX, jak je popsáno [zde](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Vytvořte databázi ADX podle kroků uvedených [tady](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database).

Pro následující krok použijeme [webové rozhraní ADX](https://docs.microsoft.com/azure/data-explorer/web-query-data) ke spuštění nezbytných dotazů. Nezapomeňte přidat svůj cluster do webového rozhraní, jak je vysvětleno v odkazu.  
 
4. Vytvořte tabulku v ADX, do které se umístí ingestovaná data.  I když třídy MonitoredItemMessageModel lze použít k definování schématu tabulky ADX, doporučuje se nejprve ingestovat data do pracovní tabulky s jedním sloupcem typu [Dynamic](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Díky tomu máme větší flexibilitu při zpracování dat a zpracování do dalších tabulek (jejich případné kombinování s ostatními zdroji dat), které slouží k pořizování požadavků pro více případů použití. Následující dotaz ADX vytvoří pracovní tabulku ' iiot_stage ' s jedním sloupcem ' datové části ',

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Také je potřeba přidat mapování ingestování JSON, aby cluster mohl dát celé zprávě JSON z centra událostí do pracovní tabulky.

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Naše tabulka je teď připravená přijímat data z centra událostí. 
6. Pomocí těchto pokynů můžete připojit centrum [událostí ke clusteru](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) ADX a začít ingestovat data do naší pracovní tabulky. Připojení je potřeba vytvořit jenom v případě, že už máme centrum událostí zřízené platformou IIoT.  
7. Po ověření připojení začnou data začít přecházet do naší tabulky a po krátkém zpoždění můžeme začít zkoumat data v naší tabulce. Pomocí následujícího dotazu ve webovém rozhraní ADX se podívejte na vzorek dat o 10 řádcích. Tady vidíte, jak se data v datové části podobají třídě MonitoredItemMessageModel uvedené výše.

![Dotaz](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Teď můžete na těchto datech spustit několik analýz, protože přímo analyzují dynamická data ve sloupci Payload. V tomto příkladu vypočítáme průměr telemetrie identifikovaného "DisplayName": "PositiveTrendData", v průběhu času s časovým obdobím 10 minut, u všech záznamů přijatých od určitého časového bodu (definovaného proměnnou min_t) let min_t = DateTime (2020-10-23); iiot_stage | kde ToDateTime (datová část. Časové razítko) > min_t | kde ToString (datová část. DisplayName) = = ' PositiveTrendData ' | sumarizace event_avg = AVG (ToDouble – (datová část). Value)) po přihrádce (ToDateTime (datová část. Časové razítko), 10 m)
 
Vzhledem k tomu, že sloupec datové části obsahuje dynamický datový typ, musíme v době dotazu provést převod dat, aby se naše výpočty prováděly na správných datových typech.

![Časové razítko datové části](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Jak jsme už uvedli dřív, ingestování dat OPC UA do pracovní tabulky s jedním dynamickým sloupcem poskytuje flexibilitu. Nicméně spuštění převodů datového typu v době dotazu může mít za následek zpoždění při provádění dotazů, zejména pokud je datový svazek velký a pokud existuje mnoho souběžných dotazů. V této fázi můžeme vytvořit další tabulku s již určenými datovými typy, abyste se vyhnuli převodům datových typů v době dotazu.
 
9. Vytvořte novou tabulku pro Analyzovaná data, která se skládají z omezeného výběru z obsahu dynamické datové části v pracovní tabulce. Všimněte si, že jsme vytvořili sloupec hodnoty pro každý z očekávaných datových typů očekávaných v naší telemetrie.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Vytvořte funkci (na úrovni databáze), která bude promítnout požadovaná data z pracovní tabulky. Tady vybereme položky timestamp, ' PublisherId ', ' DisplayName ', ' DataType ' a ' NodeId ' ze sloupce ' Payload ' a projekt jako ' Tag_Timestamp ', ' Tag_PublisherId ', ' tag ', ' Tag_Datatype ', ' Tag_NodeId '. Položka value je na základě typu "DataType" založena na třech různých částech.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Další informace o mapování datových typů v ADX najdete [tady](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)a pro funkce v ADX, které můžete začít [tady](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Použijte funkci z předchozího kroku na analyzovanou tabulku pomocí zásad aktualizace. [Zásada aktualizace zásady](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) ADX automaticky připojí data do cílové tabulky, když se do zdrojové tabulky vloží nová data, a to na základě transformačního dotazu, který běží na datech vložených do zdrojové tabulky. Následující dotaz můžeme použít k přiřazení analyzované tabulky jako cíle a tabulky fáze jako zdroje pro zásady aktualizace definované funkcí, kterou jsme vytvořili v předchozím kroku.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Po provedení výše uvedeného dotazu začnou data začít naplňovat a naplňovat cílovou tabulku ' iiot_parsed '. Data v "iiot_parsed můžeme sledovat takto:".

![Analyzovaná tabulka](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Podíváme se, jak bychom mohli opakovat analýzy, které jsme provedli v předchozím kroku. vypočítá průměr telemetrie identifikovaného "DisplayName": "PositiveTrendData", v průběhu času s časovým obdobím 10 minut, u všech záznamů přijatých od určitého časového bodu (definovaného proměnnou min_t). Protože teď máme hodnoty značky ' PositveTrendData ' uložené ve sloupci Double data Type, očekáváme zlepšení výkonu dotazu.

![Opakování analýzy](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. V obou případech můžeme nakonec porovnat výkon dotazů. Můžeme najít čas potřebný k provedení dotazu pomocí ' stat ' v uživatelském rozhraní ADX (který se může nacházet nad výsledky dotazu).  

![Dotaz na výkon 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Dotaz na výkon 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Vidíte, že dotaz, který používá analyzovanou tabulku, je zhruba dvakrát stejný jako u pracovní tabulky. V tomto příkladu máme malou datovou sadu a neběží žádné souběžné dotazy, takže vliv na dobu provádění dotazu není Skvělé, ale pro realistickou úlohu by to znamenalo velký dopad na výkon. To je důvod, proč je důležité zvážit oddělení různých datových typů do různých sloupců.

> [!NOTE] 
> Zásady aktualizace fungují jenom na datech, která se ingestují do pracovní tabulky po nastavení zásady a která se nevztahují na žádná existující data. To je potřeba vzít v úvahu, když například potřebujeme změnit zásadu aktualizace. Úplné podrobnosti najdete v dokumentaci k ADX.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak změnit výchozí hodnoty konfigurace, můžete 

> [!div class="nextstepaction"]
> [Konfigurace komponent pro průmyslové IoT](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Vizualizace a analýza dat pomocí Time Series Insights](tutorial-visualize-data-time-series-insights.md)