---
title: Kurz – export dat a vizualizace přehledů v Azure IoT Central
description: V tomto kurzu se naučíte exportovat data z IoT Central a vizualizovat přehledy na řídicím panelu Power BI.
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
ms.openlocfilehash: f00448f19cc0a2118477a9527005548fea25537e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187268"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Kurz: Export dat z Azure IoT Central a vizualizace Insights v Power BI



V obou předchozích kurzech jste vytvořili a přizpůsobili IoT Central aplikaci pomocí šablony aplikace v rámci služby **Store Analytics** . V tomto kurzu nakonfigurujete IoT Central aplikaci pro export telemetrie shromážděných ze zařízení. Pak použijete Power BI k vytvoření vlastního řídicího panelu pro správce úložiště, který bude vizualizovat přehledy odvozené z telemetrie.

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Nakonfigurujte aplikaci IoT Central pro export telemetrie do centra událostí.
> * Pomocí Logic Apps můžete odesílat data z centra událostí do datové sady streamování Power BI.
> * Vytvořte řídicí panel Power BI pro vizualizaci dat v datové sadě streamování.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Pokud chcete dokončit předchozí dva kurzy, [vytvořte v Azure aplikaci pro analýzu v obchodě IoT Central](./tutorial-in-store-analytics-create-app.md) a [Přizpůsobte řídicí panel operátora a spravujte zařízení ve službě Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.
* Účet Power BI. Pokud nemáte účet Power BI, zaregistrujte si [bezplatnou zkušební verzi Power BI pro](https://app.powerbi.com/signupredirect?pbi_source=web) , než začnete.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením centra událostí a aplikace logiky je potřeba vytvořit skupinu prostředků pro jejich správu. Skupina prostředků by měla být ve stejném umístění jako vaše aplikace IoT Central pro **analýzu v rámci služby Store** . Vytvoření skupiny prostředků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **skupiny prostředků**. Pak vyberte **Přidat**.
1. V poli **předplatné** vyberte název předplatného Azure, které jste použili k vytvoření aplikace IoT Central.
1. Jako název **skupiny prostředků** zadejte _Retail-Store-Analysis_*.
1. V poli **oblast** vyberte stejnou oblast, kterou jste zvolili pro IoT Central aplikaci.
1. Vyberte **zkontrolovat + vytvořit**.
1. Na stránce **Revize + vytvořit** vyberte **vytvořit**.

Teď máte v předplatném skupinu prostředků s názvem **Retail-Store-Analysis** .

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Předtím, než budete moci nakonfigurovat aplikaci maloobchodního monitorování pro export telemetrie, je nutné vytvořit centrum událostí pro příjem exportovaných dat. Následující kroky ukazují, jak vytvořit centrum událostí:

1. V Azure Portal v levém horním rohu obrazovky vyberte **vytvořit prostředek** .
1. V **části Hledat na Marketplace** zadejte _Event Hubs_ a potom stiskněte klávesu **ENTER**.
1. Na stránce **Event Hubs** vyberte **vytvořit**.
1. Na stránce **vytvořit obor názvů** proveďte následující kroky:
    * Zadejte jedinečný název oboru názvů, jako je například _Your-Retail-Store-Analysis_. Systém zkontroluje, zda je tento název k dispozici.
    * Vyberte cenovou úroveň **Basic** .
    * Vyberte stejné **předplatné** , které jste použili k vytvoření aplikace IoT Central.
    * Vyberte skupinu prostředků **Retail-Store-Analysis** .
    * Vyberte stejné umístění, které jste použili pro vaši aplikaci IoT Central.
    * Vyberte **Vytvořit**. Je možné, že budete muset několik minut počkat, než systém zřídí prostředky.
1. Na portálu přejděte do skupiny prostředků **Retail-Store-Analysis** . Počkejte, až se nasazení dokončí. Možná budete muset vybrat možnost **aktualizovat** , aby se aktualizoval stav nasazení. V **oznámeních** můžete taky zjistit stav vytvoření oboru názvů centra událostí.
1. Ve skupině prostředků **Retail-Store-Analysis** vyberte **obor názvů Event Hubs**. Na portálu se zobrazí domovská stránka **oboru názvů Event Hubs** .

Teď máte **Event Hubs obor názvů**, můžete vytvořit **centrum událostí** pro použití s vaší aplikací IoT Central:

1. Na domovské stránce **Event Hubs oboru názvů** na portálu vyberte **+ centrum událostí**.
1. Na stránce **vytvořit centrum událostí** jako název zadejte _Store-telemetrie_ a pak vyberte **vytvořit**.

Teď máte k dispozici centrum událostí, které můžete použít při konfiguraci exportu dat z aplikace IoT Central:

![Centrum událostí](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Konfigurace exportu dat

Teď máte centrum událostí, ve kterém můžete nakonfigurovat aplikaci pro **vyjmutí v rámci Storu** pro export telemetrie z připojených zařízení. Následující postup ukazuje, jak nakonfigurovat export:

1. Přihlaste se k vaší aplikaci IoT Central pro **analýzy v rámci obchodu** .
1. V levém podokně vyberte **exportovat data** .
1. Vyberte **nový > Azure Event Hubs**.
1. Jako **Zobrazovaný název** zadejte _Export telemetrie_ .
1. Vyberte svůj **obor názvů Event Hubs**.
1. Vyberte centrum událostí **telemetrie úložiště** .
1. V části **data k exportu** přepněte na **zařízení** a **šablony zařízení** .
1. Vyberte **Uložit**.

Export dat může trvat několik minut, než se zahájí odesílání telemetrie do centra událostí. Stav exportu můžete zobrazit na stránce **exporty dat** :

![Konfigurace kontinuálního exportu dat](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Vytvoření datových sad Power BI

Řídicí panel Power BI zobrazí data z vaší aplikace pro monitorování maloobchodního prodeje. V tomto řešení použijete Power BI datové sady streamování jako zdroj dat pro Power BI řídicí panel. V této části definujete schéma datových sad streamování, aby aplikace logiky mohla přesílat data z centra událostí. Následující kroky ukazují, jak vytvořit dvě datové sady streamování pro senzory prostředí a jednu datovou sadu streamování pro senzor obsazení:

1. Přihlaste se ke svému účtu **Power BI**.
1. Vyberte **pracovní prostory** a pak vyberte **vytvořit pracovní prostor**.
1. Na stránce **vytvořit pracovní prostor** zadejte v části _Store Analytics –_ jako **název pracovního prostoru** proveďte rezervaci.
1. Posuňte se do dolní části okna **Vítejte na stránce pracovní prostor pro analýzu v úložišti** a vyberte **Přeskočit**.
1. Na stránce pracovní prostor vyberte **vytvořit > datovou sadu streamování**.
1. Na stránce **Nová datová sada streamování** zvolte **rozhraní API** a pak vyberte **Další**.
1. Jako **název datové sady** zadejte _zóna 1 snímač_ .
1. Zadejte tři **hodnoty ze streamu** v následující tabulce:

    | Název hodnoty  | Typ hodnoty |
    | ----------- | ---------- |
    | Timestamp   | DateTime   |
    | Vlhkost    | Číslo     |
    | Teplota | Číslo     |

1. Přepněte **historické analýzy dat** na.
1. Vyberte **vytvořit** a pak **Hotovo**.
1. Vytvořte další datovou sadu streamu s názvem **zóna 2 snímač** se stejným schématem a nastavením jako datovou sadu streamování **zóna 1 senzorů** .

Teď máte dvě datové sady streamování. Aplikace logiky bude směrovat telemetrii ze dvou senzorů prostředí, které jsou připojené k vaší aplikaci pro **vyjmutí v rámci obchodu** , do těchto dvou datových sad:

![Datové sady zóny](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Toto řešení využívá jednu datovou sadu streamování pro každý senzor, protože není možné použít filtry pro streamovaná data v Power BI.

Také potřebujete datovou sadu streamování pro telemetrii obsazení:

1. Na stránce pracovní prostor vyberte **vytvořit > datovou sadu streamování**.
1. Na stránce **Nová datová sada streamování** zvolte **rozhraní API** a pak vyberte **Další**.
1. Jako **název datové sady** zadejte _snímač obsazení_ .
1. Zadejte pět **hodnot ze streamu** v následující tabulce:

    | Název hodnoty     | Typ hodnoty |
    | -------------- | ---------- |
    | Timestamp      | DateTime   |
    | Délka fronty 1 | Číslo     |
    | Délka fronty 2 | Číslo     |
    | Doba bydlení 1   | Číslo     |
    | Doba bydlení 2   | Číslo     |

1. Přepněte **historické analýzy dat** na.
1. Vyberte **vytvořit** a pak **Hotovo**.

Teď máte třetí datovou sadu streamování, která ukládá hodnoty ze senzoru simulovaného obsazení. Tento senzor oznamuje délku fronty na dvou rezervacích ve Storu a dobu, po kterou zákazníci čekají v těchto frontách:

![Datová sada obsazených dat](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

V tomto řešení aplikace logiky přečte telemetrii z centra událostí, analyzuje data a pak je pošle do Power BI datových sad streamování, které jste vytvořili.

Než vytvoříte aplikaci logiky, budete potřebovat ID zařízení dvou senzorů RuuviTag, které jste připojili k vaší aplikaci IoT Central v kurzu [Vytvoření analytické aplikace v rámci obchodu Azure IoT Central](./tutorial-in-store-analytics-create-app.md) :

1. Přihlaste se k vaší aplikaci IoT Central pro **analýzy v rámci obchodu** .
1. V levém podokně vyberte **zařízení** . Pak vyberte **RuuviTag**.
1. Poznamenejte si **ID zařízení**. Na následujícím snímku obrazovky jsou ID **f5dcf4ac32e8** a **e29ffc8d5326**:

    ![ID zařízení](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Následující kroky ukazují, jak vytvořit aplikaci logiky v Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a v levém horním rohu obrazovky vyberte **vytvořit prostředek** .
1. V **části Hledat na Marketplace** zadejte _aplikaci logiky_ a potom stiskněte klávesu **ENTER**.
1. Na stránce **Aplikace logiky** vyberte **vytvořit**.
1. Na stránce vytvoření **Aplikace logiky** :
    * Zadejte jedinečný název vaší aplikace logiky, jako je například _Your-Retail-Store-Analysis_.
    * Vyberte stejné **předplatné** , které jste použili k vytvoření aplikace IoT Central.
    * Vyberte skupinu prostředků **Retail-Store-Analysis** .
    * Vyberte stejné umístění, které jste použili pro vaši aplikaci IoT Central.
    * Vyberte **Vytvořit**. Je možné, že budete muset několik minut počkat, než systém zřídí prostředky.
1. V Azure Portal přejděte na novou aplikaci logiky.
1. Na stránce **návrháře Logic Apps** se posuňte dolů a vyberte **prázdná aplikace logiky**.
1. V **vyhledávacích konektorech a triggerech** zadejte _Event Hubs_.
1. V **aktivačních** událostech vyberte, **kdy jsou události k dispozici v centru událostí**.
1. Jako **název připojení** zadejte _telemetrie úložiště_ a vyberte svůj **obor názvů Event Hubs**.
1. Vyberte zásadu **RootManageSharedAccess** a vyberte **vytvořit**.
1. V části **události když jsou k dispozici události centra událostí** :
    * V **název centra událostí** vyberte **Store-telemetrie**.
    * V **typu obsahu** vyberte **Application/JSON**.
    * Nastavte **interval** na tři a **četnost** na sekundy.
1. Výběrem možnosti **Uložit** aplikaci logiky uložte.

Chcete-li přidat logiku k návrhu aplikace logiky, vyberte **zobrazení kódu**:

1. Nahraďte `"actions": {},` následujícím kódem JSON. Nahraďte dva zástupné symboly `[YOUR RUUVITAG DEVICE ID 1]` a `[YOUR RUUVITAG DEVICE ID 2]` identifikátory, které jste si poznamenali u svých dvou zařízení RuuviTag:

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

1. Vyberte **Save (Uložit** ) a pak vyberte **Návrhář** a zobrazte si vizuální verzi logiky, kterou jste přidali:

    ![Návrh aplikace logiky](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Kliknutím na **přepínač přepnout podle DeviceID** tuto akci rozbalíte. Pak vyberte **zóna 1 prostředí** a vyberte **přidat akci**.
1. Do **vyhledávacích konektorů a akcí** zadejte **Power BI** a potom stiskněte klávesu **ENTER**.
1. Vyberte akci **Přidat řádky do datové sady (Preview)** .
1. Vyberte **Přihlásit** se a podle pokynů se přihlaste ke svému účtu Power BI.
1. Po dokončení procesu přihlašování v akci **Přidat řádky do datové sady** :
    * Vyberte **in-Store Analytics – proveďte rezervaci** jako pracovní prostor.
    * Jako datovou sadu vyberte **zóna 1 senzor** .
    * Jako tabulku vyberte **RealTimeData** .
    * Vyberte **Přidat nový parametr** a pak vyberte pole **časové razítko**, **vlhkost** a **teplota** .
    * Vyberte pole **časové razítko** a pak ze seznamu **dynamického obsahu** vyberte **x-opt-enqueuedtime** .
    * Vyberte pole **vlhkost** a pak pro **analýzu telemetrie** vyberte **Zobrazit více** . Pak vyberte **vlhkost**.
    * Vyberte pole **teplota** a pak pro **analýzu telemetrie** vyberte **Zobrazit více** . Pak vyberte možnost **teplota**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **zóna 1 prostředí** vypadá jako na následujícím snímku obrazovky: ![ zóna 1 prostředí.](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Vyberte akci **zóna 2 prostředí** a vyberte **přidat akci**.
1. Do **vyhledávacích konektorů a akcí** zadejte **Power BI** a potom stiskněte klávesu **ENTER**.
1. Vyberte akci **Přidat řádky do datové sady (Preview)** .
1. V akci **Přidat řádky do datové sady 2** :
    * Vyberte **in-Store Analytics – proveďte rezervaci** jako pracovní prostor.
    * Jako datovou sadu vyberte **zóna 2 senzor** .
    * Jako tabulku vyberte **RealTimeData** .
    * Vyberte **Přidat nový parametr** a pak vyberte pole **časové razítko**, **vlhkost** a **teplota** .
    * Vyberte pole **časové razítko** a pak ze seznamu **dynamického obsahu** vyberte **x-opt-enqueuedtime** .
    * Vyberte pole **vlhkost** a pak pro **analýzu telemetrie** vyberte **Zobrazit více** . Pak vyberte **vlhkost**.
    * Vyberte pole **teplota** a pak pro **analýzu telemetrie** vyberte **Zobrazit více** . Pak vyberte možnost **teplota**.
    Vyberte **Uložit** a uložte tak provedené změny.  Akce **zóna 2 prostředí** vypadá jako na následujícím snímku obrazovky: ![ zóna 2 prostředí.](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Vyberte akci **obsazení** a pak vyberte akci **Přepnout podle ID rozhraní** .
1. Vyberte akci **rozhraní pro dobu bydlení** a vyberte **přidat akci**.
1. Do **vyhledávacích konektorů a akcí** zadejte **Power BI** a potom stiskněte klávesu **ENTER**.
1. Vyberte akci **Přidat řádky do datové sady (Preview)** .
1. V akci **Přidat řádky do datové sady** :
    * Vyberte **in-Store Analytics – proveďte rezervaci** jako pracovní prostor.
    * Jako datovou sadu vyberte **senzor obsazení** .
    * Jako tabulku vyberte **RealTimeData** .
    * Vyberte možnost **Přidat nový parametr** a potom vyberte pole časové **razítko**, **doba bydlení 1** a **Doba obydlí 2** .
    * Vyberte pole **časové razítko** a pak ze seznamu **dynamického obsahu** vyberte **x-opt-enqueuedtime** .
    * Vyberte pole **Doba obydlí 1** a potom pro **analýzu telemetrie** vyberte **Zobrazit další** . Pak vyberte **DwellTime1**.
    * Vyberte pole **Doba obydlí 2** a potom pro **analýzu telemetrie** vyberte **Zobrazit další** . Pak vyberte **DwellTime2**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **rozhraní pro dobu bydlení** vypadá jako na následujícím snímku obrazovky: ![ snímek obrazovky, který zobrazuje akci "rozhraní pro dobu bydlení".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Vyberte akci **rozhraní počtu osob** a pak vyberte **přidat akci**.
1. Do **vyhledávacích konektorů a akcí** zadejte **Power BI** a potom stiskněte klávesu **ENTER**.
1. Vyberte akci **Přidat řádky do datové sady (Preview)** .
1. V akci **Přidat řádky do datové sady** :
    * Vyberte **in-Store Analytics – proveďte rezervaci** jako pracovní prostor.
    * Jako datovou sadu vyberte **senzor obsazení** .
    * Jako tabulku vyberte **RealTimeData** .
    * Vyberte **Přidat nový parametr** a pak vyberte pole **časové razítko**, **Délka fronty 1** a **délka 2 fronty** .
    * Vyberte pole **časové razítko** a pak ze seznamu **dynamického obsahu** vyberte **x-opt-enqueuedtime** .
    * Vyberte pole **Délka fronty 1** a pak vyberte **Zobrazit více** vedle možnosti **analyzovat telemetrii**. Pak vyberte **count1**.
    * Vyberte pole **Délka fronty 2** a potom vyberte **Zobrazit více** vedle možnosti **analyzovat telemetrii**. Pak vyberte **count2**.
    * Vyberte **Uložit** a uložte tak provedené změny. Akce **rozhraní počtu osob** vypadá jako na následujícím snímku obrazovky: ![ obsazená akce](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

Aplikace logiky se spustí automaticky. Pokud chcete zobrazit stav každého spuštění, přejděte na stránku **Přehled** aplikace logiky v Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Vytvoření řídicího panelu Power BI

Nyní máte k dispozici telemetrii z vaší aplikace IoT Central prostřednictvím centra událostí. Aplikace logiky pak analyzuje zprávy centra událostí a přidá je do datové sady streamování Power BI. Nyní můžete vytvořit řídicí panel Power BI pro vizualizaci telemetrie:

1. Přihlaste se ke svému účtu **Power BI**.
1. Vyberte **pracovní prostory > Analytics v obchodě – rezervace**.
1. Vyberte **vytvořit > řídicí panel**.
1. Jako název řídicího panelu zadejte **Analytics úložiště** a vyberte **vytvořit**.

### <a name="add-line-charts"></a>Přidat spojnicové grafy

Přidejte čtyři dlaždice spojnicového grafu pro zobrazení teploty a vlhkosti ze dvou senzorů pro životní prostředí. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte tím, že vyberete **... (Další možnosti) > přidat dlaždici**. Vyberte **vlastní streamovaná data** a pak vyberte **Další**:

| Nastavení | #1 grafu | #2 grafu | #3 grafu | #4 grafu |
| ------- | -------- | -------- | -------- | -------- |
| Datová sada | Senzor Zóna 1 | Senzor Zóna 1 | Senzor Zóna 2 | Senzor Zóna 2 |
| Typ vizualizace | Spojnicový graf | Spojnicový graf | Spojnicový graf | Spojnicový graf |
| Osa | Timestamp | Timestamp | Timestamp | Timestamp |
| Hodnoty | Teplota | Vlhkost | Teplota | Vlhkost |
| Časové okno | 60 minut | 60 minut | 60 minut | 60 minut |
| Nadpis | Teplota (1 hodina) | Vlhkost (1 hodina) | Teplota (1 hodina) | Vlhkost (1 hodina) |
| Podnadpis | Zóna 1 | Zóna 1 | Zóna 2 | Zóna 2 |

Následující snímek obrazovky ukazuje nastavení prvního grafu:

![Nastavení čárového grafu](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Přidání karet pro zobrazení dat o životním prostředí

Přidejte čtyři dlaždice karet k zobrazení nejaktuálnějších hodnot teploty a vlhkosti ze dvou senzorů pro životní prostředí. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte tím, že vyberete **... (Další možnosti) > přidat dlaždici**. Vyberte **vlastní streamovaná data** a pak vyberte **Další**:

| Nastavení | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Datová sada | Senzor Zóna 1 | Senzor Zóna 1 | Senzor Zóna 2 | Senzor Zóna 2 |
| Typ vizualizace | Karta | Karta | Karta | Karta |
| Pole | Teplota | Vlhkost | Teplota | Vlhkost |
| Nadpis | Teplota (F) | Humidity (%) | Teplota (F) | Humidity (%) |
| Podnadpis | Zóna 1 | Zóna 1 | Zóna 2 | Zóna 2 |

Následující snímek obrazovky ukazuje nastavení pro první kartu:

![Nastavení karty](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Přidat dlaždice pro zobrazení rezervací dat obsazení

Přidejte čtyři dlaždice karet, abyste zobrazili délku fronty a dobu trvání obydlí pro dvě rezervace ve Storu. K vytvoření dlaždic použijte informace v následující tabulce. Chcete-li přidat každou dlaždici, začněte tím, že vyberete **... (Další možnosti) > přidat dlaždici**. Vyberte **vlastní streamovaná data** a pak vyberte **Další**:

| Nastavení | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Datová sada | Senzor obsazení | Senzor obsazení | Senzor obsazení | Senzor obsazení |
| Typ vizualizace | Skupinový sloupcový graf | Skupinový sloupcový graf | Měřidlo | Měřidlo |
| Osa    | Timestamp | Timestamp | N/A | N/A |
| Hodnota | Doba bydlení 1 | Doba bydlení 2 | Délka fronty 1 | Délka fronty 2 |
| Časové okno | 60 minut | 60 minut |  N/A | N/A |
| Nadpis | Doba obydlí | Doba obydlí | Délka fronty | Délka fronty |
| Podnadpis | Rezervace 1 | Rezervace 2 | Rezervace 1 | Rezervace 2 |

Změňte velikost dlaždic na řídicím panelu, aby vypadala jako na následujícím snímku obrazovky:

![Snímek obrazovky zobrazující řídicí panel Power B se změněnou velikostí a znovu uspořádanými dlaždicemi](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Můžete přidat další grafické prostředky k dalšímu přizpůsobení řídicího panelu:

![Řídicí panel Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste hotovi s aplikací IoT Central, můžete ji odstranit přihlášením k aplikaci a přechodem na stránku **nastavení aplikace** v části **Správa** .

Pokud chcete aplikaci zachovat, ale snížit náklady spojené s ní, zakažte export dat, který odesílá telemetrii do vašeho centra událostí.

Centrum událostí a aplikaci logiky můžete v Azure Portal odstranit tak, že odstraníte skupinu prostředků s názvem **Retail-Store-Analysis**.

Datové sady a řídicí panely Power BI můžete odstranit tak, že odstraníte pracovní prostor ze stránky nastavení Power BI pracovního prostoru.

## <a name="next-steps"></a>Další kroky

Tyto tři kurzy vám ukázaly ucelené řešení, které používá šablonu aplikace v rámci služby **Store analytics** IoT Central. Připojili jste zařízení k aplikaci, použili IoT Central k monitorování zařízení a použili Power BI k vytvoření řídicího panelu pro zobrazení přehledů z telemetrie zařízení. Doporučený další krok je prozkoumat jednu z dalších šablon aplikací IoT Central:

> [!div class="nextstepaction"]
> * [Sestavování řešení pro energetiku s využitím služby IoT Central](../energy/overview-iot-central-energy.md)
> * [Vytváření řešení pro státní správu s využitím služby IoT Central](../government/overview-iot-central-government.md)
> * [Sestavování řešení pro zdravotnictví s využitím služby IoT Central](../healthcare/overview-iot-central-healthcare.md)
