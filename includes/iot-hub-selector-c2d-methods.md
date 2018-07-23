> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/quickstart-control-device-node.md)
> * [C#](../articles/iot-hub/quickstart-control-device-dotnet.md)
> * [Java](../articles/iot-hub/quickstart-control-device-java.md)
> * [Python](../articles/iot-hub/quickstart-control-device-python.md)

Azure IoT Hub je plně spravovaná služba, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a řešením back-endu. Předchozí kurzy ([Začínáme se službou IoT Hub] a [Odesílání zpráv typu Cloud-zařízení pomocí služby IoT Hub]) ilustrují základních funkcí zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub. IoT Hub také dává možnost volat metody netrvalý na zařízení z cloudu. Přímé metody představují požadavek odpověď interakce se zařízením podobný volání rozhraní HTTPS, úspěch nebo selhání okamžitě (po uživatelem zadaný časový), umožníte uživateli zjistit stav volání. Tento článek [vyvolání přímé metody v zařízení] [ lnk-devguide-methods] přímých metod podrobněji popisuje a nabízí informace o tom, kdy použití přímých metod namísto zprávy typu cloud zařízení nebo požadované vlastnosti.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která má přímé metody, které je možné vyvolat v cloudu.
* Vytvoření konzolové aplikace, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Odesílání zpráv typu Cloud-zařízení pomocí služby IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Začínáme se službou IoT Hub]: ../articles/iot-hub/quickstart-send-telemetry-node.md