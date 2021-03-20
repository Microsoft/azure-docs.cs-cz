---
title: Rychlý Start – použití symetrického klíče k zřízení zařízení do Azure IoT Hub pomocí Node.js
description: V tomto rychlém startu použijete sadu Azure IoT SDK pro Node.js se službou Device Provisioning (DPS) k zřízení zařízení symetrického klíče ke službě IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92429346"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>Rychlý Start: zřízení symetrického klíčového zařízení pomocí Node.js

V tomto rychlém startu se dozvíte, jak zřídit vývojový počítač s Windows jako zařízení pro Centrum IoT pomocí Node.js. Toto zařízení bude používat symetrický klíč a jednotlivou registraci k ověřování pomocí instance služby Device Provisioning (DPS), aby se daly přiřadit ke službě IoT Hub. K zřízení zařízení se použije ukázkový kód ze [sady Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node.git) . 

I když tento článek popisuje, jak zřídit jednotlivé registrace, můžete také použít skupiny registrací. Při používání skupin registrací jsou některé rozdíly. Například je nutné použít odvozený klíč zařízení s jedinečným ID registrace pro zařízení. Možnost [zřízení zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) poskytuje příklad skupiny registrací. Další informace o skupinách registrace najdete v tématu [registrace skupin pro ověření symetrického klíče](concepts-symmetric-key-attestation.md#group-enrollments).

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . 

Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). Tento rychlý start vyžaduje vytvořenou instanci služby Device Provisioning Service.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v tématu [zřízení pro víceklientské architektury](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Předpoklady

- Seznamte se s koncepty [zřizování](about-iot-dps.md#provisioning-process) .
- Dokončení [nastavení IoT Hub Device Provisioning Service s Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- [Git](https://git-scm.com/download/).


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com), v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete instanci služby Device Provisioning Service (DPS).

2. Vyberte kartu **spravovat registrace** a pak v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

3. Na panelu **Přidat registraci** zadejte následující informace a stiskněte tlačítko **Uložit** .

   - **Mechanismus:** Jako *Mechanismus* pro ověření identity vyberte **Symetrický klíč**.

   - **Automaticky generovat klíče**: zaškrtněte toto políčko.

   - **ID registrace**: Zadejte ID registrace k identifikaci registrace. Použijte k tomu pouze malá písmena, číslice a spojovník („-“). Například **symm-Key-NodeJS-Device-01**.

   - **ID zařízení IoT Hubu**: Zadejte identifikátor zařízení – Například **NodeJS-Device-01**.

     ![Přidání jednotlivé registrace pro ověření symetrického klíče na webu Azure Portal](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. Po uložení registrace se vygeneruje **primární klíč** a **sekundární klíč** a přidá se k položce registrace. Vaše registrace zařízení symetrického klíče se zobrazí ve sloupci *ID registrace* na kartě *jednotlivé registrace* jako **symm-Key-NodeJS-Device-01** . 

5. Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče**. Tuto hodnotu klíče a **ID registrace** budete používat později, když přidáte proměnné prostředí pro použití s ukázkovým kódem zřizování zařízení.



## <a name="prepare-the-nodejs-environment"></a>Příprava Node.jsho prostředí 

1. Otevřete prostředí příkazového řádku Git CMD nebo Git bash. Naklonujte [sadu Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node.git) úložiště GitHub pomocí následujícího příkazu:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Příprava kódu zřizování zařízení

V této části přidáte následující čtyři proměnné prostředí, které se použijí jako parametry pro vzorový kód Device Provisioning pro zřízení zařízení se symetrickým klíčem. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

Zřizovací kód se spojí s instancí DPS na základě těchto proměnných, aby bylo možné vaše zařízení ověřit. Zařízení se pak přiřadí ke službě IoT Hub, která už je propojená s instancí DPS na základě individuální konfigurace registrace. Po zřízení ukázkový kód pošle několik testovacích telemetrie do služby IoT Hub.

1. V [Azure Portal](https://portal.azure.com)nabídce služba Device Provisioning vyberte **Přehled** a zkopírujte _koncový bod služby_ a _obor ID_. Tyto hodnoty použijete pro `PROVISIONING_HOST` `PROVISIONING_IDSCOPE` proměnné prostředí a.

    ![Informace o službě](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Otevřete příkazový řádek pro spuštění příkazů Node.js a přejděte do následujícího adresáře *zřizování/zařízení/ukázky* .

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. Ve složce *zřizování/zařízení/ukázky* otevřete *register_symkey.js* a zkontrolujte kód. 

    Všimněte si, že vzorový kód nastaví vlastní datovou část...

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Tento kód není v tomto rychlém startu potřeba. Tento kód je příkladem nastavení vlastní datové části, pokud jste chtěli použít vlastní přidělení funkce k přiřazení zařízení k IoT Hub. Další informace najdete v tématu [kurz: použití vlastních zásad přidělování](tutorial-custom-allocation-policies.md).

    `provisioningClient.register()`Metoda se pokusí zaregistrovat vaše zařízení.

    Ukázkovému kódu k registraci zařízení nebudou nutné žádné změny.

4. Do příkazového řádku přidejte proměnné prostředí pro zřizovacího hostitele, rozsah ID, ID registrace a primární symetrický klíč, který jste zkopírovali z jednotlivé registrace v předchozí části.  

    Následující příkazy jsou příklady pro zobrazení syntaxe příkazu. Ujistěte se, že používáte správné hodnoty.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. Sestavte a spusťte vzorový kód pomocí následujících příkazů.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. Očekávaný výstup by měl vypadat podobně jako v následujícím příkladu, který zobrazuje propojené centrum IoT, ke kterému bylo zařízení přiřazeno, na základě jednotlivých nastavení registrace. Do centra se pošle řetězec "Hello World" jako zkušební zpráva:

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. V Azure Portal přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **zařízení IoT** . Po úspěšném zřízení symetrického klíčového zařízení do centra se zobrazí ID zařízení se *stavem* **povoleno**. Možná budete muset stisknout tlačítko **aktualizovat** v horní části, pokud jste už okno otevřeli před spuštěním ukázkového kódu zařízení. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete **spravovat registrace** pro vaši službu a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna. 
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete **zařízení IoT** pro vaše centrum, zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zřídili zařízení symetrického klíče založeného na systému Windows ke službě IoT Hub pomocí IoT Hub Device Provisioning Service. Pokud chcete zjistit, jak zřídit zařízení s certifikátem X. 509 pomocí Node.js, pokračujte v rychlém startu níže pro zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – zřizování zařízení X. 509 pomocí DPS a Node.js](quick-create-simulated-device-x509-node.md)
