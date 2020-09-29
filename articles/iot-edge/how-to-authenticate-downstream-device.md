---
title: Ověřování pro všechna zařízení – Azure IoT Edge | Microsoft Docs
description: Jak ověřit podřízená zařízení nebo listová zařízení pro IoT Hub a směrovat jejich připojení prostřednictvím zařízení Azure IoT Edge brány.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a9d2116062dc45f3602bf5ee0efba31ad815c0c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447845"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Ověření podřízeného zařízení pro Azure IoT Hub

V případě transparentní brány mají podřízená zařízení (někdy označovaná jako listová zařízení nebo podřízená zařízení) identity v IoT Hub jako jakékoli jiné zařízení. Tento článek vás provede možnostmi ověřování zařízení pro příjem dat IoT Hub a pak ukazuje, jak deklarovat připojení brány.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje druhý krok:

1. Nakonfigurujte zařízení brány jako server tak, aby se k nim mohli připojit podřízená zařízení bezpečně. Nastavte bránu tak, aby přijímala zprávy ze zařízení pro příjem dat, a směrovat je do správného umístění. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro fungování jako transparentní brány](how-to-create-transparent-gateway.md).
2. **Vytvořte identitu zařízení pro zařízení pro příjem dat, aby se mohla ověřit pomocí IoT Hub. Nakonfigurujte zařízení pro příjem zpráv, aby odesílala zprávy přes zařízení brány.**
3. Připojte zařízení pro příjem dat k zařízení brány a začněte odesílat zprávy. Další informace najdete v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Zařízení pro příjem dat se můžou pomocí IoT Hub ověřit pomocí jedné ze tří metod: symetrických klíčů (někdy označovaných jako sdílené přístupové klíče), certifikátů podepsaných držitelem X. 509 nebo certifikátů podepsaných certifikační autoritou (CA) X. 509. Postup ověřování se podobá postupům, které se používají k nastavení zařízení bez IoT Edge s IoT Hub, s malým rozdílem na deklaraci vztahu brány.

Kroky v tomto článku ukazují ruční zřizování zařízení. Automatické zřizování podřízených zařízení s Azure IoT Hub Device Provisioning Service (DPS) se nepodporuje.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části [konfigurace IoT Edge zařízení, které bude fungovat jako transparentní brána](how-to-create-transparent-gateway.md).

Pokud používáte ověřování X. 509, vygenerujete certifikáty pro vaše zařízení pro příjem dat. Používejte stejný certifikát kořenové certifikační autority a skript pro generování certifikátů, který jste použili pro článek transparentní brány, který je k dispozici k opětovnému použití.

Tento článek odkazuje na *název hostitele brány* na několika místech. Název hostitele brány je deklarovaný v parametru **hostname** souboru config. yaml na zařízení IoT Edge brány. Je odkazováno v připojovacím řetězci zařízení pro příjem dat. Název hostitele brány musí být přeložitelný na IP adresu, a to buď pomocí DNS, nebo zadáním souboru hostitele v zařízení pro příjem dat.

## <a name="register-device-with-iot-hub"></a>Registrace zařízení pomocí IoT Hub

Určete, jak chcete, aby se pro vaše zařízení pro příjem dat ověřilo IoT Hub:

* [Ověřování pomocí symetrického klíče](#symmetric-key-authentication): IoT Hub vytvoří klíč, který vložíte do zařízení pro příjem dat. Když se zařízení ověří, IoT Hub zkontroluje, jestli se tyto dva klíče shodují. Nemusíte vytvářet další certifikáty pro použití ověřování pomocí symetrického klíče.
* [Ověřování pomocí certifikátu x. 509](#x509-self-signed-authentication): někdy se nazývá ověřování kryptografických otisků, protože tento kryptografický otisk sdílíte z certifikátu X. 509 zařízení pomocí IoT Hub.
* [Ověřování podepsaného certifikační autoritou X. 509](#x509-ca-signed-authentication): Nahrajte certifikát kořenové certifikační autority do IoT Hub. Když zařízení prezentují svůj certifikát X. 509 pro ověřování, IoT Hub zkontroluje, jestli patří do řetězce důvěry podepsaného stejným kořenovým certifikátem certifikační autority.

Až zaregistrujete zařízení pomocí jedné z těchto tří metod, přejděte k další části, kde můžete [načíst a upravit připojovací řetězec](#retrieve-and-modify-connection-string) pro vaše zařízení pro příjem dat.

### <a name="symmetric-key-authentication"></a>Ověřování symetrického klíče

Ověřování pomocí symetrického klíče nebo ověřování pomocí sdíleného přístupového klíče je nejjednodušší způsob, jak ověřit IoT Hub. Při ověřování pomocí symetrického klíče je klíč Base64 přidružený k vašemu ID zařízení IoT v IoT Hub. Tento klíč zahrnete do svých aplikací IoT, aby ho zařízení mohl prezentovat při připojení k IoT Hub.

Do služby IoT Hub přidejte nové zařízení IoT pomocí Azure Portal, rozhraní příkazového řádku Azure nebo rozšíření IoT pro Visual Studio Code. Mějte na paměti, že zařízení se musí identifikovat v IoT Hub jako běžná zařízení IoT, ne IoT Edge zařízení.

Když vytváříte novou identitu zařízení, zadejte následující informace:

* Vytvořte ID pro vaše zařízení.

* Jako typ ověřování vyberte **symetrický klíč** .

* Volitelně můžete zvolit **Nastavení nadřazeného zařízení** a vybrat zařízení IoT Edge brány, ke kterému se toto zařízení pro příjem dat připojí. Tento krok je volitelný pro ověřování pomocí symetrického klíče, ale doporučuje se, protože nastavení nadřazeného zařízení umožňuje [offline funkce](offline-capabilities.md) pro vaše zařízení pro příjem dat. Můžete vždy aktualizovat podrobnosti o zařízení a přidat nebo změnit nadřazený objekt později.

   ![Vytvoření ID zařízení s symetrickým ověřováním pomocí klíče na portálu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

K provedení stejné operace taky můžete použít [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s ověřováním pomocí symetrického klíče a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení a správě nadřazených a podřízených objektů najdete v referenčním obsahu pro příkaz [AZ IoT Hub Device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) .

Pak [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby se vaše zařízení vědělo o připojení přes bránu.

### <a name="x509-self-signed-authentication"></a>Ověřování s přihlášeným držitelem X. 509

Pro ověřování X. 509 podepsané svým držitelem, které se někdy označuje jako ověřování kryptografickým otiskem, je potřeba vytvořit certifikáty, které se mají umístit na zařízení pro příjem dat. Tyto certifikáty obsahují kryptografický otisk, který sdílíte s IoT Hub pro ověřování.

1. Pomocí certifikátu certifikační autority vytvořte pro podřízené zařízení dva certifikáty zařízení (primární a sekundární).

   Pokud nemáte certifikační autoritu k vytváření certifikátů X. 509, můžete k [Vytvoření certifikátů pro příjem zařízení](how-to-create-test-certificates.md#create-downstream-device-certificates)použít IoT Edge ukázkových skriptů certifikátu. Postupujte podle kroků pro vytvoření certifikátů podepsaných svým držitelem. Použijte stejný certifikát kořenové certifikační autority, který vygeneroval certifikáty pro vaše zařízení brány.

   Pokud vytváříte vlastní certifikáty, ujistěte se, že je název subjektu certifikátu zařízení nastavený na ID zařízení, které používáte při registraci zařízení IoT v IoT Hub Azure. Toto nastavení se vyžaduje pro ověřování.

2. Načte otisk prstu SHA1 (nazývaný kryptografický otisk v rozhraní IoT Hub) z každého certifikátu, což je 40 šestnáctkový řetězec znaků. K zobrazení certifikátu a nalezení otisku prstu použijte následující příkaz OpenSSL:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Spusťte tento příkaz dvakrát, jednou pro primární certifikát a jednou pro sekundární certifikát. Při registraci nového zařízení IoT pomocí certifikátů X. 509 podepsaných svým držitelem zadáte otisky prstů pro oba certifikáty.

3. V Azure Portal přejděte do služby IoT Hub a vytvořte novou identitu zařízení IoT s následujícími hodnotami:

   * Zadejte **ID zařízení** , které se shoduje s názvem subjektu vašich certifikátů zařízení.
   * Jako typ ověřování vyberte **X. 509 podepsaný svým držitelem** .
   * Vložte šestnáctkové řetězce, které jste zkopírovali z primárního a sekundárního certifikátu vašeho zařízení.
   * Vyberte možnost **nastavit nadřazené zařízení** a zvolte zařízení IoT Edge brány, ke kterému se bude toto zařízení s podřízenou položkou připojovat. Pro ověřování X. 509 zařízení pro příjem dat se vyžaduje nadřazené zařízení.

   ![Vytvoření ID zařízení pomocí ověřování X. 509 podepsaného svým držitelem na portálu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Zkopírujte certifikáty primárního i sekundárního zařízení a jejich klíče do libovolného umístění v zařízení pro příjem dat. Přesuňte také kopii certifikátu sdílené kořenové certifikační autority, který vygeneroval certifikát zařízení brány i certifikáty pro příjem dat.

   Na tyto soubory certifikátů odkazujete ve všech aplikacích na zařízeních, která se připojují k IoT Hub. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

5. V závislosti na preferovaném jazyce si přečtěte ukázky, jak se můžou v aplikacích IoT odkazovat na certifikáty X. 509:

   * C#: [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

K dokončení stejné operace vytváření zařízení můžete použít také [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s ověřováním X. 509 podepsaným svým držitelem a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Další informace o příkazech rozhraní příkazového řádku Azure pro vytváření zařízení, generování certifikátů a nadřazené a podřízené správě najdete v referenčním obsahu pro příkaz [AZ IoT Hub Device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) .

Pak [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby se vaše zařízení vědělo o připojení přes bránu.

### <a name="x509-ca-signed-authentication"></a>Ověřování podepsané certifikační autoritou X. 509

Pro ověřování podepsané certifikační autoritou (CA) X. 509 potřebujete certifikát od kořenové certifikační autority zaregistrovaný v IoT Hub, který používáte k podepisování certifikátů pro vaše zařízení pro příjem dat. Jakékoli zařízení, které vyvolalo certifikát od kořenové certifikační autority nebo jakýkoli z jeho zprostředkujících certifikátů, bude povoleno ověřování.

Tato část je založená na pokynech, které jsou popsané v IoT Hub článku [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

1. Pomocí certifikátu certifikační autority vytvořte pro podřízené zařízení dva certifikáty zařízení (primární a sekundární).

   Pokud nemáte certifikační autoritu k vytváření certifikátů X. 509, můžete k [Vytvoření certifikátů pro příjem zařízení](how-to-create-test-certificates.md#create-downstream-device-certificates)použít IoT Edge ukázkových skriptů certifikátu. Postupujte podle kroků pro vytváření certifikátů podepsaných certifikační autoritou. Použijte stejný certifikát kořenové certifikační autority, který vygeneroval certifikáty pro vaše zařízení brány.

2. Postupujte podle pokynů v části [Registrace certifikátů certifikační autority x. 509 do centra IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) *v tématu Nastavení zabezpečení X. 509 ve službě Azure IoT Hub*. V této části provedete následující kroky:

   1. Nahrajte certifikát kořenové certifikační autority. Pokud používáte ukázkové certifikáty, kořenová certifikační autorita se ** \<path> /certs/Azure-IoT-test-Only.root.ca.CERT.pem**.

   2. Ověřte, že vlastníte certifikát kořenové certifikační autority.

3. Postupujte podle pokynů v části [Vytvoření zařízení x. 509 pro službu IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *v tématu Nastavení zabezpečení x. 509 ve službě Azure IoT Hub*. V této části provedete následující kroky:

   1. Přidejte nové zařízení. Zadejte malý název **ID zařízení**a vyberte typ ověřování, který je **podepsaný certifikační autoritou X. 509**.

   2. Nastavte nadřazené zařízení. V části zařízení pro příjem dat vyberte možnost **nastavit nadřazené zařízení** a zvolte zařízení IoT Edge brány, které bude poskytovat připojení k IoT Hub.

4. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. K provedení tohoto řetězce použijte stejný certifikát od kořenové certifikační autority, který jste nahráli do IoT Hub. Použijte stejné ID zařízení s malým písmenem, které jste přiřadili identitě zařízení na portálu.

5. Zkopírujte certifikát a klíče zařízení do libovolného umístění na zařízení pro příjem dat. Přesuňte také kopii certifikátu sdílené kořenové certifikační autority, který vygeneroval certifikát zařízení brány i certifikáty pro příjem dat.

   Na tyto soubory se odkazujete ve všech aplikacích v zařízení, které se připojují k IoT Hub. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

6. V závislosti na preferovaném jazyce si přečtěte ukázky, jak se můžou v aplikacích IoT odkazovat na certifikáty X. 509:

   * C#: [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

K dokončení stejné operace vytváření zařízení můžete použít také [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) . Následující příklad vytvoří nové zařízení IoT s podpisem certifikační autority X. 509 podepsané ověřováním a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Další informace najdete v referenčním obsahu Azure CLI pro příkazy [AZ IoT Hub Device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) .

Pak [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby se vaše zařízení vědělo o připojení přes bránu.

## <a name="retrieve-and-modify-connection-string"></a>Načtení a úprava připojovacího řetězce

Po vytvoření identity zařízení IoT na portálu můžete načíst primární nebo sekundární klíč. Jeden z těchto klíčů je nutné zahrnout do připojovacího řetězce, který aplikace používá ke komunikaci s IoT Hub. Pro ověřování symetrických klíčů IoT Hub poskytuje plně vytvořený připojovací řetězec v podrobnostech o zařízení pro vaše pohodlí. Do připojovacího řetězce je třeba přidat další informace o zařízení brány.

Připojovací řetězce pro zařízení pro příjem dat potřebují následující komponenty:

* Centrum IoT, ke kterému se zařízení připojuje: `Hostname={iothub name}.azure-devices.net`
* ID zařízení zaregistrované u centra: `DeviceID={device ID}`
* Metoda ověřování, bez ohledu na certifikáty symetrického klíče nebo X. 509
  * Pokud používáte ověřování symetrického klíče, zadejte buď primární, nebo sekundární klíč: `SharedAccessKey={key}`
  * Pokud používáte ověřování pomocí certifikátu X. 509, zadejte příznak: `x509=true`
* Zařízení brány, ke kterému se zařízení připojuje Zadejte hodnotu **názvu hostitele** ze souboru config. yaml zařízení IoT Edge brány: `GatewayHostName={gateway hostname}`

Celý připojovací řetězec se všemi dohromady vypadá takto:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ani

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Pokud jste pro toto zařízení pro příjem dat vytvořili vztah nadřazenosti/podřízenosti, můžete připojovací řetězec zjednodušit voláním brány přímo jako hostitele připojení. Pro ověřování X. 509 se vyžadují relace nadřazený-podřízený, ale volitelné pro ověřování symetrického klíče. Příklad:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Tento upravený připojovací řetězec použijete v dalším článku průhledné řady bran.

## <a name="next-steps"></a>Další kroky

V tomto okamžiku máte IoT Edge zařízení zaregistrované ve službě IoT Hub a nakonfigurované jako transparentní bránu. Máte i zařízení zaregistrované ve službě IoT Hub a odkazujete na jeho zařízení brány.

Kroky v tomto článku nastavily vaše zařízení pro příjem dat, aby se ověřilo IoT Hub. Dále musíte nakonfigurovat zařízení pro příjem dat, aby důvěřovalo zařízení brány a bezpečně se k němu připojilo. Pokračujte na další článek v transparentní sérii bran, [Připojte zařízení pro Azure IoT Edge k bráně](how-to-connect-downstream-device.md).
