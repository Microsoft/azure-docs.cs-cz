---
title: 'Kurz: odesílání dat zařízení přes transparentní bránu – Machine Learning v Azure IoT Edge'
description: V tomto kurzu se dozvíte, jak můžete použít vývojový počítač jako simulované IoT Edge zařízení a posílat data do IoT Hub tím, že projdete zařízení nakonfigurované jako transparentní bránu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 25320fb8cde0cc5a38f1edf4019a2f0e07cefe14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461092"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Kurz: posílání dat přes transparentní bránu

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

V tomto článku se znovu používá vývojový virtuální počítač jako simulované zařízení. Místo odesílání dat přímo do IoT Hub ale zařízení odesílá data do IoT Edgeho zařízení nakonfigurovaného jako transparentní bránu.

Sledujeme operace IoT Edge zařízení, zatímco simulované zařízení odesílá data. Jakmile se zařízení dokončí, podíváme se na data v našem účtu úložiště, abyste ověřili, co všechno fungovalo podle očekávání.

Tento krok obvykle provádí vývojář cloudu nebo zařízení.

V této části kurzu se dozvíte, jak:

> [!div class="checklist"]
>
> * Sestavování a spouštění zařízení typu list.
> * Ověřte, že se vygenerovaná data ukládají do úložiště objektů BLOB v Azure.
> * Ověřte, jestli model strojového učení klasifikuje data zařízení.

## <a name="prerequisites"></a>Předpoklady

Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přejděte na [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="review-device-harness"></a>Zkontrolovat svazek zařízení

Znovu použijte [projekt DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) k simulaci zařízení pro příjem dat (nebo list). Připojení k transparentní bráně vyžaduje dvě další věci:

* Zaregistrujte certifikát, aby zařízení IoT pro příjem dat důvěřovalo certifikační autoritě, kterou používá modul runtime IoT Edge. V našem případě je pro zařízení pro příjem dat vývojový virtuální počítač.
* Přidejte do připojovacího řetězce zařízení plně kvalifikovaný název domény (FQDN) hraniční brány.

Podívejte se na kód a podívejte se, jak jsou tyto dvě položky implementovány.

1. Na vývojovém počítači otevřete Visual Studio Code.

1. Pomocí   >  **otevřené složky** souborů otevřete soubor C: \\ source \\ IoTEdgeAndMlSample \\ DeviceHarness.

1. Podívejte se na metodu InstallCertificate () v programu program. cs.

1. Všimněte si, že pokud kód nalezne cestu k certifikátu, zavolá metodu CertificateManager. InstallCACert pro instalaci certifikátu do počítače.

1. Nyní se podívejte na metodu GetIotHubDevice třídy TurbofanDevice.

1. Pokud uživatel určí plně kvalifikovaný název domény brány pomocí možnosti "-g", tato hodnota se předává této metodě jako `gatewayFqdn` Proměnná, která se připojí k připojovacímu řetězci zařízení.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Sestavování a spouštění zařízení typu list

1. Když je projekt DeviceHarness stále otevřený v Visual Studio Code, sestavte projekt. V nabídce **terminálu** vyberte **Spustit úlohu sestavení** a vyberte **sestavení**.

1. Najděte plně kvalifikovaný název domény (FQDN) pro bránu Edge tak, že přejdete na zařízení IoT Edge (virtuální počítač se systémem Linux) v Azure Portal a zkopírujete hodnotu pro **název DNS** ze stránky přehled.

1. Spusťte zařízení IoT (virtuální počítač se systémem Linux), pokud ještě není spuštěno.

1. Otevřete Visual Studio Code terminálu. V nabídce **terminál** vyberte **nový terminál** a spusťte následující příkaz a nahraďte `<edge_device_fqdn>` názvem DNS, který jste zkopírovali ze zařízení IoT Edge (virtuální počítač Linux):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Aplikace se pokusí nainstalovat certifikát do vývojového počítače. Pokud k tomu dojde, přijměte upozornění zabezpečení.

1. Po zobrazení výzvy k zadání připojovacího řetězce IoT Hub klikněte na tlačítko se třemi tečkami (**...**) na panelu zařízení Azure IoT Hub a vyberte **Kopírovat IoT Hub připojovací řetězec**. Vložte hodnotu do terminálu.

1. Zobrazí se výstup podobný tomuto:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Všimněte si přidání "GatewayHostName" do připojovacího řetězce zařízení, což způsobí, že zařízení komunikuje prostřednictvím IoT Hub přes IoT Edge transparentní bránu.

## <a name="check-output"></a>Kontrolovat výstup

### <a name="iot-edge-device-output"></a>Výstup zařízení IoT Edge

Výstup z modulu avroFileWriter se dá snadno pozorovat tak, že se podíváte na zařízení IoT Edge.

1. K vašemu IoT Edgemu virtuálnímu počítači se SSH.

1. Vyhledejte soubory zapsané na disk.

   ```bash
   find /data/avrofiles -type f
   ```

1. Výstup příkazu bude vypadat jako v následujícím příkladu:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   V závislosti na časování běhu můžete mít více než jeden soubor.

1. Věnujte pozornost časovým razítkům. Modul avroFileWriter nahrává soubory do cloudu, jakmile je čas poslední změny v minulosti více než 10 minut (viz \_ časový limit upraveného souboru \_ v Uploader.py v modulu avroFileWriter).

1. Po uplynutí 10 minut by měl modul nahrávat soubory. Pokud je nahrání úspěšné, odstraní soubory z disku.

### <a name="azure-storage"></a>Azure Storage

Na základě účtů úložiště, ve kterých očekáváme, že se data mají směrovat, můžeme sledovat výsledky našeho zařízení na listech, které odesílají data.

1. Na vývojovém počítači otevřete Visual Studio Code.

1. Na panelu úložiště AZURE v okně prozkoumat přejděte ke stromu, kde najdete účet úložiště.

1. Rozbalte uzel **kontejnery objektů BLOB** .

1. Od práce, kterou jsme provedli v předchozí části tohoto kurzu, očekáváme, že kontejner **ruldata** by měl obsahovat zprávy s RUL. Rozbalte uzel **ruldata** .

1. Zobrazí se jeden nebo více souborů objektů BLOB s názvem, jako je: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Klikněte pravým tlačítkem na jeden ze souborů a vyberte **Stáhnout objekt BLOB** a uložte soubor do vývojového počítače.

1. Dále rozbalte uzel **uploadturbofanfiles** . V předchozím článku jsme toto umístění nastavili jako cíl pro soubory odeslané modulem avroFileWriter.

1. Klikněte pravým tlačítkem na soubory a vyberte **Stáhnout objekt BLOB** a uložte ho do vývojového počítače.

### <a name="read-avro-file-contents"></a>Číst obsah souboru Avro

Zahrnuli jsme jednoduchý nástroj příkazového řádku pro čtení souboru Avro a vrácení řetězce JSON zpráv v souboru. V této části ji Nainstalujeme a spustíte.

1. Otevřete terminál v Visual Studio Code (**terminál**  >  **nový terminál**).

1. Nainstalovat hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Pomocí hubavroreader si přečtěte soubor Avro, který jste stáhli z **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Všimněte si, že tělo zprávy vypadá podle očekávání s ID zařízení a předpovězeným RUL.

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

1. Spusťte stejný příkaz předáním souboru Avro, který jste stáhli z **uploadturbofanfiles**.

1. Podle očekávání obsahují tyto zprávy všechna data senzorů a provozní nastavení z původní zprávy. Tato data se dají použít ke zlepšení modelu RUL na našem hraničním zařízení.

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

Pokud plánujete prozkoumat prostředky používané v tomto uceleném kurzu, počkejte, až budete hotovi, a vyčistěte prostředky, které jste vytvořili. V opačném případě je odstraňte pomocí následujících kroků:

1. Odstraňte vytvořené skupiny prostředků pro vývoj virtuálních počítačů, IoT Edge virtuálních počítačů, IoT Hub, účtu úložiště, služby Machine Learning Workspace Service (a vytvořených prostředků: registr kontejnerů, Application Insights, Trezor klíčů, účet úložiště).

1. Odstraňte projekt Machine Learning v [Azure Notebooks](https://notebooks.azure.com).

1. Pokud jste úložiště naklonováni místně, zavřete všechny prostředí PowerShell nebo VS Code Windows odkazující na místní úložiště a pak odstraňte adresář úložiště.

1. Pokud jste certifikáty vytvořili místně, odstraňte složku c: \\ edgeCertificates.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili náš vývojový virtuální počítač pro simulaci zařízení, které odesílá senzor a provozní data do našeho IoT Edge zařízení. Ověřili jsme, že moduly v zařízení směrované, klasifikované, zachované a nahrály data pomocí prověření operace hraničního zařízení v reálném čase a prohlížením souborů odeslaných do účtu úložiště.

Pokud chcete pokračovat ve získávání možností IoT Edge, zkuste tento kurz dál:

> [!div class="nextstepaction"]
> [Vytvoření hierarchie zařízení IoT Edge (Preview)](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)