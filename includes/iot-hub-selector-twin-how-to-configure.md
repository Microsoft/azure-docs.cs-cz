> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Úvod

V [začít pracovat s dvojčata zařízení IoT Hub][lnk-twin-tutorial], jste se dozvěděli, jak nastavit metadat zařízení z back-end vašeho řešení pomocí *značky*, sestavy podmínky zařízení z aplikace na zařízení pomocí *hlášené vlastnosti*a tyto informace pomocí jazyka SQL jako dotaz.

V tomto kurzu zjistíte, jak používat dvojče zařízení *potřeby vlastnosti* spolu s *hlášené vlastnosti*, pro vzdálenou konfiguraci aplikací pro zařízení. Přesněji řečeno tento kurz ukazuje, jak dvojče zařízení hlášené a požadované vlastnosti povolte vícekrokový konfiguraci aplikace zařízení a poskytovat viditelnost na back-end řešení stavu této operace na všech zařízeních. Můžete najít další informace o roli konfigurací zařízení v [přehled správy zařízení s centrem IoT][lnk-dm-overview].

Použití dvojčata zařízení na vysoké úrovni, umožňuje řešení back-endu zadejte požadovanou konfiguraci pro spravovaná zařízení, místo abyste odesílali určité příkazy. Toto zařízení starosti nastavení nejlepší způsob, jak aktualizaci konfigurace (důležité ve scénářích IoT, kde určité zařízení podmínky vliv na schopnost okamžitě provádět určité příkazy), vloží při průběžně reporting na back-end řešení aktuální stav a potenciální chybové stavy procesu aktualizace. Tento vzor je instrumentálního na správu velkých sad zařízení, protože umožňuje back-end řešení tak, aby měl úplný přehled o stavu procesu konfigurace ve všech zařízeních.

> [!NOTE]
> Ve scénářích, kde jsou ovládaná zařízení více interaktivní způsobem (zapněte ventilátor z aplikace řízené uživatele), zvažte použití [přímé metody][lnk-methods].
> 
> 

V tomto kurzu back-end řešení změny konfigurace telemetrie cílové zařízení a v důsledku této, aplikace zařízení odpovídá několika krocích k instalaci aktualizace konfigurace (například nutnosti restartování modulu softwaru, které tento kurz simuluje s jednoduché zpožděním).

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

> [!NOTE]
> Vzhledem k tomu, že konfigurace může být složité objekty, jsou přiřazeny jedinečné ID (hodnoty hash nebo [identifikátory GUID][lnk-guid]) ke zjednodušení jejich porovnání.
> 
> 

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

Po přijetí nové požadované konfigurace aplikace zařízení sestavy čekající konfigurace změnou informace:

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

Potom později některé aplikace zařízení hlásí úspěch nebo selhání této operace aktualizací vlastnost výše.
Všimněte si, jak je možné, back-end řešení kdykoli dotaz na stav procesu konfigurace ve všech zařízeních.

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení, která přijímá aktualizace konfigurace z back-end řešení a hlásí víc aktualizací jako *hlášené vlastnosti* na konfiguraci proces aktualizovat.
* Vytvořte aplikaci back-end, která aktualizuje požadovanou konfiguraci zařízení a následně se dotazuje proces aktualizace konfigurace.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
