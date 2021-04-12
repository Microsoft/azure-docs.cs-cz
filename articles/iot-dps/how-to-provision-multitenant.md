---
title: Jak zřídit zařízení pro víceklientské architektury v Azure IoT Hub Device Provisioning Service
description: Postup zřízení zařízení pro víceklientské prostředí pomocí instance služby Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 04ab4ae4d3de6f33f800011b9b0802665d5fb16e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228312"
---
# <a name="how-to-provision-for-multitenancy"></a>Postupy zřizování pro architekturu s více tenanty 

Tento článek ukazuje, jak bezpečně zřídit více zařízení symetrického klíče pro skupinu centra IoT pomocí [zásad přidělování](concepts-service.md#allocation-policy). Zásady přidělování definované službou zřizování podporují celou řadu scénářů přidělování. Mezi dva běžné scénáře patří:

* **Zeměpisná poloha/geografická latence**: když se zařízení pohybuje mezi místy, zlepšuje se latence sítě díky tomu, že je zařízení zřízené službě IoT Hub nejblíže každému umístění. V tomto scénáři jsou pro registrace vybrány skupiny služby IoT Hub, které jsou rozloženy mezi oblastmi. Pro tyto registrace se vybere nejnižší zásada přidělení **latence** . Tato zásada způsobí, že služba Device Provisioning vyhodnocuje latenci zařízení a určí skříň IoT Hub ze skupiny centra IoT. 

* **Víceklientská architektura: zařízení** používaná v rámci řešení IoT může být potřeba přiřadit ke konkrétnímu centru IoT nebo skupině centra IoT. Řešení může vyžadovat, aby všechna zařízení pro konkrétního tenanta komunikovala s konkrétní skupinou služby IoT Hub. V některých případech může tenant vlastnit centra IoT a vyžadovat, aby zařízení byla přiřazena ke svým centrům IoT.

Tyto dva scénáře je běžné kombinovat. Například řešení IoT pro víceklientské klienty bude obvykle přiřazování klientských zařízení pomocí skupiny služby IoT Hub, které jsou rozptýlené napříč oblastmi. Tato zařízení klientů se dají přiřadit ke službě IoT Hub v této skupině, která má nejnižší latenci na základě geografického umístění.

Tento článek používá ukázku simulovaného zařízení ze [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a předvádí, jak zřídit zařízení ve víceklientské situaci napříč oblastmi. V tomto článku provedete následující kroky:

> [!div class="checklist"]
> * Použití rozhraní příkazového řádku Azure k vytvoření dvou oblastí centra IoT (**západní USA** a **východní USA**)
> * Vytvoření víceklientské registrace
> * Použití rozhraní příkazového řádku Azure k vytvoření dvou virtuálních počítačů se systémem Linux, které budou fungovat jako zařízení ve stejných oblastech (**západní USA** a **východní USA**)
> * Nastavení vývojového prostředí pro sadu Azure IoT C SDK na virtuálních počítačích se systémem Linux
> * Simulujte zařízení, abyste viděli, že jsou zřízené pro stejného tenanta v nejbližší oblasti.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

- Dokončení [nastavení IoT Hub Device Provisioning Service pomocí](./quick-setup-auto-provision.md) nástroje pro rychlý Start Azure Portal
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Vytvoření dvou oblastí centra IoT

V této části použijete Azure Cloud Shell k vytvoření dvou nových oblastí centra IoT v oblasti **západní USA** a **východní USA** pro tenanta.


1. Pomocí Azure Cloud Shell vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

    Následující příklad vytvoří skupinu prostředků s názvem *Contoso-US-Resource-Group* v oblasti *eastus* . Doporučuje se používat tuto skupinu pro všechny prostředky vytvořené v tomto článku. Díky tomu bude vyčištění snazší po dokončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Pomocí Azure Cloud Shell vytvořte centrum IoT v oblasti **eastus** pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Centrum IoT se přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem *Contoso-východ-hub* v umístění *eastus* . Místo **Contoso-východ-hub** musíte použít vlastní jedinečný název centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Dokončení tohoto příkazu může trvat několik minut.

3. Pomocí Azure Cloud Shell vytvořte centrum IoT v oblasti **westus** pomocí příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Toto centrum IoT se taky přidá do *skupiny contoso-US-Resource-Group*.

    Následující příklad vytvoří centrum IoT s názvem *Contoso-západ-hub* v umístění *westus* . Místo názvu **Contoso-západ-hub** musíte použít vlastní jedinečný název centra.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.



## <a name="create-the-multitenant-enrollment"></a>Vytvoření víceklientské registrace

V této části vytvoříte novou skupinu registrace pro zařízení tenanta.  

V zájmu jednoduchosti Tento článek používá k registraci [symetrický klíč s ověřením identity](concepts-symmetric-key-attestation.md) . Pro bezpečnější řešení zvažte použití [ověření certifikátu X. 509](concepts-x509-attestation.md) s řetězem důvěryhodnosti.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete instanci služby Device Provisioning.

2. Vyberte kartu **spravovat registrace** a pak klikněte na tlačítko **Přidat skupinu** registrací v horní části stránky. 

3. Do pole **Přidat skupinu** registrací zadejte následující informace a klikněte na tlačítko **Uložit** .

    **Název skupiny**: zadejte **Contoso-US-Devices**.

    **Typ ověření identity**: vyberte **symetrický klíč**.

    **Automaticky vygenerovat klíče**: Toto zaškrtávací políčko by již mělo být zaškrtnuté.

    **Vyberte, jak chcete přiřadit zařízení k rozbočovačům**: vyberte **nejnižší latenci**.

    ![Přidat skupinu registrací víceklientské architektury pro ověření symetrického klíče](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. V části **Přidat skupinu** registrací klikněte na **propojit nové centrum IoT** a propojte obě vaše místní centra.

    **Předplatné**: Pokud máte více předplatných, vyberte předplatné, ve kterém jste vytvořili oblastní centra IoT.

    **IoT Hub**: vyberte jedno z regionálních rozbočovačů, které jste vytvořili.

    **Zásady přístupu**: vyberte **iothubowner**.

    ![Propojení regionálních center IoT se službou zřizování](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po propojení obou místních rozbočovačů IoT je musíte vybrat pro skupinu registrací a kliknutím na **Uložit** vytvořit místní skupinu IoT Hub pro registraci.

    ![Vytvoření místní skupiny hub pro registraci](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po uložení registrace ho znovu otevřete a poznamenejte si **primární klíč**. Nejdřív musíte uložit registraci, aby se vygenerovaly klíče. Tento klíč se použije k vygenerování jedinečných klíčů zařízení pro simulovaná zařízení později.


## <a name="create-regional-linux-vms"></a>Vytváření virtuálních počítačů pro místní Linux

V této části vytvoříte dva virtuální počítače pro místní Linux. Tyto virtuální počítače spustí ukázku simulace zařízení z každé oblasti a předvedením zřizování zařízení pro klientská zařízení z obou oblastí.

K jednoduššímu vyčištění se tyto virtuální počítače přidají do stejné skupiny prostředků, která obsahuje centra IoT, která se vytvořila, *Contoso-US-Resource-Group*. Virtuální počítače se ale spustí v různých oblastech (**západní USA** a **východní USA**).

1. V Azure Cloud Shell spuštěním následujícího příkazu vytvořte virtuální počítač **východní USA** oblasti po provedení následujících změn parametrů v příkazu:

    **--Name**: Zadejte jedinečný název pro **východní USA** místním virtuálním počítači zařízení. 

    **--admin-username**: použijte vlastní uživatelské jméno správce.

    **--admin-heslo**: použijte vlastní heslo správce.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Dokončení tohoto příkazu bude trvat několik minut. Až se příkaz dokončí, poznamenejte si hodnotu **publicIpAddress** pro virtuální počítač východní USA oblasti.

1. V Azure Cloud Shell spuštěním příkazu vytvořte virtuální počítač **západní USA** oblasti po provedení následujících změn parametrů v příkazu:

    **--Name**: Zadejte jedinečný název pro **západní USA** místním virtuálním počítači zařízení. 

    **--admin-username**: použijte vlastní uživatelské jméno správce.

    **--admin-heslo**: použijte vlastní heslo správce.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Dokončení tohoto příkazu bude trvat několik minut. Až se příkaz dokončí, poznamenejte si hodnotu **publicIpAddress** pro virtuální počítač západní USA oblasti.

1. Otevřete dvě prostředí příkazového řádku. Pomocí SSH se připojte k jednomu z regionálních virtuálních počítačů v jednotlivých prostředích. 

    Předejte uživatelské jméno správce a veřejnou IP adresu, kterou jste si poznamenali pro virtuální počítač, jako parametry pro SSH. Po zobrazení výzvy zadejte heslo správce.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí sady Azure IoT C SDK

V této části budete naklonovat sadu Azure IoT C SDK na každém virtuálním počítači. Sada SDK obsahuje ukázku, která simuluje zřizování zařízení tenanta z každé oblasti.

1. Pro každý virtuální počítač nainstalujte **cmake**, **g + +**, **RSZ** a [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) pomocí následujících příkazů:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

1. Naklonujte [sadu Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) na obou virtuálních počítačích.  Použijte značku, kterou jste našli v předchozím kroku, jako hodnotu `-b` parametru:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Pro oba virtuální počítače vytvořte novou složku **cmake** v úložišti a změňte ji do této složky.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. U obou virtuálních počítačů spusťte následující příkaz, který sestaví verzi sady SDK specifickou pro vaši vývojovou platformu. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Odvodit jedinečné klíče zařízení

Při použití ověřování symetrického klíče pomocí certifikátů skupin se nepoužívají klíče skupiny registrací přímo. Místo toho pro každé zařízení vytvoříte jedinečný odvozený klíč a v [rámci skupinových registrací pomocí symetrických klíčů](concepts-symmetric-key-attestation.md#group-enrollments)jste zmíněni v zápisu skupin.

Pokud chcete vygenerovat klíč zařízení, použijte hlavní klíč skupiny k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) jedinečného ID registrace zařízení a výsledek převeďte na Formát Base64.

Nezahrnujte hlavní klíč skupiny do kódu zařízení.

Použijte příklad prostředí bash k vytvoření odvozeného klíče zařízení pro každé zařízení pomocí **OpenSSL**.

- Nahraďte hodnotu **klíče klíčem** s **primárním klíčem** , který jste si předtím poznamenali pro registraci.

- Nahraďte hodnotu pro **REG_ID** vlastním jedinečným registračním ID pro každé zařízení. Pro definování identifikátorů použijte malé alfanumerické znaky a spojovníky ('-').

Příklad generování klíče zařízení pro *Contoso-simdevice-východ*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Příklad generování klíče zařízení pro *Contoso-simdevice-západ*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Klientské zařízení budou každý použít svůj odvozený klíč zařízení a jedinečné ID registrace k provedení ověření symetrického klíče se skupinou registrace během zřizování pro centra IoT Hub.




## <a name="simulate-the-devices-from-each-region"></a>Simulace zařízení z každé oblasti


V této části budete aktualizovat ukázku zřizování v sadě Azure IoT C SDK pro oba místní virtuální počítače. 

Vzorový kód simuluje spouštěcí sekvenci zařízení, která odesílá požadavek na zřízení do instance služby Device Provisioning. Spouštěcí sekvence způsobí, že se zařízení rozpozná a přiřadí do centra IoT, které je nejblíže v závislosti na latenci.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otevřete **~/Azure-IoT-SDK-c/Provisioning \_ Client/Samples/prov \_ dev \_ Client \_ Sample/prov \_ dev \_ Client \_ Sample. c** pro úpravy na obou virtuálních počítačích.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Ve stejném souboru vyhledejte definici funkce `main()`. Ujistěte se, že `hsm_type` je proměnná nastavená tak `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` , jak je uvedeno níže, aby odpovídala metodě ověření skupiny registrací. 

    Uložte změny do souborů na obou virtuálních počítačích.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obou virtuálních počítačích Najděte volání v části `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ Sample. c** , který je zakomentován.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně lomených závorek) jedinečnými identifikátory registrace a odvozenými klíči zařízení pro každé zařízení. Níže uvedené klíče jsou pouze příklady pro účely. Použijte klíče, které jste vygenerovali dříve.

    Východní USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Západní USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Uložte soubory.

1. Na obou virtuálních počítačích přejděte do ukázkové složky zobrazené níže a sestavte ukázku.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Po úspěšném sestavení spusťte příkaz **prov \_ dev \_ Client \_sample.exe** na obou virtuálních počítačích pro simulaci zařízení tenanta z každé oblasti. Všimněte si, že každé zařízení je přiděleno ke službě IoT Hub tenanta nejbližší k oblastem simulovaného zařízení.

    Spusťte simulaci:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Příklad výstupu Východní USA virtuálního počítače:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Příklad výstupu Západní USA virtuálního počítače:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s prostředky vytvořenými v tomto článku, můžete je nechat. Pokud nechcete pokračovat v používání prostředku, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto článkem, aby nedocházelo k zbytečným poplatkům.

V těchto krocích se předpokládá, že jste vytvořili všechny prostředky v tomto článku podle pokynů ve stejné skupině prostředků s názvem **Contoso-US-Resource-Group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT.
>

Odstranění skupiny prostředků podle názvu:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. Do textového pole **filtrovat podle názvu...** zadejte název skupiny prostředků obsahující vaše prostředky, **Contoso-US-Resource-Group**. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

* Další informace o opětovném zřizování najdete v tématu.

> [!div class="nextstepaction"]
> [Koncepce opětovného zřizování zařízení IoT Hub](concepts-device-reprovision.md)

* Další informace o zrušení zřízení najdete v tématu.
> [!div class="nextstepaction"]
> [Postup zrušení zřízení zařízení, která byla dříve automaticky zřízena](how-to-unprovision-devices.md)