---
title: Posílání telemetrie zařízení do Azure IoT Central rychlý Start (Python)
description: V tomto rychlém startu použijete sadu Azure IoT Hub Device SDK pro Python k odeslání telemetrie ze zařízení do IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047163"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Rychlý Start: odeslání telemetrie ze zařízení do služby Azure IoT Central (Python)

**Platí pro**: [vývoj aplikací pro zařízení](about-iot-develop.md#device-application-development)

V tomto rychlém startu se naučíte základní pracovní postup vývoje aplikací pro zařízení IoT. Nejprve použijte Azure IoT Central k vytvoření cloudové aplikace. Pak použijete sadu SDK Azure IoT Python k sestavení simulovaného zařízení, připojení k IoT Central a odeslání telemetrie typu zařízení-Cloud. 

## <a name="prerequisites"></a>Požadavky
- [Python 3.7 +](https://www.python.org/downloads/). Další podporované verze Pythonu najdete v tématu [funkce zařízení Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Pokud chcete zajistit aktuálnost verze Pythonu, spusťte příkaz `python --version` . Pokud máte nainstalované Python 2 i Python 3 a používáte prostředí Python 3, nainstalujte všechny knihovny pomocí nástroje `pip3` . Spuštění `pip3` zajistí, že se knihovny nainstalují do modulu runtime Python 3.
    > [!IMPORTANT]
    > V instalačním programu Python vyberte možnost **Přidání Pythonu k cestě**. Pokud už máte nainstalovaný Python 3,7 nebo vyšší verzi, potvrďte, že jste přidali instalační složku Pythonu do `PATH` proměnné prostředí.

## <a name="create-an-application"></a>Vytvoření aplikace
V této části vytvoříte aplikaci IoT Central. IoT Central je aplikační platforma IoT založená na portálu, která pomáhá snižovat složitost a náklady na vývoj a správu řešení IoT.

Vytvoření aplikace Azure IoT Central:
1. Přejděte do [Azure IoT Central](https://apps.azureiotcentral.com/) a přihlaste se pomocí osobního, pracovního nebo školního účtu Microsoft.
1. Přejděte k **sestavení** a vyberte **vlastní aplikace**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Úvodní stránka IoT Central":::
1. Do pole **název aplikace** zadejte jedinečný název nebo použijte vygenerovaný název.
1. Do pole **Adresa URL** zadejte adresu URL zapamatovatelné adresy URL aplikace nebo použijte vygenerovanou PŘEDPONU adresy URL.
1. Ponechte **šablonu aplikace** nastavenou na *vlastní aplikaci*. V rozevíracím seznamu se můžou zobrazit další možnosti, pokud už ve vašem účtu existují nějaké šablony.
1. Vyberte možnost **cenového plánu** . 
    - Chcete-li používat aplikaci zdarma po dobu sedmi dnů, vyberte možnost **Free**. Bezplatnou aplikaci můžete převést na standardní ceny před tím, než vyprší její platnost.
    - Volitelně můžete vybrat Cenový tarif Standard. Pokud vyberete ceny Standard, zobrazí se další možnosti a budete muset nastavit **adresář**, **předplatné Azure** a **umístění**. Další informace o cenách najdete v tématu [ceny služby Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Adresář** je Azure Active Directory, ve kterém vytvoříte aplikaci. Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Azure Active Directory, vytvoří se při vytváření předplatného Azure.
        - **Předplatné Azure** umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud nemáte předplatné Azure, můžete [ho vytvořit zdarma](https://azure.microsoft.com/free/). Po vytvoření předplatného se vraťte na stránku IoT Central **novou aplikaci** . Vaše nové předplatné se zobrazí v rozevíracím seznamu **předplatné Azure** .
        - **Umístění** je [geografická oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , ve které vytvoříte aplikaci. Pro dosažení optimálního výkonu vyberte umístění, které je fyzicky nejbližší pro vaše zařízení. Po zvolení umístění nelze aplikaci přesunout do jiného umístění.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Dialog IoT Central nové aplikace":::
1. Vyberte **Vytvořit**.
    
    Po IoT Central aplikaci vytvoří, přesměruje vás na řídicí panel aplikace.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central nový řídicí panel aplikace":::

## <a name="add-a-device"></a>Přidání zařízení
V této části přidáte do aplikace IoT Central nové zařízení. Zařízení je instance šablony zařízení, která představuje reálné nebo simulované zařízení, které se připojíte k aplikaci. 

Vytvoření nového zařízení:
1. V levém podokně vyberte **zařízení** a pak vyberte **+ Nový**. Tím se otevře dialogové okno nové zařízení.
1. Ponechte **šablonu zařízení** nastavenou na *nepřiřazený*.

    > [!NOTE]
    > V tomto rychlém startu se pro zjednodušení připojí simulované zařízení, které používá nepřiřazenou šablonu. Pokud budete nadále používat IoT Central ke správě zařízení, přečtěte si o používání šablon zařízení. Přehled práce s šablonami zařízení najdete v tématu [rychlý Start: Přidání simulovaného zařízení do aplikace IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Nastavte popisný **název zařízení** a **ID zařízení**. Volitelně můžete použít generované hodnoty.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Dialog IoT Central nové zařízení":::
1. Vyberte **Vytvořit**.

    Vytvořené zařízení se zobrazí v seznamu **všechna zařízení** .
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Seznam IoT Central všech zařízení":::
    
Načtení podrobností o připojení pro nové zařízení:
1. V seznamu **všechna zařízení** dvakrát klikněte na název propojeného zařízení, aby se zobrazily podrobnosti. 
1. V horní nabídce vyberte **Připojit**.

    Dialogové okno **připojení zařízení** zobrazí podrobnosti o připojení: :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central podrobnosti připojení zařízení"::: .
1. Zkopírujte následující hodnoty z dialogu **připojení zařízení** do bezpečného umístění. Pomocí těchto kroků v další části zařízení připojíte k IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Odesílání zpráv a monitorování telemetrie
V této části použijete sadu Python SDK k sestavení simulovaného zařízení a k odeslání telemetrie do aplikace IoT Central. 

1. Otevřete terminál pomocí Windows CMD nebo PowerShellu nebo bash (pro Windows nebo Linux). Pomocí terminálu nainstalujete sadu Python SDK, aktualizujete proměnné prostředí a spustíte ukázku kódu Pythonu.

1. Zkopírujte [ukázky zařízení sady SDK Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) do svého místního počítače.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Přejděte do adresáře *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples* .

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Nainstalujte sadu SDK Azure IoT Python.

    ```console
    pip install azure-iot-device
    ```

1. Nastavte každou z následujících proměnných prostředí, abyste mohli simulované zařízení připojit k IoT Central. V případě `ID_SCOPE` , `DEVICE_ID` a `DEVICE_KEY` použijte hodnoty, které jste uložili v dialogovém okně IoT Central *připojení zařízení* .

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Pro Windows CMD nejsou kolem připojovacího řetězce ani jiných hodnot proměnných uvozovky.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux nebo Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. V terminálu spusťte kód ukázkového souboru * simple_send_temperature. py. Tento kód přistupuje k simulovanému zařízení IoT a pošle zprávu IoT Central.

    Spuštění ukázky Pythonu z terminálu:
    ```console
    python ./simple_send_temperature.py
    ```

    Volitelně můžete spustit kód Pythonu z ukázky v integrovaném vývojovém prostředí (IDE) Pythonu:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Protože kód Pythonu pošle do vaší aplikace IoT Central zprávu z vašeho zařízení, zprávy se zobrazí na kartě **nezpracovaná data** v zařízení v IoT Central. Možná budete muset aktualizovat stránku, aby se zobrazily nedávné zprávy.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Snímek obrazovky IoT Central výstup nezpracovaných dat":::

Vaše zařízení je teď bezpečně připojené a odesílá telemetrii do Azure IoT.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete prostředky IoT Central vytvořené v tomto kurzu, můžete je odstranit z portálu IoT Central. Pokud budete chtít pokračovat v dokumentaci v této příručce, můžete také aplikaci, kterou jste vytvořili, zachovat a znovu ji použít pro jiné ukázky.

Odebrání ukázkové aplikace Azure IoT Central a všech jejích zařízení a prostředků:
1. Vyberte možnost **Správa**  >  **aplikace**.
1. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili základní pracovní postup aplikace Azure IoT pro bezpečné připojení zařízení ke cloudu a odesílání telemetrie typu zařízení-Cloud. Použili jste IoT Central Azure k vytvoření aplikace a zařízení, pak jste použili sadu SDK Azure IoT Python k vytvoření simulovaného zařízení a odeslání telemetrie. Použili jste také IoT Central k monitorování telemetrie.

V dalším kroku Prozkoumejte sadu Azure IoT Python SDK prostřednictvím ukázek aplikací.

- [Asynchronní ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Tento adresář obsahuje asynchronní ukázky Pythonu pro další IoT Hub scénáře.
- [Synchronní ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Tento adresář obsahuje ukázky Pythonu pro použití s pythonem 2,7 nebo se synchronními scénáři kompatibility pro Python 3.6 +.
- [Ukázky IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Tento adresář obsahuje ukázky Pythonu pro práci s hraničními moduly a zařízeními pro příjem dat.
