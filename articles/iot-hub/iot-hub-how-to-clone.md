---
title: Jak klonovat azure iot hub
description: Jak klonovat azure iot hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429158"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Jak naklonovat azure iot hub do jiné oblasti

Tento článek zkoumá způsoby klonování centra IoT Hub a obsahuje některé otázky, které je třeba odpovědět před zahájením. Tady je několik důvodů, proč můžete chtít klonovat centrum IoT:
 
* Přesouváte společnost z jedné oblasti do druhé, například z Evropy do Severní Ameriky (nebo naopak) a chcete, aby vaše zdroje a data byly geograficky blízké vašemu novému umístění, takže je třeba přesunout své centrum.

* Nastavujete rozbočovač pro vývojové versus produkční prostředí.

* Chcete provést vlastní implementaci s vysokou dostupností více rozbočovačů. Další informace najdete v tématu [Jak dosáhnout průřezu HA sekce Služby IoT Hub vysoké dostupnosti a zotavení po havárii](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Chcete zvýšit počet [oddílů](iot-hub-scaling.md#partitions) nakonfigurovaných pro rozbočovač. Tato nastavení je nastavena při prvním vytvoření centra a nelze ji změnit. Informace v tomto článku můžete naklonovat rozbočovač a při vytvoření klonování zvýšit počet oddílů.

Chcete-li klonovat rozbočovač, potřebujete předplatné s přístupem pro správu k původnímu rozbočovači. Nové centrum můžete umístit do nové skupiny prostředků a oblasti, ve stejném předplatném jako původní rozbočovač nebo dokonce do nového předplatného. Prostě nelze použít stejný název, protože název centra musí být globálně jedinečný.

> [!NOTE]
> V tuto chvíli není k dispozici žádná funkce pro automatické klonování služby IoT hub. Je to především manuální proces, a proto je poměrně náchylný k chybám. Složitost klonování rozbočovače je přímo úměrná složitosti centra. Například klonování služby IoT hub bez směrování zpráv je poměrně jednoduché. Pokud přidáte směrování zpráv pouze jako jednu složitost, klonování rozbočovače se stane alespoň řádově složitější. Pokud také přesunete prostředky používané pro směrování koncových bodů, je to další pořadí magniture složitější. 

## <a name="things-to-consider"></a>Co je třeba zvážit

Existuje několik věcí, které je třeba zvážit před klonováním centra IoT hub.

* Ujistěte se, že všechny funkce dostupné v původním umístění jsou také k dispozici v novém umístění. Některé služby jsou ve verzi preview a ne všechny funkce jsou k dispozici všude.

* Před vytvořením a ověřením klonované verze neodeberte původní prostředky. Jakmile centrum odeberete, je navždy pryč a neexistuje žádný způsob, jak ho obnovit, abyste zkontrolovali nastavení nebo data, abyste se ujistili, že je centrum replikováno správně.

* Mnoho prostředků vyžaduje globálně jedinečné názvy, takže je nutné použít různé názvy pro klonované verze. Měli byste také použít jiný název pro skupinu prostředků, do které patří klonované centrum. 

* Data pro původní centrum IoT se nemigrují. To zahrnuje telemetrické zprávy, příkazy cloud-to-device (C2D) a informace související s úlohami, jako jsou plány a historie. Metriky a výsledky protokolování se také nemigrují. 

* U dat nebo zpráv směrovaných do Služby Azure Storage můžete data ponechat v původním účtu úložiště, přenést tato data do nového účtu úložiště v nové oblasti nebo ponechat stará data na místě a vytvořit nový účet úložiště v novém umístění pro nová data. Další informace o přesouvání dat v úložišti objektů Blob [najdete v tématu Začínáme s AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Data pro centra událostí a pro témata a fronty služby Service Bus nelze migrovat. Jedná se o data v okamžiku a není uložena po zpracování zpráv.

* Je třeba naplánovat prostoje migrace. Klonování zařízení do nového rozbočovače nějakou dobu trvá. Pokud používáte metodu importu a exportu, testování srovnávacích testů odhalilo, že přesun 500 000 zařízení může trvat přibližně dvě hodiny a přesun milionu zařízení o čtyři hodiny. 

* Zařízení můžete zkopírovat do nového rozbočovače, aniž byste museli zařízení vypínat nebo měnit. 

    * Pokud byla zařízení původně zřízena pomocí DPS, opětovné zřizování je aktualizuje informace o připojení uložené v každém zařízení. 
    
    * V opačném případě budete muset použít Import/Export metoda přesunout zařízení a pak zařízení musí být upraveny tak, aby použití nového rozbočovače. Zařízení můžete například nastavit tak, aby spotřebovávaly název hostitele služby IoT Hub z požadovaných vlastností dvojčete. Zařízení převezme tento název hostitele služby IoT Hub, odpojí zařízení od starého rozbočovače a znovu jej připojí k novému.
    
* Je třeba aktualizovat všechny certifikáty, které používáte, abyste je mohli používat s novými prostředky. Také budete mít pravděpodobně rozbočovač definovaný v tabulce DNS někde - budete muset aktualizovat, že informace DNS.

## <a name="methodology"></a>Metodologie

Toto je obecná metoda, kterou doporučujeme pro přesunutí centra IoT z jedné oblasti do druhé. Pro směrování zpráv to předpokládá, že prostředky nejsou přesunuty do nové oblasti. Další informace naleznete v [části Směrování zpráv](#how-to-handle-message-routing).

   1. Exportujte centrum a jeho nastavení do šablony Správce prostředků. 
   
   1. Proveďte potřebné změny šablony, jako je například aktualizace všech výskytů názvu a umístění klonovaného centra. Pro všechny prostředky v šabloně používané pro koncové body směrování zpráv aktualizujte klíč v šabloně pro tento prostředek.
   
   1. Importujte šablonu do nové skupiny prostředků v novém umístění. Tím se vytvoří klon.

   1. Ladění podle potřeby. 
   
   1. Přidejte vše, co nebylo exportováno do šablony. 
   
       Například skupiny spotřebitelů nejsou exportovány do šablony. Musíte přidat skupiny spotřebitelů do šablony ručně nebo použít [portál Azure](https://portal.azure.com) po vytvoření centra. Existuje příklad přidání jedné skupiny spotřebitelů do šablony v článku [Použití šablony Správce prostředků Azure ke konfiguraci směrování zpráv služby IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Zkopírujte zařízení z původního rozbočovače do klonu. To je uvedeno v části [Správa zařízení registrovaných na centrum IoT](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Jak zpracovat směrování zpráv

Pokud vaše centrum používá [vlastní směrování](iot-hub-devguide-messages-read-custom.md), export šablony pro rozbočovač zahrnuje konfiguraci směrování, ale nezahrnuje samotné prostředky. Musíte zvolit, zda chcete přesunout prostředky směrování do nového umístění nebo je ponechat na místě a nadále je používat "tak, jak jsou". 

Řekněme například, že máte rozbočovač v západní USA, který směruje zprávy do účtu úložiště (také v západní USA) a chcete přesunout rozbočovač do usa – východ. Můžete přesunout rozbočovač a mít ho stále směrovat zprávy do účtu úložiště v západní USA, nebo můžete přesunout rozbočovač a také přesunout účet úložiště. Může být malý výkon přístupů z směrování zpráv do prostředků koncového bodu v jiné oblasti.

Rozbočovač, který používá směrování zpráv, můžete přesunout poměrně snadno, pokud také nepřesunete prostředky používané pro koncové body směrování. 

Pokud centrum používá směrování zpráv, máte dvě možnosti. 

1. Přesuňte prostředky používané pro koncové body směrování do nového umístění.

    * Nové prostředky musíte vytvořit sami ručně na [webu Azure Portal](https://portal.azure.com) nebo pomocí šablon Správce prostředků. 

    * Při jejich vytváření v novém umístění je nutné přejmenovat všechny prostředky, protože mají globálně jedinečné názvy. 
     
    * Před vytvořením nového centra je nutné aktualizovat názvy prostředků a klíče prostředků v šabloně nového rozbočovače. Prostředky by měly být k dispozici při vytvoření nového rozbočovače.

1. Nepřesouvejte prostředky používané pro koncové body směrování. Použijte je "na místě".

   * V kroku, kde upravíte šablonu, budete muset před vytvořením nového centra načíst klíče pro každý prostředek směrování a vložit je do šablony. 

   * Centrum stále odkazuje na původní prostředky směrování a směruje zprávy do nich, jak je nakonfiguroval.

   * Budete mít malý výkon přístupů, protože rozbočovač a prostředky koncového bodu směrování nejsou ve stejném umístění.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Příprava na migraci centra do jiné oblasti

Tato část obsahuje konkrétní pokyny pro migraci rozbočovače.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Najděte původní rozbočovač a exportujte ho do šablony prostředků.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

1. Přejděte do **skupin y prostředků** a vyberte skupinu prostředků obsahující centrum, které chcete přesunout. Můžete také přejít na **zdroje a** najít rozbočovač tímto způsobem. Vyberte rozbočovač.

1. Ze seznamu vlastností a nastavení centra vyberte **Exportovat šablonu.** 

   ![Snímek obrazovky s příkazem pro export šablony pro Centrum IoT Hub](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Chcete-li stáhnout šablonu, vyberte **stáhnout.** Uložte soubor někde, kde jej můžete znovu najít. 

   ![Snímek obrazovky s příkazem pro stažení šablony pro Centrum IoT Hub](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Zobrazit šablonu 

1. Přejděte do složky Soubory ke stažení (nebo do složky, kterou jste použili při exportu šablony) a vyhledejte soubor zip. Otevřete soubor zip a `template.json`vyhledejte soubor s názvem . Vyberte ji a pak vyberte Ctrl+C, chcete-li šablonu zkopírovat. Přejděte do jiné složky, která není v souboru zip, a vložte soubor (Ctrl+V). Nyní jej můžete upravit.
 
    Následující příklad je určen pro obecný rozbočovač bez konfigurace směrování. Jedná se o rozbočovač úrovně S1 (s 1 jednotkou) s názvem **ContosoTestHub29358** v oblasti **westus**. Zde je exportovaná šablona.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Úprava šablony 

Před vytvořením nového centra v nové oblasti je třeba provést některé změny. K úpravě šablony použijte [Kód VS](https://code.visualstudio.com) nebo textový editor.

#### <a name="edit-the-hub-name-and-location"></a>Úprava názvu a umístění centra

1. Odeberte část parametrů v horní části – je mnohem jednodušší použít název rozbočovače, protože nebudeme mít více parametrů. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Změňte název tak, aby používal skutečný (nový) název, místo toho, abyste ho načítali z parametru (který jste odebrali v předchozím kroku). 

    Pro nový rozbočovač použijte název původního rozbočovače a *klon* řetězce, abyste vytvořili nový název. Začněte vyčištěním názvu a umístění rozbočovače.
    
    Stará verze:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nová verze: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Dále zjistíte, že hodnoty pro **cestu** obsahují starý název rozbočovače. Změňte je tak, aby používaly nový. Jedná se o hodnoty cesty pod **eventHubEndpoints** s názvem **události** a **OperationsMonitoringEvents**.

    Až budete hotovi, měl by vypadat oddíl koncových bodů centra událostí takto:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aktualizace klíčů pro prostředky směrování, které nejsou přesunuty

Při exportu šablony Správce prostředků pro rozbočovač, který má nakonfigurované směrování, uvidíte, že klíče pro tyto prostředky nejsou k dispozici v exportované šabloně – jejich umístění je označeno hvězdičkami. Musíte je vyplnit tak, že přejdete na tyto prostředky na portálu a načtete klíče **před** importem šablony nového centra a vytvořením centra. 

1. Načtěte klíče potřebné pro všechny prostředky směrování a vložte je do šablony. Klíče můžete načíst z prostředku na [webu Azure Portal](https://portal.azure.com). 

   Například pokud směrujete zprávy do kontejneru úložiště, vyhledejte účet úložiště na portálu. V části Nastavení vyberte **Přístupové klávesy**a zkopírujte jeden z nich. Tady je, jak klíč vypadá při prvním exportu šablony:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Po načtení klíče účtu pro účet úložiště jej vložte `AccountKey=****` do šablony v klauzuli místo hvězdičky. 

1. U front sběrnice získáte sdílený přístupový klíč odpovídající službě SharedAccessKeyName. Zde je klíč `SharedAccessKeyName` a v json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Totéž platí pro témata služby Service Bus a připojení centra událostí.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Vytvoření nových prostředků směrování v novém umístění

Tato část platí pouze v případě, že přesouváte prostředky používané rozbočovačem pro koncové body směrování.

Chcete-li přesunout prostředky technologického postupu, je nutné je nastavit ručně v novém umístění. Prostředky směrování můžete vytvořit pomocí [portálu Azure nebo](https://portal.azure.com)exportem šablony Správce prostředků pro každý prostředek používaný směrováním zpráv, jejich úpravou a jejich importem. Po nastavení prostředků můžete importovat šablonu rozbočovače (která zahrnuje konfiguraci směrování).

1. Vytvořte každý prostředek používaný směrováním. Můžete to udělat ručně pomocí [portálu Azure](https://portal.azure.com)nebo vytvořit prostředky pomocí šablon Správce prostředků. Pokud chcete použít šablony, postupujte takto:

    1. Pro každý prostředek používaný směrováním jej exportujte do šablony Správce prostředků.
    
    1. Aktualizujte název a umístění prostředku. 

    1. Aktualizujte všechny křížové odkazy mezi prostředky. Pokud například vytvoříte šablonu pro nový účet úložiště, budete muset aktualizovat název účtu úložiště v této šabloně a všechny ostatní šablony, která na ni odkazuje. Ve většině případů je oddíl směrování v šabloně pro rozbočovač jedinou další šablonou, která odkazuje na prostředek. 

    1. Importujte každou šablonu, která nasazuje každý prostředek.

    Po nastavení a spuštění prostředků používaných v technologickém postupu můžete pokračovat.

1. V šabloně pro službu IoT hub změňte název každého prostředků směrování na nový název a v případě potřeby aktualizujte umístění. 

Nyní máte šablonu, která vytvoří nový rozbočovač, který vypadá téměř přesně jako staré rozbočovač, v závislosti na tom, jak jste se rozhodli zpracovat směrování.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Move -- vytvoření nového rozbočovače v nové oblasti načtením šablony

Vytvořte nový rozbočovač v novém umístění pomocí šablony. Pokud máte prostředky směrování, které se budou přesouvat, měly by být prostředky nastaveny v novém umístění a odkazy v šabloně aktualizovány tak, aby odpovídaly. Pokud nepřesouváte prostředky směrování, měly by být v šabloně s aktualizovanými klíči.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte **Vytvořit prostředek**. 

1. Do vyhledávacího pole vložte "nasazení šablony" a vyberte Enter.

1. Vyberte **nasazení šablony (nasazení pomocí vlastních šablon).** Tím přejdete na obrazovku pro nasazení šablony. Vyberte **Vytvořit**. Zobrazí se tato obrazovka:

   ![Snímek obrazovky s příkazem pro vytvoření vlastní šablony](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. V editoru vyberte **Vytvořit vlastní šablonu**, která vám umožní nahrát šablonu ze souboru. 

1. Vyberte **Načíst soubor**. 

   ![Snímek obrazovky s příkazem pro nahrání souboru šablony](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Vyhledejte novou šablonu, kterou jste upravili, a vyberte ji a pak vyberte **Otevřít**. Načte šablonu do editačního okna. Vyberte **Uložit**. 

   ![Snímek obrazovky s načítáním šablony](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Vyplňte následující pole.

   **Předplatné**: vyberte předplatné, které chcete použít.

   **Skupina prostředků**: vytvořte novou skupinu prostředků v novém umístění. Pokud již máte novou nastavenou, můžete ji vybrat místo vytvoření nového.

   **Umístění:** Pokud jste vybrali existující skupinu prostředků, je vyplněna tak, aby odpovídala umístění skupiny prostředků. Pokud jste vytvořili novou skupinu prostředků, bude to její umístění.

   **Souhlasím s tím, zaškrtávací políčko**: to v podstatě říká, že souhlasíte s tím, platit za zdroje, které vytváříte.

1. Vyberte **tlačítko Koupit.**

Portál teď ověří vaši šablonu a nasadí vaše klonované centrum. Pokud máte konfigurační data směrování, budou zahrnuta do nového rozbočovače, ale budou ukazovat na prostředky v předchozím umístění.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Správa zařízení registrovaných v centru IoT

Nyní, když máte klon v provozu, musíte zkopírovat všechna zařízení z původního rozbočovače do klonu. 

Existuje několik způsobů, jak toho dosáhnout. Buď jste původně [použili službu Zřizování zařízení (DPS)](/azure/iot-dps/about-iot-dps)k zřízení zařízení, nebo jste to neudělali. Pokud ano, není to těžké. Pokud jste tak neučinili, může to být velmi složité. 

Pokud jste k zřizování zařízení nepoužívali DPS, můžete přeskočit další část a začít [pomocí importu nebo exportu a přesunout zařízení do nového rozbočovače](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Použití DPS k opětovnému zřízení zařízení v novém rozbočovači

Pokud chcete zařízení přesunout do nového umístění pomocí DPS, přečtěte si informace o tom, [jak zařízení znovu zřídit](../iot-dps/how-to-reprovision.md). Po dokončení můžete zobrazit zařízení na webu [Azure Portal](https://portal.azure.com) a ověřit, že jsou v novém umístění.

Přejděte do nového rozbočovače pomocí [portálu Azure](https://portal.azure.com). Vyberte rozbočovač a pak vyberte **Zařízení IoT**. Zobrazí se zařízení, která byla znovu zřízena do klonovaného rozbočovače. Můžete také zobrazit vlastnosti klonovaného rozbočovače. 

Pokud jste implementovali směrování, otestujte a ujistěte se, že vaše zprávy jsou směrovány do prostředků správně.

### <a name="committing-the-changes-after-using-dps"></a>Potvrzení změn po použití DPS

Tato změna byla spáchána službou DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Vrácení zpět změny po použití DPS. 

Pokud chcete vrátit zpět změny, znovu zřídit zařízení z nového rozbočovače do starého.

Nyní jste dokončili migraci rozbočovače a jeho zařízení. Můžete přeskočit na [Vyčištění](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Přesunutí zařízení do nového rozbočovače pomocí importu a exportu

Aplikace cílí na .NET Core, takže ji můžete spustit na Windows nebo Linuxu. Můžete stáhnout ukázku, načíst připojovací řetězce, nastavit příznaky, pro které bity chcete spustit, a spustit jej. Můžete to udělat bez otevření kódu.

### <a name="downloading-the-sample"></a>Stažení ukázky

1. Použijte ukázky IoT C# z této stránky: [Ukázky Azure IoT pro C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Stáhněte si soubor zip a rozbalte jej v počítači. 

1. Relevantní kód je v ./iot-hub/Samples/service/ImportExportDevicesSample. Ke spuštění aplikace nemusíte kód zobrazovat ani upravovat.

1. Chcete-li aplikaci spustit, zadejte tři připojovací řetězce a pět možností. Tato data předáte jako argumenty příkazového řádku nebo použijete proměnné prostředí nebo použijete kombinaci těchto dvou. Budeme předat možnosti jako argumenty příkazového řádku a připojovací řetězce jako proměnné prostředí. 

   Důvodem je, že připojovací řetězce jsou dlouhé a nemotorné a pravděpodobně se nezmění, ale možná budete chtít změnit možnosti a spustit aplikaci více než jednou. Chcete-li změnit hodnotu proměnné prostředí, musíte zavřít příkazové okno a Visual Studio nebo VS Kód, podle toho, co používáte. 

### <a name="options"></a>Možnosti

Zde je pět možností, které zadáte při spuštění aplikace. Za chvíli je dáme na příkazovou čáru.

*   **addDevices** (argument 1) -- nastavte tuto hodnotu na hodnotu true, pokud chcete přidat virtuální zařízení, která jsou generována za vás. Ty jsou přidány do zdrojového centra. Nastavte také **numToAdd** (argument 2) a určete, kolik zařízení chcete přidat. Maximální počet zařízení, která můžete zaregistrovat do rozbočovače, je jeden milion. Účelem této možnosti je testování – můžete vygenerovat určitý počet zařízení a pak je zkopírovat do jiného rozbočovače.

*   **copyDevices** (argument 3) -- nastavit toto true zkopírovat zařízení z jednoho rozbočovače do druhého. 

*   **deleteSourceDevices** (argument 4) -- nastavte tuto hodnotu na hodnotu true, chcete-li odstranit všechna zařízení registrovaná ve zdrojovém rozbočovači. Doporučujeme počkat, až si budete jisti, že všechna zařízení byla přenesena, než toto spustíte. Jakmile zařízení smažete, nemůžete je získat zpět.

*   **deleteDestDevices** (argument 5) -- nastavte tuto hodnotu na hodnotu true, chcete-li odstranit všechna zařízení registrovaná v cílovém rozbočovači (klon). Můžete to udělat, pokud chcete zkopírovat zařízení více než jednou. 

Základní příkaz bude *dotnet spustit* -- to říká .NET k sestavení místního souboru csproj a pak jej spustit. Argumenty příkazového řádku přidáte na konec před jeho spuštěním. 

Váš příkazový řádek bude vypadat takto:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Použití proměnných prostředí pro připojovací řetězce

1. Chcete-li spustit ukázku, potřebujete připojovací řetězce ke starým a novým centrům IoT a k účtu úložiště, který můžete použít pro dočasné pracovní soubory. Hodnoty pro ně uložíme do proměnných prostředí.

1. Chcete-li získat hodnoty připojovacího řetězce, přihlaste se na [portál Azure](https://portal.azure.com). 

1. Vložte připojovací řetězce někam, kde je můžete načíst, například NotePad. Pokud zkopírujete následující, můžete vložit připojovací řetězce přímo tam, kam jdou. Nepřidávejte mezery kolem znaménko rovná se nebo změní název proměnné. Také nepotřebujete dvojité uvozovky kolem připojovacích řetězců. Pokud kolem připojovacího řetězce účtu úložiště vložíte uvozovky, nebude to fungovat.

   V systému Windows nastavujete proměnné prostředí takto:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Pro Linux, to je, jak definovat proměnné prostředí:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Pro připojovací řetězce centra IoT přejděte na každé rozbočovače na portálu. Můžete hledat v **resources** pro rozbočovač. Pokud znáte skupinu prostředků, můžete přejít na **skupiny prostředků**, vybrat skupinu prostředků a potom vybrat centrum ze seznamu prostředků v této skupině prostředků. 

1. V nastavení centra vyberte **zásady sdíleného přístupu,** vyberte **iothubowner** a zkopírujte jeden z připojovacích řetězců. Proveďte totéž pro cílové centrum. Přidejte je do příslušných příkazů SET.

1. Pro připojovací řetězec účtu úložiště najděte účet úložiště v **resources** nebo v jeho **skupině prostředků** a otevřete ho. 
   
1. V části Nastavení vyberte **přístupové klávesy** a zkopírujte jeden z připojovacích řetězců. Vložte připojovací řetězec do textového souboru pro příslušný příkaz SET. 

Nyní máte proměnné prostředí v souboru s příkazy SET a víte, jaké jsou vaše argumenty příkazového řádku. Projedme vzorek.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Spuštění ukázkové aplikace a použití argumentů příkazového řádku

1. Otevřete okno příkazového řádku. Vyberte Windows `command prompt` a zadejte, chcete-li získat okno příkazového řádku.

1. Zkopírujte příkazy, které nastavují proměnné prostředí po jednom, a vložte je do okna příkazového řádku a vyberte Enter. Až budete hotovi, `SET` zadejte do okna příkazového řádku, abyste viděli proměnné prostředí a jejich hodnoty. Po zkopírování do okna příkazového řádku je není nutné je znovu kopírovat, pokud neotevřete nové okno příkazového řádku.

1. V okně příkazového řádku změňte adresáře, dokud nejste v ./ImportExportDevicesSample (kde existuje soubor ImportExportDevicesSample.csproj). Potom zadejte následující a zadejte argumenty příkazového řádku.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Příkaz dotnet vytvoří a spustí aplikaci. Vzhledem k tomu, že předáváte možnosti při spuštění aplikace, můžete změnit jejich hodnoty při každém spuštění aplikace. Můžete ji například jednou spustit a vytvořit nová zařízení, pak je znovu spustit a zkopírovat tato zařízení do nového rozbočovače a tak dále. Můžete také provést všechny kroky ve stejném spuštění, i když doporučujeme neodstranění žádné zařízení, dokud si nejste jisti, že jste hoted s klonování. Zde je příklad, který vytvoří 1000 zařízení a zkopíruje je do jiného rozbočovače.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Po ověření, že zařízení byla úspěšně zkopírována, můžete odebrat zařízení ze zdrojového centra takto:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Spuštění ukázkové aplikace pomocí sady Visual Studio

1. Pokud chcete spustit aplikaci v sadě Visual Studio, změňte aktuální adresář do složky, kde se nachází soubor IoTHubServiceSamples.sln. Potom spusťte tento příkaz v okně příkazového řádku a otevřete řešení v sadě Visual Studio. Je nutné provést ve stejném příkazovém okně, kde nastavíte proměnné prostředí, takže tyto proměnné jsou známy.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Klikněte pravým tlačítkem myši na projekt *ImportExportDevicesSample* a vyberte **nastavit jako spouštěcí projekt**.    
    
1. Nastavte proměnné v horní části Program.cs ve složce ImportExportDevicesSample pro pět možností.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Chcete-li aplikaci spustit, vyberte možnost F5. Po dokončení spuštění můžete zobrazit výsledky.

### <a name="view-the-results"></a>Zobrazení výsledků 

Zařízení můžete zobrazit na [webu Azure Portal](https://portal.azure.com) a ověřit, že jsou v novém umístění.

1. Přejděte do nového rozbočovače pomocí [portálu Azure](https://portal.azure.com). Vyberte rozbočovač a pak vyberte **Zařízení IoT**. Zobrazí se zařízení, která jste právě zkopírovali ze starého rozbočovače do klonovaného centra. Můžete také zobrazit vlastnosti klonovaného rozbočovače. 

1. Zkontrolujte chyby importu a exportu tak, že přejdete `devicefiles` na `ImportErrors.log`účet úložiště Azure na webu [Azure portal](https://portal.azure.com) a v kontejneru hledíte do kontejneru pro . Pokud je tento soubor prázdný (velikost je 0), nebyly zjištěny žádné chyby. Pokud se pokusíte importovat stejné zařízení více než jednou, odmítne zařízení podruhé a přidá chybovou zprávu do souboru protokolu.

### <a name="committing-the-changes"></a>Potvrzení změn 

V tomto okamžiku jste zkopírovali rozbočovač do nového umístění a migrovali zařízení do nového klonu. Nyní je třeba provést změny, aby zařízení fungovala s klonovaným rozbočovačem.

Chcete-li potvrdit změny, zde jsou kroky, které je třeba provést: 

* Aktualizujte každé zařízení a změňte název hostitele služby IoT Hub tak, aby ukazoval název hostitele služby IoT Hub na nový rozbočovač. Měli byste to provést stejnou metodou, kterou jste použili při prvním zřízení zařízení.

* Změňte všechny aplikace, které máte, které odkazují na staré rozbočovač přejděte na nové rozbočovače.

* Po dokončení by měl být nový rozbočovač v provozu. Starý rozbočovač by neměl mít žádná aktivní zařízení a měl by být v odpojeném stavu. 

### <a name="rolling-back-the-changes"></a>Vrácení změn zpět

Pokud se rozhodnete vrátit změny zpět, zde jsou kroky k provedení:

* Aktualizujte každé zařízení a změňte název hostitele služby IoT Hub tak, aby ukazoval název hostitele služby IoT Hub pro staré centrum. Měli byste to provést stejnou metodou, kterou jste použili při prvním zřízení zařízení.

* Změňte všechny aplikace, které máte, které odkazují na nové rozbočovač přejděte na staré rozbočovače. Pokud například používáte Azure Analytics, možná budete muset překonfigurovat [vstup Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Odstraňte nový rozbočovač. 

* Pokud máte prostředky směrování, konfigurace na starém rozbočovači by měla stále ukazovat na správnou konfiguraci směrování a měla by s těmito prostředky pracovat po restartování centra.

### <a name="checking-the-results"></a>Kontrola výsledků 

Chcete-li zkontrolovat výsledky, změňte řešení IoT tak, aby ukazovalo na centrum v novém umístění a spouštělo ho. Jinými slovy, proveďte stejné akce s novým centrem, které jste provedli s předchozím centrem a ujistěte se, že fungují správně. 

Pokud jste implementovali směrování, otestujte a ujistěte se, že vaše zprávy jsou směrovány do prostředků správně.

## <a name="clean-up"></a>Úklid

Nečistěte, dokud si nejste opravdu jisti, že nový rozbočovač je v provozu a zařízení pracují správně. Také nezapomeňte otestovat směrování, pokud používáte tuto funkci. Až budete připraveni, vyčistěte staré prostředky provedením těchto kroků:

* Pokud jste tak dosud neučinili, odstraňte staré centrum. Tím odeberete všechna aktivní zařízení z rozbočovače.

* Pokud máte prostředky směrování, které jste přesunuli do nového umístění, můžete odstranit staré prostředky směrování.

## <a name="next-steps"></a>Další kroky

Naklonovali jste centrum IoT do nového centra v nové oblasti, doplněné o zařízení. Další informace o provádění hromadných operací s registrem identit v centru IoT Hub naleznete [v tématu Import a export identit zařízení služby IoT Hub hromadně](iot-hub-bulk-identity-mgmt.md).

Další informace o ioT hubu a vývoj i pro centrum, naleznete v následujících článcích.

* [Příručka pro vývojáře ioT Hubu](iot-hub-devguide.md)

* [Kurz směrování služby IoT Hub](tutorial-routing.md)

* [Přehled správy zařízení centra IoT Hub](iot-hub-device-management-overview.md)

* Pokud chcete nasadit ukázkovou aplikaci, přečtěte si informace [o nasazení aplikace .NET Core](https://docs.microsoft.com/dotnet/core/deploying/index).
