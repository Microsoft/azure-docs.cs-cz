---
title: Automatické nasazení pro skupiny zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Automatické nasazení ve službě Azure IoT Edge použít ke správě skupiny zařízení, na základě sdílené značek
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 18cd27ae8bf0a395fa351cf283bc1d40f94dac53
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100102"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku

Postupujte podle zařízení Azure IoT Edge [životní cyklus zařízení](../iot-hub/iot-hub-device-management-overview.md) , který se podobá jiným typům zařízení IoT:

1. Zřizovat nová zařízení IoT Edge imaging zařízení s operační systém a instalaci [modul runtime IoT Edge](iot-edge-runtime.md).
2. Konfigurace zařízení ke spuštění [moduly IoT Edge](iot-edge-modules.md)a potom monitorovat jejich stav. 
3. Nakonec vyřazení zařízení, když se nahradí nebo jsou zastaralé.  

Azure IoT Edge poskytuje dva způsoby, jak nakonfigurovat moduly, které chcete spustit na zařízeních IoT Edge: jeden pro vývoj a rychlé iterace na jednom zařízení (použít tuto metodu ve službě Azure IoT Edge [kurzy](tutorial-deploy-function.md)), a jeden pro správu velkého loďstev z Zařízení IoT Edge. Oba tyto přístupy jsou k dispozici na webu Azure Portal a prostřednictvím kódu programu. Pro cílí na skupiny nebo velký počet zařízení, můžete určit zařízení, která byste chtěli nasadit moduly pomocí [značky](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) ve dvojčeti zařízení. Následující kroky mluvit o nasazení pro skupinu zařízení státu Washington, který je identifikován vlastnost tags. 

Tento článek se zaměřuje na konfiguraci a monitorování fáze pro flotily nebo zařízení, souhrnně označovány jako automatické nasazení IoT Edge. Celkový postup nasazení jsou následující: 

1. Operátor definuje nasazení, který popisuje sadu modulů, stejně jako cílová zařízení. Každé nasazení má manifest nasazení, který odráží tyto informace. 
2. Služba IoT Hub komunikuje se všechna cílová zařízení nakonfigurovat požadované moduly. 
3. Služba IoT Hub načítá stav ze zařízení IoT Edge a zpřístupní je operátor.  Operátor můžete třeba zjistit, když se úspěšně nakonfigurovala hraničního zařízení nebo pokud modulu nezdaří za běhu. 
4. V každém okamžiku nová zařízení IoT Edge, které splňují podmínky cílení jsou nakonfigurované pro nasazení. Například nasazení, které cílí na všechna zařízení IoT Edge ve státě Washington automaticky nakonfiguruje nová zařízení IoT Edge je zřízený a přidán do skupiny zařízení státě Washington. 
 
Tento článek popisuje jednotlivých komponent zahrnutých v konfiguraci a monitorování nasazení. Postup vytvoření a nasazení aktualizace, najdete v části [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

## <a name="deployment"></a>Nasazení

Automatické nasazení IoT Edge přiřadí IoT Edge bitové kopie modulu spouštět jako instance na cílovou sadu zařízení IoT Edge. Funguje to tím, že nakonfigurujete manifest nasazení IoT Edge do seznamu modulů s odpovídající inicializační parametry pro zahrnutí. Nasazení je možné přiřadit na jediné zařízení (podle ID zařízení) nebo do skupiny zařízení (na základě značek). Po zařízení IoT Edge dostane manifest nasazení, stáhne a nainstaluje Image kontejneru z úložišť příslušného kontejneru a nakonfiguruje je odpovídajícím způsobem. Po vytvoření nasazení operátor můžete monitorovat stav nasazení chcete zobrazit, zda jsou správně nakonfigurované cílová zařízení.

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

Cílová podmínka se průběžně Vyhodnocená throughtout životnost nasazení. Všechna nová zařízení, které splňují požadavky jsou zahrnuty, a odeberou se všechna existující zařízení, která už se. Nasazení se znovu aktivovat, pokud služba detekuje všechny změny stavu cílového. 

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

Prioritu definuje, zda má být použita nasazení na cílovém zařízení vzhledem k jiné nasazení. Priorita nasazení je kladné celé číslo, s větší čísla, které označuje vyšší prioritu. Pokud zařízení IoT Edge je cílí více než jedno nasazení, použije se nasazení s nejvyšší prioritou.  Nasazení s nižší prioritou se nepoužije, ani se sloučení.  Pokud zařízení je cílem dvě nebo víc nasazení se stejnou prioritou, použije se nedávno vytvořené nasazení (určené časové razítko vytvoření).

### <a name="labels"></a>Popisky 

Popisky jsou páry klíč/hodnota řetězce, které můžete použít k filtrování a skupiny nasazení. Nasazení může mít více popisky. Popisky jsou volitelné a proveďte žádný vliv skutečnou konfiguraci zařízení IoT Edge. 

### <a name="deployment-status"></a>Stav nasazení

Chcete-li zjistit, jestli se úspěšně použity pro všechny cílové zařízení IoT Edge se dá monitorovat nasazení.  Cílové zařízení Edge se zobrazí v jedné nebo více z následujících kategorií stavu: 

* **Cíl** uvedena na hraničních zařízeních IoT zařízení, které odpovídají nasazení cílí na podmínku.
* **Skutečné** zobrazuje cílové hraničních zařízeních IoT zařízení, která nejsou cílem jiné nasazení s vyšší prioritou.
* **V pořádku** zobrazuje na hraničních zařízeních IoT zařízení, která mají hlášeny zpět do služby, moduly byla nasazena úspěšně. 
* **Není v pořádku** ukazuje na hraničních zařízeních IoT zařízení hlášeny zpět ke službě, že jedna nebo moduly nebyly nasazeny úspěšně. Aby to prověřili chybu, vzdálené připojení k těmto zařízením a zobrazit soubory protokolů.
* **Neznámý** zobrazuje zařízení, u kterých není nahlášená libovolný stav týkající se tohoto nasazení IoT Edge. Aby to prověřili, zobrazte služby informace a soubory protokolu.

## <a name="phased-rollout"></a>Postupné zavádění 

Postupné zavádění je celkový proces kterým operátor nasadí změny do otevření sadu zařízení IoT Edge. Cílem je, aby změny postupně, aby se snížilo riziko zpřístupnění celou škálovací rozbíjející změny.  

Postupné zavádění je proveden v následujících fází a kroků: 

1. Vytvořit testovací prostředí pro zařízení IoT Edge je zřizování a nastavením značku dvojčete zařízení jako `tag.environment='test'`. Testovací prostředí zrcadlit v provozním prostředí, nakonec se zaměří na nasazení. 
2. Vytvořte nasazení, včetně modulů a konfigurací. Cílení podmínka musí cílit na testovací prostředí pro zařízení IoT Edge.   
3. Ověření konfigurace nového modulu v testovacím prostředí.
4. Aktualizace nasazení, aby zahrnují podmnožinu produkční zařízení IoT Edge tak, že přidáte novou značku k cílení podmínku. Také se ujistěte, že priorita pro nasazení je vyšší než jiná nasazení aktuálně cílená na těchto zařízeních 
5. Ověřte, že nasazení bylo úspěšné na cílových zařízeních IoT tím, že zobrazíte stav nasazení.
6. Aktualizujte na cílit na všechny zbývající zařízení IoT Edge produkčního nasazení.

## <a name="rollback"></a>Vrácení zpět

Nasazení může být vrácena zpět, pokud se zobrazí chyby nebo chybné konfigurace.  Protože nasazení definuje konfiguraci absolutní modulu pro zařízení IoT Edge, další nasazení musí také cílit na stejné zařízení s nižší prioritou i v případě, že cílem je odebrat všechny moduly.  

Proveďte vrácení zpět v následujícím pořadí: 

1. Potvrďte, že druhé nasazení cílit také na stejnou sadu zařízení. Pokud je cílem vrácení změn odebrat všechny moduly, druhé nasazení by neměl obsahovat žádné moduly. 
2. Změnit nebo odebrat Cílový výraz podmínky nasazení, který chcete vrátit zpět tak, že zařízení už splňují cílovou podmínku.
3. Ověřte, že vrácení změn zobrazením stavu nasazení.
   * Vrátit zpět nasazení již musí zobrazit stav pro zařízení, které byly vráceny zpět.
   * Druhé nasazení by teď měl obsahovat stav nasazení pro zařízení, které byly vráceny zpět.


## <a name="next-steps"></a>Další postup

* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
* Další informace o dalších pojmech IoT Edge jako [modul runtime IoT Edge](iot-edge-runtime.md) a [moduly IoT Edge](iot-edge-modules.md).

