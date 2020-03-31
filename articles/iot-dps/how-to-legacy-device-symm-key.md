---
title: Zřídit starší zařízení pomocí symetrických klíčů – služba Azure IoT Hub Device Provisioning Service
description: Jak používat symetrické klíče k zřízení starších zařízení s instancí Služby DPS (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434713"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Jak zřídit starší zařízení pomocí symetrických klíčů

Běžným problémem s mnoha staršími zařízeními je, že často mají identitu, která se skládá z jedné informace. Tyto informace o identitě jsou obvykle MAC adresa nebo sériové číslo. Starší zařízení nemusí mít certifikát, čip TPM ani žádnou jinou funkci zabezpečení, kterou lze bezpečně identifikovat. Služba zřizování zařízení pro službu IoT hub obsahuje symetrické atestace klíčů. Symetrické atestace klíče lze použít k identifikaci zařízení na základě informací, jako je adresa MAC nebo sériové číslo.

Pokud můžete snadno nainstalovat [modul hardwarového zabezpečení (HSM)](concepts-security.md#hardware-security-module) a certifikát, pak to může být lepší přístup pro identifikaci a zřizování vašich zařízení. Vzhledem k tomu, že tento přístup může umožnit obejít aktualizaci kódu nasazeného do všech vašich zařízení a nebude mít tajný klíč vložený v bitové kopii zařízení.

Tento článek předpokládá, že ani hsm nebo certifikát je životaschopná možnost. Předpokládá se však, že máte nějaký způsob aktualizace kódu zařízení pro použití služby device provisioning pro zřízení těchto zařízení. 

Tento článek také předpokládá, že aktualizace zařízení probíhá v zabezpečeném prostředí, aby se zabránilo neoprávněnému přístupu ke klíči hlavní skupiny nebo odvozenému klíči zařízení.

Tento článek je orientovaný na pracovní stanici s Windows. Stejným postupem se však můžete řídit i na Linuxu. Příklad pro Linux najdete v článku o [zřizování architektury s více tenanty](how-to-provision-multitenant.md).

> [!NOTE]
> Ukázka použitá v tomto článku je napsána v c. K dispozici je také [c# zařízení zřizování symetrické klíče vzorku](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) k dispozici. Chcete-li použít tuto ukázku, stáhněte nebo klonujte úložiště [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) a postupujte podle in-line pokynů v ukázkovém kódu. Podle pokynů v tomto článku můžete vytvořit skupinu zápisu symetrických klíčů pomocí portálu a najít id obor a skupinu registrace primární a sekundární klíče potřebné ke spuštění vzorku. Můžete také vytvořit jednotlivé registrace pomocí ukázky.

## <a name="overview"></a>Přehled

Pro každé zařízení bude definováno jedinečné ID registrace na základě informací, které toto zařízení identifikují. Například adresa MAC nebo sériové číslo.

Skupina registrace, která používá [atestaci symetrického klíče,](concepts-symmetric-key-attestation.md) bude vytvořena pomocí služby Zřizování zařízení. Skupina zápisu bude obsahovat hlavní klíč skupiny. Tento hlavní klíč bude použit k hash každé jedinečné ID registrace k vytvoření jedinečného klíče zařízení pro každé zařízení. Zařízení použije tento odvozený klíč zařízení s jeho jedinečným ID registrace k potvrzení službou Device Provisioning service a bude přiřazenka službě IoT hub.

Kód zařízení znázorněný v tomto článku bude následovat stejný vzor jako [rychlý start: Zřízení simulované zařízení s symetrickými klíči](quick-create-simulated-device-symm-key.md). Kód bude simulovat zařízení pomocí ukázky z [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Simulované zařízení bude dosvědčit se skupinou registrace namísto jednotlivé registrace, jak je znázorněno v rychlém startu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Dokončení [služby Nastavení zřizování zařízení služby IoT Hub pomocí rychlého startu portálu Azure.](./quick-setup-auto-provision.md)

Následující předpoklady jsou pro vývojové prostředí systému Windows. Pro Linux nebo macOS, najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci sady SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 s povoleným temanem [pro vývoj plochy s C++.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Visual Studio 2015 a Visual Studio 2017 jsou také podporovány.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části připravíte vývojové prostředí použité k sestavení [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

Sada SDK obsahuje ukázkový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřídit během spouštěcí sekvence zařízení.

1. Stáhněte si [systém sestavení CMake](https://cmake.org/download/).

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkazy a naklonovat nejnovější verzi úložiště [GitHub Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Jako hodnotu `-b` parametru použijte značku, kterou jste našli v předchozím kroku:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Spusťte z adresáře následující příkazy: `azure-iot-sdk-c`

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Spuštěním následujícího příkazu sestavte verzi sady SDK určenou pro platformu vašeho vývojového klienta. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Vytvoření symetrické skupiny klíčů pro zápis

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete instanci služby Zřizování zařízení.

2. Vyberte kartu **Spravovat zápisy** a v horní části stránky klikněte na tlačítko **Přidat skupinu zápisů.** 

3. V **části Přidat skupinu zápisů**zadejte následující informace a klepněte na tlačítko **Uložit.**

   - **Název skupiny**: Zadejte **mylegacydevices**.

   - **Typ atestace**: Vyberte **symetrický klíč**.

   - **Automaticky vygenerovat klíče**: Toto políčko zaškrtněte.

   - **Vyberte způsob přiřazení zařízení k rozbočovačům**: Vyberte **statickou konfiguraci,** abyste ji mohli přiřadit ke konkrétnímu rozbočovači.

   - **Vyberte centra IoT, ke kterým lze tuto skupinu přiřadit:** Vyberte jeden z vašich center.

     ![Přidat skupinu zápisů pro atestaci symetrického klíče](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Po uložení registrace se vygeneruje **Primární klíč** a **Sekundární klíč** a tyto klíče se přidají do položky registrace. Vaše skupina zápisu symetrický klíč se zobrazí jako **mylegacydevices** pod název *skupiny* sloupec na kartě *skupiny zápisu.* 

    Otevřete registraci a zkopírujte hodnotu vygenerovaného **primárního klíče**. Tento klíč je hlavní klíč skupiny.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Zvolte jedinečné ID registrace pro zařízení

Pro identifikaci každého zařízení musí být definováno jedinečné ID registrace. Můžete použít mac adresu, sériové číslo nebo jakékoli jedinečné informace ze zařízení. 

V tomto příkladu používáme kombinaci adresy MAC a sériového čísla tvořícího následující řetězec pro ID registrace.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Vytvořte jedinečné ID registrace pro vaše zařízení. Platné znaky jsou malá alfanumerická a pomlčka ('-').


## <a name="derive-a-device-key"></a>Odvození klíče zařízení 

Chcete-li generovat klíč zařízení, použijte hlavní klíč skupiny k výpočtu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) jedinečného Registračního ID pro zařízení a převeďte výsledek do formátu Base64.

Do kódu zařízení nezahrnujte hlavní klíč skupiny.


#### <a name="linux-workstations"></a>Linuxové pracovní stanice

Pokud používáte pracovní stanici Linux, můžete použít openssl ke generování odvozeného klíče zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** svým Registračním ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Pracovní stanice založené na systému Windows

Pokud používáte pracovní stanici se systémem Windows, můžete pomocí prostředí PowerShell vygenerovat odvozený klíč zařízení, jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíče** **primárním klíčem,** který jste si poznamenali dříve.

Nahraďte hodnotu **REG_ID** svým Registračním ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Vaše zařízení použije odvozený klíč zařízení s jedinečným ID registrace k provedení symetrického atestace klíče se skupinou registrace během zřizování.



## <a name="create-a-device-image-to-provision"></a>Vytvoření bitové kopie zařízení, která se zřaží

V této části aktualizujete ukázku zřizování s názvem **ukázka\_klienta\_\_prov dev** umístěnou v sadě Azure IoT C SDK, kterou jste nastavili dříve. 

Tento ukázkový kód simuluje spouštěcí sekvenci zařízení, která odešle požadavek na zřízení instanci služby Device Provisioning Service. Spouštěcí sekvence způsobí, že zařízení bude rozpoznáno a přiřazeno k centru IoT, které jste nakonfigurovali ve skupině zápisů.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio otevřete soubor řešení **azure_iot_sdks.sln,** který byl vygenerován spuštěním CMake dříve. Soubor řešení by se měl nacházet v následujícím umístění:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Samples**. Rozbalte ukázkový projekt s názvem **prov\_dev\_client\_sample**. Rozbalte **zdrojové soubory** a otevřete **prov\_dev\_client\_sample.c**.

4. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Ve stejném souboru vyhledejte definici funkce `main()`. Zkontrolujte, jestli je proměnná `hsm_type` nastavená na hodnotu `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, jak je vidět dole:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Najděte volání `prov_dev_set_symmetric_key_info()` v **\_prov\_\_dev klientsample.c,** který je komentoval.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentujte volání funkce a nahraďte zástupné hodnoty (včetně úhlových závorek) jedinečným ID registrace pro vaše zařízení a odvozený klíč zařízení, který jste vygenerovali.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Uložte soubor.

7. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**. 

8. V nabídce Visual Studio vyberte **ladění** > **start bez ladění** ke spuštění řešení. Po zobrazení výzvy ke znovusestavení projektu klikněte na **Ano** a před spuštěním projekt znovu sestavte.

    Následující výstup je příkladem úspěšného spuštění simulovaného zařízení a připojení k instanci služby zřizování pro přiřazení k IoT Hubu:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Na portálu přejděte do centra IoT, ke kterým bylo přiřazeno vaše simulované zařízení, a klikněte na kartu **Zařízení IoT.** Při úspěšném zřizování simulovaného do rozbočovače se jeho ID zařízení zobrazí na okně **Zařízení IoT** s *funkcí STATUS* jako **povolenou**. Možná budete muset nahoře kliknout na tlačítko **Aktualizovat**. 

    ![Zařízení je zaregistrované u centra IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Obavy o bezpečnost

Uvědomte si, že to ponechává odvozené zařízení klíč součástí bitové kopie, což není doporučený osvědčený postup zabezpečení. To je jeden z důvodů, proč bezpečnost a snadnost použití jsou kompromisy. 





## <a name="next-steps"></a>Další kroky

* Další informace o opětovném zřízení najdete v tématu [Koncepty opětovného zřizování zařízení centra IoT Hub](concepts-device-reprovision.md) 
* [Rychlý start: Zřízení simulovaného zařízení se symetrickými klíči](quick-create-simulated-device-symm-key.md)
* Další informace o zrušení zřízení najdete v tématu [Jak odzřízení zařízení, která byla dříve automaticky zřízených](how-to-unprovision-devices.md) 











