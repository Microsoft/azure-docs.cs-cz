---
title: Rychlý Start – použití symetrického klíče k zřízení zařízení do Azure IoT Hub pomocí Pythonu
description: V tomto rychlém startu použijete sadu SDK Azure IoT Python k zřízení zařízení symetrického klíče pro službu IoT Hub s využitím Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 0fe9d59e97ebbc9aba17fea14aed43756300d56e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90528583"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>Rychlý Start: zřízení zařízení v Pythonu pomocí symetrických klíčů

V tomto rychlém startu se dozvíte, jak zřídit vývojový počítač s Windows jako zařízení pro IoT Hub pomocí Pythonu. Toto zařízení bude používat symetrický klíč k ověřování pomocí instance služby Device Provisioning (DPS), aby se daly přiřadit ke službě IoT Hub. Ověřené zařízení bude rozpoznáno pomocí DPS na základě individuální registrace a přiřazeno ke službě IoT Hub. K zřízení zařízení se použije ukázkový kód ze [sady Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) . 

I když tento článek popisuje, jak zřídit jednotlivé registrace, můžete také použít skupiny registrací. Při používání skupin registrací jsou některé rozdíly. Například je nutné použít odvozený klíč zařízení s jedinečným ID registrace pro zařízení. Přestože skupiny registrací symetrického klíče nejsou omezené na starší verze zařízení, příklad skupiny registrací najdete v článku o [zřízení starší verze zařízení pomocí osvědčení symetrického klíče](how-to-legacy-device-symm-key.md). Další informace najdete v článku o [osvědčení symetrického klíče v části o skupinových registracích](concepts-symmetric-key-attestation.md#group-enrollments).

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . 

Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). Tento rychlý start vyžaduje vytvořenou instanci služby Device Provisioning Service.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v článku o [zřizování architektury s více tenanty](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Předpoklady

* Ujistěte se, že na počítači se systémem Windows máte nainstalovaný [Python 3,7](https://www.python.org/downloads/) nebo novější. Verzi Pythonu můžete kontrolovat spuštěním `python --version` .

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>Příprava prostředí Python SDK 

1. Ujistěte se, že je na vašem počítači nainstalovaný Git a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

2. Otevřete příkazový řádek. Naklonujte úložiště GitHub pro sadu SDK Azure IoT Python:
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. Přejděte do `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` adresáře, kde je umístěn ukázkový soubor _provision_symmetric_key. py_.
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. Spusťte následující příkaz a nainstalujte knihovnu _Azure-IoT-Device_ .

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com), v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete instanci služby Device Provisioning Service (DPS).

2. Vyberte kartu **spravovat registrace** a pak v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

3. Na panelu **Přidat registraci** zadejte následující informace a stiskněte tlačítko **Uložit** .

   - **Mechanismus:** Jako *Mechanismus* pro ověření identity vyberte **Symetrický klíč**.

   - **Automaticky generovat klíče**: zaškrtněte toto políčko.

   - **ID registrace**: Zadejte ID registrace k identifikaci registrace. Použijte k tomu pouze malá písmena, číslice a spojovník („-“). Například **symm-Key-Python-Device-008**.

   - **ID zařízení IoT Hubu**: Zadejte identifikátor zařízení – Například **Python-Device-008**.

     ![Přidání jednotlivé registrace pro ověření symetrického klíče na webu Azure Portal](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. Po uložení registrace se vygeneruje **primární klíč** a **sekundární klíč** a přidá se k položce registrace. Vaše registrace zařízení symetrického klíče se zobrazí jako **symm-Key-Python-Device-008** pod sloupcem *ID registrace* na kartě *jednotlivé registrace* . 

5. Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče**. Tuto hodnotu klíče a **ID registrace** budete používat později, když přidáte proměnné prostředí pro použití s ukázkovým kódem zřizování zařízení.



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Příprava kódu zřizování zařízení

V této části přidáte následující čtyři proměnné prostředí, které se použijí jako parametry pro ukázkový kód zřizování zařízení pro zařízení se symetrickým klíčem. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

Zřizovací kód se spojí s instancí DPS na základě těchto proměnných, aby bylo možné vaše zařízení ověřit. Zařízení se pak přiřadí ke službě IoT Hub, která už je propojená s instancí DPS na základě individuální konfigurace registrace. Po zřízení ukázkový kód pošle několik testovacích telemetrie do služby IoT Hub.

1. V [Azure Portal](https://portal.azure.com)nabídce služba Device Provisioning vyberte **Přehled** a zkopírujte _koncový bod služby_ a _obor ID_. Tyto hodnoty použijete pro `PROVISIONING_HOST` `PROVISIONING_IDSCOPE` proměnné prostředí a.

    ![Informace o službě](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. Do příkazového řádku Pythonu přidejte proměnné prostředí pomocí hodnot, které jste zkopírovali. 

    Následující příkazy jsou příklady pro zobrazení syntaxe příkazu. Ujistěte se, že používáte správné hodnoty.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. Do příkazového řádku Pythonu přidejte proměnné prostředí pro ID registrace a symetrický klíč, který jste zkopírovali z jednotlivé registrace v předchozí části. 

    Následující příkazy jsou příklady pro zobrazení syntaxe příkazu. Ujistěte se, že používáte správné hodnoty.

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. Spusťte vzorový kód Pythonu v souboru _provision_symmetric_key. py_.

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. Očekávaný výstup by měl vypadat podobně jako v následujícím příkladu, který ukazuje propojené centrum IoT, ke kterému bylo zařízení přiřazeno, na základě jednotlivých nastavení registrace. Některé ukázkové zprávy telemetrie rychlosti větru se také odesílají do centra jako test:

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. V Azure Portal přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **zařízení IoT** . Po úspěšném zřízení symetrického klíčového zařízení do centra se zobrazí ID zařízení se *stavem* **povoleno**. Možná budete muset stisknout tlačítko **aktualizovat** v horní části, pokud jste už okno otevřeli před spuštěním ukázkového kódu zařízení. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete **spravovat registrace** pro vaši službu a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna. 
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete **zařízení IoT** pro vaše centrum, zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zřídili zařízení symetrického klíče založeného na systému Windows ke službě IoT Hub pomocí IoT Hub Device Provisioning Service. Pokud se chcete dozvědět, jak zřídit zařízení s certifikátem X. 509 pomocí Pythonu, pokračujte v rychlém startu níže pro zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – zřizování zařízení X. 509 pomocí DPS a Pythonu](quick-create-simulated-device-x509-python.md)
