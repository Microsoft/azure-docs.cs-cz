---
title: Automatické nasazení pro skupiny zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Automatické nasazení ve službě Azure IoT Edge použít ke správě skupiny zařízení, na základě sdílené značek
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13390de8d3008907a0b55bf3a61c931dfdcd84e6
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552351"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku

Automatická nasazení a vrstvená nasazení vám pomůžou spravovat a konfigurovat moduly na velkém počtu IoT Edgech zařízení. 

Azure IoT Edge poskytuje dva způsoby, jak nakonfigurovat moduly, které se mají spouštět na IoT Edge zařízeních. První metodou je nasazení modulů na základě jednotlivých zařízení. Vytvoříte manifest nasazení a pak ho použijete pro konkrétní zařízení podle názvu. Druhou metodou je nasadit moduly automaticky do libovolného registrovaného zařízení, které splňuje sadu definovaných podmínek. Vytvoříte manifest nasazení a pak definujete, která zařízení se vztahují na závislosti na [značkách](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) v zařízení. 

Tento článek se zaměřuje na konfiguraci a monitorování fáze pro flotily nebo zařízení, souhrnně označovány jako automatické nasazení IoT Edge. Celkový postup nasazení je následující: 

1. Operátor definuje nasazení, který popisuje sadu modulů, stejně jako cílová zařízení. Každé nasazení má manifest nasazení, který tyto informace odráží. 
2. Služba IoT Hub komunikuje se všechna cílová zařízení nakonfigurovat požadované moduly. 
3. Služba IoT Hub načítá stav ze zařízení IoT Edge a zpřístupní je operátor.  Například operátor může zobrazit, když hraniční zařízení není úspěšně nakonfigurováno nebo pokud modul během běhu selhává. 
4. V každém okamžiku nová zařízení IoT Edge, které splňují podmínky cílení jsou nakonfigurované pro nasazení. 
 
Tento článek popisuje jednotlivých komponent zahrnutých v konfiguraci a monitorování nasazení. Postup vytvoření a nasazení aktualizace, najdete v části [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

## <a name="deployment"></a>Nasazení

Automatické nasazení IoT Edge přiřadí IoT Edge bitové kopie modulu spouštět jako instance na cílovou sadu zařízení IoT Edge. Funguje to tím, že nakonfigurujete manifest nasazení IoT Edge do seznamu modulů s odpovídající inicializační parametry pro zahrnutí. Nasazení se dá přiřadit k jednomu zařízení (na základě ID zařízení) nebo ke skupině zařízení (na základě značek). Jakmile zařízení IoT Edge obdrží manifest nasazení, stáhne a nainstaluje image kontejneru z příslušných úložišť kontejnerů a nakonfiguruje je odpovídajícím způsobem. Po vytvoření nasazení může operátor sledovat stav nasazení a zjistit, jestli jsou cílová zařízení správně nakonfigurovaná.

Nasazení můžete nakonfigurovat pouze zařízení IoT Edge. Následující požadavky musí být na zařízení, než může přijímat nasazení:

* Základní operační systém
* Systém správy kontejneru, jako je Moby nebo Dockeru
* Zřizování modul runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifest nasazení

Manifest nasazení je dokument JSON, který popisuje moduly, které chcete nakonfigurovat na cílovém zařízení IoT Edge. Obsahuje metadat konfigurace pro všechny moduly, včetně požadovaný systém modulů (konkrétně agenta IoT Edge a Centrum IoT Edge).  

Zahrnuje metadat konfigurace pro každý modul: 

* Verze 
* Typ 
* Stav (například spuštěná nebo zastavená) 
* Zásady restartování 
* Image a container registry
* Trasy pro vstupní a výstupní data 

Pokud je bitové kopie modulu uložené v privátním registru kontejneru, agenta IoT Edge obsahuje přihlašovací údaje registru. 

### <a name="target-condition"></a>Cílová podmínka

Cílová podmínka se průběžně vyhodnocuje po celou dobu životnosti nasazení. Všechna nová zařízení, které splňují požadavky jsou zahrnuty, a odeberou se všechna existující zařízení, která už se. Nasazení se znovu aktivovat, pokud služba detekuje všechny změny stavu cílového. 

Například máte nasazení A s tags.environment podmínku cílové = 'prod'. Pokud zahájíte nasazení, se 10 zařízení produkčního prostředí. Moduly se úspěšně nainstaloval v těchto 10 zařízení. Stav agenta IoT Edge se zobrazuje jako 10 Celkový počet zařízení, 10 úspěšné odpovědi, 0 odpovědí na selhání a 0 odpovědi – čekání. Teď přidáte pět další zařízení s tags.environment = 'prod'. Služba zjistí změnu a stav agenta IoT Edge bude 15 celkový počet zařízení, 10 úspěšné odpovědi, 0 odpovědí na selhání a 5 odpovědi – čekání při pokusu o nasazení do pěti nových zařízení.

Vybrat cílová zařízení pomocí značky dvojčat zařízení nebo deviceId jakoukoli logickou podmínku. Pokud chcete použít podmínku se značkami, budete muset přidat "tags":{} oddílu ve dvojčeti zařízení v rámci stejné úrovně jako vlastnosti. [Další informace o značkách ve dvojčeti zařízení](../iot-hub/iot-hub-devguide-device-twins.md)

Příklady podmínek cíl:

* deviceId = 'linuxprod1.
* Tags.Environment = 'prod'
* Tags.Environment = 'prod' AND tags.location = 'westus'
* Tags.Environment = 'prod' OR tags.location = 'westus'
* Tags.Operator = "Jan" a tags.environment = 'prod' není deviceId = 'linuxprod1.

Tady jsou některé omezí při konstrukci cílovou podmínku:

* Ve dvojčeti zařízení může vytvořit pouze cílovou podmínku pomocí značek nebo ID zařízení.
* Dvojité uvozovky nejsou povolené v každé části cílovou podmínku. Použijte jednoduché uvozovky.
* Jednoduché uvozovky představují hodnoty cílovou podmínku. Proto musíte před jednoduchá uvozovka s jinou jednoduché uvozovky, pokud je součástí názvu zařízení. Například cíl na nějaké zařízení názvem `operator'sDevice`, zápis `deviceId='operator''sDevice'`.
* V cílové podmínku hodnoty jsou povoleny číslice, písmena a následující znaky: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorita

Prioritu definuje, zda má být použita nasazení na cílovém zařízení vzhledem k jiné nasazení. Priorita nasazení je kladné celé číslo, s větší čísla, které označuje vyšší prioritu. Pokud zařízení IoT Edge je cílí více než jedno nasazení, použije se nasazení s nejvyšší prioritou.  Nasazení s nižšími prioritami se neaplikují, ani se nesloučí.  Pokud je zařízení cíleno na dvě nebo více nasazení se stejnou prioritou, platí poslední vytvořené nasazení (určené časovým razítkem vytvoření).

### <a name="labels"></a>Popisky 

Popisky jsou páry klíč/hodnota řetězce, které lze použít k filtrování a seskupení nasazení. Nasazení může mít více popisků. Popisky jsou volitelné a neovlivňují skutečnou konfiguraci IoT Edgech zařízení. 

### <a name="metrics"></a>Metriky

Ve výchozím nastavení všechna nasazení hlásí čtyři metriky:

* **Cíl** zobrazuje IoT Edge zařízení, která se shodují s podmínkou cílení nasazení.
* **Použito** ukazuje cílené IoT Edge zařízení, na která necílí jiné nasazení s vyšší prioritou.
* **Po úspěšném hlášení** se zobrazí IoT Edge zařízení, která se vrátila ke službě, kterou moduly úspěšně nasadily. 
* **Při hlášení** se zobrazí IoT Edge zařízení, která se vrátila ke službě, že jeden nebo více modulů nebyl úspěšně nasazen. Aby to prověřili chybu, vzdálené připojení k těmto zařízením a zobrazit soubory protokolů.

Kromě toho můžete definovat vlastní metriky, které vám pomůžou monitorovat a spravovat nasazení. 

Metriky poskytují souhrnné počty různých stavů, které mohou zařízení nahlásit zpět v důsledku použití konfigurace nasazení. Metriky se mohou dotazovat na [nedokončené hlášené vlastnosti modulu edgeHub](module-edgeagent-edgehub.md#edgehub-reported-properties), jako je například poslední požadovaný stav nebo čas posledního připojení. Příklad: 

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Přidání vlastních metrik je volitelné a nemá vliv na skutečnou konfiguraci IoT Edgech zařízení. 

## <a name="layered-deployment"></a>Vrstvené nasazení

Navrstvená nasazení jsou automatická nasazení, která se dají vzájemně kombinovat, aby se snížil počet jedinečných nasazení, která se musí vytvořit. Vrstvená nasazení jsou užitečná ve scénářích, kdy se stejné moduly opakovaně používají v různých kombinacích v mnoha automatických nasazeních. 

Navrstvená nasazení mají stejné základní komponenty jako jakékoli automatické nasazení. Cílí na zařízení na základě značek v zařízení a poskytují stejné funkce kolem popisků, metrik a hlášení stavu. Navrstvená nasazení mají taky přiřazené priority, ale místo toho, abyste zjistili, jaké nasazení se na zařízení používá, Priorita určuje, jak se v zařízení přiděluje více nasazení. Pokud například dvě vrstvená nasazení mají modul nebo trasu se stejným názvem, použije se vrstvené nasazení s vyšší prioritou, zatímco je nižší priorita přepsána. 

Moduly runtime systému, edgeAgent a edgeHub, nejsou nakonfigurované jako součást navrstveného nasazení. Jakékoli IoT Edge zařízení, na které cílí vrstvené nasazení, potřebuje pro něj nejprve standardní automatické nasazení, které poskytne základ pro přidání vrstvených nasazení. 

Zařízení IoT Edge může použít jedno a pouze jedno standardní automatické nasazení, ale může použít více vrstevných automatických nasazení. Všechna vrstvená nasazení, která cílí na zařízení, musí mít vyšší prioritu než automatické nasazení tohoto zařízení. 

Zvažte například následující scénář společnosti, která spravuje budovy. Vyvinuly IoT Edge moduly pro shromažďování dat z bezpečnostních kamer, snímačů pohybu a výtahů. Ne všechny jejich budovy ale můžou používat všechny tři moduly. U standardních automatických nasazení potřebuje společnost vytvořit jednotlivá nasazení pro všechny kombinace modulů, které jejich budovy potřebují. 

![Standardní automatické nasazení musí vyhovovat každé kombinaci modulů.](./media/module-deployment-monitoring/standard-deployment.png)

Jakmile se však společnost přepne do vrstveného automatického nasazení, zjistí, že mohou vytvořit stejné kombinace modulů pro své budovy s menším počtem nasazení, které je potřeba spravovat. Každý modul má své vlastní vrstvené nasazení a značky zařízení identifikují, které moduly se přidají do jednotlivých budov. 

![Vrstvené automatické nasazení zjednodušuje scénáře, kdy jsou stejné moduly kombinovány různými způsoby.](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfigurace se zdvojeným modulem

Při práci s vrstvenými nasazeními můžete záměrně nebo jinak mít dvě nasazení se stejným modulem, který cílí na zařízení. V těchto případech se můžete rozhodnout, jestli má nasazení s vyšší prioritou přepsat modul s dvojitou prioritou, nebo k němu připojit. Například můžete mít nasazení, které se vztahuje na stejný modul na různá zařízení 100. Nicméně 10 z těchto zařízení je v zabezpečených zařízeních a vyžaduje další konfiguraci, aby bylo možné komunikovat prostřednictvím proxy serverů. Pomocí vrstveného nasazení můžete přidat nepoužitelné vlastnosti modulu, které umožní, aby tato 10 zařízení komunikovala bezpečně, aniž by bylo nutné přepsat stávající modul informacemi z základního nasazení. 

V manifestu nasazení můžete připojit modul s požadovanými vlastnostmi. Kde ve standardním nasazení přidáte vlastnosti do **vlastností. požadovaný** oddíl v modulu navýšení se v vrstveném nasazení dá deklarovat novou podmnožinu požadovaných vlastností. 

Například ve standardním nasazení můžete přidat modul simulovaného senzoru teploty s následujícími požadovanými vlastnostmi, které mu sdělí, aby odesílali data v intervalu 5 sekund:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

V vrstveném nasazení, které cílí na stejná zařízení, nebo na podmnožinu stejných zařízení, můžete chtít přidat další vlastnost, která simuluje senzor, aby odesílal zprávy 1000 a pak se zastavil. Nechcete přepsat existující vlastnosti, takže vytvoříte novou část v rámci požadovaných vlastností s názvem `layeredProperties`, která obsahuje novou vlastnost:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Zařízení, které má nainstalovanou obě nasazení, bude v modulu pro simulaci simulace teploty odpovídat následujícímu: 

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

Pokud nastavíte pole `properties.desired` modulu v vrstveném nasazení, přepíše se požadované vlastnosti pro tento modul v nasazení s nižší prioritou. 

## <a name="phased-rollout"></a>Postupné zavádění 

Postupné zavádění je celkový proces kterým operátor nasadí změny do otevření sadu zařízení IoT Edge. Cílem je, aby změny postupně, aby se snížilo riziko zpřístupnění celou škálovací rozbíjející změny. Automatické nasazení vám pomůžou spravovat dvoufázové uvádění v rámci loďstva zařízení IoT Edge. 

Postupné zavádění je proveden v následujících fází a kroků: 

1. Vytvořit testovací prostředí pro zařízení IoT Edge je zřizování a nastavením značku dvojčete zařízení jako `tag.environment='test'`. Testovací prostředí by mělo zrcadlit provozní prostředí, na které bude nasazení nakonec cílit. 
2. Vytvořte nasazení, včetně modulů a konfigurací. Cílení podmínka musí cílit na testovací prostředí pro zařízení IoT Edge.   
3. Ověření konfigurace nového modulu v testovacím prostředí.
4. Aktualizace nasazení, aby zahrnují podmnožinu produkční zařízení IoT Edge tak, že přidáte novou značku k cílení podmínku. Také se ujistěte, že priorita pro nasazení je vyšší než jiná nasazení aktuálně cílená na těchto zařízeních 
5. Ověřte, že nasazení bylo úspěšné na cílových zařízeních IoT tím, že zobrazíte stav nasazení.
6. Aktualizujte na cílit na všechny zbývající zařízení IoT Edge produkčního nasazení.

## <a name="rollback"></a>Vrácení zpět

Nasazení může být vrácena zpět, pokud se zobrazí chyby nebo chybné konfigurace. Vzhledem k tomu, že nasazení definuje absolutní konfiguraci modulu pro IoT Edge zařízení, musí být další nasazení cíleno na stejné zařízení s nižší prioritou, i když je cílem odebrat všechny moduly.  

Odstraněním nasazení nedojde k odebrání modulů z cílových zařízení. Musí existovat jiné nasazení, které definuje novou konfiguraci pro zařízení, a to i v případě, že se jedná o prázdné nasazení. 

Proveďte vrácení zpět v následujícím pořadí: 

1. Potvrďte, že druhé nasazení cílit také na stejnou sadu zařízení. Pokud je cílem vrácení změn odebrat všechny moduly, druhé nasazení by neměl obsahovat žádné moduly. 
2. Změnit nebo odebrat Cílový výraz podmínky nasazení, který chcete vrátit zpět tak, že zařízení už splňují cílovou podmínku.
3. Ověřte, že vrácení změn zobrazením stavu nasazení.
   * Vrátit zpět nasazení již musí zobrazit stav pro zařízení, které byly vráceny zpět.
   * Druhé nasazení by teď měl obsahovat stav nasazení pro zařízení, které byly vráceny zpět.


## <a name="next-steps"></a>Další kroky

* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
* Další informace o dalších pojmech IoT Edge jako [modul runtime IoT Edge](iot-edge-runtime.md) a [moduly IoT Edge](iot-edge-modules.md).

