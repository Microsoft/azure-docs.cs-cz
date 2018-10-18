---
title: Vytvoření transparentní brány s použitím Azure IoT Edge – Windows | Dokumentace Microsoftu
description: Použití Azure IoT Edge k vytvoření transparentní brány, která dokáže zpracovávat informace pro různá zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f584e2cdcb038c6f8e9fcdbeecc22fb957bd7f8d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394802"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Vytvoření zařízení Windows IoT Edge, který funguje jako transparentní brána

Tento článek obsahuje podrobné pokyny k používání zařízení IoT Edge jako transparentní brána. Pro zbývající část tohoto článku termín *brána IoT Edge* odkazuje na zařízení IoT Edge použít jako transparentní brána. Další informace najdete v tématu [zařízení jak IoT Edge může sloužit jako brána](./iot-edge-as-gateway.md), která poskytuje koncepční přehled.

>[!NOTE]
>Aktuálně:
> * Pokud brána není připojený k ze služby IoT Hub, se nemůže ověřit podřízené zařízení s bránou.
> * Hraniční zařízení nemůže připojit k brány IoT Edge. 
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Obtížnou část o vytvoření transparentní brány je bezpečné připojení brány pro příjem dat zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných KLÍČŮ nastavení zabezpečení připojení protokol TLS mezi tato zařízení. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána.  Zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu hraniční zařízení, protože pouze chcete, aby vaše zařízení připojující se k vaší brány a potenciálně škodlivých bránu.

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se budeme předpokládat stejné nastavení certifikátu, který můžete použít k povolení [zabezpečení certifikační Autority X.509](../iot-hub/iot-hub-x509ca-overview.md) ve službě IoT Hub, který zahrnuje certifikátu webu X.509 přidružený ke konkrétní IoT hub (IoT hub vlastník certifikační Autority) a řadou certifikáty, Hraniční zařízení zaregistrovali pomocí této certifikační Autority a certifikační Autority.

![Instalační program brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Brána svůj certifikát hraniční zařízení certifikační Autority podřízené zařízení během inicializace připojení. Příjem dat zařízení zkontroluje, ujistěte se, že hraniční zařízení certifikační Autority certifikát je podepsaný certifikátem pro certifikační Autority vlastníka. Tento proces povolí příjem dat zařízení potvrďte, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytváření certifikátů a jejich instalace na správných místech.

## <a name="prerequisites"></a>Požadavky
1. [Nainstalovat modul runtime Azure IoT Edge](./how-to-install-iot-edge-windows-with-windows.md) na zařízení s Windows, kterou chcete použít jako transparentní brána.

1. Získejte OpenSSL pro Windows. Můžete nainstalovat OpenSSL, mnoha způsoby:

   >[!NOTE]
   >Pokud už máte nainstalovaný na zařízení s Windows OpenSSL, můžete tento krok přeskočte, ale zajistěte, aby `openssl.exe` je k dispozici ve vašich `%PATH%` proměnné prostředí.

   * Stáhněte a nainstalujte všechny [binárních souborů třetí strany OpenSSL](https://wiki.openssl.org/index.php/Binaries), například z [tento projekt na SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Stáhněte si zdrojový kód OpenSSL a sestavit binární soubory na svém počítači sami nebo prostřednictvím [vcpkg](https://github.com/Microsoft/vcpkg). Níže uvedené pokyny používají vcpkg stáhnout zdrojový kód, kompilace a nainstalujte OpenSSL na svůj počítač s Windows pomocí jednoduchých kroků.

      1. Přejděte do adresáře, kam chcete nainstalovat vcpkg. Dále budeme odkazovat na tuto jako $VCPKGDIR. Postupujte podle pokynů ke stažení a instalaci [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. Po instalaci se vcpkg z příkazového řádku powershellu, spusťte následující příkaz k instalaci balíčku OpenSSL pro Windows x64. Instalace obvykle trvá přibližně 5 minut na dokončení.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Přidat `$VCPKGDIR\installed\x64-windows\tools\openssl` do vaší `PATH` proměnnou prostředí tak, aby `openssl.exe` soubor je k dispozici pro vyvolání.

1. Přejděte do adresáře, ve kterém chcete pracovat. Dále budeme odkazovat na tuto jako $WRKDIR.  Vytvoří se všechny soubory v tomto adresáři.
   
   CD $WRKDIR

1.  Získáte skripty ke generování požadované certifikáty li se o neprodukční pomocí následujícího příkazu. Tyto skripty vám pomůžou vytvořit potřebné certifikáty pro nastavení transparentní brány.

      ```PowerShell
      git clone https://github.com/Azure/azure-iot-sdk-c.git
      ```

1. Zkopírujte soubory konfigurace a skript do pracovního adresáře. Kromě toho nastavit proměnnou env OPENSSL_CONF použijte openssl_root_ca.cnf konfigurační soubor.

   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
   ```

1. Povolit spouštění skriptů spuštěním následujícího příkazu prostředí PowerShell

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Používání funkce, které skripty, používají do globálního oboru názvů Powershellu podle dot-sourcing pomocí následujícího příkazu
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Ověřte, zda je že správně nainstalovaná OpenSSL a ujistěte se, že spuštěním následujícího příkazu nesmí být kolize názvů s využitím existujících certifikátů. Pokud dochází k problémům, by měl skript popisují, jak je opravit ve vašem systému.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Vytvoření certifikátu
1. Vytvořte certifikát certifikační Autority vlastníka a jeden zprostředkující certifikát. Certifikáty jsou umístěny v `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1. Pomocí následujícího příkazu vytvořte certifikát hraniční zařízení certifikační Autority a privátního klíče.

   >[!NOTE]
   > **Ne** použít název, který je stejný jako název hostitele DNS této brány. To způsobí, že certifikát klienta pro tyto certifikáty k selhání.

   ```PowerShell
   New-CACertsEdgeDevice "<gateway device name>"
   ```

## <a name="certificate-chain-creation"></a>Vytvoření řetězce certifikátu
Z certifikátu certifikační Autority vlastníka, zprostředkující certifikát a certifikát certifikační Autority hraničních zařízení pomocí následujícího příkazu vytvořte řetěz certifikátů. Umístíte do řetězce souboru umožňuje snadno nainstalovat na hraniční zařízení naprogramujete sloužit jako transparentní brána.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

   Tento skript vytvoří následující certifikáty a klíče:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Instalace brány
1. Zkopírujte následující soubory z $WRKDIR kdekoli v hraničním zařízení, budeme odkazovat na tento jako $CERTDIR. Pokud jste vygenerovali certifikáty na hraniční zařízení, tento krok přeskočte.

   * Certifikát certifikační Autority zařízení –  `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Privátní klíč certifikační Autority zařízení – `$WRKDIR\private\new-edge-device.key.pem`
   * Vlastník CA- `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2. Nastavte `certificate` vlastností v umístění souborů certifikát a klíč souboru yaml zabezpečení démon konfigurace k cestě.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub do brány
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Tato část obsahuje, můžete vytvořit zdánlivě prázdný nasazení; Centrum Edge se ale automaticky přidá pro všechna nasazení i v případě, že nejsou žádné další moduly, které jsou k dispozici. Centrum Edge se pouze modul, který je potřeba na hraniční zařízení ho sloužit jako transparentní brána tak vytváření prázdného nasazení je dostatečná. 
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
Příjem dat zařízení může být libovolná aplikace pomocí [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md), jako je jednoduché je popsáno v [připojení zařízení k IoT hubu pomocí .NET](../iot-hub/quickstart-send-telemetry-dotnet.md). Aplikaci pro příjem dat zařízení musí důvěřovat **vlastníka certifikační Autority** certifikát k ověřování TLS připojení k zařízení brány. Tento krok lze obvykle provést dvěma způsoby: na úrovni operačního systému, nebo (pro některé jazyky) na úrovni aplikace.

### <a name="os-level"></a>Úroveň operačního systému
Instalace tohoto certifikátu v úložišti certifikátů operačního systému vám umožní všechny aplikace, aby používaly vlastníka certifikát certifikační Autority jako důvěryhodný certifikát.

* Ubuntu – tady je příklad toho, jak nainstalovat certifikát certifikační Autority na hostiteli se systémem Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Zobrazí se zpráva, že "aktualizace certifikáty v /etc/ssl/certs... 1 přidání, odebrání 0; Hotovo."

* Windows – tady je příklad toho, jak nainstalovat certifikát certifikační Autority na hostiteli Windows.
  1. V nabídce start se zadejte v "Spravovat certifikáty počítače". To by měl vyvolat nástroj, který volá `certlm`.
  2. Přejděte do **certifikátů místního počítače** > **důvěryhodných kořenových certifikátů** > **certifikáty** > klikněte pravým tlačítkem myši klikněte na > **Všechny úkoly** > **importovat** spustíte Průvodce importem certifikátu.
  3. Postupujte podle pokynů a importovat $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem soubor certifikátu.
  4. Po dokončení, měli byste vidět zprávu "Úspěšně naimportoval".

### <a name="application-level"></a>Úrovni aplikace
Pro aplikace .NET můžete přidat následující fragment kódu důvěřovat certifikátu ve formátu PEM. Inicializovat proměnnou `certPath` s `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Připojení zařízení příjem dat k bráně
Inicializujte sadu sdk služby IoT Hub device připojovací řetězec odkazující na název hostitele zařízení brány. Uděláte to pomocí připojení `GatewayHostName` vlastnost připojovací řetězec zařízení. Například tady je ukázka zařízení připojovací řetězec pro zařízení, do které budeme připojí `GatewayHostName` vlastnost:

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

Další informace o směrování zpráv, najdete v části [složení modulu](./module-composition.md).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Další postup
[Pochopení požadavků a nástroje pro vývoj modulů IoT Edge](module-development.md).
