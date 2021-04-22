---
title: 'Rychlý Start – použití symetrického klíče k zřízení zařízení do Azure IoT Hub pomocí jazyka C #'
description: V tomto rychlém startu použijete sadu SDK pro zařízení v C# pro službu Device Provisioning (DPS) k zřízení zařízení symetrického klíče ke službě IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: a38d58eecd200ec312e7677a05531e27bf4ba6b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868694"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Rychlý Start: zřízení symetrického klíčového zařízení pomocí jazyka C #

V tomto rychlém startu se dozvíte, jak zřídit vývojový počítač s Windows jako zařízení do služby IoT Hub pomocí jazyka C#. Toto zařízení bude používat symetrický klíč a jednotlivou registraci k ověřování pomocí instance služby Device Provisioning (DPS), aby se daly přiřadit ke službě IoT Hub. K zřízení zařízení se použije ukázkový kód ze [vzorků Azure IoT pro jazyk C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) . 

I když tento článek popisuje, jak zřídit jednotlivé registrace, můžete také použít skupiny registrací. Při používání skupin registrací jsou některé rozdíly. Například je nutné použít odvozený klíč zařízení s jedinečným ID registrace pro zařízení. Možnost [zřízení zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) poskytuje příklad skupiny registrací. Další informace o skupinách registrace najdete v tématu [registrace skupin pro ověření symetrického klíče](concepts-symmetric-key-attestation.md#group-enrollments).

Pokud nejste obeznámeni s procesem automatického zřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . 

Než budete pokračovat v tomto rychlém zprovoznění, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). Tento rychlý start vyžaduje vytvořenou instanci služby Device Provisioning Service.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v tématu [zřízení pro víceklientské architektury](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že na počítači se systémem Windows máte nainstalovanou [sadu .NET Core 2,1 SDK](https://dotnet.microsoft.com/download) nebo novější.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

1. Přihlaste se k [Azure Portal](https://portal.azure.com), v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete instanci služby Device Provisioning Service (DPS).

2. Vyberte kartu **spravovat registrace** a pak v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

3. Na panelu **Přidat registraci** zadejte následující informace a stiskněte tlačítko **Uložit** .

   - **Mechanismus:** Jako *Mechanismus* pro ověření identity vyberte **Symetrický klíč**.

   - **Automaticky generovat klíče**: zaškrtněte toto políčko.

   - **ID registrace**: Zadejte ID registrace k identifikaci registrace. Použijte k tomu pouze malá písmena, číslice a spojovník („-“). Například **symm-Key-CSharp-Device-01**.

   - **ID zařízení IoT Hubu**: Zadejte identifikátor zařízení – Například **CSharp-Device-01**.

     ![Přidání jednotlivé registrace pro ověření symetrického klíče na webu Azure Portal](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Po uložení registrace se vygeneruje **primární klíč** a **sekundární klíč** a přidá se k položce registrace. Vaše registrace zařízení symetrického klíče se zobrazí ve sloupci *ID registrace* na kartě *jednotlivé registrace* jako **symm-Key-CSharp-Device-01** . 

5. Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče** a **sekundárního klíče**. Tuto hodnotu klíče a **ID registrace** budete používat později, když přidáte proměnné prostředí pro použití s ukázkovým kódem zřizování zařízení.



## <a name="prepare-the-c-environment"></a>Příprava prostředí C# 

1. Otevřete prostředí příkazového řádku Git CMD nebo Git bash. Pomocí následujícího příkazu naklonujte [ukázky Azure IoT pro](https://github.com/Azure-Samples/azure-iot-samples-csharp) úložiště GitHubu v jazyce C#:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Příprava kódu zřizování zařízení

V této části přidáte následující čtyři proměnné prostředí, které se použijí jako parametry pro vzorový kód Device Provisioning pro zřízení zařízení se symetrickým klíčem. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Zřizovací kód se spojí s instancí DPS na základě těchto proměnných, aby bylo možné vaše zařízení ověřit. Zařízení se pak přiřadí ke službě IoT Hub, která už je propojená s instancí DPS na základě individuální konfigurace registrace. Po zřízení ukázkový kód pošle několik testovacích telemetrie do služby IoT Hub.

1. V [Azure Portal](https://portal.azure.com)nabídce služba Device Provisioning vyberte **Přehled** a zkopírujte _koncový bod služby_ a _obor ID_. Tyto hodnoty použijete pro `PROVISIONING_HOST` `DPS_IDSCOPE` proměnné prostředí a.

    ![Informace o službě](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Otevřete příkazový řádek a přejděte do *SymmetricKeySample* v úložišti klonovaných ukázek:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. Ve složce *SymmetricKeySample* otevřete *program. cs* v textovém editoru a najděte řádky kódu, které nastavují `individualEnrollmentPrimaryKey` `individualEnrollmentSecondaryKey` řetězce a. Aktualizujte tyto řádky kódu následujícím způsobem, aby se místo pevného kódování klíčů používaly proměnné prostředí.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "&quot;;
        //private const string individualEnrollmentSecondaryKey = &quot;&quot;;

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable(&quot;PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Také vyhledejte řádek kódu, který nastaví `registrationId` řetězec, a aktualizujte ho následujícím způsobem, aby se také použila proměnná prostředí, jak je znázorněno níže:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Uložte změny do *programu program. cs*.

3. Do příkazového řádku přidejte proměnné prostředí pro rozsah ID, ID registrace, primární a sekundární symetrický klíč, který jste zkopírovali z jednotlivé registrace v předchozí části.  

    Následující příkazy jsou příklady pro zobrazení syntaxe příkazu. Ujistěte se, že používáte správné hodnoty.

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. Sestavte a spusťte vzorový kód pomocí následujícího příkazu.

    ```console
    dotnet run
    ```

5. Očekávaný výstup by měl vypadat podobně jako v následujícím příkladu, který ukazuje propojené centrum IoT, ke kterému bylo zařízení přiřazeno, na základě jednotlivých nastavení registrace. Příkladem řetězce "TestMessage" je odeslání do centra jako test:

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. V Azure Portal přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **zařízení IoT** . Po úspěšném zřízení symetrického klíčového zařízení do centra se zobrazí ID zařízení se *stavem* **povoleno**. Možná budete muset stisknout tlačítko **aktualizovat** v horní části, pokud jste už okno otevřeli před spuštěním ukázkového kódu zařízení. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete **spravovat registrace** pro vaši službu a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna. 
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete **zařízení IoT** pro vaše centrum, zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zřídili zařízení symetrického klíče založeného na systému Windows ke službě IoT Hub pomocí IoT Hub Device Provisioning Service. Pokud se chcete dozvědět, jak zřídit zařízení s certifikátem X. 509 pomocí jazyka C#, pokračujte v rychlém startu níže pro zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – zřizování zařízení X. 509 pomocí DPS a C #](quick-create-simulated-device-x509-csharp.md)
