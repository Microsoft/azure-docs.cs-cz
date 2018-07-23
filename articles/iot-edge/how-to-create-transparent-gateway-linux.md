---
title: Vytvoření transparentní brány s použitím Azure IoT Edge – Linux | Dokumentace Microsoftu
description: Použití Azure IoT Edge k vytvoření transparentní brány, která dokáže zpracovávat informace pro různá zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f54001c26938ea508111542b930b189342303633
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186858"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Vytvoření zařízení Linux IoT Edge, který funguje jako transparentní brána

Tento článek obsahuje podrobné pokyny k používání zařízení IoT Edge jako transparentní brána. Pro zbývající část tohoto článku termín *brána IoT Edge* odkazuje na zařízení IoT Edge použít jako transparentní brána. Další informace najdete v tématu [zařízení jak IoT Edge může sloužit jako brána][lnk-edge-as-gateway], která poskytuje koncepční přehled.

>[!NOTE]
>Aktuálně:
> * Pokud brána není připojený k ze služby IoT Hub, se nemůže ověřit podřízené zařízení s bránou.
> * Hraniční zařízení nemůže připojit k brány IoT Edge. 
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Obtížnou část o vytvoření transparentní brány je bezpečné připojení brány pro příjem dat zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných KLÍČŮ nastavení zabezpečení připojení protokol TLS mezi tato zařízení. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána.  Zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu hraniční zařízení, protože pouze chcete, aby vaše zařízení připojující se k vaší brány a potenciálně škodlivých bránu.

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se budeme předpokládat stejné nastavení certifikátu, který můžete použít k povolení [zabezpečení certifikační Autority X.509] [ lnk-iothub-x509] ve službě IoT Hub, která zahrnuje přidružený ke konkrétní IoT hub (IoT hub vlastníka certifikační Autority certifikátu webu X.509 ) a řadě certifikáty podepsané této certifikační Autority a certifikační Autority pro hraniční zařízení.

![Instalační program brány][1]

Brána svůj certifikát hraniční zařízení certifikační Autority podřízené zařízení během inicializace připojení. Příjem dat zařízení zkontroluje, ujistěte se, že hraniční zařízení certifikační Autority certifikát je podepsaný certifikátem pro certifikační Autority vlastníka. Tento proces povolí příjem dat zařízení potvrďte, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytváření certifikátů a jejich instalace na správných místech.

## <a name="prerequisites"></a>Požadavky
1.  Nainstalujte modul runtime Azure IoT Edge na zařízení systému Linux, kterou chcete použít jako transparentní brána.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  Získáte skripty ke generování požadované certifikáty li se o neprodukční pomocí následujícího příkazu. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Tyto skripty používají ke generování požadované certifikáty OpenSSL a OpenSSL vyžaduje některé nastavení.
   
   1. Přejděte do adresáře, ve kterém chcete pracovat. Dále budeme odkazovat na tuto jako $WRKDIR.  Vytvoří se všechny soubory v tomto adresáři.

      CD $WRKDIR
   
   1. Zkopírujte soubory config a skript do pracovního adresáře.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Vytvoření certifikátu
1.  Vytvořte certifikát certifikační Autority vlastníka a jeden zprostředkující certifikát. Tyto jsou umístěny v `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   Výstupy provádění skriptu jsou následující certifikáty a klíče:
   * Certifikáty
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Klíče
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Pomocí následujícího příkazu vytvořte certifikát hraniční zařízení certifikační Autority a privátního klíče.

   >[!NOTE]
   > **Ne** použít název, který je stejný jako název hostitele DNS této brány. To způsobí, že certifikát klienta pro tyto certifikáty k selhání.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   Výstupy provádění skriptu jsou následující klíče a certifikáty:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Vytvoření řetězce certifikátu
Z certifikátu certifikační Autority vlastníka, zprostředkující certifikát a certifikát certifikační Autority hraničních zařízení pomocí následujícího příkazu vytvořte řetěz certifikátů. Umístíte do řetězce souboru umožňuje snadno nainstalovat na hraniční zařízení naprogramujete sloužit jako transparentní brána.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Instalace brány
1.  Zkopírujte následující soubory z $WRKDIR kdekoli v hraničním zařízení, budeme odkazovat na tento jako $CERTDIR. Tento krok přeskočte, pokud se generuje certifikáty na hraniční zařízení.

   * Certifikát certifikační Autority zařízení –  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Privátní klíč certifikační Autority zařízení – `$WRKDIR/private/new-edge-device.key.pem`
   * Vlastník CA- `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Nastavte `certificate` vlastností v umístění souborů certifikát a klíč souboru yaml zabezpečení démon konfigurace k cestě.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub do brány
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Tato část obsahuje, můžete vytvořit zdánlivě prázdný nasazení; Centrum Edge se ale automatcially přidat pro všechna nasazení, i když nejsou žádné další moduly, které jsou k dispozici. Centrum Edge se pouze modul, který je potřeba na hraniční zařízení ho sloužit jako transparentní brána tak vytváření prázdného nasazení je dostatečná. 
1. Na webu Azure Portal přejděte do svého centra IoT.
2. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge, který chcete použít jako bránu.
3. Vyberte **Nastavit moduly**.
4. Vyberte **Další**.
5. V kroku **Specify routes** (Zadejte trasy) musíte mít výchozí trasu, která odesílá všechny zprávy ze všech modulů do IoT Hubu. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. V kroku zkontrolujte šablony, vyberte **odeslat**.

## <a name="installation-on-the-downstream-device"></a>Instalace na příjem dat zařízení
Příjem dat zařízení může být libovolná aplikace pomocí [zařízení Azure IoT SDK][lnk-devicesdk], jako je jednoduché je popsáno v [připojení zařízení k IoT hubu pomocí .NET] [ lnk-iothub-getstarted]. Aplikaci pro příjem dat zařízení musí důvěřovat **vlastníka certifikační Autority** certifikát k ověřování TLS připojení k zařízení brány. Tento krok lze obvykle provést dvěma způsoby: na úrovni operačního systému, nebo (pro některé jazyky) na úrovni aplikace.

### <a name="os-level"></a>Úroveň operačního systému
Instalace tohoto certifikátu v úložišti certifikátů operačního systému vám umožní všechny aplikace, aby používaly vlastníka certifikát certifikační Autority jako důvěryhodný certifikát.

* Ubuntu – tady je příklad toho, jak nainstalovat certifikát certifikační Autority na hostiteli se systémem Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Zobrazí se zpráva, že "aktualizace certifikáty v /etc/ssl/certs... 1 přidání, odebrání 0; Hotovo."

* Windows – tady je příklad toho, jak nainstalovat certifikát certifikační Autority na hostiteli Windows.
  * V nabídce start se zadejte v "Spravovat certifikáty počítače". To by měl vyvolat nástroj, který volá `certlm`.
  * Přejděte na certifikátů místního počítače--> důvěryhodných kořenových certifikátů--> certifikáty--> vpravo klikněte na tlačítko--> všechny úlohy--> Import ke spuštění Průvodce importem certifikátu.
  * Postupujte podle pokynů a importovat $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem soubor certifikátu.
  * Po dokončení, měli byste vidět zprávu "Úspěšně naimportoval".

### <a name="application-level"></a>Úrovni aplikace
Pro aplikace .NET můžete přidat následující fragment kódu důvěřovat certifikátu ve formátu PEM. Inicializovat proměnnou `certPath` s `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Připojení zařízení příjem dat k bráně
Sady sdk služby IoT Hub device musíte inicializovat připojovací řetězec odkazující na název hostitele zařízení brány. Uděláte to pomocí připojení `GatewayHostName` vlastnost připojovací řetězec zařízení. Například tady je ukázka zařízení připojovací řetězec pro zařízení, do které budeme připojí `GatewayHostName` vlastnost:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Toto je ukázkový příkaz, jaké testy, které všechno, co bylo nastavit správně. Můžete sohuld zpráva s oznámením "ověřit OK".
   >
   >OpenSSL s_client-connect - CAfile mygateway.contoso.com:8883 $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení příjem dat
Modul runtime IoT Edge může směrovat zprávy odeslané ze zařízení příjem dat, stejně jako zprávy odeslané moduly. To umožňuje provádět analýzy v modulu na bráně spuštěna před odesláním všechna data do cloudu. Níže trasy se použije pro odesílání zpráv ze zařízení příjem dat s názvem `sensor` název modulu `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Odkazovat [modulu složení článku] [ lnk-module-composition] podrobné informace o směrování zpráv.

## <a name="next-steps"></a>Další postup
[Pochopení požadavků a nástroje pro vývoj modulů IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/quickstart-send-telemetry-dotnet.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
