> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub trvá dvojče zařízení pro každé zařízení, která se k němu připojuje.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použijte dvojčata zařízení na:

* Ukládání metadat ze zařízení z back end vašeho řešení.
* Sestava aktuální informace o stavu, jako jsou k dispozici funkce a podmínky (třeba připojení metoda se používá) z vaší aplikací.
* Synchronizujte stav pracovních dlouho běžící (například aktualizací firmwaru a konfigurace) mezi aplikací zařízení a back-end aplikace.
* Dotaz na vaše zařízení metadata, konfigurace nebo stavu.

Dvojčata zařízení jsou navrženy pro synchronizaci a pro dotazování konfigurací zařízení a podmínky. Další informace o použití dvojčata zařízení naleznete v [pochopit dvojčata zařízení][lnk-twins].

Dvojčata zařízení se ukládají do služby IoT hub a obsahovat:

* *značky*, metadat zařízení přístupná jenom pro back-end řešení;
* *požadovaného vlastnosti*, objekty JSON upravitelnými řešení back end a lze zobrazit aplikace zařízení; a
* *hlášené vlastnosti*, objekty JSON upravitelnými aplikace zařízení a přečíst back-end řešení. Značky a vlastností nesmí obsahovat pole, ale mohou být vnořené objekty.

![][img-twin]

Kromě toho se můžete dotazovat dvojčata zařízení na základě výše uvedené dat back-end řešení.
Odkazovat na [pochopit dvojčata zařízení] [ lnk-twins] Další informace o dvojčata zařízení a [IoT Hub dotazovací jazyk] [ lnk-query] odkaz pro dotazování.


V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace back-end, který přidává *značky* dvojče zařízení a aplikaci simulovaného zařízení, která generuje sestavy jeho připojení kanálu jako *hlášené vlastnost* na dvojče zařízení.
* Dotaz na zařízení z back-end aplikace pomocí filtrů o vlastnostech dříve vytvořili a značky.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md