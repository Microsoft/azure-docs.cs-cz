---
title: Kurz – export dat a vizualizace přehledů v Azure IoT Central
description: V tomto kurzu se dozvíte, jak exportovat data z IoT Central a vizualizovat přehledy na řídicím panelu Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022082"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Kurz: Export dat z Azure IoT Central a vizualizace přehledů v Power BI



Ve dvou předchozích kurzech jste vytvořili a přizpůsobili aplikaci IoT Central pomocí šablony **aplikace V obchodě - pokladna.** V tomto kurzu nakonfigurujete aplikaci IoT Central pro export telemetrie shromážděné ze zařízení. Potom pomocí Power BI vytvořte vlastní řídicí panel pro správce obchodu k vizualizaci přehledů odvozených z telemetrie.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nakonfigurujte aplikaci IoT Central pro export telemetrie do centra událostí.
> * Pomocí aplikací Logic Apps můžete odesílat data z centra událostí do datové sady streamování Power BI.
> * Vytvořte řídicí panel Power BI pro vizualizaci dat v datové sadě datových proudů.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Chcete-li dokončit předchozí dva kurzy, [vytvořte analytickou aplikaci v obchodě v Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) a [přizpůsobte řídicí panel operátora a spravujte zařízení v Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
* Účet Power BI. Pokud nemáte účet Power BI, zaregistrujte si [bezplatnou zkušební verzi Power BI Pro,](https://app.powerbi.com/signupredirect?pbi_source=web) než začnete.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením centra událostí a aplikace logiky je potřeba vytvořit skupinu prostředků, která je bude spravovat. Skupina prostředků by měla být ve stejném umístění jako **vaše analýza v obchodě – pokladna** aplikace IoT Central. Vytvoření skupiny prostředků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém navigačním panelu vyberte **položku Skupiny prostředků**. Pak vyberte **Přidat**.
1. V **části Předplatné**vyberte název předplatného Azure, které jste použili k vytvoření aplikace IoT Central.
1. Pro název **skupiny prostředků** zadejte _analýzu maloobchodu_*.
1. Pro **oblast**vyberte stejnou oblast, kterou jste zvolili pro aplikaci IoT Central.
1. Vyberte **zkontrolovat + vytvořit**.
1. Na stránce **Revize + Vytvoření** vyberte **Vytvořit**.

Nyní máte skupinu prostředků s názvem **analýza maloobchodu** v rámci předplatného.

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Před konfigurací maloobchodní monitorovací aplikace pro export telemetrie, je třeba vytvořit centrum událostí pro příjem exportovaných dat. Následující kroky ukazují, jak vytvořit centrum událostí:

1. Na webu Azure Portal vyberte **Vytvořit prostředek** v levém horním rohu obrazovky.
1. Ve **vyhledávání na marketplace**zadejte Centra _událostí_a stiskněte **Enter**.
1. Na stránce **Centra událostí** vyberte **Vytvořit**.
1. Na stránce **Vytvořit obor názvů** postupujte takto:
    * Zadejte jedinečný název oboru názvů, například _analýzu _retail-store - ._ Systém zkontroluje, zda je tento název k dispozici.
    * Zvolte **základní** cenovou úroveň.
    * Vyberte stejné **předplatné,** které jste použili k vytvoření aplikace IoT Central.
    * Vyberte skupinu prostředků **analýzy maloobchodu.**
    * Vyberte stejné umístění, které jste použili pro aplikaci IoT Central.
    * Vyberte **Vytvořit**. Bude pravděpodobně muset počkat několik minut pro systém zřídit prostředky.
1. Na portálu přejděte do skupiny prostředků **analýzy maloobchodního úložiště.** Počkejte, než se nasazení dokončí. Možná budete muset vybrat **Aktualizovat aktualizovat** stav nasazení. Můžete také zkontrolovat stav vytvoření oboru názvů centra událostí v **oznámeních**.
1. Ve skupině prostředků **analýzy maloobchodu** vyberte **obor názvů Centra událostí**. Na portálu se zobrazí domovská stránka oboru **názvů centra událostí.**

Teď máte **obor názvů Centra událostí**, můžete vytvořit **Centrum událostí,** které se bude používat s aplikací IoT Central:

1. Na domovské stránce oboru **názvů Centra událostí** na portálu vyberte + Event **Hub**.
1. Na stránce **Vytvořit centrum událostí** zadejte jako název _telemetrii úložiště_ a pak vyberte **Vytvořit**.

Nyní máte centrum událostí, které můžete použít při konfiguraci exportu dat z aplikace IoT Central:

![Centrum událostí](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Konfigurace exportu dat

Nyní máte centrum událostí, můžete nakonfigurovat **analýzu v obchodě - pokladní** aplikaci pro export telemetrie z připojených zařízení. Následující kroky ukazují, jak nakonfigurovat export:

1. Přihlaste se ke své **analýze v obchodě – pokladně** aplikace IoT Central.
1. V levém podokně vyberte **Export dat.**
1. Vyberte **Nová > Centra událostí Azure**.
1. Zadejte _export telemetrie_ jako **zobrazovaný název**.
1. Vyberte **obor názvů Event Hubs**.
1. Vyberte centrum událostí **telemetrie úložiště.**
1. Vypněte **zařízení** a **šablony zařízení** v části Data **k exportu.**
1. Vyberte **Uložit**.

Export dat může trvat několik minut, než se začne odesílat telemetrická data do centra událostí. Stav exportu můžete zobrazit na stránce **Export dat:**

![Konfigurace průběžného exportu dat](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Vytvoření datových sad Power BI

Na řídicím panelu Power BI se zobrazí data z vaší maloobchodní monitorovací aplikace. V tomto řešení použijete datové sady streamování Power BI jako zdroj dat pro řídicí panel Power BI. V této části definujete schéma datových sad datových proudů tak, aby aplikace logiky mohla předávat data z centra událostí. Následující kroky ukazují, jak vytvořit dvě datové sady datových proudů pro senzory prostředí a jednu datovou sadu datových souborů pro snímač obsazenosti:

1. Přihlaste se ke svému účtu **Power BI**.
1. Vyberte **Pracovní prostory**a pak **vyberte Vytvořit pracovní prostor**.
1. Na stránce **Vytvořit pracovní prostor** zadejte _analýzu v obchodě – pokladnu_ jako **název pracovního prostoru**.
1. Přejděte do dolní části stránky **Vítá vás analýza v obchodě – pokladna v pracovním prostoru** a vyberte **Přeskočit**.
1. Na stránce pracovního prostoru vyberte **Vytvořit > datové sady datových proudů**.
1. Na stránce **Nová datová sada datových proudů** zvolte **ROZHRANÍ API**a pak vyberte **Další**.
1. Jako název datové **sady**zadejte _snímač zóny 1_ .
1. Zadejte tři **hodnoty z datového proudu** v následující tabulce:

    | Název hodnoty  | Typ hodnoty |
    | ----------- | ---------- |
    | Časové razítko   | DateTime   |
    | Vlhkost    | Číslo     |
    | Teplota | Číslo     |

1. Zapnout **analýzu historických dat.**
1. Vyberte **Vytvořit** a potom **Hotovo**.
1. Vytvořte další streamovací datovou sadu nazvanou **Snímač zóny 2** se stejným schématem a nastavením jako datová sada datových **proudů dat ze snímače zóny 1.**

Nyní máte dvě datové sady datových proudů. Aplikace logiky bude směrovat telemetrii ze dvou senzorů prostředí připojených k **analýze v obchodě - pokladní** aplikace do těchto dvou datových sad:

![Datové sady zóny](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Toto řešení používá pro každý senzor jednu datovou sadu datových proudů, protože není možné aplikovat filtry na streamovaná data v Power BI.

Potřebujete také datovou sadu datových proudů pro telemetrii obsazenosti:

1. Na stránce pracovního prostoru vyberte **Vytvořit > datové sady datových proudů**.
1. Na stránce **Nová datová sada datových proudů** zvolte **ROZHRANÍ API**a pak vyberte **Další**.
1. Jako **název datové sady**zadejte snímač _obsazenosti_ .
1. Do následující tabulky zadejte pět **hodnot z datového proudu:**

    | Název hodnoty     | Typ hodnoty |
    | -------------- | ---------- |
    | Časové razítko      | DateTime   |
    | Délka fronty 1 | Číslo     |
    | Délka fronty 2 | Číslo     |
    | Doba přebývat 1   | Číslo     |
    | Doba přebytů 2   | Číslo     |

1. Zapnout **analýzu historických dat.**
1. Vyberte **Vytvořit** a potom **Hotovo**.

Nyní máte třetí datovou sadu datových proudů, která ukládá hodnoty ze simulovaného snímače obsazenosti. Tento senzor hlásí délku fronty u dvou poobchodů v obchodě a jak dlouho zákazníci čekají v těchto frontách:

![Soubor údajů o obsazenosti](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

V tomto řešení aplikace logiky čte telemetrie z centra událostí, analyzuje data a pak je odešle do datových sad streamování Power BI, které jste vytvořili.

Před vytvořením aplikace logiky potřebujete ID zařízení dvou senzorů RuuviTag, které jste připojili k aplikaci IoT Central v kurzu [Vytvořit analytickou aplikaci v obchodě v kurzu Azure IoT Central:](./tutorial-in-store-analytics-create-app-pnp.md)

1. Přihlaste se ke své **analýze v obchodě – pokladně** aplikace IoT Central.
1. V levém podokně vyberte **Zařízení.** Pak vyberte **RuuviTag**.
1. Poznamenejte si **ID zařízení**. Na následujícím snímku obrazovky jsou ID **f5dcf4ac32e8** a **e29ffc8d5326**:

    ![ID zařízení](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

Následující kroky ukazují, jak vytvořit aplikaci logiky na webu Azure Portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a v levém horním rohu obrazovky vyberte **Vytvořit prostředek.**
1. Ve **vyhledávání na marketplace**zadejte _Aplikaci logiky_a stiskněte **Enter**.
1. Na stránce **Aplikace logiky** vyberte **Vytvořit**.
1. Na stránce **vytvoření aplikace logiky:**
    * Zadejte jedinečný název aplikace logiky, jako je _vaše jméno-maloobchodní-store-analýza_.
    * Vyberte stejné **předplatné,** které jste použili k vytvoření aplikace IoT Central.
    * Vyberte skupinu prostředků **analýzy maloobchodu.**
    * Vyberte stejné umístění, které jste použili pro aplikaci IoT Central.
    * Vyberte **Vytvořit**. Bude pravděpodobně muset počkat několik minut pro systém zřídit prostředky.
1. Na webu Azure Portal přejděte do nové aplikace logiky.
1. Na stránce **Návrhář eaplikací logiky** přejděte dolů a vyberte **Blank Logic App**.
1. V **polechy Hledat a aktivační události**zadejte Centra _událostí_.
1. V **seznamu Aktivační události**vyberte možnost Kdy jsou události dostupné v centru **událostí**.
1. Zadejte _telemetrii Store_ jako **název připojení**a vyberte obor názvů **Centra událostí**.
1. Vyberte zásadu **RootManageSharedAccess** a vyberte **Vytvořit**.
1. V akci Když jsou události k dispozici v centru **událostí:**
    * V **názvu centra událostí**vyberte **telemetrii úložiště**.
    * V **části Typ obsahu**vyberte **položku Application/json**.
    * Nastavte **interval** na tři a **frekvenci** na sekundy
1. Vyberte **Uložit,** chcete-li aplikaci logiky uložit.

Chcete-li přidat logiku do návrhu aplikace **logiky,** vyberte zobrazení kódu :

1. Nahraďte `"actions": {},` následujícím jsonem. Nahraďte dva `[YOUR RUUVITAG DEVICE ID 1]` `[YOUR RUUVITAG DEVICE ID 2]` zástupné symboly a idy, která jste si všimli ze svých dvou zařízení RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Vyberte **Uložit** a pak vyberte **Návrhář,** abyste viděli vizuální verzi přidané logiky:

    ![Návrh aplikace logiky](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Chcete-li akci rozbalit, vyberte **Přepnout podle ID** zařízení. Pak vyberte **prostředí zóny 1**a vyberte Přidat **akci**.
1. V **polem Hledat konektory a akce**zadejte Power **BI**a stiskněte **Enter**.
1. Vyberte akci Přidat řádky do akce **datové sady (náhledu).**
1. Vyberte **Přihlásit se** a postupujte podle pokynů k přihlášení k účtu Power BI.
1. Po dokončení procesu přihlášení v akci **Přidat řádky do datové sady:**
    * Vyberte **analýzy v obchodě – pokladna** jako pracovní prostor.
    * Jako datovou sadu vyberte **snímač zóny 1.**
    * Jako tabulku vyberte **RealTimeData.**
    * Vyberte **Přidat nový parametr** a pak vyberte pole Časové **razítko**, **Vlhkost**a **Teplota.**
    * Vyberte pole **Časové razítko** a pak vyberte **x-opt-queuedtime** ze seznamu **Dynamický obsah.**
    * Vyberte pole **Vlhkost** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Poté zvolte **vlhkost**.
    * Vyberte pole **Teplota** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Poté zvolte **teplotu**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **prostředí zóny 1** vypadá jako ![následující snímek obrazovky: Prostředí zóny 1](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Vyberte akci **prostředí zóny 2** a vyberte Přidat **akci**.
1. V **polem Hledat konektory a akce**zadejte Power **BI**a stiskněte **Enter**.
1. Vyberte akci Přidat řádky do akce **datové sady (náhledu).**
1. V akci **Přidat řádky do datové sady 2:**
    * Vyberte **analýzy v obchodě – pokladna** jako pracovní prostor.
    * Jako datovou sadu vyberte **snímač zóny 2.**
    * Jako tabulku vyberte **RealTimeData.**
    * Vyberte **Přidat nový parametr** a pak vyberte pole Časové **razítko**, **Vlhkost**a **Teplota.**
    * Vyberte pole **Časové razítko** a pak vyberte **x-opt-queuedtime** ze seznamu **Dynamický obsah.**
    * Vyberte pole **Vlhkost** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Poté zvolte **vlhkost**.
    * Vyberte pole **Teplota** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Poté zvolte **teplotu**.
    Vyberte **Uložit** a uložte tak provedené změny.  Akce **prostředí Zóny 2** vypadá jako ![následující snímek obrazovky: Prostředí zóny 2](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Vyberte akci **Obsazení** a pak vyberte akci **Přepnout podle ID rozhraní.**
1. Vyberte akci **rozhraní Přebývat čas** a vyberte **Přidat akci**.
1. V **polem Hledat konektory a akce**zadejte Power **BI**a stiskněte **Enter**.
1. Vyberte akci Přidat řádky do akce **datové sady (náhledu).**
1. V akci **Přidat řádky do datové sady:**
    * Vyberte **analýzy v obchodě – pokladna** jako pracovní prostor.
    * Jako datovou sadu vyberte **snímač obsazenosti.**
    * Jako tabulku vyberte **RealTimeData.**
    * Vyberte **Přidat nový parametr** a pak vyberte pole Časové **razítko**, **Doba přepzení 1**a Doba **přepychu 2.**
    * Vyberte pole **Časové razítko** a pak vyberte **x-opt-queuedtime** ze seznamu **Dynamický obsah.**
    * Vyberte pole **Doba přepů 1** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Pak vyberte **DwellTime1**.
    * Vyberte pole **Doba přepů 2** a pak vyberte **Zobrazit další** vedle **položky Analyzovat telemetrii**. Pak vyberte **DwellTime2**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **rozhraní Doba přebývat** vypadá jako ![následující snímek obrazovky: Akce obsazenosti](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Vyberte akci **Rozhraní Počet osob** a vyberte Přidat **akci**.
1. V **polem Hledat konektory a akce**zadejte Power **BI**a stiskněte **Enter**.
1. Vyberte akci Přidat řádky do akce **datové sady (náhledu).**
1. V akci **Přidat řádky do datové sady:**
    * Vyberte **analýzy v obchodě – pokladna** jako pracovní prostor.
    * Jako datovou sadu vyberte **snímač obsazenosti.**
    * Jako tabulku vyberte **RealTimeData.**
    * Vyberte **Přidat nový parametr** a pak vyberte pole Časové **razítko**, **Délka fronty 1**a Délka fronty **2.**
    * Vyberte pole **Časové razítko** a pak vyberte **x-opt-queuedtime** ze seznamu **Dynamický obsah.**
    * Vyberte pole **Délka fronty 1** a pak vyberte Zobrazit **další** vedle **položky Analyzovat telemetrii**. Pak vyberte **count1**.
    * Vyberte pole **Délka fronty 2** a pak vyberte Zobrazit **další** vedle **položky Analyzovat telemetrii**. Pak vyberte **count2**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **rozhraní Počet lidí** vypadá jako ![následující snímek obrazovky: Akce obsazenosti](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

Aplikace logiky se spustí automaticky. Pokud chcete zobrazit stav každého spuštění, přejděte na stránku **Přehled** aplikace logiky na webu Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Vytvoření řídicího panelu Power BI

Teď máte telemetrická data z vaší aplikace IoT Central prostřednictvím centra událostí. Pak vaše aplikace logiky analyzuje zprávy centra událostí a přidá je do datové sady streamování Power BI. Teď můžete vytvořit řídicí panel Power BI pro vizualizaci telemetrie:

1. Přihlaste se ke svému účtu **Power BI**.
1. Vyberte **pracovní prostory > analýzy v obchodě – pokladna**.
1. Vyberte **vytvořit > řídicí panel**.
1. Jako název řídicího panelu zadejte **analýzy obchodu** a vyberte **Vytvořit**.

### <a name="add-line-charts"></a>Přidání spojnicových grafů

Přidejte čtyři dlaždice spojnicových grafů, které zobrazí teplotu a vlhkost ze dvou senzorů prostředí. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte výběrem **... (Další možnosti) > Přidat dlaždici**. Vyberte **Vlastní streamovaná data**a pak vyberte **Další**:

| Nastavení | #1 grafu | #2 grafu | #3 grafu | #4 grafu |
| ------- | -------- | -------- | -------- | -------- |
| Datová sada | Senzor zóny 1 | Senzor zóny 1 | Senzor zóny 2 | Senzor zóny 2 |
| Typ vizualizace | Spojnicový graf | Spojnicový graf | Spojnicový graf | Spojnicový graf |
| Osa | Časové razítko | Časové razítko | Časové razítko | Časové razítko |
| Hodnoty | Teplota | Vlhkost | Teplota | Vlhkost |
| Časové okno | 60 minut | 60 minut | 60 minut | 60 minut |
| Nadpis | Teplota (1 hodina) | Vlhkost vzduchu (1 hodina) | Teplota (1 hodina) | Vlhkost vzduchu (1 hodina) |
| Podnadpis | Zóna 1 | Zóna 1 | Zóna 2 | Zóna 2 |

Následující snímek obrazovky ukazuje nastavení pro první graf:

![Nastavení čárového grafu](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Přidání karet pro zobrazení dat prostředí

Přidejte čtyři dlaždice karet, které zobrazují nejnovější hodnoty teploty a vlhkosti ze dvou senzorů prostředí. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte výběrem **... (Další možnosti) > Přidat dlaždici**. Vyberte **Vlastní streamovaná data**a pak vyberte **Další**:

| Nastavení | #1 karet | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Datová sada | Senzor zóny 1 | Senzor zóny 1 | Senzor zóny 2 | Senzor zóny 2 |
| Typ vizualizace | Karta | Karta | Karta | Karta |
| Fields (Pole) | Teplota | Vlhkost | Teplota | Vlhkost |
| Nadpis | Teplota (F) | Humidity (%) | Teplota (F) | Humidity (%) |
| Podnadpis | Zóna 1 | Zóna 1 | Zóna 2 | Zóna 2 |

Následující snímek obrazovky ukazuje nastavení pro první kartu:

![Nastavení karty](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Přidání dlaždic pro zobrazení dat o obsazenosti pokladny

Přidejte čtyři dlaždice karet, které zobrazí délku fronty a dobu prodlouží ní dvě pokladny v obchodě. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte výběrem **... (Další možnosti) > Přidat dlaždici**. Vyberte **Vlastní streamovaná data**a pak vyberte **Další**:

| Nastavení | #1 karet | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Datová sada | Snímač obsazenosti | Snímač obsazenosti | Snímač obsazenosti | Snímač obsazenosti |
| Typ vizualizace | Skupinový sloupcový graf | Skupinový sloupcový graf | Měřidlo | Měřidlo |
| Osa    | Časové razítko | Časové razítko | Není dostupné. | Není dostupné. |
| Hodnota | Doba přebývat 1 | Doba přebytů 2 | Délka fronty 1 | Délka fronty 2 |
| Časové okno | 60 minut | 60 minut |  Není dostupné. | Není dostupné. |
| Nadpis | Doba přebývat | Doba přebývat | Délka fronty | Délka fronty |
| Podnadpis | Pokladna 1 | Pokladna 2 | Pokladna 1 | Pokladna 2 |

Změňte velikost a změňte uspořádání dlaždic na řídicím panelu tak, aby vypadaly jako následující snímek obrazovky:

![Řídicí panel Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

Můžete přidat některé další grafické prostředky pro další přizpůsobení řídicího panelu:

![Řídicí panel Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili s aplikací IoT Central, můžete ji odstranit po přihlášení k aplikaci a přechodem na stránku **Nastavení aplikace** v části **Správa.**

Pokud chcete zachovat aplikaci, ale snížit náklady s ní spojené, zakažte export dat, který odesílá telemetrická data do centra událostí.

Centrum událostí a aplikaci logiky můžete odstranit na webu Azure Portal odstraněním skupiny prostředků nazývané **analýza maloobchodu**.

Datové sady a řídicí panel Power BI můžete odstranit odstraněním pracovního prostoru ze stránky Nastavení Power BI pro pracovní prostor.

## <a name="next-steps"></a>Další kroky

Tyto tři kurzy vám ukázaly komplexní řešení, které používá **šablonu aplikace In-Store Analytics - checkout** IoT Central. Připojili jste zařízení k aplikaci, použili jste IoT Central k monitorování zařízení a pomocí Power BI jste vytvořili řídicí panel pro zobrazení přehledů z telemetrie zařízení. Doporučeným dalším krokem je prozkoumat jednu z dalších šablon aplikací IoT Central:

> [!div class="nextstepaction"]
> * [Sestavování řešení pro energetiku s využitím služby IoT Central](../energy/overview-iot-central-energy.md)
> * [Vytváření řešení pro státní správu s využitím služby IoT Central](../government/overview-iot-central-government.md)
> * [Sestavování řešení pro zdravotnictví s využitím služby IoT Central](../healthcare/overview-iot-central-healthcare.md)
