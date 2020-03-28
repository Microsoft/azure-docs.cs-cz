---
title: 'Kurz: Odesílání dat zařízení přes transparentní bránu – Machine Learning na Azure IoT Edge'
description: Tento kurz ukazuje, jak můžete použít vývojový počítač jako simulované zařízení IoT Edge k odesílání dat do služby IoT Hub procházením zařízení nakonfigurovaného jako transparentní brána.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706915"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Kurz: Odesílání dat přes transparentní bránu

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

V tomto článku znovu použijeme vývojový počítač jako simulované zařízení, ale místo odesílání dat přímo do služby IoT Hub zařízení odesílá data do zařízení IoT Edge nakonfigurovaného jako transparentní brána.

Sledujeme provoz zařízení IoT Edge, zatímco simulované zařízení odesílá data. Po dokončení provozu zařízení se podíváme na data v našem účtu úložiště, abychom ověřili, že vše fungovalo podle očekávání.

Tento krok obvykle provádí vývojář cloudu nebo zařízení.

## <a name="review-device-harness"></a>Zkontrolovat postroj zařízení

Znovu použít [DeviceHarness projektu](tutorial-machine-learning-edge-03-generate-data.md) simulovat downstream (nebo list) zařízení. Připojení k průhledné bráně vyžaduje dvě další věci:

* Zaregistrujte certifikát, aby navazující zařízení (v tomto případě náš vývojový počítač) důvěřoval certifikační autoritě používané runtime IoT Edge.
* Přidejte plně kvalifikovaný název domény (FQDN) hraniční brány do připojovacího řetězce zařízení.

Podívejte se na kód a zjistěte, jak jsou implementovány tyto dvě položky.

1. Ve vývojovém počítači otevřete visual studio kód.

2. Pomocí**složky Otevřít** **soubor...** > \\otevřete nástroj C:\\\\zdroj IoTEdgeAndMlSample DeviceHarness.

3. Podívejte se na Metodu InstallCertificate() v Program.cs.

4. Všimněte si, že pokud kód najde cestu k certifikátu, volá metodu CertificateManager.InstallCACert k instalaci certifikátu do počítače.

5. Nyní se podívejte na metodu GetIotHubDevice ve třídě TurbofanDevice.

6. Když uživatel určí hlavní název sítě brány pomocí možnosti "-g", tato hodnota je předána této metodě jako gatewayFqdn, který získá připojen k připojovacímu řetězci zařízení.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Sestavení a spuštění zařízení list

1. Pokud je projekt DeviceHarness stále otevřený v kódu sady Visual Studio, sestavte projekt (Ctrl + Shift + B nebo Úloha sestavení **terminálu** > **spustit...**) a v dialogovém okně vyberte **Sestavit.**

2. Najděte plně kvalifikovaný název domény (FQDN) pro bránu hraniční ho dužiny tak, že přejdete na virtuální počítač zařízení IoT Edge na portálu a zkopírujete hodnotu **názvu DNS** z přehledu.

3. Otevřete terminál kódu Visual Studia **(Terminál** > **Nový terminál)** `<edge_device_fqdn>` a spusťte následující příkaz, který nahradí název DNS, který jste zkopírovali z virtuálního počítače:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Aplikace se pokusí nainstalovat certifikát do vývojového počítače. Pokud ano, přijměte upozornění zabezpečení.

5. Po zobrazení výzvy k zadání připojovacího řetězce služby IoT Hub klikněte na tři tečky (**...**) na panelu zařízení Azure IoT Hub a vyberte **Kopírovat připojovací řetězec služby IoT Hub**. Vložte hodnotu do terminálu.

6. Uvidíte výstup jako:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Všimněte si přidání "GatewayHostName" do připojovacího řetězce zařízení, který způsobí, že zařízení komunikovat prostřednictvím služby IoT Hub prostřednictvím brány transparentní IoT Edge.

## <a name="check-output"></a>Zkontrolovat výstup

### <a name="iot-edge-device-output"></a>Výstup zařízení IoT Edge

Výstup z modulu avroFileWriter lze snadno sledovat při pohledu na zařízení IoT Edge.

1. SSH do virtuálního počítače IoT Edge.

2. Vyhledejte soubory zapsané na disk.

   ```bash
   find /data/avrofiles -type f
   ```

3. Výstup příkazu bude vypadat jako v následujícím příkladu:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   V závislosti na časování spuštění může mít více než jeden soubor.

4. Dávejte pozor na časová razítka. Modul avroFileWriter nahraje soubory do cloudu, jakmile je čas poslední změny více\_než\_10 minut v minulosti (viz UPRAVENÝ ČASOVÝ ČAS SOUBORU v uploader.py v modulu avroFileWriter).

5. Po uplynutí 10 minut by měl modul nahrát soubory. Pokud je nahrávání úspěšné, odstraní soubory z disku.

### <a name="azure-storage"></a>Úložiště Azure

Výsledky našeho listového zařízení, které odesílá data, můžeme sledovat na účtech úložiště, kde očekáváme, že data budou směrována.

1. Ve vývojovém počítači otevřete visual studio kód.

2. V panelu "AZURE STORAGE" v okně prozkoumat, přejděte do stromu najít svůj účet úložiště.

3. Rozbalte uzel **kontejnerů objektů blob.**

4. Z práce, kterou jsme provedli v předchozí části kurzu, očekáváme, že kontejner **ruldata** by měl obsahovat zprávy s RUL. Rozbalte uzel **ruldata.**

5. Zobrazí se jeden nebo více souborů `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`objektů BLOB s názvem: .

6. Klikněte pravým tlačítkem myši na jeden ze souborů a zvolte **Stáhnout blob** pro uložení souboru do vývojového počítače.

7. Dále rozbalte uzel **uploadturbofanfiles.** V předchozím článku jsme nastavili toto umístění jako cíl pro soubory nahrané modulem avroFileWriter.

8. Klikněte pravým tlačítkem myši na soubory a zvolte **Stáhnout Blob** uložit do vývojového počítače.

### <a name="read-avro-file-contents"></a>Čtení obsahu souboru Avro

Zahrnuli jsme jednoduchý nástroj příkazového řádku pro čtení souboru Avro a vrácení řetězce JSON zpráv v souboru. V této části ji nainstalujeme a spustíme.

1. Otevřete terminál v kódu Visual Studio **(Terminál** > **Nový terminál).**

2. Instalace hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Použijte hubavroreader číst soubor Avro, který jste stáhli z **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Všimněte si, že tělo zprávy vypadá, jak jsme očekávali s ID zařízení a předpokládané RUL.

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

5. Spusťte stejný příkaz předávání souboru Avro, který jste stáhli z **uploadturbofanfiles**.

6. Podle očekávání tyto zprávy obsahují všechna data ze senzoru a provozní nastavení z původní zprávy. Tato data by mohla být použita ke zlepšení modelu RUL na našem hraničním zařízení.

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
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu prozkoumat prostředky používané v tomto kurzu end-to-end, počkejte, až budete hotovi vyčistit prostředky, které jste vytvořili. Pokud neplánujete pokračovat, odstraňte je pomocí následujících kroků:

1. Odstraňte skupiny prostředků vytvořené pro uložení virtuálního počítače Pro Vývoj, Virtuální počítač IoT Edge, IoT Hub, účet úložiště, služba pracovního prostoru pro strojové učení (a vytvořené prostředky: registr kontejnerů, přehledy aplikací, trezor klíčů, účet úložiště).

2. Odstraňte projekt strojového učení v [poznámkových blouzncích Azure](https://notebooks.azure.com).

3. Pokud jste naklonovali úložiště místně, zavřete všechna okna powershellu nebo kódu VS odkazující na místní úložiště a odstraňte adresář úložiště.

4. Pokud jste certifikáty vytvořili místně,\\odstraňte složku c: edgeCertificates.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili náš vývojový stroj k simulaci listového zařízení, které odesílá senzor a provozní data do našeho hraničního zařízení. Ověřili jsme, že moduly na zařízení směrovány, klasifikovány, trvalé a nahráldata nejprve tím, že zkoumá v reálném čase provoz hraničnízařízení a pak při pohledu na soubory nahrané do účtu úložiště.

Více informací naleznete na následujících stránkách:

* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)
* [Ukládání dat na hraniční chod pomocí Azure Blob Storage na IoT Edge (preview)](how-to-store-data-blob.md)
