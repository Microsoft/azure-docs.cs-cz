---
title: Posílat data zařízení přes transparentní brány – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: K odesílání dat do služby IoT Hub zařízení prostřednictvím zařízení nakonfigurovaný jako transparentní brána použijte svůj vývojářský počítač jako Simulovaná zařízení IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12793ff28bf13f26bc2cc3d436b644601fc48ac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081382"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Kurz: Posílat data přes transparentní brány

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

V tomto článku používáme vývojovém počítači znovu jako Simulovaná zařízení, ale místo odesílání dat přímo ke službě IoT Hub zařízení odesílá data do zařízení IoT Edge, který je nakonfigurovaný jako transparentní brána.

Můžeme monitorovat používání zařízení IoT Edge, zatímco simulovaného zařízení odesílá data. Po dokončení zařízení se systémem, podíváme na data v našich účet úložiště a ověřit vše fungovalo podle očekávání.

Tento krok obvykle provádí vývojář cloudu nebo zařízení.

## <a name="review-device-harness"></a>Projděte si prostředí zařízení

Znovu použít [DeviceHarness projektu](tutorial-machine-learning-edge-03-generate-data.md) simulovat příjem dat (nebo listové) zařízení. Připojení k transparentní brána vyžaduje další dvě věci:

* Zaregistrovat certifikát, který chcete vytvořit podřízený zařízení (v tomto případě náš vývojový počítač) důvěryhodná certifikační autorita používá modul runtime IoT Edge.
* Přidáte hraniční bránu plně kvalifikovaný název domény (FQDN) na připojovací řetězec zařízení.

Prohlédněte si kód chcete zobrazit, jak se implementují tyto dvě položky.

1. Na svém vývojovém počítači otevřete Visual Studio Code.

2. Použití **souboru** > **otevřete složku...**  otevřete C:\\zdroj\\IoTEdgeAndMlSample\\DeviceHarness.

3. Podívejte se na metodu InstallCertificate() v souboru Program.cs.

4. Všimněte si, že pokud kód najde cestě k certifikátu, volá metodu CertificateManager.InstallCACert k instalaci certifikátu v počítači.

5. Nyní se podívejte na metodu GetIotHubDevice TurbofanDevice třídy.

6. Když uživatel zadá plně kvalifikovaného názvu domény pomocí brány "-g" možnost, že je hodnota předaná této metodě jako gatewayFqdn, který získá připojovací řetězec zařízení připojí.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Sestavte a spusťte zařízení typu list

1. S projektem DeviceHarness stále otevřen v aplikaci Visual Studio Code, sestavte projekt (Ctrl + Shift + B nebo **terminálu** > **spustit úkol sestavení...** ) a vyberte **sestavení** z tohoto dialogového okna.

2. Najít plně kvalifikovaný název domény (FQDN) pro bránu edge tak, že přejdete na virtuální počítač zařízení IoT Edge na portálu a kopíruje hodnotu pro **název DNS** Přehled.

3. Otevřete terminál aplikace Visual Studio Code (**terminálu** > **novém terminálu**) a spusťte následující příkaz a nahraďte `<edge_device_fqdn>` s názvem DNS, který jste zkopírovali z virtuálního počítače:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Aplikace se pokouší nainstalovat certifikát do svého vývojového počítače. Pokud ano, přijměte upozornění zabezpečení.

5. Po zobrazení výzvy k kliknutím na připojovací řetězec služby IoT Hub se třemi tečkami ( **...** ) na Azure IoT Hub zařízení panelu a vyberte **připojovacího řetězce centra IoT kopírování**. Vložte hodnotu do terminálu.

6. Zobrazí se výstup podobný:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Poznámka: přidání "GatewayHostName" na připojovací řetězec zařízení, což způsobí, že zařízení komunikovat prostřednictvím služby IoT Hub jako transparentní brána IoT Edge.

## <a name="check-output"></a>Zkontrolujte výstup

### <a name="iot-edge-device-output"></a>Výstup zařízení IoT Edge

Výstup z modulu avroFileWriter lze snadno pozorovat pohledem na zařízení IoT Edge.

1. Připojte přes SSH k virtuálnímu počítači, IoT Edge.

2. Hledat soubory zapsané na disk.

   ```bash
   find /data/avrofiles -type f
   ```

3. Výstup tohoto příkazu bude vypadat jako v následujícím příkladu:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Může mít víc než jeden soubor v závislosti na načasování spustit.

4. Věnujte pozornost časová razítka. Modul avroFileWriter tyto soubory nahraje do cloudu po více než 10 minut v minulosti čas poslední změny (viz ZMĚNĚNÉ\_souboru\_uploader.py v modulu avroFileWriter vypršel časový limit).

5. Po uplynutí 10 minut by měl modul nahrajte soubory. Pokud bude odesílání úspěšné, odstraní soubory z disku.

### <a name="azure-storage"></a>Úložiště Azure

Můžete podle našich zkušeností výsledky našich odesílání dat pohledem na účty úložiště, kde Očekáváme, že data bude směrovat zařízení typu list.

1. Otevřete Visual Studio Code na vývojovém počítači.

2. V panelu "AZURE STORAGE" v okně prozkoumat procházení stromu a vyhledejte svůj účet úložiště.

3. Rozbalte **kontejnery objektů Blob** uzlu.

4. Z práce jsme to udělali v předchozí části tohoto kurzu jsme očekávat, že **ruldata** kontejner může obsahovat zpráv pomocí hodnot RUL. Rozbalte **ruldata** uzlu.

5. Zobrazí se jeden nebo více souborů objektů blob s názvem, jako jsou: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Klikněte pravým tlačítkem na jeden ze souborů a zvolte **stáhnout objekt Blob** k uložení souboru do svého vývojového počítače.

7. Dále rozšířit **uploadturbofanfiles** uzlu. V předchozím článku nastavíme toto umístění jako cíl pro soubory nahrané avroFileWriter modulu.

8. Klikněte pravým tlačítkem na soubor a zvolte **stáhnout objekt Blob** uložte ho do svého vývojového počítače.

### <a name="read-avro-file-contents"></a>Obsah souboru pro čtení Avro

Jsme zahrnuli jednoduchého nástroje příkazového řádku pro čtení souboru Avro a vrátí řetězec JSON zpráv v souboru. V této části jsme se nainstalujte a spusťte ho.

1. Otevřete terminál ve Visual Studio Code (**terminálu** > **novém terminálu**).

2. Nainstalujte hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Čtení souboru Avro, který jste stáhli ze služby pomocí hubavroreader **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Všimněte si, že vyhledá text zprávy očekává se ID zařízení a předpovídat zbývající doby životnosti.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. Spustit stejný příkaz Avro soubor, který jste stáhli ze služby předávání **uploadturbofanfiles**.

6. Podle očekávání, tyto zprávy obsahují všechna data ze senzorů a provozní nastavení z původní zprávy. Tato data může použít ke zlepšení modelu zbývající doby životnosti v našich hraničním zařízení.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": “<time>”
   }
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete prozkoumat prostředky používané v tomto kurzu začátku do konce, počkejte, dokud nedokončíte Pokud chcete vyčistit prostředky, které jste vytvořili. Pokud neplánujete pokračovat, je odstranit, použijte následující kroky:

1. Odstranění skupiny prostředků vytvořené pro virtuální počítač Dev, IoT Edge virtuálního počítače, služby IoT Hub, účet úložiště, službu pracovního prostoru machine learning (a vytvořili prostředky: registr, služby application insights, trezor klíčů, účtu úložiště).

2. Odstranění projektu machine learningu v [poznámkových bloků Azure](https://notebooks.azure.com).

3. Pokud jste naklonovali úložiště místně, zavřete všechna okna prostředí PowerShell nebo VS Code odkazující na místní úložiště, odstraňte adresář úložiště.

4. Pokud jste nevytvořili místně certifikáty, odstraňte c: složka\\edgeCertificates.

## <a name="next-steps"></a>Další postup

V tomto článku jsme použili náš vývojový počítač pro simulaci odesílání snímače zařízení typu list a provozních dat k naší hraniční zařízení. Jsme ověřili, že modulů na zařízení směrovat, klasifikovat, trvalé a nejprve nahráli do něj data tím, že kontroluje v reálném čase operace hraniční zařízení a pak zobrazením soubory nahrát do účtu úložiště.

Další informace najdete na následujících stránkách:

* [Připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md)
* [Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)](how-to-store-data-blob.md)
