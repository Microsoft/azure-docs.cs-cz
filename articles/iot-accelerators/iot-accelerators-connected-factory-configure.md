---
title: Konfigurace topologie propojené továrny – Azure | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení propojené továrny, včetně jeho topologie.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002234"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurace akcelerátoru řešení propojené továrny

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Akcelerátor řešení propojené továrny zobrazuje simulovaný řídicí panel pro fiktivní společnost Contoso. V této společnosti jsou globální továrny v mnoha globálních umístěních.

V tomto článku se jako příklad používá contoso, který popisuje konfiguraci topologie řešení propojené továrny.

## <a name="simulated-factories-configuration"></a>Konfigurace simulovaných továren

Každý továrna Contoso má výrobní linky, které se skládají ze tří stanic. Každá stanice je skutečným serverem OPC UA s konkrétní rolí:

* Montážní stanice
* Testovací stanice
* Balírna

Tyto servery OPC UA mají uzly OPC UA a [Vydavatel OPC](overview-opc-publisher.md) odesílá hodnoty těchto uzlů do propojené továrny. Sem patří:

* Aktuální provozní stav, jako je aktuální spotřeba energie.
* Produkční informace, jako je počet vyrobených produktů.

Řídicí panel můžete použít k přechodu do topologie továrny contoso z globálního zobrazení na zobrazení na úrovni stanice. Řídicí panel propojené továrny umožňuje:

* Vizualizace hodnot celkové efektivity zařízení a klíčových ukazatelů výkonu pro každou vrstvu v topologii.
* Vizualizace aktuálních hodnot uzlů OPC UA ve stanicích.
* Agregace hodnot celkové efektivity zařízení a klíčového ukazatele výkonu z úrovně stanice na globální úroveň.
* Vizualizace výstrah a akcí, které se mají provést, pokud hodnoty dosáhnou specifických prahových hodnot.

## <a name="connected-factory-topology"></a>Topologie propojené továrny

Topologie továren, výrobních linek a stanic je hierarchická:

* Globální úroveň má uzly továrny jako podřízené objekty.
* Továrny mají jako podřízené uzly výrobní linky.
* Výrobní linky mají uzly stanice jako podřízené položky.
* Stanice (servery OPC UA) mají uzly OPC UA jako podřízené objekty.

Každý uzel v topologii má společnou sadu vlastností, které definují:

* Jedinečný identifikátor uzlu topologie.
* Název.
* Popis.
* Obrázek.
* Podřízené položky uzlu topologie.
* Minimální, cílové a maximální hodnoty pro celkové efektivity zařízení a hodnoty klíčových ukazatelů výkonu a akce výstrah, které mají být provedeny.

## <a name="topology-configuration-file"></a>Konfigurační soubor topologie

K nakonfigurování vlastností uvedených v předchozí části používá řešení připojené továrny konfigurační soubor s názvem [ContosoTopologyDescription.js](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Tento soubor najdete ve zdrojovém kódu řešení ve `WebApp/Contoso/Topology` složce.

Následující fragment kódu ukazuje osnovu `ContosoTopologyDescription.json` konfiguračního souboru:

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

Společné vlastnosti `<global_configuration>` ,, a `<factory_configuration>` `<production_line_configuration>` `<station_configuration>` jsou:

* **Název** (typ řetězce)

  Definuje popisný název, který by měl být pro uzel topologie na řídicím panelu zobrazen pouze jedním slovem.

* **Popis** (typ řetězce)

  Popisuje uzel topologie podrobněji.

* **Image** (typ řetězce)

  Cesta k obrázku v řešení WebApp, která se zobrazí, když se na řídicím panelu zobrazí informace o uzlu topologie.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>` )

  Tyto vlastnosti definují minimální, cílové a maximální hodnoty provozního obrázku používaného ke generování výstrah. Tyto vlastnosti také definují akce, které se mají provést, pokud je zjištěna výstraha.

`<factory_configuration>`Položky a `<production_line_configuration>` mají vlastnost:

* **GUID** (typ řetězce)

  Jednoznačně identifikuje uzel topologie.

`<factory_configuration>` má vlastnost:

* **Umístění** (typ `<location_definition>` )

  Určuje, kde se nachází objekt pro vytváření.

`<station_configuration>` má vlastnosti:

* **OpcUri** (typ řetězce)

  Tato vlastnost musí být nastavená na identifikátor URI aplikace OPC UA serveru OPC UA.
  Vzhledem k tomu, že musí být globálně jedinečný pomocí specifikace OPC UA, tato vlastnost slouží k identifikaci uzlu topologie stanice.

* **OpcNodes**, což jsou pole uzlů OPC UA (typ `<opc_node_description>` )

`<location_definition>` má vlastnosti:

* **City** (typ řetězce)

  Název města nejblíže k umístění

* **Country** (typ řetězce)

  Země umístění

* **Zeměpisná šířka** (typ Double)

  Zeměpisná šířka místa

* **Zeměpisná délka** (typ Double)

  Zeměpisná délka místa

`<performance_definition>` má vlastnosti:

* **Minimum** (typ Double)

  Dolní prahová hodnota, na kterou může tato hodnota dorazit. Pokud je aktuální hodnota pod touto prahovou hodnotou, vygeneruje se výstraha.

* **Cíl** (typ Double)

  Ideální cílová hodnota.

* **Maximum** (typ Double)

  Horní prahová hodnota, kterou může tato hodnota dosáhnout. Pokud je aktuální hodnota nad rámec této prahové hodnoty, vygeneruje se výstraha.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definuje sadu akcí, které lze provést jako odpověď na minimální výstrahu.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definuje sadu akcí, které lze provést jako reakci na maximální výstrahu.

`<alert_action`> má vlastnosti:

* **Typ** (typ řetězce)

  Typ akce výstrahy Jsou známy následující typy:

  * **AcknowledgeAlert**: stav výstrahy by se měl změnit na potvrzeno.
  * **CloseAlert**: na řídicím panelu se už nesmí zobrazovat všechny starší výstrahy stejného typu.
  * **CallOpcMethod**: je třeba volat metodu OPC UA.
  * **OpenWebPage**: mělo by se otevřít okno prohlížeče zobrazující další kontextové informace.

* **Popis** (typ řetězce)

  Popis akce zobrazené na řídicím panelu

* **Parametr** (typ String)

  Parametry vyžadované pro provedení akce Hodnota závisí na typu akce.

  * **AcknowledgeAlert**: není vyžadován žádný parametr.
  * **CloseAlert**: není vyžadován žádný parametr.
  * **CallOpcMethod**: informace o uzlu a parametry metody OPC UA pro volání ve formátu "NodeId nadřazených uzlů", NodeId metody volání, identifikátor URI serveru OPC UA. "
  * **OpenWebPage**: adresa URL, která se má zobrazit v okně prohlížeče.

`<opc_node_description>` obsahuje informace o uzlech OPC UA ve stanici (OPC UA Server). Uzly, které nepředstavují žádné existující uzly OPC UA, ale používají se jako úložiště v logice výpočtu propojené továrny, jsou také platné. Má následující vlastnosti:

* **NodeId** (typ řetězce)

  Adresa uzlu OPC UA v adresním prostoru stanice (OPC UA Server). Syntaxe musí být zadaná ve specifikaci OPC UA pro NodeId.

* **Symbolického** řetězce (typ řetězce)

  Název, který se má zobrazit na řídicím panelu, když se zobrazí hodnota tohoto uzlu OPC UA

* **Relevance** (pole typu String)

  Určuje, pro který výpočet celkové efektivity zařízení nebo klíčový ukazatel výkonu je relevantní hodnota uzlu OPC UA. Každý prvek pole může být jedna z následujících hodnot:

  * **OeeAvailability_Running**: hodnota je relevantní pro výpočet dostupnosti celkové efektivity zařízení.
  * **OeeAvailability_Fault**: hodnota je relevantní pro výpočet dostupnosti celkové efektivity zařízení.
  * **OeePerformance_Ideal**: hodnota je relevantní pro výpočet výkonu celkové efektivity zařízení a obvykle se jedná o konstantní hodnotu.
  * **OeePerformance_Actual**: hodnota je relevantní pro výpočet výkonu celkové efektivity zařízení.
  * **OeeQuality_Good**: hodnota je relevantní pro výpočet kvality celkové efektivity zařízení.
  * **OeeQuality_Bad**: hodnota je relevantní pro výpočet kvality celkové efektivity zařízení.
  * **Kpi1**: hodnota je relevantní pro výpočet Kpi1.
  * **Kpi2**: hodnota je relevantní pro výpočet Kpi2.

* **Opcode** (typ řetězce)

  Určuje, jak je hodnota uzlu OPC UA zpracována v rámci dotazů Time Series Insight a výpočtů celkové efektivity zařízení/KPI. Každý dotaz Time Series Insight se zaměřuje na konkrétní časové rozpětí, což je parametr dotazu a poskytuje výsledek. Operační kód řídí, jak je výsledek vypočítán, a může být jedna z následujících hodnot:

  * **Diff**: rozdíl mezi poslední a první hodnotou v časovém intervalu.
  * **AVG**: průměr všech hodnot v poli TimeSpan.
  * **Sum**: součet všech hodnot v poli TimeSpan.
  * **Poslední**: aktuálně se nepoužívá.
  * **Count**(počet): počet hodnot v časovém rozmezí.
  * **Max**: maximální hodnota v časovém rozmezí.
  * **Min**: minimální hodnota v časovém rozmezí.
  * **Const**: Výsledkem je hodnota zadaná vlastností ConstValue.
  * **SubMaxMin**: rozdíl mezi maximální a minimální hodnotou.
  * **TimeSpan**: TimeSpan.

* **Jednotky** (typ řetězce)

  Definuje jednotku hodnoty, která se má zobrazit na řídicím panelu.

* **Viditelné** (typ Boolean)

  Určuje, zda má být hodnota zobrazena na řídicím panelu.

* **ConstValue** (typ Double)

  Pokud je **operační kód** **const**, pak tato vlastnost je hodnotou uzlu.

* **Minimum** (typ Double)

  Pokud aktuální hodnota klesne pod tuto hodnotu, bude vygenerována minimální výstraha.

* **Maximum** (typ Double)

  Pokud aktuální hodnota vyvolá tuto hodnotu, bude vygenerována maximální výstraha.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definuje sadu akcí, které lze provést jako odpověď na minimální výstrahu.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definuje sadu akcí, které lze provést jako reakci na maximální výstrahu.

Na úrovni stanice vidíte také objekty **simulace** . Tyto objekty slouží pouze ke konfiguraci simulace připojené továrny a neměly by se používat ke konfiguraci reálné topologie.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Jak se používají konfigurační data za běhu

Všechny vlastnosti použité v konfiguračním souboru lze seskupit do různých kategorií v závislosti na tom, jak jsou použity. Tyto kategorie jsou:

### <a name="visual-appearance"></a>Vzhled vizuálu

Vlastnosti v této kategorii definují vizuální vzhled řídicího panelu propojené továrny. Mezi příklady patří:

* Název
* Description
* Image
* Umístění
* Jednotky
* Viditelné

### <a name="internal-topology-tree-addressing"></a>Adresování stromu interní topologie

WebApp udržuje interní datový slovník, který obsahuje informace o všech uzlech topologie. Vlastnosti **GUID** a **OpcUri** se používají jako klíče pro přístup k tomuto slovníku a musí být jedinečné.

### <a name="oeekpi-computation"></a>Výpočet celkové efektivity zařízení/KPI

Hodnoty celkové efektivity zařízení/KPI pro simulaci propojené továrny jsou parametrizované pomocí:

* Hodnoty uzlu OPC UA, které mají být zahrnuty do výpočtu.
* Jak je vypočítána hodnota z hodnot telemetrie.

Propojená továrna používá vzorce celkové efektivity zařízení jako publikované [http://www.oeefoundation.org](http://www.oeefoundation.org) .

Objekty uzlu UA OPC ve stanicích umožňují tagování pro použití při výpočtu celkové efektivity zařízení/KPI. Vlastnost **relevance** určuje, pro který celkové efektivity zařízení/klíčový ukazatel výkonu má být použita hodnota uzlu OPC UA. Vlastnost **opcode** definuje, jak je hodnota obsažena v výpočtu.

### <a name="alert-handling"></a>Zpracování výstrah

Propojená továrna podporuje jednoduchý mechanismus generování výstrah na základě prahové hodnoty minima a maxima. V reakci na tyto výstrahy můžete nakonfigurovat řadu předdefinovaných akcí. Tento mechanismus řídí následující vlastnosti:

* Maximum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korelace s daty telemetrie

Pro určité operace, jako je například vizualizace poslední hodnoty nebo vytváření dotazů služby Time Series Insight, vyžaduje WebApp schéma adresování pro ingestovaná data telemetrie. Telemetrii odesílaná do propojené továrny musí být také uložená ve vnitřních datových strukturách. Dvě vlastnosti, které umožňují tyto operace, jsou na úrovni stanice (Server OPC UA) a na úrovni uzlu OPC UA:

* **OpcUri**

  Identifikuje (globálně jedinečný) Server OPC UA, ze kterého přichází telemetrie. V ingestných zprávách se tato vlastnost pošle jako **ApplicationUri**.

* **NodeId**

  Určuje hodnotu uzlu na serveru OPC UA. Formát vlastnosti musí být zadaný ve specifikaci OPC UA. V ingestných zprávách se tato vlastnost pošle jako **NodeId**.

Další informace o tom, jak se data telemetrie ingestují do propojené továrny, najdete v tématu [co je OPC Publisher](overview-opc-publisher.md) .

## <a name="example-how-kpi1-is-calculated"></a>Příklad: jak se počítá KPI1

Konfigurace v `ContosoTopologyDescription.json` souboru řídí, jak se počítají hodnoty celkové efektivity zařízení/KPI. Následující příklad ukazuje, jak vlastnosti v tomto souboru řídí výpočet KPI1.

V části propojená továrna KPI1 se používá k měření počtu úspěšně vyráběných produktů za poslední hodinu. Každá stanice (Server OPC UA) v simulaci propojené továrny poskytuje uzel OPC UA ( `NodeId: "ns=2;i=385"` ), který poskytuje telemetrii k výpočtu tohoto klíčového ukazatele výkonu.

Konfigurace pro tento uzel OPC UA vypadá jako následující fragment kódu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Tato konfigurace umožňuje dotazování na hodnoty telemetrie tohoto uzlu pomocí Time Series Insights. Dotaz Time Series Insights načte:

* Počet hodnot.
* Minimální hodnota.
* Maximální hodnota.
* Průměr všech hodnot.
* Součet všech hodnot pro všechny jedinečné páry **OpcUri** (**ApplicationUri**), **NodeId** v daném časovém rozpětí.

Jedna z vlastností **NumberOfManufactureredProducts** uzlu znamená, že se zvyšuje jenom. K výpočtu počtu produktů vyrobených v rámci TimeSpan používá propojená továrna SubMaxMin **operačního** **SubMaxMin** systému. Výpočet načte minimální hodnotu na začátku TimeSpan a maximální hodnotu na konci časového rozpětí.

**Operační kód** v konfiguraci konfiguruje logiku výpočtu pro výpočet výsledku rozdílu mezi maximální a minimální hodnotou. Tyto výsledky se pak sčítají až do kořenové (globální) úrovně a zobrazují se na řídicím panelu.

## <a name="next-steps"></a>Další kroky

Navržený další krok se naučíte, jak [přizpůsobit řešení propojené továrny](iot-accelerators-connected-factory-customize.md).
