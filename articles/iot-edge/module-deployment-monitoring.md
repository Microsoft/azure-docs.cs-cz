---
title: Automatické nasazení pro skupiny zařízení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Správa skupin zařízení založených na sdílených značkách pomocí automatických nasazení v Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895963"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Seznamte se s automatickými nasazeními IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku

Automatická nasazení a vrstvené nasazení vám pomohou spravovat a konfigurovat moduly na velkém počtu zařízení IoT Edge.

Azure IoT Edge poskytuje dva způsoby konfigurace modulů pro spouštění na zařízeních IoT Edge. První metodou je nasazení modulů na základě pro zařízení. Vytvoříte manifest nasazení a potom jej použijete na konkrétní zařízení podle názvu. Druhou metodou je automatické nasazení modulů do libovolného registrovaného zařízení, které splňuje sadu definovaných podmínek. Vytvoříte manifest nasazení a pak definujete, na která zařízení se vztahuje, na základě [značek](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) v dvojčeti zařízení.

Tento článek se zaměřuje na konfiguraci a monitorování vozových parků zařízení, souhrnně označovaných jako *automatická nasazení IoT Edge*.Základní kroky nasazení jsou následující:

1. Operátor definuje nasazení, které popisuje sadu modulů a cílových zařízení.Každé nasazení má manifest nasazení, který odráží tyto informace.
2. Služba IoT Hub komunikuje se všemi cílenými zařízeními a nakonfiguruje je s deklarovanými moduly.
3. Služba IoT Hub načte stav ze zařízení IoT Edge a zpřístupní je operátorovi.Operátor například může zobrazit, když zařízení Edge není úspěšně nakonfigurováno nebo pokud modul selže za běhu.
4. Kdykoli jsou pro nasazení nakonfigurována nová zařízení IoT Edge, která splňují podmínky cílení.

Tento článek popisuje každou součást, která se podílí na konfiguraci a monitorování nasazení. Návod k vytvoření a aktualizaci nasazení najdete v tématu [Nasazení a monitorování modulů IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

## <a name="deployment"></a>Nasazení

Automatické nasazení IoT Edge přiřazuje ioT Edge image modulu ke spuštění jako instance na cílové sadě zařízení IoT Edge. Funguje tak, že konfiguruje manifest nasazení IoT Edge tak, aby zahrnoval seznam modulů s odpovídajícími parametry inicializace.Nasazení lze přiřadit k jednomu zařízení (na základě ID zařízení) nebo ke skupině zařízení (na základě značek).Jakmile zařízení IoT Edge obdrží manifest nasazení, stáhne a nainstaluje ifotky kontejneru z příslušných úložišť kontejnerů a odpovídajícím způsobem je nakonfiguruje.Po vytvoření nasazení může operátor sledovat stav nasazení a zjistit, zda jsou cílená zařízení správně nakonfigurována.

S nasazením lze nakonfigurovat jenom zařízení IoT Edge. Následující požadavky musí být na zařízení před přijetím nasazení:

* Základní operační systém
* Systém správy kontejnerů, jako je Moby nebo Docker
* Zřizování runtime IoT Edge

### <a name="deployment-manifest"></a>Manifest nasazení

Manifest nasazení je dokument JSON, který popisuje moduly, které mají být nakonfigurovány na cílených zařízeních IoT Edge. Obsahuje metadata konfigurace pro všechny moduly, včetně požadovaných systémových modulů (konkrétně agenta IoT Edge a centra IoT Edge).  

Metadata konfigurace pro každý modul zahrnují:

* Version
* Typ
* Stav (například spuštěný nebo zastavený)
* Zásady restartování
* Registr obrázků a kontejnerů
* Trasy pro vstup a výstup dat

Pokud je bitová kopie modulu uložena v registru privátního kontejneru, je agent IoT Edge držitelem pověření registru.

### <a name="target-condition"></a>Cílová podmínka

Cílová podmínka je průběžně vyhodnocována po celou dobu životnosti nasazení. Všechna nová zařízení, která splňují požadavky, jsou zahrnuta a všechna existující zařízení, která již nesplňují, budou odebrána. Nasazení je znovu aktivováno, pokud služba zjistí jakoukoli změnu cílové podmínky.

Například máte nasazení s cílovou podmínkou tags.environment = 'prod'. Když spustíte nasazení, existuje 10 produkčních zařízení. Moduly jsou úspěšně nainstalovány v těchto 10 zařízeních. Stav agenta IoT Edge zobrazuje celkem 10 zařízení, 10 úspěšných odpovědí, 0 odpovědí na selhání a 0 čekajících odpovědí. Nyní přidáte dalších pět zařízení s tags.environment = 'prod'. Služba zjistí změnu a stav agenta IoT Edge se stane 15 celkovýpočet zařízení, 10 úspěšných odpovědí, 0 selhání odpovědí a 5 čekající odpovědi při nasazení do pěti nových zařízení.

Použijte libovolnou logickou podmínku u značek dvojčete zařízení, ohlášených vlastností dvojčete zařízení nebo deviceId k výběru cílových zařízení. Pokud chcete použít podmínku se značkami, musíte{} přidat "značky": oddíl v dvojčeti zařízení pod stejnou úroveň jako vlastnosti. [Další informace o značkách v části Dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md)

Příklady cílových podmínek:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' A tags.location = 'westus'
* tags.environment = 'prod' NEBO tags.location = 'westus'
* tags.operator = 'John' And tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Při vytváření cílové podmínky zvažte tato omezení:

* V dvojčeti zařízení můžete vytvořit cílovou podmínku pouze pomocí značek, ohlášených vlastností nebo id zařízení.
* Dvojité uvozovky nejsou povoleny v žádné části cílové podmínky. Použijte jednoduché uvozovky.
* Jednoduché uvozovky představují hodnoty cílové podmínky. Proto je nutné uniknout jedné uvozovky s jinou jednu uvozovky, pokud je součástí názvu zařízení. Chcete-li například cílit na zařízení s názvem `operator'sDevice`, zapište `deviceId='operator''sDevice'`.
* Čísla, písmena a následující znaky jsou povoleny v cílových hodnotách podmínek: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorita

Priorita definuje, zda má být nasazení použito na cílové zařízení vzhledem k ostatním nasazením. Priorita nasazení je kladné celé číslo, přičemž větší čísla označují vyšší prioritu. Pokud je zařízení IoT Edge cílem více než jednoho nasazení, použije se nasazení s nejvyšší prioritou.Nasazení s nižšími prioritami nejsou použita ani nejsou sloučena.Pokud je zařízení zacíleno se dvěma nebo více nasazeními se stejnou prioritou, použije se naposledy vytvořené nasazení (určené časovým razítkem vytvoření).

### <a name="labels"></a>Popisky

Popisky jsou páry klíčů a hodnot řetězce, které můžete použít k filtrování a seskupování nasazení.Nasazení může mít více popisků. Popisky jsou volitelné a nemají vliv na skutečnou konfiguraci zařízení IoT Edge.

### <a name="metrics"></a>Metriky

Ve výchozím nastavení se všechna nasazení hlásí na čtyři metriky:

* **Cílené** zobrazuje zařízení IoT Edge, které odpovídají podmínce cílení nasazení.
* **Použité** ukazuje cílené Zařízení IoT Edge, které nejsou cílem jiné nasazení s vyšší prioritou.
* **Vytváření sestav Úspěch** zobrazuje zařízení IoT Edge, které oznámily, že moduly byly úspěšně nasazeny.
* **Selhání vykazování** zobrazuje zařízení IoT Edge, která ohlásila, že jeden nebo více modulů nebylo úspěšně nasazeno. Chcete-li chybu dále prozkoumat, připojte se vzdáleně k těmto zařízením a zobrazte soubory protokolu.

Kromě toho můžete definovat vlastní metriky, které vám pomohou sledovat a spravovat nasazení.

Metriky poskytují souhrnpočty různých stavů, které zařízení mohou vykazovat zpět v důsledku použití konfigurace nasazení. Metriky mohou dotaz [edgeHub modul twin hlášené vlastnosti](module-edgeagent-edgehub.md#edgehub-reported-properties), jako *lastDesiredStatus* nebo *lastConnectTime*. Například:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Přidání vlastních metrik je volitelné a nemá vliv na skutečnou konfiguraci zařízení IoT Edge.

## <a name="layered-deployment"></a>Nasazení s vrstvami

Vrstvená nasazení jsou automatická nasazení, která lze kombinovat dohromady, aby se snížil počet jedinečných nasazení, která je třeba vytvořit. Vrstvené nasazení jsou užitečné ve scénářích, kde jsou stejné moduly znovu použity v různých kombinacích v mnoha automatických nasazeních.

Vrstvená nasazení mají stejné základní součásti jako jakékoli automatické nasazení. Zaměřují se na zařízení založená na značkách v dvojčatech zařízení a poskytují stejné funkce týkající se popisků, metrik a reportování stavu. Vrstvená nasazení mají také přiřazené priority, ale namísto použití priority k určení, které nasazení se použije na zařízení, priorita určuje, jak jsou na zařízení seřazeny více nasazení. Například pokud dvě vrstvená nasazení mají modul nebo trasu se stejným názvem, vrstvené nasazení s vyšší prioritou se použije při přepsání nižší priority.

Moduly runtime systému, edgeAgent a edgeHub, nejsou nakonfigurovány jako součást vrstveného nasazení. Jakékoli zařízení IoT Edge, na které se zaměřuje vrstvené nasazení, potřebuje nejprve standardní automatické nasazení. Automatické nasazení poskytuje základnu, na které lze přidat vrstvená nasazení.

Zařízení IoT Edge může použít jedno a jediné standardní automatické nasazení, ale může použít více vrstvených automatických nasazení. Všechna vrstvená nasazení zaměřená na zařízení musí mít vyšší prioritu než automatické nasazení pro toto zařízení.

Zvažte například následující scénář společnosti, která spravuje budovy. Vyvinuli moduly IoT Edge pro sběr dat z bezpečnostních kamer, snímačů pohybu a výtahů. Ne všechny jejich budovy však mohou používat všechny tři moduly. Se standardním automatickým nasazením musí společnost vytvořit individuální nasazení pro všechny kombinace modulů, které jejich budovy potřebují.

![Standardní automatická nasazení musí vyhovovat každé kombinaci modulů](./media/module-deployment-monitoring/standard-deployment.png)

Jakmile však společnost přepne na vrstvená automatická nasazení, zjistí, že mohou vytvořit stejné kombinace modulů pro své budovy s menším počtem nasazení pro správu. Každý modul má své vlastní vrstvené nasazení a značky zařízení určují, které moduly se přidají do každé budovy.

![Vrstvená automatická nasazení zjednodušují scénáře, ve kterých jsou stejné moduly kombinovány různými způsoby](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfigurace dvojčete modulu

Při práci s vrstvenými nasazeními můžete mít záměrně nebo jinak dvě nasazení se stejným modulem zaměřeným na zařízení. V těchto případech můžete rozhodnout, zda vyšší prioritu nasazení by měl přepsat dvojče modulu nebo připojit k němu. Můžete mít například nasazení, které platí stejný modul pro 100 různých zařízení. 10 z těchto zařízení je však v zabezpečených zařízeních a potřebují další konfiguraci pro komunikaci prostřednictvím proxy serverů. Vrstvené nasazení můžete použít k přidání vlastností dvojčete modulu, které umožňují těmto 10 zařízením bezpečně komunikovat bez přepsání existujících informací o dvojčeti modulu ze základního nasazení.

Do manifestu nasazení můžete připojit požadované vlastnosti dvojčete modulu. Kde ve standardním nasazení byste přidat vlastnosti v **properties.desired** části dvojčete modulu, ve vrstvené nasazení můžete deklarovat novou podmnožinu požadovaných vlastností.

Například ve standardním nasazení můžete přidat modul simulovaného teplotního senzoru s následujícími požadovanými vlastnostmi, které mu říkají, že má odesílat data v 5sekundových intervalech:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

V vrstvené nasazení, které se zaměřuje na některé nebo všechny stejné zařízení, můžete přidat vlastnost, která říká simulovaný senzor odeslat 1000 zpráv a potom zastavit. Nechcete přepsat existující vlastnosti, takže vytvoříte nový oddíl v rámci `layeredProperties`požadované vlastnosti s názvem , který obsahuje novou vlastnost:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Zařízení, které má obě použitá nasazení, bude odrážet následující vlastnosti v dvojčeti modulu pro simulovaný teplotní senzor:

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

Pokud nastavíte `properties.desired` pole dvojčete modulu v nasazení vrstvené, přepíše požadované vlastnosti pro tento modul v jakékoli nasazení s nižší prioritou.

## <a name="phased-rollout"></a>Postupné zavádění

Postupné zavádění je celkový proces, kdy operátor nasazuje změny do rozšiřující se sady zařízení IoT Edge. Cílem je provádět změny postupně, aby se snížilo riziko provádění rozsáhlých změn. Automatická nasazení pomáhají spravovat postupné zavádění napříč flotilou zařízení IoT Edge.

Postupné zavádění se provádí v následujících fázích a krocích:

1. Vytvořte testovací prostředí zařízení IoT Edge jejich zřízením a `tag.environment='test'`nastavením značky dvojčete zařízení, jako je .Testovací prostředí by mělo odrážet produkční prostředí, na které bude nasazení nakonec cílit.
2. Vytvořte nasazení včetně požadovaných modulů a konfigurací. Podmínka cílení by měla cílit na testovací prostředí zařízení IoT Edge.
3. Ověřte novou konfiguraci modulu v testovacím prostředí.
4. Aktualizujte nasazení tak, aby zahrnovalo podmnožinu produkčních zařízení IoT Edge přidáním nové značky do podmínky cílení. Také se ujistěte, že priorita pro nasazení je vyšší než u jiných nasazení, která jsou aktuálně zaměřena na tato zařízení.
5. Ověřte, zda bylo nasazení úspěšné na cílených zařízeních IoT zobrazením stavu nasazení.
6. Aktualizujte nasazení tak, aby se zaměřilo na všechna zbývající produkční zařízení IoT Edge.

## <a name="rollback"></a>Vrácení zpět

Nasazení lze vrátit zpět, pokud obdržíte chyby nebo chybné konfigurace.Vzhledem k tomu, že nasazení definuje absolutní konfiguraci modulu pro zařízení IoT Edge, další nasazení musí být také zaměřena na stejné zařízení s nižší prioritou i v případě, že cílem je odebrat všechny moduly.  

Odstraněním nasazení neodeberete moduly z cílených zařízení. Musí existovat jiné nasazení, které definuje novou konfiguraci pro zařízení, i když se jedná o prázdné nasazení.

Proveďte vrácení zpět v následujícím pořadí:

1. Zkontrolujte, zda je druhé nasazení také zaměřeno na stejnou sadu zařízení. Pokud je cílem vrácení zpět odebrat všechny moduly, druhé nasazení by nemělo obsahovat žádné moduly.
2. Upravte nebo odeberte výraz cílové podmínky nasazení, které chcete vrátit zpět, aby zařízení již nesplňovala podmínku cílení.
3. Ověřte, zda bylo vrácení úspěšné zobrazením stavu nasazení.
   * Nasazení vráceného zpět by již nemělo zobrazovat stav pro zařízení, která byla vrácena zpět.
   * Druhé nasazení by nyní mělo zahrnovat stav nasazení pro zařízení, která byla vrácena zpět.

## <a name="next-steps"></a>Další kroky

* Projděte si kroky k vytvoření, aktualizaci nebo odstranění nasazení v [modulech Deploy and Monitor IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
* Přečtěte si další informace o dalších konceptech IoT Edge, jako jsou [moduly IoT Edge runtime](iot-edge-runtime.md) a [IoT Edge](iot-edge-modules.md).
