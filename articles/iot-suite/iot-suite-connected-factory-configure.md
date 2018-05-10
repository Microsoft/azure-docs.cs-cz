---
title: Konfigurace topologie připojení Factory | Microsoft Docs
description: Postup konfigurace topologii akcelerátor připojen objekt pro vytváření řešení.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 4230914c6fb35201a8c162e2e7ecb31262d2bdca
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurace akcelerátoru řešení připojen objekt pro vytváření

Akcelerátor připojen objekt pro vytváření řešení zobrazuje simulované řídicí panel pro fiktivní společnost Contoso. Tuto společnost má objekty Factory globálně v mnoha globální umístění.

Tento článek používá Contoso jako příklad k popisu konfiguraci topologie připojen objekt pro vytváření řešení.

## <a name="simulated-factories-configuration"></a>Konfigurace simulované objekty Factory

Každý objekt pro vytváření Contoso má produkční řádky, které se skládají ze tří stanice. Každá stanice je skutečné OPC UA server s konkrétní rolí:

* Stanice sestavení
* Test stanice
* Balení stanice

Tyto servery OPC UA mít OPC UA uzly a [OPC vydavatele](https://github.com/Azure/iot-edge-opc-publisher) odešle hodnoty tyto uzly k vytváření připojení. To zahrnuje následující:

* Aktuální operační stav například aktuální spotřeby energie.
* Provozní informace, jako je počet produktů, vytváří.

Řídicí panel můžete přejít k podrobnostem topologie factory Contoso z globální zobrazení na úrovni zobrazení stanice. Řídicí panel umožňuje vytváření připojení:

* Vizualizace OEE a klíčového ukazatele výkonu obrázků pro jednotlivé úrovně v topologii.
* Vizualizace aktuální hodnoty OPC UA uzlů v stanice.
* Agregace OEE a klíčového ukazatele výkonu údaje z úrovně stanice na globální úrovni.
* Vizualizace výstrah a akce lze provádět, pokud hodnoty dosáhnout specifické prahové hodnoty.

## <a name="connected-factory-topology"></a>Připojené objekt pro vytváření topologie

Tato topologie objekty Factory, řádky výroby a stanice je hierarchická:

* Globální úrovni má objekt factory uzly jako podřízené objekty.
* Objekty Factory mít výrobní uzly jako podřízené objekty.
* Řádky výrobní mít stanice uzly jako podřízené objekty.
* Stanice (OPC UA servery) mít OPC UA uzly jako podřízené objekty.

Každý uzel v topologii má společnou sadu vlastností, které definují:

* Jedinečný identifikátor pro uzel topologie.
* Název.
* Popis.
* Bitovou kopii.
* Podřízené objekty daného uzlu topologie.
* Minimální, cíl a maximální hodnoty pro obrázky OEE a klíčových ukazatelů výkonu a výstrahy akce provést.

## <a name="topology-configuration-file"></a>Topologie konfiguračního souboru

Konfigurovat vlastnosti uvedené v předchozí části, připojen objekt pro vytváření řešení používá konfigurační soubor s názvem [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Tento soubor můžete najít ve zdrojovém kódu řešení v `WebApp/Contoso/Topology` složky.

Následující fragment kódu ukazuje přehled `ContosoTopologyDescription.json` konfigurační soubor:

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

Běžné vlastnosti `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, a `<station_configuration>` jsou:

* **Název** (zadejte řetězec)

  Určuje popisný název, které by měly být pouze jednoho slova pro uzel topologie pro zobrazení v řídicím panelu.

* **Popis** (zadejte řetězec)

  Popisuje uzlu topologie podrobněji.

* **Obrázek** (zadejte řetězec)

  Cesta k obrázku v řešení WebApp zobrazíte, když se zobrazí informace o topologii uzlu v řídicím panelu.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  Tyto vlastnosti definovat minimální, cíl a maximální hodnoty provozní obrázek sloužící ke generování výstrahy. Tyto vlastnosti také definovat akce, které chcete spustit, pokud je výstraha zjištěna.

`<factory_configuration>` a `<production_line_configuration>` položky mají vlastnost:

* **Identifikátor GUID** (zadejte řetězec)

  Jednoznačně identifikuje uzlu topologie.

`<factory_configuration>` má vlastnost:

* **Umístění** (typ `<location_definition>`)

  Určuje, kde se nachází objekt pro vytváření.

`<station_configuration>` má vlastnosti:

* **OpcUri** (zadejte řetězec)

  Tato vlastnost musí být nastavená na URI OPC UA aplikace OPC UA serveru.
  Protože musí být globálně jedinečný specifikací OPC UA, tato vlastnost slouží k identifikaci uzlu topologie stanice.

* **OpcNodes**, což jsou pole OPC UA uzlů (typ `<opc_node_description>`)

`<location_definition>` má vlastnosti:

* **Města** (zadejte řetězec)

  Název města nejblíže k umístění

* **Země** (zadejte řetězec)

  Země umístění

* **Zeměpisná šířka** (double – typ)

  Zeměpisná šířka umístění

* **Zeměpisná délka** (double – typ)

  Zeměpisná délka umístění

`<performance_definition>` má vlastnosti:

* **Minimální** (double – typ)

  Prahová hodnota nižší hodnota dosáhnout. Pokud je aktuální hodnota je menší než tato prahová hodnota, je vygenerována výstraha.

* **Cíl** (double – typ)

  Ideální cílovou hodnotu.

* **Maximální** (double – typ)

  Vyšší prahová hodnota dosáhnout. Pokud je aktuální hodnota je vyšší než tato prahová hodnota, je vygenerována výstraha.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které můžete provést jako odpověď na minimální výstrahy.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které můžete provést jako odpověď na maximální výstrahy.

`<alert_action`> má vlastnosti:

* **Typ** (zadejte řetězec)

  Typ výstrahy akce. Jsou známé následující typy:

  * **AcknowledgeAlert**: stav výstrahy by se měl změnit na potvrzené.
  * **CloseAlert**: všechny výstrahy starší stejného typu už se mají na řídicím panelu.
  * **CallOpcMethod**: metodu OPC UA by měla být volána.
  * **OpenWebPage**: okno prohlížeče, musí být otevřen zobrazující další kontextové informace.

* **Popis** (zadejte řetězec)

  Popis akce na řídicím panelu.

* **Parametr** (zadejte řetězec)

  Parametry ke spuštění akce. Hodnota závisí na typ akce.

  * **AcknowledgeAlert**: žádný parametr požadované.
  * **CloseAlert**: žádný parametr požadované.
  * **CallOpcMethod**: uzel informace a parametry metody OPC UA volat ve formátu "NodeId nadřazeného uzlu, NodeId metoda k volání, identifikátor URI serveru OPC UA."
  * **OpenWebPage**: adresa URL, které se zobrazí v okně prohlížeče.

`<opc_node_description>` obsahuje informace o OPC UA uzly v stanice (OPC UA server). Uzly, které představují žádné existující OPC UA uzly, ale slouží jako úložiště v logice výpočetní objektu pro vytváření připojení jsou také platné. Má následující vlastnosti:

* **NodeId** (zadejte řetězec)

  Adresa OPC UA uzlu (OPC UA serveru) stanice na adresní prostor. Musí být syntaxe uvedené ve specifikaci OPC UA pro NodeId.

* **Symbolickou** (zadejte řetězec)

  Název zobrazený v řídicím panelu, když je hodnota tohoto uzlu OPC UA zobrazena.

* **Relevance** (pole typu řetězec)

  Označuje, pro které je výpočet OEE nebo klíčového ukazatele výkonu hodnota uzlu OPC UA relevantní. Každý element pole může být jedna z následujících hodnot:

  * **OeeAvailability_Running**: hodnota je relevantní pro výpočet dostupnosti OEE.
  * **OeeAvailability_Fault**: hodnota je relevantní pro výpočet dostupnosti OEE.
  * **OeePerformance_Ideal**: hodnota je relevantní pro výpočet OEE výkonu a je obvykle konstantní hodnotu.
  * **OeePerformance_Actual**: hodnota je relevantní pro výpočet OEE výkonu.
  * **OeeQuality_Good**: hodnota je relevantní pro výpočet OEE kvality.
  * **OeeQuality_Bad**: hodnota je relevantní pro výpočet OEE kvality.
  * **Kpi1**: hodnota je relevantní pro výpočet KPI1.
  * **Kpi2**: hodnota je relevantní pro výpočet KPI2.

* **Operační kód** (zadejte řetězec)

  Určuje, jak zpracovává hodnotu uzlu OPC UA v dotazech časové řady přehledy a výpočty OEE/klíčového ukazatele výkonu. Každý dotaz časové řady přehledy cílí na konkrétní časové rozpětí, která je parametr dotazu a doručí výsledku. OpCode řídí, jak výsledek je počítaný a může být jedna z následujících hodnot:

  * **Diff**: rozdíl mezi poslední a první hodnotu v časový interval.
  * **Průměr**: průměr všech hodnot v časový interval.
  * **Součet**: součet všech hodnot v časový interval.
  * **Poslední**: aktuálně nepoužívá.
  * **Počet**: počet hodnot v časový interval.
  * **Maximální počet**: maximální hodnota v časový interval.
  * **Minimální**: minimální hodnota v časový interval.
  * **Const**: Výsledkem je hodnota zadaná ve vlastnosti ConstValue.
  * **SubMaxMin**: rozdíl mezi maximální a minimální hodnota.
  * **Časový interval**: časový interval.

* **Jednotky** (zadejte řetězec)

  Definuje jednotku hodnoty pro zobrazení v řídicím panelu.

* **Viditelné** (typu logická hodnota)

  Ovládací prvky, pokud hodnota se mají zobrazit v řídicím panelu.

* **ConstValue** (double – typ)

  Pokud **OpCode** je **Const**, pak tato vlastnost je hodnota uzlu.

* **Minimální** (double – typ)

  Pokud aktuální hodnota klesne pod tuto hodnotu, je generována výstraha o minimální.

* **Maximální** (double – typ)

  Pokud je aktuální hodnota vyvolá nad tuto hodnotu, pak maximální výstraha se vygeneruje.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které můžete provést jako odpověď na minimální výstrahy.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které můžete provést jako odpověď na maximální výstrahy.

Na úrovni stanice, můžete také zjistit **simulace** objekty. Tyto objekty se používají pouze ke konfiguraci simulace připojen objekt pro vytváření a neměl by sloužit ke konfiguraci skutečné topologie.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Použití konfiguračních dat za běhu

Všechny vlastnosti používané v konfiguračním souboru je možné seskupit do různých kategorií v závislosti na tom, jak se používají. Jsou těchto kategorií:

### <a name="visual-appearance"></a>Vzhled

Vlastnosti v této kategorii definujte vzhled připojen objekt pro vytváření řídicího panelu. Příklady obsahují:

* Název
* Popis
* Image
* Umístění
* Jednotky
* Viditelný

### <a name="internal-topology-tree-addressing"></a>Adresování stromu interní topologie

WebApp udržuje slovníku interních datových obsahujícího informace o všech uzlů topologie. Vlastnosti **Guid** a **OpcUri** slouží jako klíče pro přístup k tohoto slovníku a musí být jedinečné.

### <a name="oeekpi-computation"></a>Výpočet OEE/klíčového ukazatele výkonu

Údaje OEE nebo klíčový ukazatel výkonu pro simulaci připojen objekt pro vytváření jsou parametry podle:

* Hodnoty uzlu OPC UA mají být zahrnuty do výpočtu.
* Jak na obrázku se počítá z hodnot telemetrie.

Připojené Factory používá vzorce OEE jako publikováno http://oeeindustrystandard.oeefoundation.org.

OPC UA uzlu objekty v stanice povolit označování pro použití v výpočet OEE/klíčového ukazatele výkonu. **Relevance** vlastnost určuje, pro které obrázek OEE nebo klíčový ukazatel výkonu je třeba použít hodnota OPC UA uzlu. **OpCode** vlastnost definuje, jak je hodnota součástí výpočet.

### <a name="alert-handling"></a>Výstrahy zpracování

Připojené Factory podporuje mechanismus jednoduché minima a maxima na základě prahové hodnoty generování výstrah. Existuje několik předdefinovaných akcí, které můžete konfigurovat v reakci na tyto výstrahy. Následující vlastnosti řídit tento mechanismus:

* Maximum
* Minimální
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korelace na telemetrická data

Pro některé operace, například vizualizace poslední hodnotu nebo vytváření dotazů přehledy časové řady WebApp požadavkům adresovacímu schématu ingestovaný telemetrická data. Telemetrická data odesílaná do připojené Factory také musí být uložen v interních datových strukturách. Dvě vlastnosti povolení tyto operace jsou na stanici (OPC UA server) a na úrovni OPC UA uzlu:

* **OpcUri**

  Identifikuje (globálně jedinečný) serveru OPC UA telemetrii pochází z. Ingestovaný zprávy, tato vlastnost se odešlou jako **ApplicationUri**.

* **nodeId**

  Hodnota uzlu na serveru OPC UA identifikuje. Musí být ve formátu vlastnosti uvedené ve specifikaci OPC UA. Ingestovaný zprávy, tato vlastnost se odešlou jako **NodeId**.

Zkontrolujte [to](https://github.com/Azure/iot-edge-opc-publisher) GitHub stránce Další informace o tom, jak je k vytváření připojení pomocí vydavatele OPC konzumována telemetrická data.

## <a name="example-how-kpi1-is-calculated"></a>Příklad: Jak se počítá KPI1

Konfigurace v `ContosoTopologyDescription.json` soubor řídí způsob výpočtu obrázky OEE/klíčového ukazatele výkonu. Následující příklad ukazuje, jak řídit vlastnosti v tomto souboru výpočet KPI1.

V připojené KPI1 Factory se používá k měření počtu úspěšně vyrobenou produktů za poslední hodinu. Každé stanici (OPC UA server) v simulaci připojené Factory poskytuje do uzlu OPC UA (`NodeId: "ns=2;i=385"`), který nabízí telemetrii k výpočtu tento klíčový ukazatel výkonu.

Konfigurace pro tento uzel OPC UA vypadá následující fragment kódu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Tato konfigurace umožňuje dotazování telemetrie hodnot tohoto uzlu pomocí statistiky časové řady. Načte časové řady Statistika dotazu:

* Počet hodnot.
* Minimální hodnota.
* Maximální hodnota.
* Průměr všech hodnot.
* Součet všech hodnot pro všechny jedinečné **OpcUri** (**ApplicationUri**), **NodeId** dvojice v dané časový interval.

Jeden vlastnosti **NumberOfManufactureredProducts** hodnota uzlu je, že jenom zvýší. Vypočítat počet produktů, byl vyroben v časový interval, vytváření připojení používá **OpCode** **SubMaxMin**. Výpočet načte minimální hodnota na začátku časový interval a maximální hodnota na konci časový interval.

**OpCode** v konfiguraci nakonfiguruje logice výpočetní vypočítat výsledek rozdíl maximální a minimální hodnoty. Tyto výsledky jsou pak nahromadění dolní až kořenové úrovni (globální) a na řídicím panelu.

## <a name="next-steps"></a>Další postup

Navrhované A dalším krokem je další postup [nasazení brány v systému Windows nebo Linux pro akcelerátoru řešení připojen objekt pro vytváření](iot-suite-connected-factory-gateway-deployment.md).