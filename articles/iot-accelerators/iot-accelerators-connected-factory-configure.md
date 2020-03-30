---
title: Konfigurace topologie připojené továrny – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení připojené továrny včetně jeho topologie.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820116"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurace akcelerátoru řešení připojené továrny

Akcelerátor řešení Connected Factory zobrazuje simulovaný řídicí panel fiktivní společnosti Contoso. Tato společnost má továrny v mnoha globálních lokalitách po celém světě.

Tento článek používá Contoso jako příklad k popisu, jak nakonfigurovat topologii řešení připojené továrny.

## <a name="simulated-factories-configuration"></a>Konfigurace simulovaných továren

Každá továrna Contoso má výrobní linky, které se skládají ze tří stanic. Každá stanice je skutečný OPC UA server se specifickou rolí:

* Montážní stanice
* Zkušební stanice
* Balicí stanice

Tyto Servery OPC UA mají uzly OPC UA a [Vydavatel OPC](overview-opc-publisher.md) odesílá hodnoty těchto uzlů do připojené továrny. To zahrnuje:

* Aktuální provozní stav, například aktuální spotřeba energie.
* Informace o výrobě, jako je počet vyrobených výrobků.

Řídicí panel můžete použít k přechodu do topologie továrny Contoso z globálního pohledu dolů do zobrazení na úrovni stanice. Řídicí panel Connected Factory umožňuje:

* Vizualizace hodnot OEE a Klíčových ukazatelů výkonu pro každou vrstvu v topologii.
* Vizualizace aktuálních hodnot UA uzlů OPC ve stanicích.
* Agregace údajů o OEE a klíčových ukazatelích výkonnosti od úrovně stanice ke globální úrovni.
* Vizualizace výstrah a akcí, které mají být v případě, že hodnoty dosáhnou určitých prahových hodnot, jsou stanoveny.

## <a name="connected-factory-topology"></a>Připojená tologie továrny

Topologie továren, výrobních linek a stanic je hierarchická:

* Globální úroveň má tovární uzly jako podřízené.
* Továrny mají uzly výrobní linky jako podřízené položky.
* Výrobní linky mají jako podřízené uzly stanice.
* Stanice (servery OPC UA) mají uzly OPC UA jako děti.

Každý uzel v topologii má společnou sadu vlastností, které definují:

* Jedinečný identifikátor pro uzel topologie.
* Jméno.
* Popis.
* Obrázek.
* Podřízené topologie uzlu.
* Minimální, cílové a maximální hodnoty pro údaje OEE a klíčových ukazatelů výkonu a výstražné akce, které mají být provedeny.

## <a name="topology-configuration-file"></a>Konfigurační soubor topologie

Chcete-li nakonfigurovat vlastnosti uvedené v předchozí části, používá řešení Connected Factory konfigurační soubor s názvem [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Tento soubor najdete ve zdrojovém `WebApp/Contoso/Topology` kódu řešení ve složce.

Následující úryvek zobrazuje obrys `ContosoTopologyDescription.json` konfiguračního souboru:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Společné vlastnosti `<global_configuration>` `<factory_configuration>`rozhraní `<production_line_configuration>`, `<station_configuration>` , , a jsou:

* **Název** (typový řetězec)

  Definuje popisný název, který by měl být pouze jedno slovo pro topologie uzlu zobrazit na řídicím panelu.

* **Popis** (typový řetězec)

  Popisuje uzel topologie podrobněji.

* **Obrázek** (typový řetězec)

  Cesta k obrázku v řešení WebApp zobrazí, když jsou na řídicím panelu zobrazeny informace o uzlu topologie.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ) `<performance_definition>`

  Tyto vlastnosti definují minimální, cílové a maximální hodnoty provozního čísla použitého ke generování výstrah. Tyto vlastnosti také definují akce, které mají být provedeny, pokud je zjištěna výstraha.

A `<factory_configuration>` `<production_line_configuration>` položky mají vlastnost:

* **Guid** (typový řetězec)

  Jednoznačně identifikuje uzel topologie.

`<factory_configuration>`má vlastnost:

* **Umístění** (typ) `<location_definition>`

  Určuje, kde se nachází továrna.

`<station_configuration>`má vlastnosti:

* **OpcUri** (typový řetězec)

  Tato vlastnost musí být nastavena na identifikátor URI aplikace OPC UA serveru OPC UA.
  Vzhledem k tomu, že musí být globálně jedinečný specifikací OPC UA, tato vlastnost se používá k identifikaci uzlu topologie stanice.

* **OpcNodes**, které jsou polem uzlů OPC `<opc_node_description>`UA (typ)

`<location_definition>`má vlastnosti:

* **Město** (typový řetězec)

  Název města nejblíže k lokalitě

* **Země** (řetězec typu)

  Země lokality

* **Zeměpisná šířka** (typ double)

  Zeměpisná šířka místa

* **Zeměpisná a diatova** (typ dvojitá)

  Zeměpisná poloha

`<performance_definition>`má vlastnosti:

* **Minimální** (typ double)

  Nižší prahová hodnota může dosáhnout. Pokud je aktuální hodnota pod touto prahovou hodnotou, vygeneruje se výstraha.

* **Cíl** (typ double)

  Ideální cílová hodnota.

* **Maximum** (typ double)

  Horní prahová hodnota může dosáhnout. Pokud je aktuální hodnota vyšší než tato prahová hodnota, vygeneruje se výstraha.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definuje sadu akcí, které lze přijmout jako odpověď na minimální výstrahu.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definuje sadu akcí, které lze přijmout jako odpověď na maximální výstrahu.

`<alert_action`> má vlastnosti:

* **Typ** (řetězec typu)

  Typ výstražné akce. Následující typy jsou známy:

  * **AcknowledgeAlert**: stav výstrahy by se měl změnit na potvrzenou.
  * **CloseAlert**: všechny starší výstrahy stejného typu by již neměly být zobrazeny na řídicím panelu.
  * **CallOpcMethod**: metoda OPC UA by měla být volána.
  * **OpenWebPage**: okno prohlížeče by mělo být otevřeno s dalšími kontextové informace.

* **Popis** (typový řetězec)

  Popis akce zobrazené na řídicím panelu.

* **Parametr** (typový řetězec)

  Parametry potřebné k provedení akce. Hodnota závisí na typu akce.

  * **AcknowledgeAlert**: není nutný žádný parametr.
  * **CloseAlert**: není nutný žádný parametr.
  * **CallOpcMethod**: informace o uzlu a parametry metody OPC UA pro volání ve formátu "NodeId nadřazeného uzlu, NodeId metody pro volání, URI serveru OPC UA."
  * **OpenWebPage**: ADRESA URL, která se zobrazí v okně prohlížeče.

`<opc_node_description>`obsahuje informace o uzlech OPC UA ve stanici (OPC UA server). Uzly, které nepředstavují žádné existující uzly OPC UA, ale používají se jako úložiště ve výpočetní logice připojené továrny, jsou také platné. Má následující vlastnosti:

* **NodeId** (řetězec typu)

  Adresa uzlu OPC UA v adresním prostoru stanice (OPC UA server). Syntaxe musí být tak, jak je uvedeno ve specifikaci OPC UA pro NodeId.

* **Symbolický název** (řetězec typu)

  Název, který se zobrazí na řídicím panelu, když je zobrazena hodnota tohoto uzlu OPC UA.

* **Relevance** (pole typu řetězce)

  Označuje, pro který výpočt OEE nebo Klíčový ukazatel výkonu hodnota uzlu OPC UA je relevantní. Každý prvek pole může být jedna z následujících hodnot:

  * **OeeAvailability_Running**: hodnota je relevantní pro výpočet dostupnosti OEE.
  * **OeeAvailability_Fault**: hodnota je relevantní pro výpočet dostupnosti OEE.
  * **OeePerformance_Ideal**: hodnota je relevantní pro výpočet výkonu OEE a je obvykle konstantní hodnota.
  * **OeePerformance_Actual**: hodnota je relevantní pro výpočet výkonnosti OEE.
  * **OeeQuality_Good**: hodnota je relevantní pro výpočet kvality OEE.
  * **OeeQuality_Bad**: hodnota je relevantní pro výpočet kvality OEE.
  * **Kpi1**: hodnota je relevantní pro výpočet klíčového ukazatele výkonu1.
  * **Kpi2**: hodnota je relevantní pro výpočet klíčového ukazatele výkonu2.

* **OpCode** (typový řetězec)

  Označuje, jak je hodnota uzlu OPC UA zpracována v dotazech Time Series Insight a výpočtech OEE/KPI. Každý časový rozsah Insight dotaz cíle konkrétní časový rozsah, což je parametr dotazu a poskytuje výsledek. OpCode určuje, jak je vypočítán výsledek a může být jedna z následujících hodnot:

  * **Rozdíl**: rozdíl mezi poslední a první hodnotou v období času.
  * **Průměr**všech hodnot v čase.
  * **Součet**: součet všech hodnot v období času.
  * **Poslední**: aktuálně nepoužívá.
  * **Počet**: počet hodnot v období času.
  * **Max**: maximální hodnota v čase.
  * **Min**: minimální hodnota v časovým rozpětí.
  * **Const**: Výsledkem je hodnota určená vlastností ConstValue.
  * **SubMaxMin**: rozdíl mezi maximální a minimální hodnotou.
  * **Časový rozsah**: časový rozsah.

* **Jednotky** (typový řetězec)

  Definuje jednotku hodnoty pro zobrazení na řídicím panelu.

* **Viditelné** (typ logické)

  Určuje, zda má být hodnota zobrazena na řídicím panelu.

* **ConstValue** (typ double)

  Pokud **OpCode** je **Const**, pak tato vlastnost je hodnota uzlu.

* **Minimální** (typ double)

  Pokud aktuální hodnota klesne pod tuto hodnotu, vygeneruje se minimální výstraha.

* **Maximum** (typ double)

  Pokud aktuální hodnota zvýší nad tuto hodnotu, vygeneruje se maximální výstraha.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definuje sadu akcí, které lze přijmout jako odpověď na minimální výstrahu.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definuje sadu akcí, které lze přijmout jako odpověď na maximální výstrahu.

Na úrovni stanice se zobrazí také **objekty simulace.** Tyto objekty se používají pouze ke konfiguraci simulace připojené továrny a neměly by být použity ke konfiguraci skutečné topologie.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Jak se konfigurační data používají za běhu

Všechny vlastnosti použité v konfiguračním souboru lze seskupit do různých kategorií v závislosti na tom, jak jsou používány. Jedná se o tyto kategorie:

### <a name="visual-appearance"></a>Vizuální vzhled

Vlastnosti v této kategorii definují vizuální vzhled řídicího panelu Připojené továrny. Příklady obsahují:

* Name (Název)
* Popis
* Image
* Umístění
* Jednotky
* Viditelné

### <a name="internal-topology-tree-addressing"></a>Interní adresování stromu topologie

WebApp udržuje interní datový slovník obsahující informace o všech uzlech topologie. Vlastnosti **Guid** a **OpcUri** se používají jako klíče pro přístup k tomuto slovníku a musí být jedinečné.

### <a name="oeekpi-computation"></a>Výpočty OEE/KPI

Hodnoty OEE/KPI pro simulaci připojené továrny jsou parametrizovány:

* Hodnoty uzlu OPC UA, které mají být zahrnuty do výpočtu.
* Jak se počítá obrázek z telemetrických hodnot.

Připojená továrna používá vzorce OEE [http://www.oeefoundation.org](http://www.oeefoundation.org)publikované v .

Objekty uzlů OPC UA ve stanicích umožňují označování pro použití ve výpočtu OEE/KPI. **Relevance** Vlastnost označuje, pro které OEE/KPI obrázek Hodnota uzlu OPC UA by měla být použita. **Vlastnost OpCode** definuje, jak je hodnota zahrnuta do výpočtu.

### <a name="alert-handling"></a>Zpracování výstrah

Connected Factory podporuje jednoduchý mechanismus generování výstrah na základě minimální/maximální prahové hodnoty. Existuje celá řada předdefinovaných akcí, které můžete nakonfigurovat v reakci na tyto výstrahy. Tento mechanismus řídí následující vlastnosti:

* Maximum
* Minimální
* Maximální akce výstrah
* Minimální akce výstrah

## <a name="correlating-to-telemetry-data"></a>Koreláce s telemetrickými daty

Pro určité operace, jako je vizualizace poslední hodnoty nebo vytváření dotazů Time Series Insight, WebApp potřebuje adresování schéma pro ingestovaná data telemetrie. Telemetrie odeslaná do připojené továrny také musí být uložena v interních datových strukturách. Dvě vlastnosti, které umožňují tyto operace, jsou na úrovni stanice (Server OPC UA) a OPC UA:

* **OpcUri**

  Identifikuje (globálně jedinečný) server OPC UA, ze kterých telemetrie pochází. V přijatých zprávách je tato vlastnost odeslána jako **ApplicationUri**.

* **NodeId**

  Identifikuje hodnotu uzlu na serveru OPC UA. Formát vlastnosti musí být uveden ve specifikaci OPC UA. V přijatých zprávách je tato vlastnost odeslána jako **NodeId**.

Další informace o tom, jak se telemetrická data ingestují do připojené továrny, najdete v tématu [Co je vydavatel OPC.](overview-opc-publisher.md)

## <a name="example-how-kpi1-is-calculated"></a>Příklad: Způsob výpočtu klíčového ukazatele výkonu1

Konfigurace v `ContosoTopologyDescription.json` souboru určuje způsob výpočtu údajů o ukazatelích výkonu OEE/Klíčových ukazatelů výkonu. Následující příklad ukazuje, jak vlastnosti v tomto souboru řídí výpočtu klíčového ukazatele výkonu1.

V připojené továrně KPI1 se používá k měření počtu úspěšně vyrobených výrobků za poslední hodinu. Každá stanice (Server OPC UA) v simulaci Připojené továrny`NodeId: "ns=2;i=385"`poskytuje uzel OPC UA ( ), který poskytuje telemetrii pro výpočet tohoto klíčového ukazatele výkonu.

Konfigurace tohoto uzlu OPC UA vypadá jako následující úryvek:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Tato konfigurace umožňuje dotazování telemetrie hodnoty tohoto uzlu pomocí Časové řady Insights. Dotaz Time Series Insights načítá:

* Počet hodnot.
* Minimální hodnota.
* Maximální hodnota.
* Průměr všech hodnot.
* Součet všech hodnot pro všechny jedinečné **OpcUri** (**ApplicationUri**), **NodeId** dvojice v daném časovým rozpětí.

Jednou z charakteristik **numberOfManufactureredProducts** hodnota uzlu je, že pouze zvyšuje. Chcete-li vypočítat počet produktů vyrobených v období časů, připojená továrna používá **OpCode** **SubMaxMin**. Výpočet načte minimální hodnotu na začátku časový rozsah a maximální hodnotu na konci časový rozsah.

**OpCode** v konfiguraci konfiguruje výpočetní logiku pro výpočet výsledku rozdílu maximální a minimální hodnoty. Tyto výsledky jsou pak akumulovány zdola nahoru na kořenovou (globální) úroveň a zobrazeny na řídicím panelu.

## <a name="next-steps"></a>Další kroky

Dalším krokem je naučit se [přizpůsobit řešení připojené továrny](iot-accelerators-connected-factory-customize.md).
