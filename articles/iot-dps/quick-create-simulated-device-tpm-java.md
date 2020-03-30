---
title: Úvodní příručka – zřízení simulovaného zařízení TPM do služby Azure IoT Hub pomocí Javy
description: Úvodní příručka – vytvoření a zřízení simulovaného zařízení TPM pomocí sady Java device SDK for Azure IoT Hub Device Provisioning Service (DPS). V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ce67b5e254a62def5f8b024e960cea7f8780e8b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605493"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Úvodní příručka: Vytvoření a zřízení simulovaného zařízení TPM pomocí sady Java device SDK pro službu zřizování zařízení Azure IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

V tomto rychlém startu vytvoříte simulované zařízení IoT v počítači se systémem Windows. Simulované zařízení obsahuje simulátor Čipu TPM jako modul hardwarového zabezpečení (HSM). Ukázkový kód Java zařízení se používá k připojení tohoto simulovaného zařízení k centru IoT pomocí individuální registrace se službou Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Požadavky

- Přehled [konceptů automatického zřizování](concepts-auto-provisioning.md).
- Dokončení [nastavení služby zřizování zařízení služby IoT Hub na webu Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Vývojová sada 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované prostředí [Java SE Development Kit 8](https://aka.ms/azure-jdks).

1. Stáhněte a nainstalujte [Maven](https://maven.apache.org/install.html).

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Spusťte simulátor [Čipu TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) jako [hsm](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) pro simulované zařízení. Kliknutím na **Povolit přístup** povolte změny nastavení brány _Windows Firewall_. Ten naslouchá přes soket na portech 2321 a 2322. Nezavírejte toto okno. musíte udržovat tento simulátor spuštěn až do konce tohoto průvodce rychlým startem. 

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![Simulátor TPM](./media/java-quick-create-simulated-device/simulator.png)

1. V samostatném příkazovém řádku přejděte do kořenové složky a sestavte ukázkové závislosti.

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

1. Přejděte do složky s ukázkou.

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení. Všimněte si globálního koncového bodu _oboru id_ a _zřizovací služby_.

    ![Informace o službě Device Provisioning](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. Upravte `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` tak, aby _zahrnovala obor ID_ a _zřizovací službu globální koncový bod,_ jak je uvedeno dříve.  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```
    Uložte soubor.

1. K sestavení projektu použijte následující příkazy, přejděte do cílové složky a spusťte vytvořený soubor JAR. Nahraďte `version` zástupný symbol verzí Javy.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. Program se spustí. _Poznamenejte_ si klíč potvrzení a _ID registrace_ pro další část a nechte program spuštěný.

    ![Program zařízení TPM v Javě](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): Používá se k registraci jednoho zařízení.

Tento článek ukazuje jednotlivé zápisy.

1. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete službu Zřizování zařízení.

1. V nabídce Služba zřizování zařízení vyberte **Spravovat registrace**. Vyberte **kartu Jednotlivé registrace** a nahoře vyberte tlačítko Přidat jednotlivé **zápisy.** 

1. V panelu **Přidat zápis** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **TPM**.
   - Zadejte *ID registrace* a *ověřovací klíč* pro zařízení TPM z hodnot, které jste si dříve poznamenali.
   - Vyberte centrum IoT propojené s vaší zřizovací službou.
   - Volitelně můžete zadat následující informace:
       - Zadejte jedinečné *ID zařízení*. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. Pokud se rozhodnete neposkytnout jeden, id registrace se použije k identifikaci zařízení místo.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení stiskněte tlačítko **Uložit.** 

     ![Zadání informací o registraci zařízení v okně portálu](./media/java-quick-create-simulated-device/enterdevice-enrollment.png)  

   Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 


## <a name="simulate-the-device"></a>Simulace zařízení

1. V příkazovém okně s ukázkovým kódem Javy na vašem počítači pokračujte v používání aplikace stisknutím *klávesy Enter.* Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT.  

    ![Konečný program zařízení TPM v Javě](./media/java-quick-create-simulated-device/program-final.png)

1. Při úspěšném zřizování simulovaného zařízení do centra IoT propojeného s vaší zřizovací službou se ID zařízení zobrazí v okně **zařízení IoT** centra.

    ![Zařízení je zaregistrované u centra IoT](./media/java-quick-create-simulated-device/hubregistration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete okno **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete okno **zařízení IoT** pro váš rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v počítači vytvořili simulované zařízení TPM a zřídíte ho do služby IoT hub pomocí služby Zřizování zařízení služby IoT Hub. Chcete-li se dozvědět, jak zaregistrovat zařízení Čipové čipové čipy programově, pokračujte na úvodním startu pro programovou registraci zařízení TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)
