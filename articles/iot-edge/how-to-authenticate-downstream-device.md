---
title: Ověření podřízené zařízení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Jak ověřit zařízení pro příjem dat nebo listová zařízení do služby IoT Hub a směrovat jejich připojení prostřednictvím zařízení brány Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8db3fedc5886e86d5f49739b87b26535665bdbc
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389320"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Ověření podřízeného zařízení pro Azure IoT Hub

Ve scénáři transparentní brány, zařízení pro příjem dat (někdy nazývané listové nebo podřízené zařízení) potřebují identity v centru IoT Hub jako každé jiné zařízení. Tento článek vás provede možnostmi ověřování zařízení pro příjem dat do služby IoT Hub a pak ukazuje, jak deklarovat připojení brány.

Existují tři obecné kroky k nastavení úspěšného připojení transparentní brány. Tento článek se zabývá druhým krokem:

1. Zařízení brány musí být schopno bezpečně se připojit k zařízením pro příjem dat, přijímat komunikaci z podřazených zařízení a směrovat zprávy na správné místo určení. Další informace najdete [v tématu Konfigurace zařízení IoT Edge tak, aby působilo jako transparentní brána](how-to-create-transparent-gateway.md).
2. **Zařízení pro příjem dat musí mít identitu zařízení, aby bylo možné ověřit pomocí služby IoT Hub a vědět, jak komunikovat prostřednictvím svého zařízení brány.**
3. Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány. Další informace najdete [v tématu Připojení podřízeného zařízení k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Zařízení pro příjem dat se mohou ověřovat pomocí služby IoT Hub pomocí jedné ze tří metod: symetrické klíče (někdy označované jako sdílené přístupové klíče), certifikáty podepsané samořízenými certifikáty X.509 nebo certifikáty podepsané certifikační autoritou X.509 (CA). Kroky ověřování jsou podobné krokům používaným k nastavení libovolného zařízení bez ioT-edge pomocí služby IoT Hub s malými rozdíly pro deklarování vztahu brány.

Kroky v tomto článku ukazují ruční zřizování zařízení, nikoli automatické zřizování se službou Azure IoT Hub Device Provisioning Service (DPS). Zřizování navazujících zařízení pomocí DPS není podporováno.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Konfigurace zařízení IoT Edge tak, aby působilo jako transparentní brána](how-to-create-transparent-gateway.md). Pokud používáte ověřování X.509 pro zařízení pro příjem dat, musíte použít stejný skript generující certifikát, který jste nastavili v článku transparentní brány.

Tento článek odkazuje na *název hostitele brány* na několika místech. Název hostitele brány je deklarován v parametru **hostname** souboru config.yaml na zařízení brány IoT Edge. Je to uvedeno v připojovacím řetězci navazujícího zařízení. Název hostitele brány musí být řešitelný na adresu IP, a to buď pomocí dns nebo položky hostitelského souboru.

## <a name="register-device-symmetric-key"></a>Zaregistrovat zařízení (symetrický klíč)

Symetrické ověřování pomocí klíče nebo ověřování pomocí sdíleného přístupového klíče je nejjednodušší způsob ověření pomocí služby IoT Hub. Díky symetrickému ověřování pomocí klíče je klíč base64 přidružený k ID vašeho ioT zařízení v iot hubu. Tento klíč zahrnete do aplikací IoT, aby ho vaše zařízení mohlo prezentovat, když se připojí k IoT Hubu.

### <a name="create-the-device-identity"></a>Vytvoření identity zařízení

Přidejte nové zařízení IoT do služby IoT hub pomocí portálu Azure, rozhraní PŘÍKAZOVÉHO PŘÍKAZU Azure nebo rozšíření IoT pro kód Visual Studia. Nezapomeňte, že zařízení pro příjem dat musí být v centru IoT Hub identifikována jako běžná zařízení IoT, ne jako zařízení IoT Edge.

Při vytváření nové identity zařízení zadejte následující informace:

* Vytvořte id zařízení.

* Jako typ ověřování vyberte **Symetrický klíč.**

* Volitelně můžete **zvolit nastavení nadřazeného zařízení** a vyberte zařízení brány IoT Edge, přes které se toto zařízení pro příjem dat připojí. Tento krok je volitelný pro symetrické ověřování pomocí klíče, ale doporučuje se, protože nastavení nadřazeného zařízení umožňuje [offline funkce](offline-capabilities.md) pro vaše zařízení pro příjem dat. Vždy můžete aktualizovat podrobnosti o zařízení a přidat nebo změnit nadřazený objekt později.

   ![Vytvoření ID zařízení se symetrickým authem klíče na portálu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

K dokončení stejné operace můžete použít [rozšíření IoT pro Azure CLI.](https://github.com/Azure/azure-iot-cli-extension) Následující příklad vytvoří nové zařízení IoT s symetrickým ověřováním pomocí klíče a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Další informace o příkazech Azure CLI pro vytváření zařízení a nadřazenou správu podřízených tématu referenční obsah pro [az iot hub příkazy identity zařízení.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Dále [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby vaše zařízení vědělo, že se má připojit prostřednictvím brány.

## <a name="register-device-x509-self-signed"></a>Registrace zařízení (X.509 s vlastním podpisem)

Pro ověřování vlastním podpisem X.509, které se někdy označuje jako ověřování kryptografickým otiskem, je třeba vytvořit nové certifikáty, které chcete umístit na zařízení IoT. Tyto certifikáty mají kryptografický otisk v nich, které sdílíte s IoT Hub pro ověřování.

Pokud nemáte certifikační autoritu k vytvoření certifikátů X.509, můžete [vytvořit ukázkové certifikáty pro testování funkcí zařízení IoT Edge](how-to-create-test-certificates.md). Při generování testovacích certifikátů pro zařízení pro příjem dat použijte stejný kořenový certifikát certifikační autority, který generoval certifikáty pro vaše zařízení brány.

1. Pomocí certifikátu certifikační autority vytvořte dva certifikáty zařízení (primární a sekundární) pro zařízení pro příjem dat.

   Certifikát zařízení musí mít název subjektu nastavený na ID zařízení, které budete používat při registraci zařízení IoT v centru Azure IoT Hub. Toto nastavení je vyžadováno pro ověřování.

2. Načíst otisk prstu SHA1 (tzv. kryptografický otisk v rozhraní služby IoT Hub) z každého certifikátu, což je řetězec 40 šestnáctkových znaků. Pomocí následujícího příkazu openssl zobrazíte certifikát a najděte otisk prstu:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Spusťte tento příkaz dvakrát, jednou pro primární certifikát a jednou pro sekundární certifikát. Otisky prstů pro oba certifikáty poskytujete při registraci nového zařízení IoT pomocí certifikátů X.509 podepsaných svým držitelem.

3. Přejděte do svého centra IoT hub na webu Azure Portal a vytvořte novou identitu zařízení IoT s následujícími hodnotami:

   * Zadejte **ID zařízení,** které odpovídá názvu subjektu certifikátů vašeho zařízení.
   * Jako typ ověřování vyberte **možnost X.509 S vlastním podpisem.**
   * Vložte šestnáctkové řetězce, které jste zkopírovali z primárních a sekundárních certifikátů zařízení.
   * Vyberte **Nastavit nadřazené zařízení** a zvolte zařízení brány IoT Edge, přes které se toto zařízení pro příjem dat připojí. Pro ověřování zařízení X.509 pro příjem dat je vyžadováno nadřazené zařízení.

   ![Vytvoření ID zařízení s upsáným authem s vlastním podpisem X.509 na portálu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Zkopírujte certifikát zařízení a klíče do libovolného umístění na zařízení pro příjem dat. Přesunutí kopie sdíleného kořenového certifikátu certifikační autority, který generoval certifikát zařízení brány i certifikáty navazujících zařízení, můžete také přesunout.

   Budete odkazovat na tyto soubory v aplikacích leaf zařízení, které se připojují k ioT hubu. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je protokol [zabezpečenékopie.](https://www.ssh.com/ssh/scp/)

5. V závislosti na vašem preferovaném jazyce si prohlédněte ukázky toho, jak lze v aplikacích IoT odkazovat na certifikáty X.509:

   * C#: [Nastavení zabezpečení X.509 ve vašem azure iot hubu](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Rozšíření [IoT pro rozhraní příkazového řádku Azure](https://github.com/Azure/azure-iot-cli-extension) můžete použít k dokončení stejné operace vytvoření zařízení. Následující příklad vytvoří nové zařízení IoT s ověřováním vlastním podpisem X.509 a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Další informace o příkazech Azure CLI pro vytváření zařízení, generování certifikátů a nadřazenou a podřízenou správu najdete v referenčním obsahu pro příkazy [identity zařízení az iot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Dále [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby vaše zařízení vědělo, že se má připojit prostřednictvím brány.

## <a name="register-device-x509-ca-signed"></a>Zaregistrovat zařízení (X.509 CA podepsáno)

Pro ověřování podepsané certifikační autoritou X.509 (CA) potřebujete kořenový certifikát certifikační autority registrovaný v centru IoT Hub, který používáte k podepisování certifikátů pro vaše zařízení IoT. Jakékoli zařízení používající certifikát, který byl vyšiden kořenovým certifikátem certifikační autority nebo jeho zprostředkujícími certifikáty, bude povoleno ověření.

Tato část je založená na pokynech popsaných v článku služby IoT Hub [Nastavení zabezpečení X.509 ve vašem centru Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md). Podle kroků v této části se dozvíte, které hodnoty se mají použít k nastavení zařízení pro příjem dat, které se připojuje přes bránu.

Pokud nemáte certifikační autoritu k vytvoření certifikátů X.509, můžete [vytvořit ukázkové certifikáty pro testování funkcí zařízení IoT Edge](how-to-create-test-certificates.md). Při generování testovacích certifikátů pro zařízení pro příjem dat použijte stejný kořenový certifikát certifikační autority, který generoval certifikáty pro vaše zařízení brány.

1. Postupujte podle pokynů v [registru certifikátů Certifikační autority X.509 do části centra IoT v](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) části Nastavení zabezpečení *X.509 ve vašem centru Azure IoT*hub . V této části provedete následující kroky:

   1. Nahrajte kořenový certifikát certifikační autority. Pokud používáte ukázkové certifikáty, kořenová certifikační autorita je ** \<cesta>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Ověřte, zda vlastníte tento kořenový certifikát certifikační autority.

2. Postupujte podle pokynů v části [Vytvoření zařízení X.509 pro část Služby IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) nastavení zabezpečení *X.509 ve vašem centru Azure IoT*hub . V této části provedete následující kroky:

   1. Přidejte nové zařízení. Zadejte malý název **id zařízení**a zvolte typ ověřování **X.509 PODEPSANÝ Certifikační autoritou**.
   2. Nastavte nadřazené zařízení. Pro zařízení pro příjem dat vyberte **Nastavit nadřazené zařízení** a zvolte zařízení brány IoT Edge, které bude poskytovat připojení k iot hubu.

3. Vytvořte řetěz certifikátů pro vaše zařízení pro příjem dat. Použijte stejný kořenový certifikát certifikační autority, který jste nahráli do služby IoT Hub, abyste vytvořili tento řetězec. Použijte stejné ID zařízení s nižšími písmeny, které jste přisoudili identitě zařízení na portálu.

4. Zkopírujte certifikát zařízení a klíče do libovolného umístění na zařízení pro příjem dat. Přesunutí kopie sdíleného kořenového certifikátu certifikační autority, který generoval certifikát zařízení brány i certifikáty navazujících zařízení, můžete také přesunout.

   Budete odkazovat na tyto soubory v aplikacích leaf zařízení, které se připojují k ioT hubu. K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je protokol [zabezpečenékopie.](https://www.ssh.com/ssh/scp/)

5. V závislosti na vašem preferovaném jazyce si prohlédněte ukázky toho, jak lze v aplikacích IoT odkazovat na certifikáty X.509:

   * C#: [Nastavení zabezpečení X.509 ve vašem azure iot hubu](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Rozšíření [IoT pro rozhraní příkazového řádku Azure](https://github.com/Azure/azure-iot-cli-extension) můžete použít k dokončení stejné operace vytvoření zařízení. Následující příklad vytvoří nové zařízení IoT s ověřováním podepsaným certifikační autoritou X.509 a přiřadí nadřazené zařízení:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Další informace najdete v referenčním obsahu rozhraní Příkazového příkazu Azure pro [az iot hub příkazy identity zařízení.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Dále [načtěte a upravte připojovací řetězec](#retrieve-and-modify-connection-string) tak, aby vaše zařízení vědělo, že se má připojit prostřednictvím brány.

## <a name="retrieve-and-modify-connection-string"></a>Načtení a úprava připojovacího řetězce

Po vytvoření identity zařízení IoT na portálu můžete načíst jeho primární nebo sekundární klíče. Jeden z těchto klíčů musí být součástí připojovacího řetězce, který aplikace používají ke komunikaci se službami IoT Hub. Pro symetrické ověřování pomocí klíče služba IoT Hub poskytuje plně formátovaný připojovací řetězec v podrobnostech zařízení pro vaše pohodlí. Do připojovacího řetězce je třeba přidat další informace o zařízení brány.

Připojovací řetězce pro následná zařízení potřebují následující součásti:

* Centrum IoT, ke kterému se zařízení připojuje:`Hostname={iothub name}.azure-devices.net`
* ID zařízení registrované v rozbočovači:`DeviceID={device ID}`
* Primární nebo sekundární klíč:`SharedAccessKey={key}`
* Zařízení brány, přes které se zařízení připojuje. Zadejte hodnotu **název hostitele** ze souboru config.yaml zařízení brány IoT Edge:`GatewayHostName={gateway hostname}`

Kompletní připojovací řetězec dohromady vypadá takto:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Pokud jste pro toto zařízení s následným přenosem vytvořili vztah nadřazený/podřízený, můžete zjednodušit připojovací řetězec voláním brány přímo jako hostitele připojení. Vztahy nadřazený/podřízený jsou vyžadovány pro ověřování X.509, ale volitelné pro symetrické ověřování pomocí klíče. Například:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

V tomto okamžiku byste měli mít zařízení IoT Edge registrované a nakonfigurované jako brána. Máte také navazující zařízení IoT registrované a směřující na jeho zařízení brány. Posledním krokem je umístění certifikátů na zařízení pro příjem dat, aby se mohlo bezpečně připojit k bráně.

Pokračujte k dalšímu článku v řadě [bran, Připojte zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Dokončením tohoto článku byste měli mít zařízení IoT Edge pracující jako transparentní brána a následné zařízení registrované v centru IoT. Dále je třeba nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala zařízení brány a bezpečně se k němu připojila. Další informace najdete [v tématu Připojení podřízeného zařízení k bráně Azure IoT Edge](how-to-connect-downstream-device.md).
