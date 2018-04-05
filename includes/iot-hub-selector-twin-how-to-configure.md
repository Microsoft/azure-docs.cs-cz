> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Úvod

V [začít pracovat s dvojčata zařízení IoT Hub][lnk-twin-tutorial], jste se dozvěděli, jak nastavit pomocí metadat zařízení *značky*. Přijaté podmínky zařízení z aplikace zařízení pomocí *hlášené vlastnosti*a pak tyto informace pomocí jazyka SQL jako dotaz.

Tento kurz popisuje, jak používat dvojče zařízení *potřeby vlastnosti* a *hlášené vlastnosti* pro vzdálenou konfiguraci aplikací pro zařízení. Hlášení a požadované vlastnosti v dvojče zařízení povolit několika kroky konfigurace aplikace zařízení a poskytují přehled o stavu tuto operaci na všech zařízeních. Můžete najít další informace o roli konfigurací zařízení v [přehled správy zařízení s centrem IoT][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Použití dvojčata zařízení na vysoké úrovni, umožňuje řešení back-endu zadejte požadovanou konfiguraci pro spravovaná zařízení, místo abyste odesílali určité příkazy. Zařízení má na starosti nastavení nejlepší způsob, jak aktualizaci konfigurace (důležité ve scénářích IoT, kde určité zařízení podmínky vliv na schopnost okamžitě provádět určité příkazy), při průběžně reporting aktuální stav a potenciální chybové stavy procesu aktualizace. Tento vzor je instrumentálního na správu velkých sad zařízení, protože nabízí back-end řešení úplný přehled o stavu procesu konfigurace ve všech zařízeních.

> [!TIP]
> Ve scénářích, kde jsou ovládaná zařízení více interaktivní způsobem (například zapnutí ventilátor z aplikace řízené uživatele), zvažte použití [přímé metody][lnk-methods].

V tomto kurzu back-end řešení změní konfiguraci telemetrie cílové zařízení tak, aby aplikace zařízení se vztahují aktualizaci konfigurace. Aktualizace konfigurace například by vyžadující restartování modulu software, který v tomto kurzu simuluje s jednoduché zpožděním.

Back-end řešení ukládá konfiguraci v požadované vlastnosti dvojče zařízení následujícím způsobem:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Vzhledem k tomu, že konfigurace může být složité objekty, jsou přiřazeny jedinečné ID (hodnoty hash nebo [identifikátory GUID][lnk-guid]).


Aplikace zařízení sestavy své aktuální konfiguraci zrcadlení požadovanou vlastnost **telemetryConfig** ve vlastnostech hlášené:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Poznámka: jak hlášení **telemetryConfig** má další vlastnost **stav**používané nahlásit stav procesu aktualizace konfigurace.

Po přijetí nové požadované konfigurace aplikace zařízení sestavy čekající konfigurace změnou stavu:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Potom později některé aplikace zařízení hlásí úspěch nebo selhání této operace aktualizací vlastnost. Back-end řešení můžete kdykoli dotaz na stav procesu konfigurace ve všech zařízeních.

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení, která přijímá aktualizace konfigurace z back-end řešení a hlásí víc aktualizací jako *hlášené vlastnosti* na konfiguraci proces aktualizovat.
* Vytvořte aplikaci back-end, která aktualizuje požadovanou konfiguraci zařízení a následně se dotazuje proces aktualizace konfigurace.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
