---
title: Automatické nasazení pro skupiny zařízení-Azure IoT Edge | Microsoft Docs
description: Použití automatických nasazení v Azure IoT Edge ke správě skupin zařízení na základě sdílených značek
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 81db9c7e729aa0be67a807d9d77a3cccb8f41604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85194786"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Pochopení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku

Automatická nasazení a vrstvená nasazení vám pomůžou spravovat a konfigurovat moduly na velkém počtu IoT Edgech zařízení.

Azure IoT Edge poskytuje dva způsoby, jak nakonfigurovat moduly, které se mají spouštět na IoT Edge zařízeních. První metodou je nasazení modulů na základě jednotlivých zařízení. Vytvoříte manifest nasazení a pak ho použijete pro konkrétní zařízení podle názvu. Druhou metodou je nasadit moduly automaticky do libovolného registrovaného zařízení, které splňuje sadu definovaných podmínek. Vytvoříte manifest nasazení a pak definujete, která zařízení se vztahují na závislosti na [značkách](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) v zařízení.

Tento článek se zaměřuje na konfiguraci a monitorování loďstva zařízení, které se souhrnně označují jako *IoT Edge Automatická nasazení*.Základní postup nasazení je následující:

1. Operátor definuje nasazení, které popisuje sadu modulů a cílová zařízení.Každé nasazení má manifest nasazení, který tyto informace odráží.
2. Služba IoT Hub komunikuje se všemi cílovými zařízeními a nakonfiguruje je pomocí deklarované moduly.
3. Služba IoT Hub načítá stav ze zařízení IoT Edge a zpřístupňuje je operátorovi.Například operátor může zobrazit, když se hraniční zařízení nenakonfigurovalo úspěšně nebo když v modulu runtime dojde k chybě.
4. V každém okamžiku jsou pro nasazení nakonfigurovaná nová IoT Edge zařízení, která splňují podmínky cílení.

Tento článek popisuje jednotlivé komponenty, které jsou součástí konfigurace a monitorování nasazení. Návod k vytvoření a aktualizaci nasazení najdete v tématu věnovaném [nasazení a sledování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Nasazení

IoT Edge automatické nasazení přiřadí image IoT Edge modulu, aby se spouštěly jako instance na cílové sadě IoT Edge zařízení. Funguje tak, že nakonfiguruje manifest nasazení IoT Edge tak, aby zahrnoval seznam modulů s odpovídajícími inicializačními parametry.Nasazení se dá přiřadit k jednomu zařízení (na základě ID zařízení) nebo ke skupině zařízení (na základě značek).Jakmile zařízení IoT Edge obdrží manifest nasazení, stáhne a nainstaluje image kontejneru z příslušných úložišť kontejnerů a nakonfiguruje je odpovídajícím způsobem.Po vytvoření nasazení může operátor sledovat stav nasazení a zjistit, jestli jsou cílová zařízení správně nakonfigurovaná.

S nasazením lze nakonfigurovat pouze IoT Edge zařízení. Než může přijmout nasazení, musí být na zařízení následující požadavky:

* Základní operační systém
* Systém správy kontejnerů, jako je Moby nebo Docker
* Zřizování modulu runtime IoT Edge

### <a name="deployment-manifest"></a>Manifest nasazení

Manifest nasazení je dokument JSON, který popisuje moduly, které mají být nakonfigurovány na cílových IoT Edge zařízeních. Obsahuje konfigurační metadata pro všechny moduly, včetně požadovaných systémových modulů (konkrétně agent IoT Edge a centrum IoT Edge).  

Konfigurační metadata pro každý modul zahrnují:

* Verze
* Typ
* Stav (například spuštěno nebo zastaveno)
* Zásady restartování
* Image a Registry kontejneru
* Trasy pro vstup a výstup dat

Pokud je image modulu uložená v soukromém registru kontejnerů, agent IoT Edge uchovává přihlašovací údaje registru.

### <a name="target-condition"></a>Cílová podmínka

Cílová podmínka se průběžně vyhodnocuje po celou dobu životnosti nasazení. Všechna nová zařízení, která splňují požadavky, jsou zahrnutá a všechna stávající zařízení, která už nejsou, se odeberou. Nasazení se znovu aktivuje, pokud služba zjistí změnu cílové podmínky.

Máte například nasazení pomocí značek cílové podmínky. Environment = ' prod '. Když zahájíte nasazení, je k dispozici 10 produkčních zařízení. Moduly jsou úspěšně nainstalovány v těchto 10 zařízeních. Stav agenta IoT Edge zobrazuje 10 celkem zařízení, 10 úspěšných odpovědí, 0 odpovědí na chyby a 0 nedokončených odpovědí. Nyní přidáte pět dalších zařízení se značkami. prostředí = ' prod '. Služba zjistí změnu a stav agenta IoT Edge se změní na 15 celkových zařízení, 10 úspěšných odpovědí, 0 odpovědí na chyby a 5 nevyřízených odpovědí, zatímco se nasadí na pět nových zařízení.

Použijte jakoukoli logickou podmínku pro nepoužité značky zařízení, vykázaných vlastností zařízení, které jsou v seznamu, a vyberte cílové zařízení. Pokud chcete použít podmínku s značkami, je nutné přidat "značky": {} oddíl do zařízení je možné nastavit na stejné úrovni jako vlastnosti. [Další informace o značkách v zařízení s dvojitou dvojicí](../iot-hub/iot-hub-devguide-device-twins.md)

Příklady cílových podmínek:

* deviceId = ' linuxprod1 '
* Tags. Environment = ' prod '
* Tags. Environment = ' prod ' a Tags. Location = ' westus '
* Tags. Environment = ' prod ' nebo Tags. Location = ' westus '
* Tags. operator = Jan a Tags. Environment = ' prod ' a NOT deviceId = ' linuxprod1 '
* Properties. inhlášeny. devicemodel = ' 4000x '

Při vytváření cílové podmínky Vezměte v úvahu tato omezení:

* V zařízení je možné vytvořit jenom cílovou podmínku pomocí značek, hlášených vlastností nebo deviceId.
* Dvojité uvozovky nejsou v žádné části podmínky cíle povoleny. Použijte jednoduché uvozovky.
* Jednoduché uvozovky reprezentují hodnoty cílové podmínky. Proto je nutné jednu uvozovku začínat jinou jednou uvozovkou, pokud je součástí názvu zařízení. Například pro cílení na zařízení nazvané `operator'sDevice` Write `deviceId='operator''sDevice'` .
* V hodnotách cílové podmínky jsou povoleny číslice, písmena a následující znaky: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Priorita

Priorita definuje, jestli se má nasazení použít u cílového zařízení vzhledem k jiným nasazením. Priorita nasazení je kladné celé číslo s větší prioritou, která označuje vyšší prioritu. Pokud je zařízení IoT Edge cíleno více než jedním nasazením, platí nasazení s nejvyšší prioritou.Nasazení s nižšími prioritami se neaplikují, ani se nesloučí.Pokud je zařízení cíleno na dvě nebo více nasazení se stejnou prioritou, platí poslední vytvořené nasazení (určené časovým razítkem vytvoření).

### <a name="labels"></a>Popisky

Popisky jsou páry klíč/hodnota řetězce, které lze použít k filtrování a seskupení nasazení.Nasazení může mít více popisků. Popisky jsou volitelné a neovlivňují skutečnou konfiguraci IoT Edgech zařízení.

### <a name="metrics"></a>Metriky

Ve výchozím nastavení všechna nasazení hlásí čtyři metriky:

* **Cíl** zobrazuje IoT Edge zařízení, která se shodují s podmínkou cílení nasazení.
* **Použito** ukazuje cílené IoT Edge zařízení, na která necílí jiné nasazení s vyšší prioritou.
* **Po úspěšném hlášení** se zobrazí IoT Edge zařízení, která ohlásila, že byly moduly nasazeny úspěšně.
* **Hlášení chyb** zobrazuje IoT Edge zařízení, která oznámily, že jeden nebo více modulů nebyl úspěšně nasazeny. Pokud chcete tuto chybu prozkoumat, připojte se vzdáleně k těmto zařízením a Prohlédněte si soubory protokolů.

Kromě toho můžete definovat vlastní metriky, které vám pomůžou monitorovat a spravovat nasazení.

Metriky poskytují souhrnné počty různých stavů, které mohou zařízení nahlásit zpět v důsledku použití konfigurace nasazení. Metriky se mohou dotazovat na [nedokončené hlášené vlastnosti modulu edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), jako je *lastDesiredStatus* nebo *lastConnectTime*. Například:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Přidání vlastních metrik je volitelné a nemá vliv na skutečnou konfiguraci IoT Edgech zařízení.

## <a name="layered-deployment"></a>Vrstvené nasazení

Navrstvená nasazení jsou automatická nasazení, která se dají vzájemně kombinovat, aby se snížil počet jedinečných nasazení, která se musí vytvořit. Vrstvená nasazení jsou užitečná ve scénářích, kdy se stejné moduly opakovaně používají v různých kombinacích v mnoha automatických nasazeních.

Navrstvená nasazení mají stejné základní komponenty jako jakékoli automatické nasazení. Cílí na zařízení na základě značek v zařízení a poskytují stejné funkce kolem popisků, metrik a hlášení stavu. Navrstvená nasazení mají taky přiřazené priority, ale místo toho, abyste zjistili, jaké nasazení se na zařízení používá, Priorita určuje, jak se v zařízení přiděluje více nasazení. Pokud například dvě vrstvená nasazení mají modul nebo trasu se stejným názvem, použije se vrstvené nasazení s vyšší prioritou, zatímco je nižší priorita přepsána.

Moduly runtime systému, edgeAgent a edgeHub, nejsou nakonfigurované jako součást navrstveného nasazení. Všechna IoT Edge zařízení, na která cílí vrstvy nasazení, musí nejdřív použít standardní automatické nasazení. Automatické nasazení poskytuje základ, na základě kterého lze přidat navrstvená nasazení.

Zařízení IoT Edge může použít jedno a pouze jedno standardní automatické nasazení, ale může použít více vrstevných automatických nasazení. Všechna vrstvená nasazení, která cílí na zařízení, musí mít vyšší prioritu než automatické nasazení tohoto zařízení.

Zvažte například následující scénář společnosti, která spravuje budovy. Vyvinuly IoT Edge moduly pro shromažďování dat z bezpečnostních kamer, snímačů pohybu a výtahů. Ne všechny jejich budovy ale můžou používat všechny tři moduly. U standardních automatických nasazení potřebuje společnost vytvořit jednotlivá nasazení pro všechny kombinace modulů, které jejich budovy potřebují.

![Standardní automatické nasazení musí vyhovovat každé kombinaci modulů.](./media/module-deployment-monitoring/standard-deployment.png)

Jakmile se však společnost přepne do vrstveného automatického nasazení, zjistí, že mohou vytvořit stejné kombinace modulů pro své budovy s menším počtem nasazení, které je potřeba spravovat. Každý modul má své vlastní vrstvené nasazení a značky zařízení identifikují, které moduly se přidají do jednotlivých budov.

![Vrstvené automatické nasazení zjednodušují scénáře, kdy jsou stejné moduly kombinovány různými způsoby.](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfigurace se zdvojeným modulem

Při práci s vrstvenými nasazeními můžete záměrně nebo jinak mít dvě nasazení se stejným modulem, který cílí na zařízení. V těchto případech se můžete rozhodnout, jestli má nasazení s vyšší prioritou přepsat modul s dvojitou prioritou, nebo k němu připojit. Například můžete mít nasazení, které se vztahuje na stejný modul na různá zařízení 100. Nicméně 10 z těchto zařízení je v zabezpečených zařízeních a vyžaduje další konfiguraci, aby bylo možné komunikovat prostřednictvím proxy serverů. Pomocí vrstveného nasazení můžete přidat nepoužitelné vlastnosti modulu, které umožní, aby tato 10 zařízení komunikovala bezpečně, aniž by bylo nutné přepsat stávající modul informacemi z základního nasazení.

V manifestu nasazení můžete připojit modul s požadovanými vlastnostmi. Kde ve standardním nasazení přidáte vlastnosti do **vlastností. požadovaný** oddíl v modulu navýšení se v vrstveném nasazení dá deklarovat novou podmnožinu požadovaných vlastností.

Například ve standardním nasazení můžete přidat modul simulovaného senzoru teploty s následujícími požadovanými vlastnostmi, které mu sdělí, aby odesílali data v intervalech 5 sekund:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

V vrstveném nasazení, které se zaměřuje na některá nebo všechna stejná zařízení, můžete přidat vlastnost, která dává simulovanému senzoru odeslání zpráv 1000 a pak se zastaví. Nechcete přepsat existující vlastnosti, takže vytvoříte novou část v rámci požadovaných vlastností `layeredProperties` , které obsahují novou vlastnost:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Zařízení, které má nasazování obou nasazení, odráží následující vlastnosti v modulu pro simulovaný senzor teploty:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Pokud nastavíte pole s `properties.desired` dvojitým navrstveným nasazením v vrstveném nasazení, přepíše se požadované vlastnosti tohoto modulu v libovolném nasazení s nižší prioritou.

## <a name="phased-rollout"></a>Dvoufázové zavedení

Postupné zavedení je celkový proces, kdy operátor nasadí změny do rozšíření sady IoT Edgech zařízení. Cílem je udělat postupně změny, abyste snížili riziko, že dojde k zásadním změnám v rámci škálování. Automatické nasazení vám pomůžou spravovat dvoufázové uvádění v rámci loďstva zařízení IoT Edge.

Postupné zavedení se provádí v následujících fázích a krocích:

1. Vytvořte testovací prostředí IoT Edgech zařízení tím, že je zřídíte a nastavíte nestejnou značku zařízení jako `tag.environment='test'` .Testovací prostředí by mělo zrcadlit provozní prostředí, na které bude nasazení nakonec cílit.
2. Vytvořte nasazení, včetně požadovaných modulů a konfigurací. Podmínka cílení by měla cílit na prostředí test IoT Edge zařízení.
3. Ověřte konfiguraci nového modulu v testovacím prostředí.
4. Aktualizujte nasazení tak, aby zahrnovalo podmnožinu produkčních IoT Edge zařízení přidáním nové značky do podmínky cílení. Také se ujistěte, že priorita pro nasazení je vyšší než jiné nasazení, které jsou aktuálně cílem těchto zařízení.
5. Zobrazením stavu nasazení ověřte, jestli se nasazení na cílových zařízeních IoT úspěšně dokončilo.
6. Aktualizujte nasazení a Zaměřte se na všechna zbývající produkční IoT Edge zařízení.

## <a name="rollback"></a>Návrat

Nasazení se dá vrátit zpátky, pokud se zobrazí chyby nebo chybné konfigurace.Vzhledem k tomu, že nasazení definuje absolutní konfiguraci modulu pro IoT Edge zařízení, musí být další nasazení cíleno na stejné zařízení s nižší prioritou, i když je cílem odebrat všechny moduly.  

Odstraněním nasazení se moduly neodstraňují z cílových zařízení. Musí existovat jiné nasazení, které definuje novou konfiguraci pro zařízení, a to i v případě, že se jedná o prázdné nasazení.

Provést vrácení zpět v následujícím pořadí:

1. Ověřte, že je druhé nasazení také cílené na stejnou sadu zařízení. Pokud je cílem odvolání odebrání všech modulů, druhé nasazení by nemělo obsahovat žádné moduly.
2. Upravte nebo odeberte výraz cílové podmínky nasazení, které chcete vrátit zpět, aby zařízení již nesplňovala podmínku cílení.
3. Zobrazením stavu nasazení ověřte, že se vrácení zpět zdařilo.
   * Nasazení vracené zpět by již nemělo zobrazovat stav u zařízení, která byla vrácena zpět.
   * Druhé nasazení by teď mělo zahrnovat stav nasazení u zařízení, která byla vrácena zpět.

## <a name="next-steps"></a>Další kroky

* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).
* Přečtěte si další informace o dalších IoT Edge konceptech, jako jsou moduly [runtime IoT Edge](iot-edge-runtime.md) a [IoT Edge](iot-edge-modules.md).
