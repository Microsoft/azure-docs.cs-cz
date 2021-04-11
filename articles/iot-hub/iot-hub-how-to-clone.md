---
title: Jak klonovat službu Azure IoT Hub
description: Jak klonovat službu Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 7f5553cc51927d878487b0875e72873451a3de3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059577"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Jak klonovat službu Azure IoT Hub do jiné oblasti

Tento článek popisuje způsoby, jak klonovat IoT Hub a poskytuje některé otázky, které před zahájením potřebujete zodpovědět. Tady je několik důvodů, proč možná budete chtít klonovat centrum IoT:
 
* Přesouváte svou společnost z jedné oblasti do druhé, třeba z Evropy do Severní Amerika (nebo naopak), a chcete, aby se vaše prostředky a data geograficky zavřela na nové místo, takže je potřeba přesunout své centrum.

* Nastavujete centrum pro vývoj a produkční prostředí.

* Chcete provést vlastní implementaci víceúrovňové vysoké dostupnosti. Další informace najdete v [části jak dosáhnout vysoké oblasti HA IoT Hub vysoké dostupnosti a zotavení po havárii](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Chcete zvýšit počet [oddílů](iot-hub-scaling.md#partitions) konfigurovaných pro vaše centrum. Tato nastavení se nastaví při prvním vytvoření centra a nedá se změnit. Informace v tomto článku můžete použít ke klonování rozbočovače a při vytvoření klonu, zvýšení počtu oddílů.

K naklonování rozbočovače potřebujete předplatné s přístupem správce k původnímu centru. Nové centrum můžete umístit do nové skupiny prostředků a oblasti ve stejném předplatném jako původní centrum nebo dokonce i v novém předplatném. Pouze nemůžete použít stejný název, protože název centra musí být globálně jedinečný.

> [!NOTE]
> V současné době není k dispozici žádná funkce pro klonování služby IoT Hub automaticky. Je primárně ruční proces, a proto je poměrně náchylná k chybám. Složitost klonování rozbočovače je přímo úměrná složitosti rozbočovače. Například klonování služby IoT Hub bez směrování zpráv je poměrně jednoduché. Pokud přidáte směrování zpráv jako jen jednu složitost, klonování rozbočovače se změní nejméně na pořadí. Pokud přesouváte také prostředky používané pro koncové body směrování, je to jiné pořadí magnitureější. 

## <a name="things-to-consider"></a>Co je potřeba zvážit

Před klonováním služby IoT Hub je potřeba zvážit několik věcí.

* Ujistěte se, že všechny funkce, které jsou k dispozici v původním umístění, jsou také k dispozici v novém umístění. Některé služby jsou ve verzi Preview, ale ne všechny funkce jsou dostupné všude.

* Před vytvořením a ověřením klonované verze Neodstraňujte původní prostředky. Po odebrání centra se trvale ztratí a neexistuje žádný způsob, jak ho obnovit, aby se zkontrolovala nastavení nebo data, aby se zajistilo správné replikace centra.

* Mnoho prostředků vyžaduje globálně jedinečné názvy, takže musíte pro klonované verze použít jiné názvy. Pro skupinu prostředků, ke které patří Klonovaný rozbočovač, byste měli použít i jiný název. 

* Data pro původní centrum IoT se nemigrují. Patří sem zprávy telemetrie, příkazy typu cloud-zařízení (C2D) a informace související s úlohou, jako jsou plány a historie. Metriky a výsledky protokolování se také nemigrují. 

* Pro data nebo zprávy směrované na Azure Storage můžete ponechat data v původním účtu úložiště, přenést tato data do nového účtu úložiště v nové oblasti nebo ponechat stará data na místě a vytvořit nový účet úložiště v novém umístění pro nová data. Další informace o přesouvání dat v úložišti objektů BLOB najdete v tématu Začínáme [s AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Data pro Event Hubs a Service Bus témat a front nelze migrovat. To jsou data v čase a po zpracování zpráv se neukládají.

* Pro migraci musíte naplánovat výpadky. Klonování zařízení do nového centra trvá určitou dobu. Pokud používáte metodu importu/exportu, testování srovnávacích testů ukázalo, že při přesunu zařízení 500 000 může trvat přibližně dvě hodiny a čtyři hodiny přesunu milionů zařízení. 

* Zařízení můžete zkopírovat do nového centra bez vypnutí nebo změny zařízení. 

    * Pokud se zařízení původně zřídila pomocí DPS, jejich opětovné zřízení aktualizuje informace o připojení uložené v jednotlivých zařízeních. 
    
    * V opačném případě je nutné použít metodu import/export k přesunutí zařízení a pak je třeba upravit zařízení, aby používala nové centrum. Můžete například nastavit, aby zařízení užívalo název hostitele IoT Hub z požadovaných vlastností. Zařízení převezme IoT Hub název hostitele, odpojte zařízení od původního centra a znovu ho připojte k novému.
    
* Je potřeba aktualizovat všechny certifikáty, které používáte, abyste je mohli používat s novými prostředky. Také pravděpodobně máte rozbočovač definovaný v tabulce DNS někam – budete muset aktualizovat tyto informace DNS.

## <a name="methodology"></a>Metodologie

Toto je obecná metoda, kterou doporučujeme pro přesun služby IoT Hub z jedné oblasti do druhé. V případě směrování zpráv se tím předpokládá, že se prostředky nepřesunou do nové oblasti. Další informace najdete v [části věnované směrování zpráv](#how-to-handle-message-routing).

   1. Exportujte centrum a jeho nastavení do šablony Správce prostředků. 
   
   1. Proveďte potřebné změny šablony, jako je například aktualizace všech výskytů názvu a umístění klonovaného rozbočovače. Pro všechny prostředky v šabloně použité pro koncové body směrování zpráv aktualizujte klíč v šabloně pro daný prostředek.
   
   1. Importujte šablonu do nové skupiny prostředků v novém umístění. Tím se vytvoří klon.

   1. Ladit podle potřeby. 
   
   1. Přidejte cokoli, co nebylo exportováno do šablony. 
   
       Například skupiny uživatelů nejsou exportovány do šablony. Musíte přidat skupiny uživatelů do šablony ručně nebo použít [Azure Portal](https://portal.azure.com) po vytvoření centra. Existuje příklad, jak přidat jednu skupinu příjemců do šablony v článku [použití šablony Azure Resource Manager ke konfiguraci směrování zpráv IoT Hub](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Zkopírujte zařízení z původního centra do klonovaného. Tento postup je popsaný v části [Správa zařízení zaregistrovaných ve službě IoT Hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Postup zpracování směrování zpráv

Pokud vaše centrum používá [vlastní směrování](iot-hub-devguide-messages-read-custom.md), export šablony pro centrum zahrnuje konfiguraci směrování, ale nezahrnuje samotné prostředky. Je nutné zvolit, zda chcete přesunout prostředky směrování do nového umístění, nebo je ponechat na místě a nadále je používat, jak je uvedeno. 

Řekněme například, že máte rozbočovač ve Západní USA, který směruje zprávy do účtu úložiště (také v Západní USA) a chcete přesunout rozbočovač na Východní USA. Můžete přesunout rozbočovač a nechat ho dál směrovat zprávy na účet úložiště v Západní USA, nebo můžete přesunout rozbočovač a také přesunout účet úložiště. Může dojít k malému výkonu směrování zpráv do prostředků koncového bodu v jiné oblasti.

Pokud nepřesouváte prostředky používané pro koncové body směrování, můžete přesunout rozbočovač, který využívá směrování zpráv poměrně snadno. 

Pokud centrum používá směrování zpráv, máte dvě možnosti. 

1. Přesuňte prostředky používané pro koncové body směrování do nového umístění.

    * Nové prostředky musíte vytvořit sami ručně v [Azure Portal](https://portal.azure.com) nebo pomocí Správce prostředků šablon. 

    * Všechny prostředky je nutné přejmenovat při jejich vytváření v novém umístění, protože mají globálně jedinečné názvy. 
     
    * Před vytvořením nového centra musíte aktualizovat názvy prostředků a klíče prostředků v šabloně nového rozbočovače. Prostředky by měly být při vytvoření nového centra přítomny.

1. Nepřesouvat prostředky používané pro koncové body směrování. Použijte je "na místě".

   * V kroku, kdy šablonu upravíte, budete muset načíst klíče pro každý prostředek směrování a vložit je do šablony před vytvořením nového centra. 

   * Centrum stále odkazuje na původní prostředky směrování a směruje zprávy do nakonfigurovaných zdrojů.

   * Dojde k malému výkonu, protože prostředky centra a koncového bodu směrování nejsou ve stejném umístění.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Příprava na migraci centra do jiné oblasti

V této části najdete konkrétní pokyny pro migraci centra.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Najděte původní centrum a exportujte ho do šablony prostředků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Přejděte do **skupiny prostředků** a vyberte skupinu prostředků obsahující rozbočovač, který chcete přesunout. Můžete také přejít na **prostředky** a najít centrum. Vyberte centrum.

1. V seznamu vlastností a nastavení pro centrum vyberte **Exportovat šablonu** . 

   ![Snímek obrazovky znázorňující příkaz pro export šablony pro IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Vyberte **Stáhnout** a stáhněte šablonu. Uložte soubor někam, kde ho můžete najít znovu. 

   ![Snímek obrazovky zobrazující příkaz pro stažení šablony pro IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Zobrazení šablony 

1. Přejít do složky Stažené soubory (nebo do jakékoli složky, kterou jste použili při exportu šablony) a vyhledejte soubor zip. Otevřete soubor zip a vyhledejte soubor s názvem `template.json` . Vyberte ji a potom vyberte CTRL + C a zkopírujte šablonu. Přejít do jiné složky, která není v souboru zip, a vložte soubor (CTRL + V). Teď ho můžete upravit.
 
    Následující příklad slouží pro obecné rozbočovače bez konfigurace směrování. Je to rozbočovač vrstev S1 (s 1 jednotkou) nazvaný **ContosoTestHub29358** v oblasti **westus**. Zde je vyexportovaná šablona.

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

Než budete moct použít šablonu k vytvoření nového centra v nové oblasti, musíte provést nějaké změny. K úpravě šablony použijte [vs Code](https://code.visualstudio.com) nebo textový editor.

#### <a name="edit-the-hub-name-and-location"></a>Upravit název a umístění centra

1. Odebrat oddíl Parameters v horní části – je mnohem jednodušší použít jenom název centra, protože nebudeme mít víc parametrů. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Změňte název tak, aby používal skutečný (nový) název místo načtení z parametru (který jste odebrali v předchozím kroku). 

    Pro nové centrum použijte název původního centra a *naklonování* řetězce, abyste vytvořili nový název. Začněte tím, že vymažete název a umístění centra.
    
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

    V dalším kroku zjistíte, že hodnoty pro **cestu** obsahují starý název centra. Změňte je tak, aby používaly nové. Jedná se o hodnoty cest v rámci **eventHubEndpoints** s názvem **události** a **OperationsMonitoringEvents**.

    Až budete hotovi, vaše koncová bodu centra událostí by měla vypadat takto:

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

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aktualizuje klíče pro prostředky směrování, které se nepřesouvá.

Když exportujete šablonu Správce prostředků pro rozbočovač s nakonfigurovaným směrováním, uvidíte, že klíče pro tyto prostředky nejsou v exportované šabloně k dispozici – jejich umístění se označuje hvězdičkami. Musíte je vyplnit tak, že na portálu nasadíte na tyto prostředky a načítajíte klíče **před** importem šablony nového centra a vytvořením centra. 

1. Načte klíče požadované pro všechny prostředky směrování a umístí je do šablony. Můžete načíst klíče z prostředku v [Azure Portal](https://portal.azure.com). 

   Pokud například směrujete zprávy do kontejneru úložiště, Najděte účet úložiště na portálu. V části nastavení vyberte **přístupové klíče** a pak zkopírujte jeden z klíčů. V takovém případě klíč vypadá při prvním exportu šablony:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Po načtení klíče účtu pro účet úložiště ho uložte do šablony v klauzuli `AccountKey=****` místo hvězdičky. 

1. V případě front Service Bus Získejte sdílený přístupový klíč, který odpovídá SharedAccessKeyName. Tady je klíč a ve formátu `SharedAccessKeyName` JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Totéž platí pro Service Bus témata a připojení centra událostí.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Vytvoření nových prostředků směrování v novém umístění

Tato část platí pouze v případě, že přesouváte prostředky používané centrem pro koncové body směrování.

Chcete-li přesunout prostředky směrování, je nutné ručně nastavit prostředky v novém umístění. Prostředky směrování můžete vytvořit pomocí [Azure Portal](https://portal.azure.com)nebo vyexportem správce prostředků šablony pro každý z prostředků používaných směrováním zpráv, jejich úpravou a importem. Po nastavení prostředků můžete importovat šablonu centra (která zahrnuje konfiguraci směrování).

1. Vytvořte jednotlivé prostředky používané směrováním. To můžete provést ručně pomocí [Azure Portal](https://portal.azure.com)nebo pomocí šablon Správce prostředků vytvořit prostředky. Pokud chcete používat šablony, postupujte podle následujících kroků:

    1. Pro každý prostředek používaný směrováním ho exportujte do šablony Správce prostředků.
    
    1. Aktualizujte název a umístění prostředku. 

    1. Aktualizujte křížové odkazy mezi prostředky. Například pokud vytvoříte šablonu pro nový účet úložiště, budete muset aktualizovat název účtu úložiště v této šabloně a jakékoli jiné šabloně, která na ni odkazuje. Ve většině případů je část směrování v šabloně pro centrum jedinou jinou šablonou, která na prostředek odkazuje. 

    1. Importujte každou šablonu, která nasadí jednotlivé prostředky.

    Až budou prostředky používané v směrování nastavené a spuštěné, můžete pokračovat.

1. V šabloně služby IoT Hub změňte název každého prostředku směrování na nový název a v případě potřeby aktualizujte umístění. 

Teď máte šablonu, která vytvoří nové centrum, které vypadá téměř přesně stejně jako staré centrum, podle toho, jak jste se rozhodli směrování zpracovat.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Přesunout – vytvoří nové centrum v nové oblasti načtením šablony.

Vytvořte nové centrum v novém umístění pomocí šablony. Pokud máte prostředky směrování, které se budou přesouvat, prostředky by se měly nastavit v novém umístění a odkazy v šabloně se budou aktualizovat tak, aby odpovídaly. Pokud nepřesouváte prostředky směrování, měly by být v šabloně s aktualizovanými klíči.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **Vytvořit prostředek**. 

1. Do vyhledávacího pole zadejte "nasazování šablony" a vyberte Enter.

1. Vyberte **nasazení šablony (nasazení pomocí vlastních šablon)**. Tím přejdete na obrazovku pro Template deployment. Vyberte **Vytvořit**. Zobrazí se tato obrazovka:

   ![Snímek obrazovky zobrazující příkaz pro vytvoření vlastní šablony](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. **V editoru vyberte vytvořit vlastní šablonu**, která umožňuje nahrát šablonu ze souboru. 

1. Vyberte **načíst soubor**. 

   ![Snímek obrazovky zobrazující příkaz pro nahrání souboru šablony](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Vyhledejte novou šablonu, kterou jste upravovali, vyberte ji a pak vyberte **otevřít**. Načte šablonu v okně Upravit. Vyberte **Uložit**. 

   ![Snímek obrazovky, který ukazuje načtení šablony](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Vyplňte následující pole.

   **Předplatné**: vyberte předplatné, které chcete použít.

   **Skupina prostředků**: Vytvořte novou skupinu prostředků v novém umístění. Pokud už máte nastavené nové nastavení, můžete ho vybrat místo vytvoření nového.

   **Umístění**: Pokud jste vybrali existující skupinu prostředků, tato možnost se vyplní tak, aby odpovídala umístění skupiny prostředků. Pokud jste vytvořili novou skupinu prostředků, bude to jejich umístění.

   **Zaškrtávací políčko** souhlasím: v tomto seznamu uvádíme, že souhlasíte s tím, že platíte za prostředky, které vytváříte.

1. Vyberte tlačítko **koupit** .

Portál teď ověří vaši šablonu a nasadí naklonované centrum. Pokud máte údaje o konfiguraci směrování, budou zahrnuty do nového centra, ale budou ukazovat na prostředky v předchozím umístění.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Správa zařízení zaregistrovaných ve službě IoT Hub

Teď, když máte naklonování a používání, musíte zkopírovat všechna zařízení z původního rozbočovače do klonu. 

To lze provést několika způsoby. Buď jste původně použili [službu Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md)k zřízení zařízení, nebo jste to neudělali. Pokud jste to provedli, není to obtížné. Pokud jste to nepoužili, může to být velmi složité. 

Pokud jste nepoužívali DPS ke zřízení zařízení, můžete přeskočit další část a začít [příkazem import/export přesunout zařízení do nového centra](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Opětovné zřízení zařízení v novém centru pomocí DPS

Pokud chcete zařízení přesunout do nového umístění pomocí DPS, přečtěte si téma [jak znovu zřídit zařízení](../iot-dps/how-to-reprovision.md). Až budete hotovi, můžete zobrazit zařízení v [Azure Portal](https://portal.azure.com) a ověřit, zda jsou v novém umístění.

Přejít na nové centrum pomocí [Azure Portal](https://portal.azure.com). Vyberte své centrum a pak vyberte **zařízení IoT**. Zobrazí se zařízení, která se znovu zřídila naklonovanému rozbočovači. Můžete také zobrazit vlastnosti naklonovaného centra. 

Pokud jste implementovali směrování, otestujte a ujistěte se, že jsou vaše zprávy správně směrovány do prostředků.

### <a name="committing-the-changes-after-using-dps"></a>Potvrzení změn po použití DPS

Tuto změnu potvrdila služba DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Vrácení změn po použití DPS. 

Pokud chcete vrátit změny zpět, znovu zajistěte zařízení z nového rozbočovače na starý.

Nyní jste dokončili migraci vašeho centra a jeho zařízení. Můžete přeskočit na [Vyčištění](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Přesun zařízení do nového centra pomocí Import-Export

Aplikace cílí na .NET Core, takže ji můžete spustit buď v systému Windows nebo Linux. Můžete si stáhnout ukázku, načíst připojovací řetězce, nastavit příznaky, pro které chcete spustit službu BITS, a spustit ji. To můžete provést bez předchozího otevření kódu.

### <a name="downloading-the-sample"></a>Stahuje se ukázka.

1. Použijte ukázky pro IoT C# z této stránky: [ukázky Azure IoT pro C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Stáhněte soubor zip a rozbalte ho v počítači. 

1. Relevantní kód je v./iot-hub/Samples/service/ImportExportDevicesSample. Není nutné zobrazovat ani upravovat kód, aby bylo možné aplikaci spustit.

1. Chcete-li spustit aplikaci, zadejte tři připojovací řetězce a pět možností. Tato data předáte jako argumenty příkazového řádku nebo použijte proměnné prostředí nebo použijte kombinaci těchto dvou. Předáváme možnosti jako argumenty příkazového řádku a připojovací řetězce jako proměnné prostředí. 

   Důvodem je to, že připojovací řetězce jsou dlouhé a nemusejí být nepravděpodobné, ale možná budete chtít změnit možnosti a spustit aplikaci více než jednou. Chcete-li změnit hodnotu proměnné prostředí, je nutné zavřít příkazové okno a sadu Visual Studio nebo VS Code, podle toho, co používáte. 

### <a name="options"></a>Možnosti

Toto jsou pět možností, které zadáte při spuštění aplikace. Do příkazového řádku za minutu vložíme.

*   **addDevices** (argument 1) – nastavte tuto hodnotu na true, pokud chcete přidat virtuální zařízení, která jsou vygenerována za vás. Ty se přidají do zdrojového centra. Také nastavte **numToAdd** (argument 2), chcete-li určit, kolik zařízení chcete přidat. Maximální počet zařízení, která můžete zaregistrovat do centra, je 1 000 000. Účelem této možnosti je testování – můžete vygenerovat určitý počet zařízení a pak je zkopírovat do jiného centra.

*   **copyDevices** (argument 3) – nastavte tuto hodnotu na true, aby se zařízení zkopírovala z jednoho rozbočovače do druhého. 

*   **deleteSourceDevices** (argument 4) – nastavte tuto hodnotu na true, pokud chcete odstranit všechna zařízení zaregistrovaná ve zdrojovém centru. Doporučujeme počkat, dokud neskončíte všechna zařízení, která byla před spuštěním předána. Když zařízení odstraníte, nebudete je moct vrátit zpátky.

*   **deleteDestDevices** (argument 5) – nastavte tuto hodnotu na true, pokud chcete odstranit všechna zařízení zaregistrovaná do cílového centra (klonování). Tato situace může být žádoucí, pokud chcete zařízení zkopírovat více než jednou. 

Příkaz Basic bude spuštěn jako *dotnet* – to oznamuje, že rozhraní .NET vytvoří místní soubor CSPROJ a pak ho spustí. Argumenty příkazového řádku můžete před spuštěním přidat do konce. 

Příkazový řádek bude vypadat jako v těchto příkladech:

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

1. Ke spuštění ukázky budete potřebovat připojovací řetězce ke starým a novým rozbočovačům IoT a k účtu úložiště, který můžete použít pro dočasné pracovní soubory. Hodnoty těchto hodnot se uloží do proměnných prostředí.

1. Pokud chcete získat hodnoty připojovacího řetězce, přihlaste se k [Azure Portal](https://portal.azure.com). 

1. Připojovací řetězce vložte do umístění, kde je můžete načíst, například Poznámkový blok. Pokud zkopírujete následující příkaz, můžete připojovací řetězce vložit přímo do umístění. Nepřidávejte mezery kolem znaménka rovná se nebo změní název proměnné. Nepotřebujete také dvojité uvozovky kolem připojovacích řetězců. Pokud vložíte uvozovky kolem připojovacího řetězce účtu úložiště, nebude to fungovat.

   V případě systému Windows se jedná o způsob nastavení proměnných prostředí:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Pro Linux se jedná o způsob, jakým definujete proměnné prostředí:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. V případě připojovacích řetězců služby IoT Hub otevřete na portálu jednotlivé rozbočovače. Můžete hledat v **prostředcích** pro centrum. Pokud znáte skupinu prostředků, můžete přejít na **skupiny prostředků**, vybrat skupinu prostředků a pak vybrat centrum ze seznamu assetů v této skupině prostředků. 

1. Vyberte **zásady sdíleného přístupu** z nastavení centra, pak vyberte **iothubowner** a zkopírujte jeden z připojovacích řetězců. Proveďte stejné pro cílové centrum. Přidejte je do příslušných příkazů SET.

1. Pro připojovací řetězec účtu úložiště Najděte účet úložiště v části **prostředky** nebo pod jeho **skupinou prostředků** a otevřete ho. 
   
1. V části nastavení vyberte **přístupové klíče** a zkopírujte jeden z připojovacích řetězců. Do textového souboru zadejte připojovací řetězec pro příslušný příkaz SET. 

Nyní máte proměnné prostředí v souboru s příkazy SET a víte, jaké argumenty příkazového řádku jsou. Pojďme spustit ukázku.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Spuštění ukázkové aplikace a použití argumentů příkazového řádku

1. Otevřete okno příkazového řádku. Vyberte Windows a zadejte, `command prompt` abyste získali okno příkazového řádku.

1. Zkopírujte příkazy, které nastaví proměnné prostředí, po jednom, a vložte je do okna příkazového řádku a vyberte Enter. Až budete hotovi, v `SET` okně příkazového řádku zadejte své proměnné prostředí a jejich hodnoty. Po zkopírování těchto příkazů do okna příkazového řádku je nebudete muset kopírovat znovu, pokud neotevřete nové okno příkazového řádku.

1. V okně příkazového řádku změňte adresáře, dokud nejste v souboru./ImportExportDevicesSample (kde existuje soubor ImportExportDevicesSample. csproj). Potom zadejte následující příkaz a přidejte argumenty příkazového řádku.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Příkaz dotnet sestaví a spustí aplikaci. Vzhledem k tomu, že předáváte možnosti při spuštění aplikace, můžete změnit jejich hodnoty pokaždé, když aplikaci spustíte. Můžete ho například chtít spustit jednou a vytvořit nová zařízení a pak znovu spustit a zkopírovat tato zařízení do nového rozbočovače atd. Můžete také provést všechny kroky ve stejném běhu, i když nebudete mít jistotu, že neodstraníte žádná zařízení, dokud nebudete chtít, abyste se klonování dokončili. Tady je příklad, který vytváří 1000 zařízení a kopíruje je do druhého centra.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Po ověření úspěšného zkopírování zařízení můžete zařízení ze zdrojového centra odebrat takto:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Spuštění ukázkové aplikace pomocí sady Visual Studio

1. Chcete-li spustit aplikaci v aplikaci Visual Studio, změňte aktuální adresář na složku, ve které se nachází soubor IoTHubServiceSamples. sln. Pak spuštěním tohoto příkazu v okně příkazového řádku otevřete řešení v aplikaci Visual Studio. To je nutné provést ve stejném příkazovém okně, kde nastavíte proměnné prostředí, takže tyto proměnné jsou známy.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Klikněte pravým tlačítkem na projekt *ImportExportDevicesSample* a vyberte **nastavit jako spouštěný projekt**.    
    
1. Nastavte proměnné v horní části programu. cs ve složce ImportExportDevicesSample pro pět možností.

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

1. Pro spuštění aplikace vyberte F5. Po dokončení práce můžete zobrazit výsledky.

### <a name="view-the-results"></a>Zobrazení výsledků 

Můžete zobrazit zařízení v [Azure Portal](https://portal.azure.com) a ověřit, zda jsou v novém umístění.

1. Přejít na nové centrum pomocí [Azure Portal](https://portal.azure.com). Vyberte své centrum a pak vyberte **zařízení IoT**. Zobrazí se zařízení, která jste právě zkopírovali ze starého centra, do klonovaného centra. Můžete také zobrazit vlastnosti naklonovaného centra. 

1. Podívejte se na účet služby Azure Storage v [Azure Portal](https://portal.azure.com) a Prohlédněte si chyby importu a exportu a podívejte se do `devicefiles` kontejneru pro `ImportErrors.log` . Pokud je tento soubor prázdný (velikost je 0), nedošlo k chybám. Pokud se pokusíte naimportovat stejné zařízení více než jednou, zařízení podruhé odmítne a přidá do souboru protokolu chybovou zprávu.

### <a name="committing-the-changes"></a>Potvrzování změn 

V tuto chvíli jste zkopírovali rozbočovač do nového umístění a migrovali zařízení do nového klonu. Teď je potřeba provést změny, aby zařízení fungovala s klonováným rozbočovačem.

Chcete-li změny potvrdit, postupujte podle kroků, které je třeba provést: 

* Aktualizujte každé zařízení a změňte IoT Hub název hostitele tak, aby odkazoval na IoT Hub název hostitele na nové centrum. Měli byste to udělat pomocí stejné metody, jakou jste použili při prvním zřízení zařízení.

* Změňte všechny aplikace, které máte, na starém rozbočovači, aby odkazovaly na nové centrum.

* Až budete hotovi, nový rozbočovač by měl být spuštěný. Původní rozbočovač by neměl mít žádná aktivní zařízení a musí být v odpojeném stavu. 

### <a name="rolling-back-the-changes"></a>Vrácení změn zpět

Pokud se rozhodnete změny vrátit zpět, proveďte následující kroky:

* Aktualizujte každé zařízení a změňte IoT Hub název hostitele tak, aby odkazoval na IoT Hub název hostitele pro původní centrum. Měli byste to udělat pomocí stejné metody, jakou jste použili při prvním zřízení zařízení.

* Změňte všechny aplikace, které máte, na nové centrum, aby odkazovaly na původní centrum. Pokud například používáte Azure Analytics, možná budete muset změnit konfiguraci [Azure Stream Analyticsho vstupu](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Odstraní nové centrum. 

* Pokud máte prostředky směrování, konfigurace ve starém rozbočovači musí stále ukazovat na správnou konfiguraci směrování a po restartování centra by měla pracovat s těmito prostředky.

### <a name="checking-the-results"></a>Kontrola výsledků 

Pokud chcete výsledky kontrolovat, změňte řešení IoT tak, aby odkazovalo na vaše centrum v novém umístění a spustilo ho. Jinými slovy, proveďte stejné akce s novým centrem, které jste provedli v předchozím centru, a ujistěte se, že fungují správně. 

Pokud jste implementovali směrování, otestujte a ujistěte se, že jsou vaše zprávy správně směrovány do prostředků.

## <a name="clean-up"></a>Vyčištění

Neprovádějte čištění, dokud nebudete mít jistotu, že je nový rozbočovač spuštěný a že zařízení fungují správně. Také Nezapomeňte otestovat směrování, pokud tuto funkci používáte. Až budete připraveni, vyčistěte staré prostředky provedením těchto kroků:

* Pokud jste to ještě neudělali, odstraňte staré centrum. Tím se z centra odstraní všechna aktivní zařízení.

* Pokud máte prostředky směrování, které jste přesunuli do nového umístění, můžete odstranit staré prostředky směrování.

## <a name="next-steps"></a>Další kroky

Naklonujte centrum IoT do nového centra v nové oblasti, které se dokončí se zařízeními. Další informace o provádění hromadných operací s registrem identit v IoT Hub najdete v části [Import a export IoT Hub identit zařízení hromadně](iot-hub-bulk-identity-mgmt.md).

Další informace o IoT Hub a vývoji pro centrum najdete v následujících článcích.

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)

* [Kurz směrování IoT Hub](tutorial-routing.md)

* [Přehled správy zařízení IoT Hub](iot-hub-device-management-overview.md)

* Pokud chcete nasadit ukázkovou aplikaci, přečtěte si prosím [nasazení aplikace .NET Core](/dotnet/core/deploying/index).