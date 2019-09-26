---
title: Ověřování pro všechna zařízení – Azure IoT Edge | Microsoft Docs
description: Jak ověřit podřízená zařízení nebo listová zařízení pro IoT Hub a směrovat jejich připojení prostřednictvím zařízení Azure IoT Edge brány.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d73c0f4dbfcc2c67a222f91693ebe8ed9ea83d98
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266129"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Ověření podřízeného zařízení pro Azure IoT Hub

V případě transparentní brány mají podřízená zařízení (někdy označovaná jako listová zařízení nebo podřízená zařízení) identity v IoT Hub jako jakékoli jiné zařízení. Tento článek vás provede možnostmi ověřování zařízení pro příjem dat IoT Hub a pak ukazuje, jak deklarovat připojení brány.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje druhý krok:

1. Zařízení brány musí být schopné bezpečně připojit se k zařízením pro příjem dat, přijímat komunikaci ze zařízení pro příjem dat a směrovat zprávy do správného umístění. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro fungování jako transparentní brány](how-to-create-transparent-gateway.md).
2. **Aby se zařízení mohla ověřit pomocí IoT Hub, musí mít k dispozici identitu zařízení, která může komunikovat prostřednictvím zařízení brány.**
3. Zařízení pro příjem dat musí být schopné se bezpečně připojit k zařízení brány. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Zařízení pro příjem dat se můžou pomocí IoT Hub ověřit pomocí jedné ze tří metod: symetrických klíčů (někdy označovaných jako sdílené přístupové klíče), certifikátů podepsaných držitelem X. 509 nebo certifikátů podepsaných certifikační autoritou (CA) X. 509. Postup ověřování se podobá postupům, které se používají k nastavení zařízení bez IoT Edge s IoT Hub, s malým rozdílem na deklaraci vztahu brány.

Kroky v tomto článku ukazují ruční zřizování zařízení, ne Automatické zřizování pomocí Azure IoT Hub Device Provisioning Service. 

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části [konfigurace IoT Edge zařízení, které bude fungovat jako transparentní brána](how-to-create-transparent-gateway.md). Pokud pro vaše zařízení pro příjem dat používáte ověřování X. 509, musíte použít stejný skript pro generování certifikátů, který jste nastavili v článku transparentní brány. 

Tento článek odkazuje na *název hostitele brány* na několika místech. Název hostitele brány je deklarovaný v parametru **hostname** souboru config. yaml na zařízení IoT Edge brány. Používá se k vytvoření certifikátů v tomto článku a odkazuje se na připojovací řetězec zařízení pro příjem dat. Název hostitele brány musí být přeložitelný na IP adresu, a to buď pomocí DNS, nebo zadáním souboru hostitele.

## <a name="symmetric-key-authentication"></a>Ověřování symetrického klíče

Ověřování pomocí symetrického klíče nebo ověřování pomocí sdíleného přístupového klíče je nejjednodušší způsob, jak ověřit IoT Hub. Při ověřování pomocí symetrického klíče je klíč Base64 přidružený k vašemu ID zařízení IoT v IoT Hub. Tento klíč zahrnete do svých aplikací IoT, aby ho zařízení mohl prezentovat při připojení k IoT Hub. 

### <a name="create-the-device-identity"></a>Vytvoření identity zařízení 

Do služby IoT Hub přidejte nové zařízení IoT pomocí Azure Portal, rozhraní příkazového řádku Azure nebo rozšíření IoT pro Visual Studio Code. Mějte na paměti, že zařízení se musí identifikovat v IoT Hub jako běžné zařízení IoT, ne IoT Edge zařízení. 

Když vytváříte novou identitu zařízení, zadejte následující informace: 

* Vytvořte ID pro vaše zařízení.

* Jako typ ověřování vyberte **symetrický klíč** . 

* Volitelně můžete zvolit **Nastavení nadřazeného zařízení** a vybrat zařízení IoT Edge brány, ke kterému se toto zařízení pro příjem dat připojí. Tento krok je volitelný pro ověřování pomocí symetrického klíče, ale doporučuje se, protože nastavení nadřazeného zařízení umožňuje [offline funkce](offline-capabilities.md) pro vaše zařízení pro příjem dat. Můžete vždy aktualizovat podrobnosti o zařízení a přidat nebo změnit nadřazený objekt později. 

   ![Vytvoření ID zařízení s symetrickým ověřováním pomocí klíče na portálu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

K provedení stejné operace můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s ověřováním pomocí symetrického klíče a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení a správě nadřazených a podřízených objektů najdete v referenčním obsahu pro příkaz [AZ IoT Hub Device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="connect-to-iot-hub-through-a-gateway"></a>Připojení k IoT Hub přes bránu

Stejný postup se používá k ověřování běžných zařízení IoT pro IoT Hub se symetrickými klíči platí i pro zařízení s podřízenou platností. Jediným rozdílem je, že je potřeba přidat ukazatel na zařízení brány, abyste mohli směrovat připojení nebo v offline scénářích zpracovávat ověřování jménem IoT Hub. 

Pro ověřování pomocí symetrického klíče se v zařízení nemusíte provádět žádné další kroky, které by bylo možné v IoT Hub ověřit. Pořád budete potřebovat certifikáty, aby se vaše zařízení pro příjem dat mohl připojit k zařízení brány, jak je popsáno v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Po vytvoření identity zařízení IoT na portálu můžete načíst primární nebo sekundární klíč. Jeden z těchto klíčů je nutné zahrnout do připojovacího řetězce, který zahrnete do jakékoli aplikace, která komunikuje s IoT Hub. Pro ověřování symetrických klíčů IoT Hub poskytuje plně vytvořený připojovací řetězec v podrobnostech o zařízení pro vaše pohodlí. Do připojovacího řetězce je třeba přidat další informace o zařízení brány. 

Připojovací řetězce symetrického klíče pro podřízená zařízení vyžadují následující komponenty: 

* Centrum IoT, ke kterému se zařízení připojuje:`Hostname={iothub name}.azure-devices.net`
* ID zařízení zaregistrované u centra:`DeviceID={device ID}`
* Buď primární, nebo sekundární klíč:`SharedAccessKey={key}`
* Zařízení brány, ke kterému se zařízení připojuje Zadejte hodnotu **názvu hostitele** ze souboru config. yaml zařízení IoT Edge brány:`GatewayHostName={gateway hostname}`

Celý připojovací řetězec se všemi dohromady vypadá takto:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Pokud jste pro toto zařízení pro příjem dat vytvořili vztah nadřazenosti/podřízenosti, můžete připojovací řetězec zjednodušit voláním brány přímo jako hostitele připojení. Příklad: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Ověřování X. 509 

Existují dva způsoby, jak ověřit zařízení IoT pomocí certifikátů X. 509. Podle toho, jak se rozhodnete k ověřování, jsou kroky pro připojení zařízení k IoT Hub stejné. Zvolte pro ověřování certifikáty podepsané svým držitelem nebo CA podepsané certifikační autoritou a potom se Naučte, jak se připojit k IoT Hub. 

Další informace o tom, jak IoT Hub používá ověřování X. 509, najdete v následujících článcích: 
* [Ověřování zařízení pomocí certifikátů certifikační autority X. 509](../iot-hub/iot-hub-x509ca-overview.md)
* [Konceptuální porozumění certifikátům CA X. 509 v oboru IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Vytvoření identity zařízení s certifikáty podepsanými svými držiteli X. 509

Pro ověřování X. 509 podepsané svým držitelem, které se někdy označuje jako ověřování kryptografickým otiskem, je potřeba vytvořit nové certifikáty, které se mají na zařízení IoT umístit. Tyto certifikáty obsahují kryptografický otisk, který sdílíte s IoT Hub pro ověřování. 

Nejjednodušší způsob, jak to provést, je použít stejný počítač, který jste použili k vytvoření certifikátů v části [konfigurace IoT Edge zařízení pro fungování jako transparentní brány](how-to-create-transparent-gateway.md). Tento počítač by už měl být nastavený pomocí správného nástroje, certifikátu kořenové certifikační autority a zprostředkující certifikační autority, aby se vytvořily certifikáty zařízení IoT. Konečné certifikáty a jejich privátní klíče můžete později zkopírovat do svého zařízení po telefonu. Podle postupu v článku o bráně nastavíte OpenSSL na svém počítači a pak jste naklonoval úložiště IoT Edge pro přístup ke skriptům pro vytváření certifikátů. Pak jste vytvořili pracovní adresář, který zavolá  **\<WRKDIR >** pro uložení certifikátů. Výchozí certifikáty jsou určeny pro vývoj a testování, takže pouze v posledních 30 dnech. Měli byste vytvořit certifikát kořenové certifikační autority a zprostředkující certifikát. 

1. Přejděte do pracovního adresáře v okně bash nebo PowerShellu. 

2. Pro zařízení pro příjem dat vytvořte dva certifikáty (primární a sekundární). Zadejte název zařízení a pak primární nebo sekundární popisek. Tyto informace slouží k pojmenování souborů, abyste mohli sledovat certifikáty pro více zařízení. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Načte otisk prstu SHA1 (nazývaný kryptografický otisk v rozhraní IoT Hub) z každého certifikátu, což je 40 šestnáctkový řetězec znaků. K zobrazení certifikátu a nalezení otisku prstu použijte následující příkaz OpenSSL:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. V Azure Portal přejděte do služby IoT Hub a vytvořte novou identitu zařízení IoT s následujícími hodnotami: 

   * Jako typ ověřování vyberte **X. 509 podepsaný svým držitelem** .
   * Vložte šestnáctkové řetězce, které jste zkopírovali z primárního a sekundárního certifikátu vašeho zařízení.
   * Vyberte možnost **nastavit nadřazené zařízení** a zvolte zařízení IoT Edge brány, ke kterému se bude toto zařízení s podřízenou položkou připojovat. Pro ověřování X. 509 zařízení pro příjem dat se vyžaduje nadřazené zařízení. 

   ![Vytvoření ID zařízení pomocí ověřování X. 509 podepsaného svým držitelem na portálu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Zkopírujte následující soubory do libovolného adresáře v zařízení pro příjem dat:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Na tyto soubory se budete odkazovat v aplikacích na listovém zařízení, které se připojují k IoT Hub. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

K dokončení stejné operace vytváření zařízení můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s ověřováním X. 509 podepsaným svým držitelem a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení, generování certifikátů a nadřazené a podřízené správě najdete v referenčním obsahu pro příkaz [AZ IoT Hub Device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Vytvoření identity zařízení s certifikátem podepsaným certifikační autoritou X. 509

Pro ověřování podepsané certifikační autoritou (CA) X. 509 potřebujete certifikát od kořenové certifikační autority zaregistrovaný v IoT Hub, který používáte k podepisování certifikátů pro vaše zařízení IoT. Jakékoli zařízení, které vyvolalo certifikát od kořenové certifikační autority nebo jakýkoli z jeho zprostředkujících certifikátů, bude povoleno ověřování. 

Tato část je založená na pokynech, které jsou popsané v IoT Hub článku [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Podle kroků v této části Zjistěte, které hodnoty se mají použít k nastavení zařízení pro příjem dat, která se připojují přes bránu. 

Nejjednodušší způsob, jak tento scénář otestovat, je použít stejný počítač, který jste použili k vytvoření certifikátů v části [konfigurace IoT Edge zařízení pro fungování jako transparentní brány](how-to-create-transparent-gateway.md). Tento počítač by už měl být nastavený pomocí správného nástroje, certifikátu kořenové certifikační autority a zprostředkující certifikační autority, aby se vytvořily certifikáty zařízení IoT. Konečné certifikáty a jejich privátní klíče můžete později zkopírovat do svého zařízení po telefonu. Podle postupu v článku o bráně nastavíte OpenSSL na svém počítači a pak jste naklonoval úložiště IoT Edge pro přístup ke skriptům pro vytváření certifikátů. Pak jste vytvořili pracovní adresář, který zavolá  **\<WRKDIR >** pro uložení certifikátů. Výchozí certifikáty jsou určeny pro vývoj a testování, takže pouze v posledních 30 dnech. Měli byste vytvořit certifikát kořenové certifikační autority a zprostředkující certifikát. 

1. Postupujte podle pokynů v části [Registrace certifikátů certifikační autority x. 509 do centra IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) *v tématu Nastavení zabezpečení X. 509 ve službě Azure IoT Hub*. V této části provedete následující kroky: 

   1. Nahrajte certifikát kořenové certifikační autority. Pokud používáte certifikáty, které jste vytvořili v článku transparentní brány, nahrajte  **\<** jako soubor kořenového certifikátu WRKDIR >/certs/Azure-IoT-test-Only.root.ca.CERT.pem. 
   2. Ověřte, že vlastníte certifikát kořenové certifikační autority. Můžete si ověřit, jestli máte k dispozici \<nástroje pro certifikace v WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Postupujte podle pokynů v části [Vytvoření zařízení x. 509 pro službu IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *v tématu Nastavení zabezpečení x. 509 ve službě Azure IoT Hub*. V této části provedete následující kroky: 

   1. Přidejte nové zařízení. Zadejte malý název **ID zařízení**a vyberte typ ověřování, který je **podepsaný certifikační autoritou X. 509**. 
   2. Nastavte nadřazené zařízení. V části zařízení pro příjem dat vyberte možnost **nastavit nadřazené zařízení** a zvolte zařízení IoT Edge brány, které bude poskytovat připojení k IoT Hub. 

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. K provedení tohoto řetězce použijte stejný certifikát od kořenové certifikační autority, který jste nahráli do IoT Hub. Použijte stejné ID zařízení s malým písmenem, které jste přiřadili identitě zařízení na portálu.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Zkopírujte následující soubory do libovolného adresáře v zařízení pro příjem dat: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Na tyto soubory se budete odkazovat v aplikacích na listovém zařízení, které se připojují k IoT Hub. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

K dokončení stejné operace vytváření zařízení můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s podpisem certifikační autority X. 509 podepsané ověřováním a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení a správě nadřazených a podřízených objektů najdete v referenčním obsahu pro příkaz [AZ IoT Hub Device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .


### <a name="connect-to-iot-hub-through-a-gateway"></a>Připojení k IoT Hub přes bránu

Každá sada Azure IoT SDK zpracovává ověřování X. 509 trochu jinak. Stejný postup se ale používá k ověřování běžných zařízení IoT, aby se IoT Hub s certifikáty X. 509, které platí i pro podřízená zařízení. Jediným rozdílem je, že je potřeba přidat ukazatel na zařízení brány, abyste mohli směrovat připojení nebo v offline scénářích zpracovávat ověřování jménem IoT Hub. Obecně platí, že můžete použít stejné kroky ověřování X. 509 pro všechna IoT Hub zařízení a pak jednoduše nahradit hodnotu **názvu hostitele** v připojovacím řetězci, aby to byl název hostitele vašeho zařízení brány. 

V následujících částech jsou uvedeny některé příklady pro různé jazyky sady SDK. 

>[!IMPORTANT]
>Následující ukázky ukazují, jak IoT Hub sady SDK používají certifikáty k ověřování zařízení. V produkčním nasazení byste měli v modulu hardwarového zabezpečení (HSM) ukládat všechny tajné kódy, jako jsou privátní nebo SAS klíče. 

#### <a name="net"></a>.NET

Příklad C# programu ověřujícího IoT Hub s certifikáty X. 509 najdete v tématu [nastavení zabezpečení x. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Tady jsou uvedené některé klíčové řádky této ukázky, které demonstrují proces ověřování.

Při deklaraci názvu hostitele pro instanci DeviceClient použijte název hostitele zařízení IoT Edge brány. Název hostitele najdete v souboru config. yaml zařízení brány. 

Pokud používáte testovací certifikáty, které poskytuje IoT Edge úložiště Git, klíč k certifikátům je **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Příklad programu v jazyce C ověřování pro IoT Hub s certifikáty X. 509 najdete v tématu Ukázka [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) sady SDK pro sadu c IoT. Tady jsou uvedené některé klíčové řádky této ukázky, které demonstrují proces ověřování.

Při definování připojovacího řetězce pro vaše zařízení pro příjem dat použijte název hostitele zařízení IoT Edge brány pro parametr **názvu hostitele** . Název hostitele najdete v souboru config. yaml zařízení brány. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Příklad programu Node. js ověřování, aby IoT Hub pomocí certifikátů X. 509, naleznete v ukázce [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) sady Node. js IoT SDK. Tady jsou uvedené některé klíčové řádky této ukázky, které demonstrují proces ověřování.

Při definování připojovacího řetězce pro vaše zařízení pro příjem dat použijte název hostitele zařízení IoT Edge brány pro parametr **názvu hostitele** . Název hostitele najdete v souboru config. yaml zařízení brány. 

Pokud používáte testovací certifikáty, které poskytuje IoT Edge úložiště Git, klíč k certifikátům je **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Sada Python SDK aktuálně podporuje jenom certifikáty x509 a klíče ze souborů, ne ty, které jsou definované jako vložené. V následujícím příkladu jsou relevantní cesty k příponám uloženy v proměnných prostředí.

Při definování názvu hostitele pro vaše zařízení pro příjem dat použijte název hostitele zařízení IoT Edge brány pro parametr **názvu hostitele** . Název hostitele najdete v souboru config. yaml zařízení brány. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

Příklad programu Java ověřujícího IoT Hub s certifikáty X. 509 naleznete v ukázce Java IoT SDK [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) . Tady jsou uvedené některé klíčové řádky této ukázky, které demonstrují proces ověřování.

Při definování připojovacího řetězce pro vaše zařízení pro příjem dat použijte název hostitele zařízení IoT Edge brány pro parametr **názvu hostitele** . Název hostitele najdete v souboru config. yaml zařízení brány. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Další kroky

Po dokončení tohoto článku byste měli mít zařízení IoT Edge fungující jako transparentní bránu a pro zařízení zaregistrovaná ve službě IoT Hub. Dále je potřeba nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala zařízení brány a odesílala do ní zprávy. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).
