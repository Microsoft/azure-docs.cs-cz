---
title: Jak zřídit zařízení pro víceklientskou architekturu v Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Jak zřídit zařízení pro víceklientskou architekturu s vaším zařízením zřizování instance služby
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 73ff58148ac68b7aeb782b77385f9f971e02edb5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457387"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak zřídit pro víceklientskou architekturu 

Zásady přidělování definované zřizovací služba podporuje širokou škálu scénářů přidělení. Dvě běžné scénáře jsou:

* **Informace o zeměpisné poloze / GeoLatency**: jak zařízení přesune mezi umístěními, zlepší se latence sítě tím, že zařízení pro službu IoT hub do jednotlivých umístění co nejblíže. V tomto scénáři skupiny centra IoT hub, které pracovat nad více oblastí, jsou vybrány pro registraci. **Nejnižší latenci** zásady přidělování je vybraná tato registrace. Tato zásada způsobí, že služby Device Provisioning k vyhodnocení zařízení latence a určit skříni služby IoT hub ze skupiny centra IoT hub. 

* **Víceklientská architektura**: zařízení používaná v rámci řešení IoT může musí mít přiřazenu na konkrétní Centrum IoT nebo skupinu centra IoT hub. Řešení může vyžadovat všechna zařízení pro konkrétního klienta ke komunikaci s konkrétní skupinu centra IoT hub. V některých případech může tenanta vlastní centra IoT hub a vyžadovat, aby zařízení má být přiřazena k jejich centra IoT hub.

Je běžné zkombinovat tyto dva scénáře. Například víceklientské řešení IoT obvykle, přiřadí se zařízení tenanta pomocí skupiny centra IoT hub, které jsou rozmístěny napříč oblastmi. Tato zařízení tenanta je přiřadit ke službě IoT hub v této skupině, která má nejnižší latenci na základě geografického umístění.

Tento článek používá ukázku simulovaného zařízení z [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) k ukazují, jak zřídit zařízení ve scénáři víceklientské napříč oblastmi. V tomto článku provedete následující kroky:

* Vytvořte dva místní centra IoT pomocí Azure CLI (**USA – západ** a **USA – východ**)
* Vytvoření víceklientských registrace
* Vytvořte dva místní virtuální počítače s Linuxem jako zařízení ve stejné oblasti pomocí rozhraní příkazového řádku Azure (**USA – západ** a **USA – východ**)
* Nastavení vývojového prostředí pro Azure IoT C SDK na obou virtuálních počítačů s Linuxem
* Simulace zařízení, že je zřízený pro stejného tenanta v nejbližší oblasti.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavení služby IoT Hub Device Provisioning pomocí webu Azure portal](./quick-setup-auto-provision.md) rychlý start.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Vytvořte dva místní centra IoT

V této části použijete Azure Cloud Shell vytvořte dva nové regionálních Center IoT hub **USA – západ** a **USA – východ** oblastí pro tenanta.


1. Použít Azure Cloud Shell vytvořte skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az-group-create) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

    Následující příklad vytvoří skupinu prostředků s názvem *contoso-us-resource-group* v *eastus* oblasti. Doporučujeme použít tuto skupinu pro všechny prostředky vytvořené v tomto článku. To vám usnadní vyčištění po skončení.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Použít k vytvoření centra IoT v Azure Cloud Shell **eastus** oblasti s [vytvořit az iot hub](/cli/azure/iot/hub#az-iot-hub-create) příkazu. IoT hub se přidají do *contoso-us-resource-group*.

    Následující příklad vytvoří službu IoT hub s názvem *contoso východ hub* v *eastus* umístění. Je nutné použít vlastní název jedinečný centra místo **contoso východ hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Tento příkaz může trvat několik minut.

3. Použít k vytvoření centra IoT v Azure Cloud Shell **westus** oblasti s [vytvořit az iot hub](/cli/azure/iot/hub#az-iot-hub-create) příkazu. Toto centrum IoT se také zařadí do *contoso-us-resource-group*.

    Následující příklad vytvoří službu IoT hub s názvem *contoso západ hub* v *westus* umístění. Je nutné použít vlastní název jedinečný centra místo **contoso západ hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Tento příkaz může trvat několik minut.



## <a name="create-the-multitenant-enrollment"></a>Vytvoření víceklientských registrace

V této části vytvoříte novou skupinu registrací pro klienta zařízení.  

Pro zjednodušení tohoto článku používá [symetrického klíče ověření](concepts-symmetric-key-attestation.md) se registrace. Bezpečnější řešení, zvažte použití [ověření certifikátu X.509](concepts-security.md#x509-certificates) s řetěz certifikátů.

1. Přihlaste se k [webu Azure portal](http://portal.azure.com)a otevřete vaší instanci služby Device Provisioning.

2. Vyberte **Správa registrací** kartu a potom klikněte na tlačítko **přidat skupinu registrací** tlačítko v horní části stránky. 

3. Na **přidat skupinu registrací**, zadejte následující informace a klikněte na tlačítko **Uložit** tlačítko.

    **Název skupiny**: Zadejte **contoso-us zařízení**.

    **Typ ověření**: vyberte **symetrický klíč**.

    **Automaticky generovat klíče**: Toto políčko mělo být již zaškrtnuté.

    **Vyberte, jak chcete přiřadit zařízení k centrům**: vyberte **nejnižší latenci**.

    ![Přidat skupinu víceklientské registrace pro ověření identity symetrického klíče](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Na **přidat skupinu registrací**, klikněte na tlačítko **propojit novou službu IoT hub** propojení i místní centra.

    **Předplatné**: Pokud máte více předplatných, vyberte předplatné, ve které jste vytvořili místní centra IoT.

    **Služby IoT hub**: vyberte jednu z místní centra, který jste vytvořili.

    **Zásady přístupu**: Zvolte **iothubowner**.

    ![Propojení místní centra IoT pomocí služby zřizování](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po propojená i místní centra IoT, musíte virtuální počítače pro skupiny registrací a klikněte na tlačítko **Uložit** vytvořte místní skupinu centra IoT pro registraci.

    ![Vytvoření skupiny regionální centrum pro registraci](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po uložení registraci, znovu ho otevřete a poznamenejte si, **primární klíč**. Registrace nejprve má klíčů vygenerovaných musíte uložit. Tento klíč se použije k vygenerování klíče jedinečné zařízení pro Simulovaná zařízení i později.


## <a name="create-regional-linux-vms"></a>Vytvořit místní virtuální počítače s Linuxem

V této části vytvoříte dvě regionální virtuální počítače s Linuxem (VM). Tyto virtuální počítače se spustí ukázku simulace zařízení z každé oblasti k předvedení zřizování zařízení pro zařízení tenanta z obou oblastí.

Chcete-li vyčistit klidní, tyto virtuální počítače se přidají do stejné skupiny prostředků, který obsahuje centra IoT hub, které byly vytvořeny, *contoso-us-resource-group*. Ale virtuálních počítačích poběží v samostatné oblasti (**USA – západ** a **USA – východ**).

1. Ve službě Azure Cloud Shell, spusťte následující příkaz k vytvoření **USA – východ** oblasti virtuálních počítačů po provedení změn následující parametr v příkazu:

    **– název**: Zadejte jedinečný název pro váš **USA – východ** místní zařízení virtuálního počítače. 

    **uživatelské jméno – správce**: použít vlastní uživatelské jméno správce.

    **– Správce hesel**: použijte heslo správce.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEest \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Tento příkaz bude trvat několik minut. Po dokončení příkazu si poznamenejte, **publicIpAddress** hodnotu pro vaši oblast východní USA virtuálního počítače.

1. Ve službě Azure Cloud Shell, můžete spustit příkaz pro vytvoření **USA – západ** oblasti virtuálních počítačů po provedení změn následující parametr v příkazu:

    **– název**: Zadejte jedinečný název pro váš **USA – západ** místní zařízení virtuálního počítače. 

    **uživatelské jméno – správce**: použít vlastní uživatelské jméno správce.

    **– Správce hesel**: použijte heslo správce.

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

    Tento příkaz bude trvat několik minut. Po dokončení příkazu si poznamenejte, **publicIpAddress** hodnotu pro vaši oblast západní USA virtuálního počítače.

1. Otevřete dvě prostředí příkazového řádku. Připojte se k jednomu z místní virtuální počítače v každé prostředí pomocí protokolu SSH. 

    Předejte své uživatelské jméno admin a veřejnou IP adresu, kterou jste si poznamenali pro virtuální počítač jako parametry pro SSH. Zadejte heslo správce, po zobrazení výzvy.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí sady Azure IoT C SDK

V této části se klonovat sady Azure IoT C SDK na každém virtuálním počítači. Tato sada SDK obsahuje ukázky, která budou simulovat zřizování z každé oblasti klienta zařízení.


1. Pro každý virtuální počítač, nainstalujte **Cmake**, **g ++**, **gcc**, a [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) pomocí následujících příkazů:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Klonování [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) oba virtuální počítače.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. Pro oba virtuální počítače, vytvořte nový **cmake** složky v úložišti a změny do této složky.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Pro oba virtuální počítače spusťte následující příkaz, který je založen na verzi sady SDK, které jsou specifické pro vaše klientská platforma pro vývoj. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON ..
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


## <a name="derive-unique-device-keys"></a>Odvození klíče jedinečné zařízení

Při ověření identity symetrického klíče pomocí skupinových registrací, nepoužívejte klíče registrace skupiny přímo. Místo toho vytvoříte jedinečný odvozené klíče pro každé zařízení a jsme už zmínili v [skupinové registrace pomocí symetrických klíčů](concepts-symmetric-key-attestation.md#group-enrollments).

Vygenerujte klíč zařízení, nepoužívejte k výpočtu skupiny hlavního klíče [HMAC SHA256](https://wikipedia.org/wiki/HMAC) jedinečným registračním ID zařízení a převodu výsledků do formátu Base64.

Nezahrnují váš hlavní klíč skupiny ve vašem kódu zařízení.

Použijte například prostředí Bash a vytvořte klíč odvozené zařízení pro každé zařízení pomocí **openssl**.

- Nahraďte hodnotu pro **klíč** s **primární klíč** jste si předtím poznamenali pro vaši registraci.

- Nahraďte hodnotu pro **REG_ID** s vlastním jedinečným registračním ID pro každé zařízení. Použít malé alfanumerické znaky a pomlčky ("-") znaků, které mají definovat i ID.

Příklad zařízení generování klíčů pro *contoso-simdevice východ*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Příklad zařízení generování klíčů pro *contoso-simdevice západní*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Klienta zařízení se každý pomocí jejich odvozené zařízení klíč a ID registrace jedinečné provádět symetrického klíče ověření identity ve skupině pro registraci během zřizování do tenanta služby IoT hubs.




## <a name="simulate-the-devices-from-each-region"></a>Simulace zařízení v jednotlivých oblastech


V této části budete aktualizovat ukázkou zřizování v sadě Azure IoT C SDK pro obě místní virtuální počítače. 

Vzorový kód simuluje posloupnost spouštěcí zařízení, která odešle žádost o zřízení instance služby Device Provisioning. Pořadí spouštění způsobí, že zařízení bude používat a přiřazen do služby IoT hub, který je nejblíž založen na latenci.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otevřít **~/azure-iot-sdk-c/provisioning\_klienta/samples/prov\_dev\_klienta\_vzorku/prov\_dev\_klienta\_sample.c**pro úpravy na obou virtuálních počítačích.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Ve stejném souboru vyhledejte definici funkce `main()`. Ujistěte se, že `hsm_type` proměnná je nastavená na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` jak je znázorněno níže tak, aby odpovídaly metoda ověření skupiny registrací. 

    Uložte provedené změny do souborů na obou virtuálních počítačích.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```


1. Otevřít **~/azure-iot-sdk-c/provisioning\_klienta/adaptéry/hsm\_klienta\_key.c** oba virtuální počítače. 

    ```bash
     vi ~/azure-iot-sdk-c/provisioning_client/adapters/hsm_client_key.c
    ```

1. Vyhledejte deklaraci konstant `REGISTRATION_NAME` a `SYMMETRIC_KEY_VALUE`. Proveďte následující změny k souborům na oba místní virtuální počítače a uložte soubory.

    Aktualizujte hodnotu `REGISTRATION_NAME` konstanty s **jedinečným registračním ID pro vaše zařízení**.
    
    Aktualizujte hodnotu `SYMMETRIC_KEY_VALUE` konstanty s vaší **odvozený klíč zařízení**.

    ```c
    static const char* const REGISTRATION_NAME = "contoso-simdevice-east";
    static const char* const SYMMETRIC_KEY_VALUE = "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=";
    ```

    ```c
    static const char* const REGISTRATION_NAME = "contoso-simdevice-west";
    static const char* const SYMMETRIC_KEY_VALUE = "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=";
    ```

1. Oba virtuální počítače přejděte do složky s ukázkou níže a sestavit ukázku.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Po úspěšném sestavení spusťte **prov\_dev\_klienta\_sample.exe** oba virtuální počítače pro simulaci zařízení klienta v každé oblasti. Všimněte si, že každé zařízení je přidělená k tenantovi nejblíže oblasti simulovaného zařízení služby IoT hub.

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

Pokud budete chtít pokračovat v práci s prostředky vytvořené v tomto článku, můžete je nechat. Pokud neplánujete pokračovat v používání prostředků, postupujte následovně Chcete-li odstranit všechny prostředky vytvořené podle tohoto článku, chcete-li se vyhnout zbytečným poplatkům.

Zde uvedené kroky předpokládají, všechny prostředky vytvořené v tomto článku podle pokynů ve stejné skupině prostředků s názvem **contoso-us-resource-group**.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit skupinu prostředků podle názvu:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

2. V **filtrovat podle názvu...**  textového pole zadejte název prostředku skupiny obsahující prostředky, **contoso-us-resource-group**. 

3. V seznamu výsledků klikněte na **...** napravo od vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění tím, že znovu zadáte název vaší skupiny prostředků, a pak klikněte na **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další postup

- Přečtěte si další Reprovisioning, najdete v článku [reprovisoning koncepty zařízení centra IoT](concepts-device-reprovision.md) 
- Zrušení zřízení Další informace najdete v tématu [jak zrušit zřízení zařízení, které byly dříve automatické zřizování ](how-to-unprovision-devices.md) 











