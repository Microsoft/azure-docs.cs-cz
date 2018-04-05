> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-direct-methods.md)

Azure IoT Hub je plně spravovaná služba, která umožňuje spolehlivou a zabezpečené obousměrnou komunikaci mezi miliony zařízení a řešením back-end. Předchozí kurzy ([Začínáme se službou IoT Hub] a [odesílání zpráv typu Cloud-zařízení s centrem IoT]) ilustrují základních funkcí zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub. Centrum IoT také vám dává možnost volat metody netrvalý na zařízení z cloudu. Přímé metody představuje požadavek odpověď interakci s zařízení, která se podobá volání HTTPS v, které budou úspěch nebo neúspěch okamžitě (po časový limit definované uživatelem), aby mohl uživatel znát stav volání. [Volání metody přímé na zařízení] [ lnk-devguide-methods] popisuje přímé metody podrobněji a nabízí informace o tom, kdy použít přímé metody místo zprávy typu cloud zařízení nebo požadované vlastnosti.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použití portálu Azure k vytvoření služby IoT hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která má přímý metodu, která je možné volat v cloudu.
* Vytvořte konzolovou aplikaci, která volá metodu přímé v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[odesílání zpráv typu Cloud-zařízení s centrem IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Začínáme se službou IoT Hub]: ../articles/iot-hub/iot-hub-node-node-getstarted.md