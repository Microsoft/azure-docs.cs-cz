> [!div class="op_single_selector"]
> * [Zařízení: Node.js Služba: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Zařízení: Node.js Služba: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [Zařízení: Služba C#: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Zařízení: Služba Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Zařízení: Služba Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Back-end aplikace můžete použít Azure IoT Hub primitiv, jako například [dvojče zařízení] [ lnk-devtwin] a [přímé metody][lnk-c2dmethod], abyste mohli vzdáleně spuštění a monitorování zařízení akce správy na zařízeních. Tento kurz ukazuje, jak back-end aplikace a aplikace na zařízení vzájemně spolupracují na zahájení a monitorování restartování vzdáleného zařízení pomocí služby IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Přímá metoda používají k navázání akce správy zařízení (například restartování, obnovení továrního nastavení a aktualizaci firmwaru) z back-end aplikace v cloudu. Zařízení je zodpovědná za:

* Zpracování metoda požadavek odeslaný ze služby IoT Hub.
* Probíhá inicializace odpovídající akci konkrétní zařízení na zařízení.
* Poskytuje stav aktualizací prostřednictvím *hlášené vlastnosti* do služby IoT Hub.

Back-end aplikačním v cloudu můžete použít ke spouštění dotazů twin zařízení hlásit průběh vaše akce správy zařízení.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
