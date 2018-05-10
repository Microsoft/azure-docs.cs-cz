## <a name="automatic-device-management"></a>Správa automatického zařízení
Automatická správa zařízení ve službě Azure IoT Hub automatizuje řadu opakovaných a složitější úlohy správy loďstev velké zařízení přes celého jejich životních cyklů. S automatickou správu zařízení můžete cílí na skupiny zařízení, na základě jejich vlastností, zadejte požadované konfigurace a nechat IoT Hub, aktualizujte si zařízení vždy, když se do oboru.  Se skládá z [konfigurací automatické zařízení](/articles/iot-hub/iot-hub-auto-device-config.md) a [IoT Edge automatické nasazení](/articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge umožňuje řízené cloudové nasazení služby Azure a kódu pro konkrétní řešení pro místní zařízení. IoT hraniční zařízení můžete shromažďovat data z jiných zařízení a provádět výpočty a analýzy, předtím, než odešle data do cloudu. Další informace najdete v tématu [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Součást zodpovědná za nasazení a monitorování modulů IoT Edge runtime.

## <a name="iot-edge-device"></a>Zařízení IoT Edge
IoT hraniční zařízení mít IoT Edge nainstalován modul runtime a jsou označeny jako "IoT hraniční zařízení" v podrobností o zařízení. Zjistěte, jak [nasazení Azure IoT Edge v simulovaném zařízení v systému Linux – Náhled](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Okraj IoT automatického nasazení
Automatické nasazení IoT Edge nakonfiguruje cíl sadu IoT hraniční zařízení pro spuštění sady IoT Edge moduly. Každé nasazení nepřetržitě zajišťuje, aby všechna zařízení, které odpovídají jeho cílovou podmínku běží Zadaná sada modulů, i když nové zařízení se vytvoří nebo jsou upraveny tak, aby odpovídaly cílovou podmínku. Každé zařízení IoT Edge pouze obdrží nejvyšší prioritou nasazení jejichž cílovou podmínku splňuje. Další informace o [IoT okraj automatického nasazení](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Okraj IoT – manifest nasazení
Dokument Json obsahující informace, která se mají zkopírovat jeden nebo více IoT hraniční zařízení modulu twin(s) nasadit sadu moduly, trasy a přidružené modulu požadované vlastnosti.

## <a name="iot-edge-gateway-device"></a>Zařízení IoT hraniční brány
IoT hraniční zařízení s podřízené zařízení. Podřízené zařízení může být buď IoT Edge, nebo není IoT hraniční zařízení.

## <a name="iot-edge-hub"></a>Centra IoT Edge
Součást zodpovědná za modulu do modulu komunikace (směrem k IoT Hub) nadřazené a podřízené (mimo IoT Hub) IoT Edge runtime komunikace. 

## <a name="iot-edge-leaf-device"></a>Zařízení IoT okraj listu
IoT hraniční zařízení s žádné podřízené zařízení. 

## <a name="iot-edge-module"></a>Modul IoT Edge
Modul IoT Edge je kontejner Docker, kterou můžete nasadit do zařízení IoT okraj. Provádí konkrétní úlohy, jako je příjem zpráv ze zařízení, transformace zprávu nebo odesílání zprávy do služby IoT hub. Ho komunikuje s z ostatních modulů a odesílání dat do modulu runtime IoT okraj. [Pochopení požadavků a nástrojů pro vývoj modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Okraj IoT modulu identity
Záznam v registru identit služby IoT Hub modul s podrobnostmi o pověření existence a zabezpečení modul používaný k ověření s hraniční rozbočovače nebo IoT Hub.

## <a name="iot-edge-module-image"></a>Obrázek modulu IoT Edge
Docker obrázek, který je použit modulem runtime IoT Edge se vytvořit instanci modulu instancí.

## <a name="iot-edge-module-twin"></a>Okraj IoT modulu twin
Ve službě IoT Hub, která ukládá informace o stavu pro modul instance jako trvalý dokumentu Json.

## <a name="iot-edge-priority"></a>Priorita IoT Edge
Při dvou nasazeních IoT Edge cíle do stejného zařízení, získá použít nasazení s vyšší prioritou. Pokud má dvě nasazení se stejnou prioritou, získá použít nasazení s novější datum vytvoření. Další informace o [s prioritou](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge
Modul runtime IoT hraniční zahrnuje vše, co Microsoft distribuuje být nainstalovány na IoT hraniční zařízení. Zahrnutý Edge agenta, rozbočovače okraj a okraj CTL nástroj.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Nastavit hraniční IoT modulů na jedno zařízení
Operace, která zkopíruje obsah manifestu IoT Edge na jednom zařízení, dvojici modulu. Základní rozhraní API je obecný použít konfiguraci, což trvá jednoduše manifest IoT okraj jako vstup.

## <a name="iot-edge-target-condition"></a>Okraj IoT cílovou podmínku
V nasazení IoT okraj, je cílovou podmínku žádné Boolean podmínka značky dvojčata zařízení vyberte zařízení cíl nasazení, například "tag.environment = prod". Cílovou podmínku nepřetržitě vyhodnotí zahrnout všechny nová zařízení, které splňují požadavky nebo odeberte zařízení, které už provést. Další informace o [cíle stavu](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)