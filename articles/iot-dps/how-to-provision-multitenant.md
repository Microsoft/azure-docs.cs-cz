---
title: Jak zřídit zařízení pro multitenancy ve službě Azure IoT Hub Device Provisioning Service
description: Jak zřídit zařízení pro víceklientské řešení s instancí služby DPS (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434740"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak stanovit víceklientské 

Zásady přidělení definované službou zřizování podporují různé scénáře přidělení. Dva běžné scénáře jsou:

* **Geolocation / GeoLatency**: Jako zařízení se pohybuje mezi umístěními, latence sítě se zlepší tím, že zařízení zřízené do služby IoT hub nejblíže ke každému umístění. V tomto scénáři je pro registrace vybrána skupina center IoT hub, která zahrnuje napříč oblastmi. Pro tyto registrace je vybrána zásada přidělení **nejnižší latence.** Tato zásada způsobí, že služba zřizování zařízení vyhodnotí latenci zařízení a určí skříň služby IoT hub ze skupiny center IoT hub. 

* **Víceklientské**: Zařízení používaná v rámci řešení IoT může být nutné přiřadit ke konkrétnímu centru IoT hub nebo skupině center IoT hub. Řešení může vyžadovat, aby všechna zařízení pro konkrétního klienta komunikovala s konkrétní skupinou center IoT hub. V některých případech může klient vlastnit služby IoT huby a vyžadovat, aby byla zařízení přiřazena k jejich centrům IoT.

Je běžné kombinovat tyto dva scénáře. Například řešení IoT s více tenanty obvykle přiřadí klientská zařízení pomocí skupiny center IoT hubů, které jsou rozptýlené napříč oblastmi. Tato zařízení tenanta lze přiřadit k centru IoT v této skupině, která má nejnižší latenci na základě geografické polohy.

Tento článek používá ukázku simulované zařízení z [azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) k předvedení, jak zřídit zařízení ve scénáři s více tenanty napříč oblastmi. V tomto článku provedete následující kroky:

* Pomocí příkazového příkazového příkazu k Azure vytvořte dvě regionální centra IoT hub **(ZÁPADNÍ USA** a **VÝCHODNÍ USA)**
* Vytvoření víceklientské registrace
* Pomocí azure cli vytvořit dva regionální virtuální počítače s Linuxem fungovat jako zařízení ve stejných oblastech **(Západní USA** a **východní USA)**
* Nastavení vývojového prostředí pro sadu Azure IoT C SDK na obou virtuálních počítačích s Linuxem
* Simulujte zařízení, abyste viděli, že jsou zřízeny pro stejného klienta v nejbližší oblasti.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Dokončení [služby Nastavení zřizování zařízení služby IoT Hub pomocí rychlého startu portálu Azure.](./quick-setup-auto-provision.md)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Vytvoření dvou regionálních center IoT

V této části použijete Prostředí Cloud Shell Azure k vytvoření dvou nových regionálních center IoT hubv oblastech **USA a** **východní USA** pro tenanta.


1. Azure Cloud Shell slouží k vytvoření skupiny prostředků pomocí příkazu [az group create.](/cli/azure/group#az-group-create) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

    Následující příklad vytvoří skupinu prostředků s názvem *contoso-us-resource-group* v oblasti *eastus.* Doporučujeme použít tuto skupinu pro všechny prostředky vytvořené v tomto článku. To bude uklízet jednodušší po dokončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Azure Cloud Shell slouží k vytvoření centra IoT v oblasti **eastus** pomocí příkazu [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Centrum IoT bude přidáno do *skupiny contoso-us-resource- .*

    Následující příklad vytvoří centrum IoT s názvem *contoso-east-hub* v umístění *eastus.* Místo **contoso-east-hub**je nutné použít vlastní jedinečný název rozbočovače .

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Dokončení tohoto příkazu může trvat několik minut.

3. Azure Cloud Shell slouží k vytvoření centra IoT v oblasti **westus** s příkazem [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Toto centrum IoT bude také přidáno do *skupiny contoso-us-resource- .*

    Následující příklad vytvoří centrum IoT s názvem *contoso-west-hub* v umístění *westus.* Místo **contoso-west-hub**je nutné použít vlastní jedinečný název rozbočovače .

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Dokončení tohoto příkazu může trvat několik minut.



## <a name="create-the-multitenant-enrollment"></a>Vytvoření víceklientské registrace

V této části vytvoříte novou skupinu registrace pro zařízení klienta.  

Pro jednoduchost tento článek používá [symetrické atestace klíče](concepts-symmetric-key-attestation.md) s zápisem. Pro bezpečnější řešení zvažte použití [certifikátu X.509 atestace](concepts-security.md#x509-certificates) s řetězcem důvěryhodnosti.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete instanci služby Zřizování zařízení.

2. Vyberte kartu **Spravovat zápisy** a v horní části stránky klikněte na tlačítko **Přidat skupinu zápisů.** 

3. V **části Přidat skupinu zápisů**zadejte následující informace a klepněte na tlačítko **Uložit.**

    **Název skupiny**: Zadejte **contoso-us-devices**.

    **Typ atestace**: Vyberte **symetrický klíč**.

    **Automatické generování klíčů**: Toto zaškrtávací políčko by již mělo být zaškrtnuto.

    **Vyberte způsob přiřazení zařízení k rozbočovačům**: Vyberte **nejnižší latenci**.

    ![Přidání víceklientské skupiny zápisu pro symetrické atestace klíčů](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. V **části Přidat skupinu registrací**klikněte na **Propojit nové centrum IoT a** propojte obě místní centra.

    **Předplatné**: Pokud máte více předplatných, zvolte předplatné, kde jste vytvořili místní centra IoT.

    **Centrum IoT**: Vyberte jedno z místních center, které jste vytvořili.

    **Zásady přístupu**: Zvolte **iothubowner**.

    ![Propojení místních center IoT se službou zřizování](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po propojení obou regionálních center IoT je nutné je vybrat pro skupinu zápisů a kliknutím na **Uložit** vytvořte pro registraci místní skupinu ioT hubů.

    ![Vytvoření skupiny regionálních centrálních center pro registraci](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po uložení registrace ji znovu otevřete a poznamenejte si **primární klíč**. Chcete-li vygenerovat klíče, musíte nejprve uložit registraci. Tento klíč bude později použit ke generování jedinečných klíčů zařízení pro obě simulovaná zařízení.


## <a name="create-regional-linux-vms"></a>Vytvoření místních virtuálních počítačů s Linuxem

V této části vytvoříte dva místní virtuální počítače linuxu (VM). Tyto virtuální aplikace spustí ukázku simulace zařízení z každé oblasti, aby demonstrovaly zřizování zařízení pro zařízení klienta z obou oblastí.

Chcete-li usnadnit čištění, tyto virtuální servery budou přidány do stejné skupiny prostředků, která obsahuje centra IoT, které byly vytvořeny, *contoso-us-resource-group*. Virtuální zařízení však bude fungovat v samostatných oblastech **(Západní USA** a **východní USA**).

1. V prostředí Azure Cloud Shell spusťte následující příkaz k vytvoření virtuálního počítače východní **oblasti USA** po provedení následujících změn parametrů v příkazu:

    **--name**: Zadejte jedinečný název pro místní virtuální počítač **pro vaše východní USA.** 

    **--admin-uživatelské jméno**: Použijte své vlastní uživatelské jméno správce.

    **--admin-password**: Použijte vlastní heslo správce.

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

    Dokončení tohoto příkazu bude trvat několik minut. Po dokončení příkazu si poznamenejte hodnotu **publicIpAddress** pro virtuální počítač v oblasti VÝCHODNÍ USA.

1. V prostředí Azure Cloud Shell spusťte příkaz k vytvoření virtuálního počítače v oblasti **západní USA** po provedení následujících změn parametrů v příkazu:

    **--name**: Zadejte jedinečný název místního virtuálního počítače **pro západní USA.** 

    **--admin-uživatelské jméno**: Použijte své vlastní uživatelské jméno správce.

    **--admin-password**: Použijte vlastní heslo správce.

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

    Dokončení tohoto příkazu bude trvat několik minut. Po dokončení příkazu si poznamenejte hodnotu **publicIpAddress** pro virtuální počítač v oblasti Západní USA.

1. Otevřete dvě granáty příkazového řádku. Připojte se k jednomu z místních virtuálních zařízení v každém prostředí pomocí SSH. 

    Předejte své uživatelské jméno správce a veřejnou IP adresu, kterou jste si pro virtuální počítač jako parametry poznamenali, sc. Po zobrazení výzvy zadejte heslo správce.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části naklonujete azure iot c sdk na každém virtuálním počítači. Sada SDK obsahuje ukázku, která bude simulovat zřizování zařízení klienta z každé oblasti.

1. Pro každý virtuální virtuální virtuální montovna nainstalujte **CMake**, **g++**, **gcc**a [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) pomocí následujících příkazů:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

1. Klonování [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) na obou virtuálních počítačích.  Jako hodnotu `-b` parametru použijte značku, kterou jste našli v předchozím kroku:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Pro oba virtuální aplikace vytvořte novou složku **cmake** uvnitř úložiště a změňte na tuto složku.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Pro oba virtuální počítače spusťte následující příkaz, který vytvoří verzi sady SDK specifickou pro vývojovou klientskou platformu. 

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


## <a name="derive-unique-device-keys"></a>Odvození jedinečných klíčů zařízení

Při použití symetrického atestace klíče s registrací skupin y nepoužíváte přímo klíče skupiny registrace. Místo toho vytvoříte jedinečný odvozený klíč pro každé zařízení a je uvedeno v [registrace skupiny s symetrické klíče](concepts-symmetric-key-attestation.md#group-enrollments).

Chcete-li generovat klíč zařízení, použijte hlavní klíč skupiny k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) jedinečného Registračního ID pro zařízení a převeďte výsledek do formátu Base64.

Do kódu zařízení nezahrnujte hlavní klíč skupiny.

V příkladu prostředí Bash můžete vytvořit odvozený klíč zařízení pro každé zařízení pomocí **openssl**.

- Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si dříve poznamenali pro zápis.

- Nahraďte hodnotu **REG_ID** vlastním jedinečným Registračním ID pro každé zařízení. K definování obou ID použijte malé alfanumerické znaky a pomlčku ('-).

Příklad generování klíče zařízení pro *contoso-simdevice-east*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Příklad generování klíčů zařízení pro *contoso-simdevice-west*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Zařízení klienta budou každý použít svůj odvozený klíč zařízení a jedinečné ID registrace k provedení symetrického atestace klíče se skupinou registrace během zřizování do center IoT klienta.




## <a name="simulate-the-devices-from-each-region"></a>Simulace zařízení z každé oblasti


V této části aktualizujete ukázku zřizování v azure iot c sdk pro oba místní virtuální počítače. 

Ukázkový kód simuluje spouštěcí sekvenci zařízení, která odešle požadavek na zřízení instanci služby Device Provisioning Service. Spouštěcí sekvence způsobí, že zařízení, které mají být rozpoznány a přiřazeny k centru IoT, který je nejblíže na základě latence.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otevřete **~/azure-iot-sdk-c/zřizování\_\_klienta/ukázek/prov dev\_client\_sample/prov\_dev\_client\_sample.c** pro úpravy na obou virtuálních počítačích.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Ve stejném souboru vyhledejte definici funkce `main()`. Ujistěte `hsm_type` se, že `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` proměnná je nastavena tak, jak je znázorněno níže, aby odpovídala metodě atestation skupiny zápisu. 

    Uložte změny do souborů na obou virtuálních počítačích.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obou virtuálních počítačích `prov_dev_set_symmetric_key_info()` najděte volání v **prov\_dev\_client\_sample.c,** který je komentovaný.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně úhlových závorek) jedinečnými ID registrace a odvozenými klíči zařízení pro každé zařízení. Níže uvedené klávesy jsou pouze pro účely. Použijte klíče, které jste vygenerovali dříve.

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

1. Na obou virtuálních počítačích přejděte do ukázkové složky uvedené níže a vytvořte ukázku.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Jakmile sestavení proběhne úspěšně, spusťte **prov\_dev\_klient\_sample.exe** na obou virtuálních počítačích simulovat zařízení klienta z každé oblasti. Všimněte si, že každé zařízení je přiděleno k centru IoT klienta nejblíže k oblasti simulované zařízení.

    Spusťte simulaci:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Příklad výstupu z východního amerického virtuálního soudu:

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

    Příklad výstupu z virtuálního zařízení v USA – západ:
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

Pokud máte v plánu pokračovat v práci s prostředky vytvořenými v tomto článku, můžete je opustit. Pokud neplánujete pokračovat v používání prostředku, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto článkem, abyste se vyhnuli zbytečným poplatkům.

Kroky zde předpokládají, že jste vytvořili všechny prostředky v tomto článku podle pokynů ve stejné skupině prostředků s názvem **contoso-us-resource-group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT.
>

Odstranění skupiny prostředků podle názvu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. Do textového pole **Filtr podle názvu...** zadejte název skupiny prostředků obsahující **prostředky, contoso-us-resource-group**. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

- Další informace o opětovném zřízení najdete v tématu [Koncepty opětovného zřizování zařízení centra IoT Hub](concepts-device-reprovision.md) 
- Další informace o zrušení zřízení najdete v tématu [Jak odzřízení zařízení, která byla dříve automaticky zřízených](how-to-unprovision-devices.md) 











