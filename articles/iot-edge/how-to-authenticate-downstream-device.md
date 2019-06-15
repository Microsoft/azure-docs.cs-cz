---
title: Ověření zařízení příjem dat – Azure IoT Edge | Dokumentace Microsoftu
description: Postup ověření podřízené zařízení nebo zařízení typu list ke službě IoT Hub a směrovat připojení prostřednictvím zařízení brány Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082388"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Ověření podřízené zařízení do služby Azure IoT Hub

V případě transparentní brána v podřízené zařízení (říká se jim podřízené zařízení nebo zařízení typu list) musí identit ve službě IoT Hub jako jakékoli jiné zařízení. Tento článek vás provede možnosti pro ověřování zařízení příjem dat do služby IoT Hub a uvidíte, jak deklarovat připojení brány.

Existují tři hlavní kroky k nastavení připojení úspěšné transparentní brány. Tento článek popisuje druhý krok:

1. Zařízení brány musí mít k zabezpečenému připojení pro příjem dat zařízení, přijímat komunikační sdělení od podřízené zařízení a směrování zpráv do správné cíl. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md).
2. **Příjem dat zařízení musí mít identitu zařízení mohli ověřovat pomocí služby IoT Hub a vědět, ke komunikaci prostřednictvím zařízení brány.**
3. Příjem dat zařízení potřebuje k bezpečnému připojování k zařízení brány. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Příjem dat zařízení objekt moci ověřovat pomocí služby IoT Hub pomocí jedné ze tří metod: symetrické klíče (někdy označované jako klíče pro sdílený přístup), certifikátů X.509 podepsaných svým držitelem nebo certifikát X.509 certifikační autority podepsané certifikáty. Ověřování postup je podobný postup použít ke konfiguraci jakémkoliv zařízení IoT Edge službou IoT Hub s malé rozdíly pro deklaraci vztah brány.

Kroky v tomto článku ukazují zařízení ručního zřizování, není automatické zřizování s Azure IoT Hub Device Provisioning Service. 

## <a name="symmetric-key-authentication"></a>Ověřování pomocí symetrického klíče

Ověřování pomocí symetrického klíče nebo sdíleného přístupového klíče ověřování, je nejjednodušší způsob, jak ověřování pomocí služby IoT Hub. Pomocí ověřování symetrickým klíčem klíč ve formátu base64 souvisí se svým ID zařízení IoT ve službě IoT Hub. Tento klíč zahrnout do vaší aplikace IoT tak, aby vaše zařízení můžete prezentovat je při připojení ke službě IoT Hub. 

### <a name="create-the-device-identity"></a>Vytvoření identity zařízení 

Přidáte nové zařízení IoT ve službě IoT hub pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo rozšíření IoT pro Visual Studio Code. Mějte na paměti, že podřízené zařízení musí spadat do služby IoT Hub jako regulární zařízení IoT, nikoli zařízením IoT Edge. 

Když vytvoříte novou identitu zařízení, zadejte následující informace: 

* Vytvoření ID pro vaše zařízení.

* Vyberte **symetrický klíč** jako typ ověřování. 

* Volitelně můžete zvolit **nastavení nadřazené zařízení** a vyberte zařízení brány IoT Edge, který tento podřízený zařízení se budou připojovat prostřednictvím. Tento krok je volitelný pro ověřování symetrickým klíčem, ale doporučuje se, protože nastavení nadřazené zařízení umožňuje [offline možnosti](offline-capabilities.md) pro příjem dat zařízení. Můžete kdykoli aktualizovat podrobnosti zařízení, které chcete přidat nebo změnit nadřazenou později. 

   ![Vytvoření ID zařízení pomocí symetrického klíče ověřování na portálu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) dokončit stejnou operaci. Následující příklad vytvoří nové zařízení IoT pomocí ověřování symetrickým klíčem a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení a správy nadřazené a podřízené najdete v tématu referenční obsah pro [az iot hub-identity zařízení](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) příkazy.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Připojení ke službě IoT Hub přes bránu

Stejný postup se používá k ověření regulárního IoT zařízení do IoT Hubu pomocí symetrických klíčů platí také pro příjem dat zařízení. Jediným rozdílem je, že budete muset přidat ukazatel pro zařízení brány mohli směrovat připojení nebo v případě offline scénářů zpracovávat ověřování jménem služby IoT Hub. 

Pro ověřování symetrickým klíčem neexistuje žádné další kroky, které musíte provést na vašem zařízení, aby ověřování službou IoT Hub. Stále potřebujete certifikáty na místě tak, aby podřízený zařízení může připojit k zařízení brány, jak je popsáno v [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Po vytvoření identity zařízení IoT na portálu, můžete získat jeho primární nebo sekundární klíče. Jeden z těchto klíčů musí být součástí připojovacího řetězce, kterou v jakékoli aplikaci, která komunikuje se službou IoT Hub. Pro ověřování symetrickým klíčem IoT Hub poskytuje plně připojovací řetězec do podrobností o zařízení pro vaše pohodlí. Budete muset přidat další informace o zařízení brány na připojovací řetězec. 

Symetrické klíče připojovací řetězce pro příjem dat zařízení potřebovat následující komponenty: 

* IoT hub se zařízení připojuje: `Hostname={iothub name}.azure-devices.net`
* ID zařízení zaregistrovaná v centru: `DeviceID={device ID}`
* Primární nebo sekundární klíč: `SharedAccessKey={key}`
* Zařízení brány, které se zařízení připojuje prostřednictvím. Zadejte **hostname** hodnotu ze souboru config.yaml zařízení brány IoT Edge: `GatewayHostName={gateway hostname}`

Všechno dohromady úplný připojovací řetězec vypadá takto:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Pokud jste vytvořili nadřazené a podřízené relace pro toto zařízení příjem dat, můžete zjednodušit připojovací řetězec voláním brány přímo jako hostitel připojení. Příklad: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Ověřování X.509 

Existují dva způsoby, jak ověřovat zařízení IoT pomocí certifikátů X.509. Libovolným způsobem budete chtít ověřit, kroky pro připojení vašich zařízení ke službě IoT Hub jsou stejné. Vyberte certifikáty podepsané svým držitelem nebo podepsaný certifikační Autoritou pro ověřování a potom pokračujte se dozvíte, jak se připojit ke službě IoT Hub. 

Další informace o tom, jak služby IoT Hub používá ověřování X.509 najdete v následujících článcích: 
* [Zařízení ověřování pomocí certifikátů webu X.509](../iot-hub/iot-hub-x509ca-overview.md)
* [Koncepční přehled o certifikáty webu X.509 v odvětví IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Vytvoření identity zařízení pomocí certifikátů X.509 podepsaného svým držitelem

X.509 podepsaného svým držitelem ověřování, někdy označovány jako kryptografický otisk ověřování budete muset vytvořit nové certifikáty chcete umístit do zařízení IoT. Tyto certifikáty mít kryptografický otisk. v nich, které sdílíte pomocí služby IoT Hub pro ověřování. 

Nejjednodušší způsob, jak tento scénář otestovat je použít stejný počítač, který jste použili k vytvoření certifikátů v [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md). Tento počítač by měl již nastavit správný nástroj, certifikát kořenové certifikační Autority a certifikát zprostředkující certifikační Autority k vytvoření certifikátů zařízení IoT. Můžete zkopírovat konečný certifikáty a jejich privátní klíče přes zařízení příjem dat později. Postupujte podle kroků v článku o bráně nastavení openssl na váš počítač potom naklonované úložiště IoT Edge pro přístup k certifikátu vytváření skriptů. Pak jste provedli pracovní adresář, který nazýváme  **\<WRKDIR >** pro uložení certifikáty. Výchozí certifikáty jsou určené pro vývoj a testování, takže pouze posledních 30 dní. Měli jste vytvořili kořenový certifikát CA a zprostředkujících certifikátů. 

1. Přejděte do pracovního adresáře v prostředí bash nebo okno Powershellu. 

2. Vytvořte dva certifikáty (primární i sekundární) pro příjem dat zařízení. Zadejte název vašeho zařízení a pak na popisek primární nebo sekundární. Tyto informace slouží k pojmenování souborů tak, aby vám může udržovat přehled o certifikáty pro různá zařízení. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Načtěte otisk SHA1 (v rozhraní služby IoT Hub jako kryptografický otisk) z každého certifikátu, což je řetězec 40 znaků šestnáctkové. Použijte následující příkaz openssl certifikátu zobrazte a vyhledejte otisk prstu:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Přejděte do služby IoT hub na webu Azure Portal a vytvořte novou identitu zařízení IoT s použitím následujících hodnot: 

   * Vyberte **X.509 podepsaného svým držitelem** jako typ ověřování.
   * Vložte hexadecimálních řetězců, které jste zkopírovali z primárního a sekundárního certifikátu vašeho zařízení.
   * Vyberte **nastavení nadřazené zařízení** a vyberte zařízení brány, toto podřízené zařízení se budou připojovat prostřednictvím IoT Edge. Nadřazené zařízení je vyžadován pro ověření X.509 na příjem dat zařízení. 

   ![Vytvoření ID zařízení pomocí podepsaného svým držitelem ověření X.509 na portálu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Zkopírujte následující soubory do libovolného adresáře v podřízené zařízení:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Tyto soubory v aplikacích zařízení typu list, které se připojují ke službě IoT Hub budete odkazovat. Je možné použít službu jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je [zabezpečené kopírování Protocol](https://www.ssh.com/ssh/scp/) přesunout soubory certifikátu.

Můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) dokončit stejnou operaci vytvoření zařízení. Následující příklad vytvoří nové zařízení IoT pomocí ověřování X.509 podepsaného svým držitelem a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení, vytvoření certifikátu a nadřazené a podřízené správy, naleznete v tématu referenční obsah pro [az iot hub-identity zařízení](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) příkazy.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Vytvoření zařízení identity s podmíněným Přístupem X.509 podepsaný certifikátů

X.509 certifikátu certifikační autority podepsané ověřování budete potřebovat certifikát kořenové certifikační Autority zaregistrovaný ve službě IoT Hub, který se používá k podepisování certifikátů pro vaše zařízení IoT. Jakékoli zařízení, která používá certifikát, který byl problémy certifikát kořenové certifikační Autority nebo některý z jeho zprostředkující certifikáty budou moci ověřit. 

Tato část je založen na pokyny najdete v článku služby IoT Hub [nastavit X.509 zabezpečení ve službě Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md). Postupujte podle kroků v této části vědět, jaké hodnoty použít nastavení zařízení příjem dat, která se připojuje prostřednictvím brány. 

Nejjednodušší způsob otestování tohoto scénáře je použít stejný počítač, který jste použili k vytvoření certifikátů v [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md). Tento počítač by měl již nastavit správný nástroj, certifikát kořenové certifikační Autority a certifikát zprostředkující certifikační Autority k vytvoření certifikátů zařízení IoT. Můžete zkopírovat konečný certifikáty a jejich privátní klíče přes zařízení příjem dat později. Postupujte podle kroků v článku o bráně nastavení openssl na váš počítač potom naklonované úložiště IoT Edge pro přístup k certifikátu vytváření skriptů. Pak jste provedli pracovní adresář, který nazýváme  **\<WRKDIR >** pro uložení certifikáty. Výchozí certifikáty jsou určené pro vývoj a testování, takže pouze posledních 30 dní. Měli jste vytvořili kořenový certifikát CA a zprostředkujících certifikátů. 

1. Postupujte podle pokynů [certifikáty webu X.509 zaregistrovat do služby IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) část *nastavit X.509 zabezpečení ve službě Azure IoT hub*. V této části provedete následující kroky: 

   1. Nahrajte certifikát kořenové certifikační Autority. Pokud používáte certifikáty, které jste vytvořili v tomto článku transparentní brána, nahrajte  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** jako soubor kořenového certifikátu. 
   2. Ověřte, že jste vlastníkem tohoto certifikátu kořenové certifikační Autority. Můžete ověřit vlastnictví certifikátu nástroje \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Postupujte podle pokynů [vytvoření zařízení X.509 pro službu IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) část *nastavit X.509 zabezpečení ve službě Azure IoT hub*. V této části provedete následující kroky: 

   1. Přidání nového zařízení. Zadejte název malými písmeny **ID zařízení**a zvolte typ ověřování **X.509 podepsaný certifikační Autority**. 
   2. Nastavte nadřazený zařízení. Pro příjem dat zařízení, vyberte **nastavení nadřazené zařízení** a vyberte zařízení brány, který bude poskytovat připojení ke službě IoT Hub na hraničních zařízeních IoT. 

3. Vytvořte řetěz certifikátů pro příjem dat zařízení. Použijte stejné kořenový certifikát, který jste nahráli do služby IoT Hub vytváří tento řetězec. Použijte stejné ID malá zařízení, která jste udělili svoji identitu zařízení na portálu.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Zkopírujte následující soubory do libovolného adresáře v podřízené zařízení: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Tyto soubory v aplikacích zařízení typu list, které se připojují ke službě IoT Hub budete odkazovat. Je možné použít službu jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je [zabezpečené kopírování Protocol](https://www.ssh.com/ssh/scp/) přesunout soubory certifikátu.

Můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) dokončit stejnou operaci vytvoření zařízení. Následující příklad vytvoří nové zařízení IoT pomocí certifikační Autority X.509 podepsaný ověřování a přiřadí nadřazené zařízení: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení a správy nadřazené a podřízené najdete v tématu referenční obsah pro [az iot hub-identity zařízení](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) příkazy.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Připojení ke službě IoT Hub přes bránu

Každá sada SDK Azure IoT trochu jinak, zpracovává ověřování X.509. Ale stejný postup se používá k ověření regulárního IoT zařízení do IoT Hubu pomocí certifikátů X.509 platí také pro příjem dat zařízení. Jediným rozdílem je, že budete muset přidat ukazatel pro zařízení brány mohli směrovat připojení nebo v případě offline scénářů zpracovávat ověřování jménem služby IoT Hub. Obecně platí, můžete použijte stejný postup ověřování X.509 pro všechna zařízení služby IoT Hub a pak stačí nahradit hodnotu **Hostname** v připojovacím řetězci jako název hostitele vašeho zařízení brány. 

Následující části vysvětlují několik příkladů, pro různé jazyky sady SDK. 

>[!IMPORTANT]
>Následující ukázky ukazují, jak se sadami SDK služby IoT Hub používat certifikáty k ověřování zařízení. V produkčním nasazení měli byste uložit všechny tajné kódy jako privátní nebo klíče SAS v modulu hardwarového zabezpečení (HSM). 

#### <a name="net"></a>.NET

Příklad C# programu ověřování pomocí certifikátů X.509 pro službu IoT Hub, najdete v článku [nastavit X.509 zabezpečení ve službě Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Některé klíčové řádky v tomto příkladu jsou zahrnuty tady předvést procesu ověřování.

Při deklaraci názvu hostitele pro vaši instanci DeviceClient, použijte název hostitele zařízení brány IoT Edge. Název hostitele najdete v souboru config.yaml zařízení brány. 

Pokud používáte testovací certifikáty poskytuje úložiště git IoT Edge, je klíčem k certifikáty **1234**.

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

Příklad jazyka C použít ověřování pomocí certifikátů X.509 pro službu IoT Hub najdete v článku sadou SDK IoT C [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) vzorku. Některé klíčové řádky v tomto příkladu jsou zahrnuty tady předvést procesu ověřování.

Při definování připojovacího řetězce pro příjem dat zařízení, použijte název hostitele zařízení brány IoT Edge pro **HostName** parametru. Název hostitele najdete v souboru config.yaml zařízení brány. 

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

Příklad aplikace Node.js ověřování pomocí certifikátů X.509 pro službu IoT Hub najdete v článku sadou SDK IoT Node.js [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) vzorku. Některé klíčové řádky v tomto příkladu jsou zahrnuty tady předvést procesu ověřování.

Při definování připojovacího řetězce pro příjem dat zařízení, použijte název hostitele zařízení brány IoT Edge pro **HostName** parametru. Název hostitele najdete v souboru config.yaml zařízení brány. 

Pokud používáte testovací certifikáty poskytuje úložiště git IoT Edge, je klíčem k certifikáty **1234**.

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

Příklad program Pythonu ověřování pomocí certifikátů X.509 pro službu IoT Hub najdete v článku Java IoT SDK [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) vzorku. Některé klíčové řádky v tomto příkladu jsou zahrnuty tady předvést procesu ověřování.

Při definování připojovacího řetězce pro příjem dat zařízení, použijte název hostitele zařízení brány IoT Edge pro **HostName** parametru. Název hostitele najdete v souboru config.yaml zařízení brány. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Příklad program v jazyce Java ověřování pomocí certifikátů X.509 pro službu IoT Hub najdete v článku Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) vzorku. Některé klíčové řádky v tomto příkladu jsou zahrnuty tady předvést procesu ověřování.

Při definování připojovacího řetězce pro příjem dat zařízení, použijte název hostitele zařízení brány IoT Edge pro **HostName** parametru. Název hostitele najdete v souboru config.yaml zařízení brány. 

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

## <a name="next-steps"></a>Další postup

Po dokončení tohoto článku, měli byste mít zařízení IoT Edge funguje jako transparentní brána a podřízené zařízení zaregistrované ve službě IoT hub. Dále je třeba pro příjem dat zařízení důvěřují zařízení brány a odesílat zprávy do něj. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).
