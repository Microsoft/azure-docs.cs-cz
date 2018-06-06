---
title: Nasazení moduly pro hraniční Azure IoT | Microsoft Docs
description: Další informace o získání nasazení modulů do hraniční zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 880a17b6029dafec9ed41e3a32802dc42b872e77
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725322"
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Pochopení IoT Edge nasazení jednoho zařízení nebo ve velkém měřítku – náhled

Postupujte podle Azure IoT hraniční zařízení [životní cyklus zařízení] [ lnk-lifecycle] podobném na jiné typy zařízení IoT:

1. Zřízení zařízení IoT okraj, který zahrnuje imaging zařízení s operační systém a instalaci [IoT Edge runtime][lnk-runtime].
1. Zařízení jsou nakonfigurovány na spuštění [IoT Edge moduly][lnk-modules]a pak monitorovat stav. 
1. Zařízení navíc nemusí být dispozici, pokud jsou nahrazena nebo jsou zastaralé.  

Azure IoT Edge nabízí dva způsoby, jak konfigurovat moduly, které ke spuštění na IoT hraniční zařízení: jeden pro vývoj a rychlé opakování na jednom zařízení (který jste použili v kurzů k Azure IoT Edge) a jeden pro správu velké loďstev IoT hraniční zařízení. Oba tyto přístupy jsou k dispozici na webu Azure Portal a programově.

Tento článek se zaměřuje na konfiguraci a monitorování fázích pro loďstva zařízení, souhrnně označovány jako IoT okraj automatického nasazení. Celkové kroky nasazení jsou následující:   

1. Operátor definuje nasazení, který popisuje sadu moduly, jakož i cílovým zařízením. Každé nasazení má manifestu nasazení, který zobrazuje tyto informace. 
1. Služba IoT Hub komunikuje se všechna cílová zařízení je nakonfigurovat s požadované moduly. 
1. Služba IoT Hub načte stav ze zařízení IoT okraj a poskytuje pro operátor monitorování.  Například operátor můžete zobrazit, když není hraniční zařízení byl úspěšně nakonfigurován nebo pokud modul nezdaří za běhu. 
1. V každém okamžiku nové IoT hraniční zařízení, které splňují podmínky cílení nakonfigurovány pro nasazení. Například nasazení, které cílí všechna zařízení IoT Edge ve státě Washington automaticky nakonfiguruje nové zařízení IoT Edge po zřízení a přidat do skupiny zařízení, Washington stavu. 
 
Tento článek vás provede jednotlivé komponenty zahrnutých v konfiguraci a monitorování nasazení. Podrobný postup vytváření a aktualizace pro nasazení najdete v části [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-howto].

## <a name="deployment"></a>Nasazení

Automatické nasazení IoT Edge přiřadí IoT Edge modulu obrázky ke spuštění jako instance v cílové sadě IoT hraniční zařízení. Funguje díky konfiguraci manifest nasazení IoT Edge zahrnout seznam modulů s odpovídající inicializační parametry. Nasazení lze přiřadit na jedno zařízení (obvykle podle Id zařízení) nebo pro skupinu zařízení (podle značek). Jakmile IoT hraniční zařízení obdrží manifest nasazení, stáhne a nainstaluje modul kontejneru Image z příslušného kontejneru úložiště a nakonfiguruje je odpovídajícím způsobem. Po vytvoření nasazení operátor můžete sledovat stav nasazení zobrazíte, zda jsou správně nakonfigurovány cílová zařízení.   

Zařízení musí být zřízená jako IoT hraniční zařízení konfigurovat pomocí nasazení. Následující předpoklady a nejsou zahrnuté v nasazení:
* Základní operační systém
* Docker 
* Zřizování modulu runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifest nasazení

Manifest nasazení je dokument JSON, který popisuje moduly nakonfigurovat na cílovém zařízení IoT okraj. Obsahuje metadata konfigurace pro všechny moduly, včetně požadované systémové moduly (konkrétně agenta IoT okraj a okraj IoT hub).  

Konfigurace metadata pro každý modul zahrnují: 
* Verze 
* Typ 
* Stav (např. spuštění nebo zastaveno) 
* Znovu spusťte zásad 
* Bitové kopie a kontejneru úložiště 
* Trasy pro data vstup a výstup 

### <a name="target-condition"></a>Cílovou podmínku

Cílovou podmínku nepřetržitě vyhodnotí zahrnout všechny nová zařízení, které splňují požadavky nebo odeberte zařízení, které už se provést prostřednictvím doba životnosti nasazení. Nasazení bude znovu aktivovat, pokud služba zjistí všechny změny stavu cíl. Například máte nasazení A, který má na cílový stav tags.environment = 'produkčního'. Pokud ji nasazení, neexistují 10 produkčnímu zařízení. Moduly jsou úspěšně nainstalováni v těchto 10 zařízení. Stav agenta Edge IoT se zobrazí jako 10 Celkový počet zařízení, 10 úspěšně odpovědi, 0 selhání odpovědí a 0 čekající odpovědi. Nyní přidáte 5 Další zařízení, s tags.environment = 'produkčního'. Služba zjistí změnu a stav agenta na IoT Edge stane 15 celkový počet zařízení, 10 úspěšně odpovědi, 0 selhání odpovědí a 5 čekající odpovědi při pokusu o nasazení na pět nových zařízení.

Všechny logické podmínku můžete použijte na značky dvojčata zařízení nebo deviceId k výběru cílových zařízení. Pokud chcete použít podmínku s značkami, budete muset přidat "značky":{} část v dvojče zařízení v rámci stejné úrovni jako vlastnosti. [Další informace o značkách v dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md)

Příklady podmínek cíl:
* deviceId = 'linuxprod1.
* Tags.Environment = 'produkčního.
* Tags.Environment = produkčního a tags.location = 'westus.
* Tags.Environment = produkčního nebo tags.location = 'westus.
* Tags.Operator = "Jan" a tags.environment = produkčního není deviceId = 'linuxprod1.

Tady jsou některé omezí při vytváření cílovou podmínku:

* V dvojče zařízení může vytvořit pouze cílovou podmínku pomocí značky nebo ID zařízení.
* Dvojité uvozovky nejsou povoleny v jakékoli její části cílovou podmínku. Použijte prosím jednoduché uvozovky.
* Jednoduchých uvozovek a být představují hodnoty cílovou podmínku. Pokud je součástí názvu zařízení, proto musí vyhnuli jednoduchých uvozovkách s jinou jednoduchých uvozovkách. Například cílovou podmínku pro: operator'sDevice by bylo potřeba zapsat jako deviceId = "operátor" sDevice'.
* Čísla, písmena a následující znaky jsou povoleny v cílové podmínku values:-:.+%_#*? (),=@;$

### <a name="priority"></a>Priorita

Prioritu definuje, zda má být použita nasazení do cílových zařízení relativně k další nasazení. Prioritu nasazení je kladné celé číslo, s vyšší číslo, které označuje, s vyšší prioritou. Pokud IoT hraniční zařízení je cílem víc než jedno nasazení, použije se nasazení s nejvyšší prioritou.  Nasazení s nižší prioritou se nepoužívají, ani se sloučení.  Pokud zařízení je cílem dvě nebo víc nasazení se stejnou prioritou, použije se nedávno vytvořených nasazení (určené časové razítko vytvoření).

### <a name="labels"></a>Popisky 

Popisky jsou páry klíč – hodnota řetězce, které můžete použít filtr a skupiny nasazení. Nasazení může mít několik popisky. Popisky jsou volitelné a proveďte konfiguraci skutečné IoT hraniční zařízení žádný vliv. 

### <a name="deployment-status"></a>Stav nasazení

Nasazení se dá sledovat k určení, zda je použito úspěšně u všech cílových zařízení IoT okraj.  Cílové hraniční zařízení se zobrazí v jedné nebo více z následujících kategorií stavu: 
* **Cíl** zobrazuje IoT hraniční zařízení, která odpovídají nasazení cílení na podmínku.
* **Skutečné** zobrazuje cílové IoT hraniční zařízení, která nejsou cílem jiné nasazení s vyšší prioritou.
* **V pořádku** zobrazuje IoT hraniční zařízení, která hlásili zpět ke službě moduly mají byla úspěšně nasazena. 
* **Není v pořádku** zobrazuje IoT hraniční zařízení hlásili zpět ke službě, že jedna nebo moduly nebyly nasazeny úspěšně. K hlubšímu prošetření chyba, vzdálené připojení k této zařízení a zobrazit soubory protokolů.
* **Neznámý** zobrazuje IoT hraniční zařízení, která nebyla nahlášena žádné stav toto nasazení, která se týkají. Chcete-li podrobněji, zobrazte služby informace a soubory protokolu.

## <a name="phased-rollout"></a>Postupné zavádění 

Postupné zavádění je celého procesu, při němž operátor nasadí změny k otevření sadě IoT hraniční zařízení. Cílem je provést změny postupně snižte riziko provádění široké škálování nejnovější změny.  

Postupné zavádění se spouštějí v následujících fází a kroků: 
1. Vytvoření testovacího prostředí IoT hraniční zařízení je zřizování a nastavením značku zařízení twin jako `tag.environment='test'`. Testovací prostředí by měl zrcadlení provozním prostředí, které bude nakonec cíle nasazení. 
1. Vytvoření nasazení, včetně požadované modulů a konfigurací. Cílem testovací prostředí IoT hraniční zařízení by měl být cílení podmínku.   
1. Ověření konfigurace nového modulu v testovacím prostředí.
1. Aktualizace nasazení obsahovat podmnožinu produkční IoT hraniční zařízení tak, že přidáte novou značku cílení podmínky. Také zkontrolujte, zda je vyšší než ostatní nasazení aktuálně cílená na těchto zařízeních prioritu pro nasazení 
1. Ověřte, že nasazení proběhlo úspěšně na cílovém zařízení IoT zobrazením stavu nasazení.
1. Aktualizace nasazení pro všechny zbývající produkční IoT hraniční zařízení.

## <a name="rollback"></a>Vrácení zpět

Nasazení může být vrácena v případě chyby nebo nesprávné konfigurace.  Vzhledem k tomu, že nasazení definuje konfiguraci absolutní modulu pro IoT hraničním zařízením, další nasazení musí cílit také na stejné zařízení s nižší prioritou i v případě, že cílem je odebrat všechny moduly.  

Proveďte odvolání v tomto pořadí: 
1. Potvrďte, že druhé nasazení je také napojeno na stejnou sadu zařízení. Pokud je cílem vrácení zpět je odebrat všechny moduly, druhé nasazení nesmí obsahovat žádné moduly. 
1. Změnit nebo odebrat podmínku Cílový výraz, který chcete vrátit zpět, aby zařízení již nesplňují podmínku zaměřená na nasazení.
1. Ověřte, že vrácení zpět byla úspěšná zobrazením stavu nasazení.
   * Vrátit zpět nasazení již musí zobrazit stav pro zařízení, které byly vráceny zpět.
   * Druhé nasazení má nyní zahrnují stav nasazení pro zařízení, které byly vráceny zpět.


## <a name="next-steps"></a>Další postup

* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-howto].
* Další informace o dalších konceptech IoT okraj jako [IoT Edge runtime] [ lnk-runtime] a [IoT Edge moduly][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md
