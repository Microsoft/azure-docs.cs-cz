---
title: Vytvořte transparentní bránu s Azure IoT okraj – Linux | Microsoft Docs
description: Použití Azure IoT Edge k vytvoření transparentní brány, který dokáže zpracovat informace pro více zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5a78d6fb8ee52f0daba80a77cc8a5e75c2e5248d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035822"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Vytvoření Linux IoT hraniční zařízení, která funguje jako brána transparentní

Tento článek obsahuje podrobné pokyny pro používání IoT hraniční zařízení jako brána transparentní. Pro zbývající část tohoto článku termín *IoT hraniční brána* odkazuje na IoT hraniční zařízení používá jako brána transparentní. Podrobnější informace najdete v části [jak IoT hraniční zařízení lze použít jako brána][lnk-edge-as-gateway], což dává koncepční přehled.

>[!NOTE]
>Aktuálně:
> * Pokud brána je odpojená ze služby IoT Hub, podřízené zařízení nemůže ověřit pomocí brány.
> * IoT hraniční zařízení nemůže připojit k IoT hraniční brány.
> * Podřízené zařízení nelze používat nahrávání souborů.

Pevné část o vytváření transparentní brána se připojuje bezpečně bránu pro příjem dat zařízení. Azure IoT Edge umožňuje používat infrastrukturu veřejných KLÍČŮ nastavit zabezpečené připojení TLS mezi tato zařízení. V takovém případě se bychom povolovat podřízené zařízení pro připojení k IoT hraniční zařízení funguje jako brána transparentní.  K zachování přiměřené zabezpečení, by měl podřízené zařízení potvrdit identitu hraniční zařízení vzhledem k tomu, že chcete pouze vaše zařízení připojující se k vaší brány a není potenciálně škodlivého brány.

Můžete vytvořit všechny infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku předpokládáme nastavení stejný certifikát, který byste použili k povolení [zabezpečení certifikační Autority X.509] [ lnk-iothub-x509] IoT hub, která zahrnuje certifikát certifikační Autority X.509, který je přidružený ke konkrétní Centrum IoT (IoT hub vlastník certifikační Autority ) a řadu certifikáty podepsané této certifikační Autority a certifikační Autoritu pro hraniční zařízení.

![Instalační program brány][1]

Brána svůj certifikát hraniční zařízení certifikační Autority podřízené zařízení během inicializace připojení. Podřízené zařízení zkontroluje, ujistěte se, že certifikát hraniční zařízení certifikační Autority je podepsaný certifikát certifikační Autority vlastníka. Tento proces umožňuje podřízené zařízení potvrďte, že brána pochází z důvěryhodného zdroje.

Následující postup vás provede procesem vytváření certifikátů a jejich instalace na správných místech.

## <a name="prerequisites"></a>Požadavky
1.  Nainstalujte modul runtime Azure IoT Edge na Linux zařízení, které chcete použít jako bránu transparentní.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  Získáte skripty ke generování požadované certifikáty mimo produkční pomocí následujícího příkazu. Tyto skripty vám pomůže vytvořit potřebné certifikáty nastavit bránu transparentní. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Tyto skripty používat OpenSSL ke generování požadované certifikáty a OpenSSL vyžaduje některé instalační program.
   
   1. Přejděte do adresáře, ve kterém chcete pracovat. Od teď budete označujeme to $WRKDIR.  Všechny soubory budou vytvořeny v tomto adresáři.

      CD $WRKDIR
   
   1. Zkopírujte soubory konfigurace a skript do pracovního adresáře.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Vytvoření certifikátu
1.  Vytvořte certifikát certifikační Autority vlastníka a jeden zprostředkující certifikát. Tyto jsou všechny umístěny v `$WRKDIR`.

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

2.  Vytvořte certifikát hraniční zařízení certifikační Autority a privátního klíče pomocí níže uvedeného příkazu.

   >[!NOTE]
   > **NECHCETE** použijte název, který je stejný jako název hostitele DNS pro bránu. To způsobí, že certifikát klienta pro tyto certifikáty k selhání.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   Výstupy provádění skriptu jsou tyto certifikáty a klíče:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Vytváření řetězu certifikátů
Vytvořte řetěz certifikátů z certifikátu certifikační Autority vlastníka, zprostředkující certifikát a certifikát certifikační Autority hraniční zařízení s následující příkaz. Umístění v řetězu souboru umožňuje snadno ji nainstalovat v hraniční zařízení funguje jako brána transparentní.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Instalace na bráně
1.  Zkopírujte následující soubory z $WRKDIR kdekoli na hraniční zařízení, budeme označovat, jako $CERTDIR. Tento krok přeskočte, pokud jste vygenerovali certifikáty na hraniční zařízení.

   * Certifikát certifikační Autority zařízení-  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Privátního klíče certifikační Autority zařízení- `$WRKDIR/private/new-edge-device.key.pem`
   * Vlastník certifikační Autority – `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Nastavte `certificate` vlastnosti v zabezpečení démon konfiguračním yaml souboru do cesty kam umístit soubory certifikát a klíč.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení EdgeHub k bráně.
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Tato část obsahuje, můžete vytvořit zdánlivě prázdný nasazení; Hraniční centra je ale automatcially přidat pro všechna nasazení, i v případě, že neexistují žádné z ostatních modulů přítomen. Centrum okraj je jenom modul, který musíte na hraniční zařízení jej fungovat jako brána transparentní, vytváření prázdného nasazení je dost. 
1. Na webu Azure Portal přejděte do svého centra IoT.
2. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge, kterou chcete použít jako bránu.
3. Vyberte **Nastavit moduly**.
4. Vyberte **Next** (Další).
5. V **určit trasy** kroku byste měli mít výchozí trasu, která odešle všechny zprávy ze všech modulů do služby IoT Hub. Pokud ne, přidejte následující kód, pak vyberte **Další**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. V kroku zkontrolujte šablony, vyberte **odeslání**.

## <a name="installation-on-the-downstream-device"></a>Instalace na příjem dat zařízení
Podřízené zařízení může být z jakékoliv aplikace pomocí [zařízení Azure IoT SDK][lnk-devicesdk], tak jak je popsáno jednoduché jeden v [připojení zařízení do služby IoT hub pomocí rozhraní .NET] [ lnk-iothub-getstarted]. Aplikaci pro příjem dat zařízení musí důvěřovat **vlastníka certifikační Autority** certifikát k ověřování TLS připojení k zařízení brány. Tento krok můžete obvykle provést dvěma způsoby: na úrovni operačního systému, nebo (pro určité jazyky) na úrovni aplikace.

### <a name="os-level"></a>Úroveň operačního systému
Instalace tohoto certifikátu v úložišti certifikátů operačního systému povolí všechny aplikace na používání vlastník certifikát certifikační Autority jako důvěryhodný certifikát.

* Ubuntu – tady je příklad toho, jak nainstalovat certifikát certifikační Autority na hostiteli Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Zobrazí se zpráva, "aktualizace certifikáty v /etc/ssl/certs... 1 přidávat, odebírat 0; Hotovo."

* Windows – [to](https://msdn.microsoft.com/en-us/library/cc750534.aspx) článku podrobnosti o tom, jak to udělat na zařízení se systémem Windows pomocí pomocí Průvodce importem certifikátu. 

### <a name="application-level"></a>Úrovni aplikace
Pro aplikace .NET můžete přidat následující fragment kódu důvěřovat certifikátu ve formátu PEM. Inicializace proměnné `certPath` s `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Připojení k bráně podřízené zařízení
Je třeba inicializovat sady sdk zařízení IoT Hub s připojovacím řetězcem odkazující na název hostitele zařízení brány. To se provádí přidáním `GatewayHostName` vlastnost, která má připojovací řetězec zařízení. Pro instanci zde je ukázka zařízení připojovací řetězec pro zařízení, na kterou jsme připojí `GatewayHostName` vlastnost:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Toto je ukázkový příkaz, které testy, které všechno, co byl nastavit správně. Můžete sohuld zpráva "ověřit OK".
   >
   >OpenSSL s_client-connect - CAfile mygateway.contoso.com:8883 $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Směrování zprávy z podřízené zařízení
Modul runtime IoT Edge může směrovat zpráv odeslaných z podřízené zařízení stejně jako zprávy odeslané moduly. To umožňuje provádět analýzy v modulu spuštěné na bráně před odesláním libovolných dat do cloudu. Níže trasy se použije k odesílání zpráv z podřízené zařízení s názvem `sensor` název modulu `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Znalostní báze [modulu složení] [lnk--složení modulu] Další informace o směrování zpráv.

## <a name="next-steps"></a>Další postup
[Pochopení požadavků a nástrojů pro vývoj modulů IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
