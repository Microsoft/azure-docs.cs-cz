---
title: Jak starší verze zařízení Azure IoT Hub Device Provisioning Service zřídit pomocí symetrických klíčů | Dokumentace Microsoftu
description: Jak používat symetrické klíče ke zřízení starší zařízení s vaším zařízením zřizování instance služby
author: wesmc7777
ms.author: wesmc
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51fea4fa1973fbe92242f1995d892cd5b038a29b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991636"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Jak zřídit starší zařízení pomocí symetrických klíčů


Běžný problém u mnoha starší zařízení je, že mají často identita, která se skládá z každé informace. Tato informace o identitě, je obvykle adresu MAC nebo sériové číslo. Starší verze zařízení nemusí mít certifikát, TPM nebo jakékoli jiné funkce zabezpečení, který slouží k zabezpečené identifikaci zařízení. Služby Device Provisioning pro službu IoT hub obsahuje symetrického klíče ověření identity. Symetrické klíče ověření slouží k identifikaci zařízení založených na informace, jako jsou adresy MAC nebo sériové číslo.

Pokud lze snadno nainstalovat [modulu hardwarového zabezpečení (HSM)](concepts-security.md#hardware-security-module) a certifikát, pak může být lepším řešením pro identifikaci a zřizování zařízení. Protože tento přístup vám umožní obejít aktualizaci kódu nasadit do všech zařízení a nebude nutné tajného klíče vložený do image vašeho zařízení.

Tento článek předpokládá, že modulu hardwarového zabezpečení ani certifikát je vhodným řešením. Nicméně se předpokládá, že máte některé metody aktualizace kódu zařízení má použít ke zřízení tato zařízení do služby Device Provisioning. 

Tento článek také předpokládá, že aktualizace zařízení probíhá v zabezpečeném prostředí k zabránění neoprávněnému přístupu k skupiny hlavní klíč nebo klíč odvozené zařízení.

Tento článek je orientované na pracovní stanici se systémem Windows. Můžete však provést postupy v Linuxu. Příklad pro Linux, najdete v části [jak zřídit pro víceklientskou architekturu](how-to-provision-multitenant.md).


## <a name="overview"></a>Přehled

Jedinečným registračním ID bude určena pro každé zařízení založené na informace, které identifikují zařízení. Například adresy MAC nebo sériové číslo.

Skupinu registrací, který používá [symetrického klíče ověření](concepts-symmetric-key-attestation.md) se vytvoří ve službě Device Provisioning Service. Skupiny registrací bude obsahovat skupinu hlavní klíč. Tento hlavní klíč se použije k vytvoření hodnoty hash jednotlivých jedinečným registračním ID, vytvoří jedinečný klíč pro každé zařízení. Které zařízení použije tento klíč odvozené zařízení s jedinečným registračním ID ověřit ve službě Device Provisioning Service a přiřadit do služby IoT hub.

Zařízení kódu demonstruje tento článek bude postup podobný jako [rychlý start: zřízení simulovaného zařízení pomocí symetrických klíčů](quick-create-simulated-device-symm-key.md). Kód bude simulace zařízení pomocí ukázky z [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Simulované zařízení se ověřit identitu s skupinu registrací místo jednotlivou registraci, jak je ukázáno v tomto rychlém startu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

* Dokončení [nastavení služby IoT Hub Device Provisioning pomocí webu Azure portal](./quick-setup-auto-provision.md) rychlý start.
* Visual Studio 2015 nebo [Visual Studio 2017](https://www.visualstudio.com/vs/) s povolenou sadou funkcí [Vývoj desktopových aplikací pomocí C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)
* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí Azure IoT C SDK

V této části se připravíte prostředí pro vývoj sloužící k sestavení [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

Sada SDK zahrnuje ukázkový kód pro simulované zařízení. Toto simulované zařízení se pokusí zřizování během pořadí spouštěcích zařízení.

1. Stáhněte si verzi 3.11.4 [sestavovací systém CMake](https://cmake.org/download/). Stažený binární soubor ověřte pomocí odpovídající kryptografické hodnoty hash. Následující příklad používá Windows PowerShell k ověření kryptografické hodnoty hash pro verzi 3.11.4 distribuce x64 MSI:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Následující hodnoty hash pro verzi 3.11.4 byly uvedeny na webu CMake v době psaní tohoto textu:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Je důležité, aby požadavky na sadu Visual Studio (Visual Studio a sada funkcí Vývoj desktopových aplikací pomocí C++) byly na vašem počítači nainstalované ještě **před** zahájením instalace `CMake`. Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz a naklonujte úložiště Azure IoT C SDK na Githubu:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.


3. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Spusťte následující příkaz, který je založen na verzi sady SDK, které jsou specifické pro vaše klientská platforma pro vývoj. V adresáři `cmake` se vygeneruje řešení Visual Studia pro simulované zařízení. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Vytvořit skupinu registrací symetrického klíče

1. Přihlaste se k [webu Azure portal](http://portal.azure.com)a otevřete vaší instanci služby Device Provisioning.

2. Vyberte **Správa registrací** kartu a potom klikněte na tlačítko **přidat skupinu registrací** tlačítko v horní části stránky. 

3. Na **přidat skupinu registrací**, zadejte následující informace a klikněte na tlačítko **Uložit** tlačítko.

    - **Název skupiny**: Zadejte **mylegacydevices**.

    - **Typ ověření**: vyberte **symetrický klíč**.

    - **Automaticky generovat klíče**: Zaškrtněte toto políčko.

    - **Vyberte, jak chcete přiřadit zařízení k centrům**: vyberte **statickou konfiguraci** tak můžete přiřadit konkrétní rozbočovače.

    - **Vyberte centra IoT hub je možné přiřadit tato skupina**: vyberte jednu z vašich rozbočovače.

    ![Přidat skupinu registrací pro ověření identity symetrického klíče](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Po uložení vaší registrace **primární klíč** a **sekundární klíč** se generují a přidávají do položky registrace. Zobrazí vaše skupina symetrického klíče registrace jako **mylegacydevices** pod *název skupiny* sloupec v *skupiny registrací* kartu. 

    Otevřete registraci a zkopírujte hodnotu vaše generované **primární klíč**. Tento klíč je klíč hlavní skupiny.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Zvolte jedinečným registračním ID zařízení

Musí být definován jedinečným registračním ID k identifikaci jednotlivých zařízení. Můžete použít adresu MAC, sériové číslo nebo žádné jedinečné informace ze zařízení. 

V tomto příkladu používáme kombinace adresy MAC a sériové číslo, které tvoří tento řetězec pro ID registrace.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Vytvoření a jedinečným registračním ID pro vaše zařízení. Platné znaky jsou malé alfanumerické znaky a pomlčky ("-").


## <a name="derive-a-device-key"></a>Odvodit klíč zařízení 

Vygenerujte klíč zařízení, nepoužívejte k výpočtu skupiny hlavního klíče [HMAC SHA256](https://wikipedia.org/wiki/HMAC) jedinečným registračním ID zařízení a převodu výsledků do formátu Base64.

Nezahrnují váš hlavní klíč skupiny ve vašem kódu zařízení.


#### <a name="linux-workstations"></a>Pracovní stanice pro Linux

Pokud používáte pracovní stanici systému Linux, můžete vygenerovat klíč odvozené zařízení, jak je znázorněno v následujícím příkladu openssl.

Nahraďte hodnotu **klíč** s **primární klíč** jste si předtím poznamenali.

Nahraďte hodnotu **REG_ID** s vaším ID registrace.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Pracovní stanice se systémem Windows

Pokud používáte pracovní stanici se systémem Windows, můžete použít PowerShell k vygenerování klíče odvozené zařízení jak je znázorněno v následujícím příkladu.

Nahraďte hodnotu **klíč** s **primární klíč** jste si předtím poznamenali.

Nahraďte hodnotu **REG_ID** s vaším ID registrace.

```PowerShell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```PowerShell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Vaše zařízení použije klíč odvozené zařízení své jedinečným registračním ID provádět symetrického klíče ověření identity ve skupině pro registraci během zřizování.



## <a name="create-a-device-image-to-provision"></a>Vytvoření obrázku zařízení pro zřízení

V této části budete aktualizovat ukázkou zřizování s názvem **prov\_dev\_klienta\_ukázka** nachází v sadě Azure IoT C SDK jste nastavili dříve. 

Tento ukázkový kód simuluje posloupnost spouštěcí zařízení, která odešle žádost o zřízení instance služby Device Provisioning. Pořadí spouštění způsobí, že bude používat a přiřazen do služby IoT hub, který jste nakonfigurovali ve skupině pro registraci zařízení.

1. Na webu Azure Portal vyberte okno **Přehled** vaší služby Device Provisioning Service a poznamenejte si hodnotu **_Rozsah ID_**.

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. V sadě Visual Studio, otevřete **azure_iot_sdks.sln** soubor řešení, který byl vytvořen starší CMake. Soubor řešení musí být v následujícím umístění:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. V podokně *Průzkumník řešení* sady Visual Studio přejděte do složky **Provision\_Samples**. Rozbalte ukázkový projekt s názvem **prov\_dev\_client\_sample**. Rozbalte **zdrojové soubory** a otevřete **prov\_dev\_client\_sample.c**.

4. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Ve stejném souboru vyhledejte definici funkce `main()`. Ujistěte se, `hsm_type` proměnná je nastavená na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` jak je znázorněno níže:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**. 

7. V sadě Visual Studio *Průzkumníku řešení* okno, přejděte **hsm\_zabezpečení\_klienta** projektu a rozbalte ho. Rozbalte **zdrojové soubory**a otevřete **hsm\_klienta\_key.c**. 

    Najít deklaraci `REGISTRATION_NAME` a `SYMMETRIC_KEY_VALUE` konstanty. Proveďte následující změny do souboru a soubor uložte.

    Aktualizujte hodnotu `REGISTRATION_NAME` konstanty s **jedinečným registračním ID pro vaše zařízení**.
    
    Aktualizujte hodnotu `SYMMETRIC_KEY_VALUE` konstanty s vaší **odvozený klíč zařízení**.

    ```c
    static const char* const REGISTRATION_NAME = "sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6";
    static const char* const SYMMETRIC_KEY_VALUE = "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=";
    ```

7. V nabídce sady Visual Studio vyberte **Ladit** > **Spustit bez ladění** a spusťte řešení. Po zobrazení výzvy k opětovnému sestavení projektu klikněte na **Ano** a před spuštěním projekt znovu sestavte.

    Následující výstup je příkladem simulovaného zařízení úspěšně spouští a připojením k instanci služby zřizování pro přiřazení do služby IoT hub:

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

8. Na portálu přejděte do služby IoT hub se přiřadila vaše simulované zařízení a klikněte na tlačítko **zařízení IoT** kartu. Úspěšném zřízení simulovaného k rozbočovači, jeho ID zařízení zobrazí v **zařízení IoT** okně s *stav* jako **povolené**. Možná budete muset klikněte na tlačítko **aktualizovat** tlačítko v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Záležitosti zabezpečení

Mějte na paměti, že zůstane klíč odvozené zařízení jsou součástí image, která není nejlepším postupem zabezpečení doporučené. Toto je jedním z důvodů, proč jsou zabezpečení a snadné použití kompromisy. 





## <a name="next-steps"></a>Další postup

* Přečtěte si další Reprovisioning, najdete v článku [reprovisoning koncepty zařízení centra IoT](concepts-device-reprovision.md) 
* [Rychlý start: Zřízení simulovaného zařízení pomocí symetrických klíčů](quick-create-simulated-device-symm-key.md)
* Zrušení zřízení Další informace najdete v tématu [jak zrušit zřízení zařízení, které byly dříve automatické zřizování ](how-to-unprovision-devices.md) 











