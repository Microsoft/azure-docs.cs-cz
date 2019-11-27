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
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456728"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku

Zařízení Azure IoT Edge sledují [životní cyklus zařízení](../iot-hub/iot-hub-device-management-overview.md) , který se podobá jiným typům zařízení IoT:

1. Zřízení nových zařízení IoT Edge pomocí vytvoření bitové kopie zařízení v operačním systému a instalace [IoT Edge runtime](iot-edge-runtime.md).
2. Nakonfigurujte zařízení, aby spouštěla [IoT Edge moduly](iot-edge-modules.md), a pak Sledujte jejich stav. 
3. Nakonec vyřazení zařízení, když se nahradí nebo jsou zastaralé.  

Azure IoT Edge poskytuje dva způsoby, jak nakonfigurovat moduly, které se mají spouštět na zařízeních IoT Edge: jednu pro vývoj a rychlé iterace na jednom zařízení (použili jste tuto metodu v [kurzech](tutorial-deploy-function.md)Azure IoT Edge) a jednu pro správu velkých loďstev IoT Edge zařízení. Oba tyto přístupy jsou k dispozici na webu Azure Portal a prostřednictvím kódu programu. Pro cílové skupiny nebo velký počet zařízení můžete určit zařízení, do kterých chcete moduly nasadit, aby se pomocí [značek](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) v zařízení nezobrazovaly. Následující kroky mluvit o nasazení pro skupinu zařízení státu Washington, který je identifikován vlastnost tags. 

Tento článek se zaměřuje na konfiguraci a monitorování fáze pro flotily nebo zařízení, souhrnně označovány jako automatické nasazení IoT Edge. Celkový postup nasazení je následující: 

1. Operátor definuje nasazení, který popisuje sadu modulů, stejně jako cílová zařízení. Každé nasazení má manifest nasazení, který tyto informace odráží. 
2. Služba IoT Hub komunikuje se všechna cílová zařízení nakonfigurovat požadované moduly. 
3. Služba IoT Hub načítá stav ze zařízení IoT Edge a zpřístupní je operátor.  Například operátor může zobrazit, když hraniční zařízení není úspěšně nakonfigurováno nebo pokud modul během běhu selhává. 
4. V každém okamžiku nová zařízení IoT Edge, které splňují podmínky cílení jsou nakonfigurované pro nasazení. Například nasazení, které cílí na všechna IoT Edge zařízení ve státu Washington, po zřízení automaticky nakonfiguruje nové zařízení IoT Edge a přidá je do skupiny zařízení státu Washington. 
 
Tento článek popisuje jednotlivých komponent zahrnutých v konfiguraci a monitorování nasazení. Návod k vytvoření a aktualizaci nasazení najdete v tématu věnovaném [nasazení a sledování IoT Edgech modulů ve velkém měřítku](how-to-deploy-monitor.md).

## <a name="deployment"></a>Nasazení

Automatické nasazení IoT Edge přiřadí IoT Edge bitové kopie modulu spouštět jako instance na cílovou sadu zařízení IoT Edge. Funguje to tím, že nakonfigurujete manifest nasazení IoT Edge do seznamu modulů s odpovídající inicializační parametry pro zahrnutí. Nasazení se dá přiřadit k jednomu zařízení (na základě ID zařízení) nebo ke skupině zařízení (na základě značek). Jakmile zařízení IoT Edge obdrží manifest nasazení, stáhne a nainstaluje image kontejneru z příslušných úložišť kontejnerů a nakonfiguruje je odpovídajícím způsobem. Po vytvoření nasazení může operátor sledovat stav nasazení a zjistit, jestli jsou cílová zařízení správně nakonfigurovaná.

Nasazení můžete nakonfigurovat pouze zařízení IoT Edge. Následující požadavky musí být na zařízení, než může přijímat nasazení:

* Základní operační systém
* Systém správy kontejneru, jako je Moby nebo Dockeru
* Zřizování modul runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifest nasazení

Manifest nasazení je dokument JSON, který popisuje moduly, které chcete nakonfigurovat na cílovém zařízení IoT Edge. Obsahuje metadat konfigurace pro všechny moduly, včetně požadovaný systém modulů (konkrétně agenta IoT Edge a Centrum IoT Edge).  

Zahrnuje metadat konfigurace pro každý modul: 

* Version 
* Typ 
* Stav (například spuštěná nebo zastavená) 
* Zásady restartování 
* Image a container registry
* Trasy pro vstupní a výstupní data 

Pokud je bitové kopie modulu uložené v privátním registru kontejneru, agenta IoT Edge obsahuje přihlašovací údaje registru. 

### <a name="target-condition"></a>Cílová podmínka

Cílová podmínka se průběžně vyhodnocuje po celou dobu životnosti nasazení. Všechna nová zařízení, které splňují požadavky jsou zahrnuty, a odeberou se všechna existující zařízení, která už se. Nasazení se znovu aktivovat, pokud služba detekuje všechny změny stavu cílového. 

Například máte nasazení A s tags.environment podmínku cílové = 'prod'. Pokud zahájíte nasazení, se 10 zařízení produkčního prostředí. Moduly se úspěšně nainstaloval v těchto 10 zařízení. Stav agenta IoT Edge se zobrazuje jako 10 Celkový počet zařízení, 10 úspěšné odpovědi, 0 odpovědí na selhání a 0 odpovědi – čekání. Teď přidáte pět další zařízení s tags.environment = 'prod'. Služba zjistí změnu a stav agenta IoT Edge bude 15 celkový počet zařízení, 10 úspěšné odpovědi, 0 odpovědí na selhání a 5 odpovědi – čekání při pokusu o nasazení do pěti nových zařízení.

Vybrat cílová zařízení pomocí značky dvojčat zařízení nebo deviceId jakoukoli logickou podmínku. Pokud chcete použít podmínku with Tags, je nutné přidat "značky":{} oddíl v zařízení se musí vystavit na stejné úrovni jako vlastnosti. [Další informace o značkách v zařízení s dvojitou dvojicí](../iot-hub/iot-hub-devguide-device-twins.md)

Příklady podmínek cíl:

* deviceId = 'linuxprod1.
* Tags.Environment = 'prod'
* Tags.Environment = 'prod' AND tags.location = 'westus'
* Tags.Environment = 'prod' OR tags.location = 'westus'
* Tags.Operator = "Jan" a tags.environment = 'prod' není deviceId = 'linuxprod1.

Tady jsou některé omezí při konstrukci cílovou podmínku:

* Ve dvojčeti zařízení může vytvořit pouze cílovou podmínku pomocí značek nebo ID zařízení.
* Dvojité uvozovky nejsou povolené v každé části cílovou podmínku. Použijte jednoduché uvozovky.
* Jednoduché uvozovky představují hodnoty cílovou podmínku. Proto musíte před jednoduchá uvozovka s jinou jednoduché uvozovky, pokud je součástí názvu zařízení. Chcete-li například cílit na zařízení s názvem `operator'sDevice`, zapište `deviceId='operator''sDevice'`.
* V hodnotách cílové podmínky jsou povoleny číslice, písmena a následující znaky: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorita

Prioritu definuje, zda má být použita nasazení na cílovém zařízení vzhledem k jiné nasazení. Priorita nasazení je kladné celé číslo, s větší čísla, které označuje vyšší prioritu. Pokud zařízení IoT Edge je cílí více než jedno nasazení, použije se nasazení s nejvyšší prioritou.  Nasazení s nižšími prioritami se neaplikují, ani se nesloučí.  Pokud je zařízení cíleno na dvě nebo více nasazení se stejnou prioritou, platí poslední vytvořené nasazení (určené časovým razítkem vytvoření).

### <a name="labels"></a>Popisky 

Popisky jsou páry klíč/hodnota řetězce, které můžete použít k filtrování a skupiny nasazení. Nasazení může mít více popisků. Popisky jsou volitelné a proveďte žádný vliv skutečnou konfiguraci zařízení IoT Edge. 

### <a name="deployment-status"></a>Stav nasazení

Chcete-li zjistit, jestli se úspěšně použity pro všechny cílové zařízení IoT Edge se dá monitorovat nasazení.  Cílené hraniční zařízení se zobrazí v jedné nebo několika následujících kategoriích stavu: 

* **Cíl** zobrazuje IoT Edge zařízení, která se shodují s podmínkou cílení nasazení.
* **Skutečnost** zobrazuje cílené IoT Edge zařízení, na která necílí jiné nasazení s vyšší prioritou.
* **V pořádku** zobrazuje IoT Edge zařízení, která se vrátila ke službě, kterou moduly úspěšně nasadily. 
* **Stav** není v pořádku. zobrazuje IoT Edge zařízení oznámila službě, že jeden nebo moduly nebyly nasazeny úspěšně. Aby to prověřili chybu, vzdálené připojení k těmto zařízením a zobrazit soubory protokolů.
* **Neznámý** zobrazuje IoT Edge zařízení, která neohlásila žádný stav týkající se tohoto nasazení. Aby to prověřili, zobrazte služby informace a soubory protokolu.

## <a name="phased-rollout"></a>Postupné zavádění 

Postupné zavádění je celkový proces kterým operátor nasadí změny do otevření sadu zařízení IoT Edge. Cílem je, aby změny postupně, aby se snížilo riziko zpřístupnění celou škálovací rozbíjející změny.  

Postupné zavádění je proveden v následujících fází a kroků: 

1. Vytvořte testovací prostředí IoT Edge zařízení tím, že je zřídíte a nastavíte nestejnou značku zařízení jako `tag.environment='test'`. Testovací prostředí by mělo zrcadlit provozní prostředí, na které bude nasazení nakonec cílit. 
2. Vytvořte nasazení, včetně modulů a konfigurací. Cílení podmínka musí cílit na testovací prostředí pro zařízení IoT Edge.   
3. Ověření konfigurace nového modulu v testovacím prostředí.
4. Aktualizace nasazení, aby zahrnují podmnožinu produkční zařízení IoT Edge tak, že přidáte novou značku k cílení podmínku. Také se ujistěte, že priorita pro nasazení je vyšší než jiná nasazení aktuálně cílená na těchto zařízeních 
5. Ověřte, že nasazení bylo úspěšné na cílových zařízeních IoT tím, že zobrazíte stav nasazení.
6. Aktualizujte na cílit na všechny zbývající zařízení IoT Edge produkčního nasazení.

## <a name="rollback"></a>Vrácení zpět

Nasazení může být vrácena zpět, pokud se zobrazí chyby nebo chybné konfigurace.  Vzhledem k tomu, že nasazení definuje absolutní konfiguraci modulu pro IoT Edge zařízení, musí být další nasazení cíleno na stejné zařízení s nižší prioritou, i když je cílem odebrat všechny moduly.  

Proveďte vrácení zpět v následujícím pořadí: 

1. Potvrďte, že druhé nasazení cílit také na stejnou sadu zařízení. Pokud je cílem vrácení změn odebrat všechny moduly, druhé nasazení by neměl obsahovat žádné moduly. 
2. Změnit nebo odebrat Cílový výraz podmínky nasazení, který chcete vrátit zpět tak, že zařízení už splňují cílovou podmínku.
3. Ověřte, že vrácení změn zobrazením stavu nasazení.
   * Vrátit zpět nasazení již musí zobrazit stav pro zařízení, které byly vráceny zpět.
   * Druhé nasazení by teď měl obsahovat stav nasazení pro zařízení, které byly vráceny zpět.


## <a name="next-steps"></a>Další kroky

* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-monitor.md).
* Přečtěte si další informace o dalších IoT Edge konceptech, jako jsou moduly [runtime IoT Edge](iot-edge-runtime.md) a [IoT Edge](iot-edge-modules.md).

