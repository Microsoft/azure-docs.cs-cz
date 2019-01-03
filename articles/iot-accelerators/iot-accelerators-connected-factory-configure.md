---
title: Konfigurace topologie připojené továrny – Azure | Dokumentace Microsoftu
description: Postup konfigurace topologie akcelerátor řešení připojená továrna.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 3fd160fbccfb5298cefed6a731797ca6962b997c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602269"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurace akcelerátor řešení připojená továrna

Akcelerátor řešení připojená továrna zobrazuje Simulovaná řídicí panel pro fiktivní společnosti Contoso. Tato společnost má objekty pro vytváření globálně v mnoha globálním měřítku.

Tento článek používá Contoso jako příklad k popisu konfigurace topologie řešení připojené továrny.

## <a name="simulated-factories-configuration"></a>Konfigurace simulované objekty pro vytváření

Každý objekt pro vytváření Contoso má výrobní linky, které se skládají ze tří stanice. Každá stanice je skutečný server OPC UA má určitou roli:

* Stanice sestavení
* Testovací stanice
* Balicí stanice

Tyto servery OPC UA mít uzlů OPC UA a [vydavatel OPC](https://github.com/Azure/iot-edge-opc-publisher) odešle hodnoty z těchto uzlů pro propojenou továrnu. To zahrnuje:

* Aktuální provozní stav, jako je například aktuální spotřebu energie.
* Vytváří se produkční informace, jako je počet produktů.

Zobrazit podrobné informace o topologii Contoso factory z až úrovně zobrazení stanice po globální zobrazení mohou pomocí řídicího panelu. Řídicí panel připojená továrna umožňuje:

* Vizualizace hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu pro každou vrstvu v topologii.
* Vizualizaci aktuální hodnoty uzlů OPC UA v stanice.
* Agregace hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu z úrovně stanice na globální úrovni.
* Vizualizace výstrahy a akce provést, pokud hodnoty dosáhnout specifické prahové hodnoty.

## <a name="connected-factory-topology"></a>Topologie propojené továrny

Topologie objekty pro vytváření, výrobní linky a stanice je hierarchická:

* Globální úroveň má objekt pro vytváření uzly jako podřízené objekty.
* Továren mít uzly výrobní linky jako podřízené objekty.
* Výrobní linky mít uzly stanice jako podřízené objekty.
* Stanice (servery OPC UA) mít uzly OPC UA jako podřízené objekty.

Každý uzel v topologii má společnou sadu vlastností, které definují:

* Jedinečný identifikátor pro uzel topologie.
* Název.
* Popis.
* Obrázek.
* Podřízené položky uzlu topologie.
* Minimální, cíle a maximální hodnoty pro hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu a upozornění akce k provedení.

## <a name="topology-configuration-file"></a>Topologie konfiguračního souboru

Pokud chcete nakonfigurovat vlastnosti uvedené v předchozí části, řešení připojená továrna používá konfigurační soubor s názvem [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Tento soubor najdete ve zdrojovém kódu v řešení `WebApp/Contoso/Topology` složky.

Následující fragment kódu ukazuje osnovu třídy `ContosoTopologyDescription.json` konfiguračního souboru:

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

Společné vlastnosti `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, a `<station_configuration>` jsou:

* **Název** (zadejte řetězec)

  Určuje popisný název, který by měl být pouze jedno slovo uzel topologie, který má zobrazit na řídicím panelu.

* **Popis** (zadejte řetězec)

  Popisuje uzel topologie podrobněji.

* **Obrázek** (zadejte řetězec)

  Cesta k obrázku v řešení WebApp zobrazíte, když se informace o topologii uzlu zobrazené v řídicím panelu.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  Tyto vlastnosti definovat minimální, cílová a maximální hodnoty provozní obrázek sloužící ke generování výstrahy. Tyto vlastnosti také definovat akce, které chcete spustit, pokud se detekuje výstraha.

`<factory_configuration>` a `<production_line_configuration>` položky mají vlastnost:

* **Identifikátor GUID** (zadejte řetězec)

  Jednoznačně identifikuje uzel topologie.

`<factory_configuration>` má vlastnost:

* **Umístění** (typ `<location_definition>`)

  Určuje, kde se nachází objekt pro vytváření.

`<station_configuration>` obsahuje vlastnosti:

* **OpcUri** (zadejte řetězec)

  Tato vlastnost musí být nastavená na identifikátor URI sady OPC UA aplikace serveru OPC UA.
  Protože musí být globálně jedinečný ve specifikaci OPC UA, tato vlastnost se používá k identifikaci uzel topologie stanice.

* **OpcNodes**, což jsou pole uzlů OPC UA (typ `<opc_node_description>`)

`<location_definition>` obsahuje vlastnosti:

* **Město** (zadejte řetězec)

  Název města nejblíže k umístění

* **Země** (zadejte řetězec)

  Země

* **Zeměpisná šířka** (double – typ)

  Zeměpisná šířka místa

* **Zeměpisná délka** (double – typ)

  Zeměpisná délka místa

`<performance_definition>` obsahuje vlastnosti:

* **Minimální** (double – typ)

  Nižší prahová hodnota hodnota dosáhnout. Pokud aktuální hodnota je nižší než tato prahová hodnota, je vygenerována výstraha.

* **Cíl** (double – typ)

  Ideální cílovou hodnotu.

* **Maximální** (double – typ)

  Můžete dosáhnout vyšší prahová hodnota. Pokud aktuální hodnota je nad touto prahovou hodnotou, je vygenerována výstraha.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které může být přijata jako reakci na oznámení na minimální.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které může být přijata jako reakci na oznámení na maximální.

`<alert_action`> má vlastnosti:

* **Typ** (zadejte řetězec)

  Typ akce upozornění. Jsou známé následující typy:

  * **AcknowledgeAlert**: stav výstrahy by měl změnit na potvrzené.
  * **CloseAlert**: všechny výstrahy starší stejného typu by měl být již nejsou zobrazené v řídicím panelu.
  * **CallOpcMethod**: metody OPC UA by měla být volána.
  * **OpenWebPage**: by měl být otevřen okno prohlížeče zobrazující další kontextové informace.

* **Popis** (zadejte řetězec)

  Popis akce zobrazené v řídicím panelu.

* **Parametr** (zadejte řetězec)

  Parametry požadované k provedení akce. Hodnota závisí na typu akce.

  * **AcknowledgeAlert**: vyžaduje parametr.
  * **CloseAlert**: vyžaduje parametr.
  * **CallOpcMethod**: informace o uzlu a parametry metody OPC UA pro volání ve formátu "NodeId nadřazeného uzlu, NodeId metody na volání, identifikátor URI serveru OPC UA."
  * **OpenWebPage**: adresa URL pro zobrazení v okně prohlížeče.

`<opc_node_description>` obsahuje informace o uzlů OPC UA v stanice (server OPC UA). Uzly, které představují žádné existující uzly OPC UA, ale slouží jako úložiště ve výpočetní logice připojené továrny platí také. Má následující vlastnosti:

* **NodeId** (zadejte řetězec)

  Adresa OPC UA adresní prostor uzel v stanice (OPC UA serveru). Syntaxe musí být ve specifikaci OPC UA NodeId jak je určeno.

* **Symbolickou** (zadejte řetězec)

  Název má být zobrazené v řídicím panelu, když se zobrazí hodnota tohoto uzlu OPC UA.

* **Podle relevance** (pole typu String)

  Určuje, pro který je relevantní výpočtu celkové efektivity zařízení nebo klíčového ukazatele výkonu hodnota uzlu OPC UA. Každý prvek pole může být jedna z následujících hodnot:

  * **OeeAvailability_Running**: hodnota je relevantní pro výpočet dostupnosti celkové efektivity zařízení.
  * **OeeAvailability_Fault**: hodnota je relevantní pro výpočet dostupnosti celkové efektivity zařízení.
  * **OeePerformance_Ideal**: hodnota je relevantní pro výpočet výkon OEE a je obvykle konstantní hodnotu.
  * **OeePerformance_Actual**: hodnota je relevantní pro výpočet výkon OEE.
  * **OeeQuality_Good**: hodnota je relevantní pro výpočet kvalita OEE.
  * **OeeQuality_Bad**: hodnota je relevantní pro výpočet kvalita OEE.
  * **Kpi1**: hodnota je relevantní pro výpočet KPI1.
  * **Kpi2**: hodnota je relevantní pro výpočet KPI2.

* **Operační kód** (zadejte řetězec)

  Určuje, jak se v Time Series Insights dotazy a výpočty celkové efektivity zařízení nebo klíčového ukazatele výkonu zpracovává hodnotu uzlu OPC UA. Každý dotaz Time Series Insights zaměřuje na konkrétní časový interval, který je parametr dotazu, který poskytuje výsledek. OpCode ovládací prvky, jak výsledek je počítaná a může být jedna z následujících hodnot:

  * **Diff**: rozdíl mezi poslední a první hodnotu v časový interval.
  * **AVG**: průměr všech hodnot v časový interval.
  * **Součet**: součet všech hodnot v časový interval.
  * **Poslední**: momentálně se nepoužívá.
  * **Počet**: počet hodnot v časový interval.
  * **Maximální počet**: maximální hodnota v časový interval.
  * **Min**: minimální hodnota v časový interval.
  * **Const**: Výsledkem je hodnota zadaná ve vlastnosti ConstValue.
  * **SubMaxMin**: rozdíl mezi maximální a minimální hodnoty.
  * **Časový interval**: časový interval.

* **Jednotky** (zadejte řetězec)

  Definuje jednotku hodnoty pro zobrazení v řídicím panelu.

* **Viditelné** (typu boolean)

  Určuje, zda hodnota by měla být zobrazené v řídicím panelu.

* **ConstValue** (double – typ)

  Pokud **operační kód** je **Const**, pak tato vlastnost je hodnota uzlu.

* **Minimální** (double – typ)

  Pokud aktuální hodnota klesne pod tuto hodnotu, je vygenerována minimální výstraha.

* **Maximální** (double – typ)

  Pokud aktuální hodnota vyvolá nad tuto hodnotu, je vygenerována maximální výstraha.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které může být přijata jako reakci na oznámení na minimální.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definuje sadu akcí, které může být přijata jako reakci na oznámení na maximální.

Na úrovni stanice se zobrazí také **simulace** objekty. Tyto objekty pouze slouží ke konfiguraci simulace propojenou továrnu a neměl by se nakonfigurovat skutečné topologie.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Použití konfiguračních dat za běhu

Všechny vlastnosti používané v konfiguračním souboru mohou být seskupeny do různých kategorií v závislosti na tom, jak se používají. Jsou tyto kategorie:

### <a name="visual-appearance"></a>Vzhled

Vlastnosti v této kategorii definují vzhled řídicí panel připojené továrny. Příklady obsahují:

* Název
* Popis
* Image
* Umístění
* Jednotky
* Viditelný

### <a name="internal-topology-tree-addressing"></a>Adresování interní topologii stromu

Webové aplikace udržuje slovníku interní data obsahující informace o všech uzlů topologie. Vlastnosti **Guid** a **OpcUri** se používají jako klíče pro přístup k tomuto slovníku a musí být jedinečný.

### <a name="oeekpi-computation"></a>Výpočtu celkové efektivity zařízení nebo klíčového ukazatele výkonu

Hodnoty celkové efektivity zařízení nebo klíčového ukazatele výkonu pro propojenou továrnu simulace jsou parametrizován:

* Hodnoty uzlu OPC UA mají být zahrnuty do výpočtu.
* Jak na obrázku je vypočítán z hodnot telemetrická data.

Propojená továrna používá vzorce celkové efektivity zařízení, jak publikuje https://www.oeefoundation.org.

Objekty uzlu OPC UA na stanicích povolit označování pro použití ve výpočtu celkové efektivity zařízení nebo klíčového ukazatele výkonu. **Relevance** vlastnost určuje, pro které obrázek celkové efektivity zařízení nebo klíčového ukazatele výkonu je třeba použít hodnotu uzlu OPC UA. **Operační kód** vlastnost definuje, jak je hodnota součástí výpočtu.

### <a name="alert-handling"></a>Zpracování oznámení

Propojená továrna podporuje mechanismus jednoduché minimální/maximální založené na prahových hodnotách generování výstrah. Existuje několik předdefinovaných akcí, které můžete nakonfigurovat v reakci na tyto výstrahy. Tyto vlastnosti řídit tento mechanismus:

* Maximum
* Minimální
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korelace telemetrická data

Pro určité operace, jako je vizualizace poslední hodnotu nebo vytváření dotazů Time Series Insights, webové aplikace potřebuje schéma adresování pro přijaté telemetrická data. Telemetrická data odesílaná pro propojenou továrnu také musí být uložena v interních datových strukturách. Dvě vlastnosti, které umožňuje tyto operace jsou na úrovni uzlu OPC UA a stanice (server OPC UA):

* **OpcUri**

  Identifikuje (globálně jedinečný) serveru OPC UA telemetrická data pochází z. V přijaté zprávy, tato vlastnost se odešle jako **ApplicationUri**.

* **NodeId**

  Určuje hodnotu uzlu na serveru OPC UA. Formát vlastnosti musí být uvedené ve specifikaci OPC UA. V přijaté zprávy, tato vlastnost se odešle jako **NodeId**.

Zkontrolujte [to](https://github.com/Azure/iot-edge-opc-publisher) stránka Githubu pro další informace o tom, jak telemetrická data ingestují pro propojenou továrnu použití vydavatele OPC.

## <a name="example-how-kpi1-is-calculated"></a>Příklad: Jak se počítá KPI1

Konfigurace v nástroji `ContosoTopologyDescription.json` soubor řídí, jak se počítají hodnoty celkové efektivity zařízení nebo klíčového ukazatele výkonu. Následující příklad ukazuje, jak řídit výpočtu KPI1 vlastnosti v tomto souboru.

V připojené továrny KPI1 se používá k měření počtu úspěšně výrobky za poslední hodinu. Připojená továrna simulace všechny stanice (server OPC UA) poskytuje uzlu OPC UA (`NodeId: "ns=2;i=385"`), která obsahuje telemetrická data pro tento klíčový ukazatel výkonu.

Konfigurace pro tento uzel OPC UA bude vypadat jako následující fragment kódu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Tato konfigurace umožňuje dotazování na telemetrická data hodnoty tohoto uzlu pomocí služby Time Series Insights. Dotazy načítají Time Series Insights:

* Počet hodnot.
* Minimální hodnota.
* Maximální hodnota.
* Průměr všech hodnot.
* Součet všech hodnot pro všechny jedinečné **OpcUri** (**ApplicationUri**), **NodeId** dvojice v daném časový interval.

Jedna vlastnost **NumberOfManufactureredProducts** hodnota uzlu je, že pouze zvyšuje. Chcete-li vypočítat počet produktů v časový interval, připojená továrna používá **operační kód** **SubMaxMin**. Výpočet načte minimální hodnota na začátku rozsahu timespan jazyka a maximální hodnota na konci časový interval.

**Operační kód** v konfiguraci nakonfiguruje výpočetní logice k výpočtu výsledků rozdíl hodnot maximální a minimální. Tyto výsledky se pak sbírají dolní až kořenové úrovni (globální) a zobrazené v řídicím panelu.

## <a name="next-steps"></a>Další postup

Navrhované A dalším krokem je další způsob [nasazení brány ve Windows nebo Linuxem pro akcelerátor řešení připojená továrna](iot-accelerators-connected-factory-gateway-deployment.md).
