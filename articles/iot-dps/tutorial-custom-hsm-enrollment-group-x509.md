---
title: Kurz – zřizování zařízení X. 509 do Azure IoT Hub pomocí vlastního modulu hardwarového zabezpečení (HSM)
description: Tento kurz používá skupiny registrací. V tomto kurzu se naučíte zřídit zařízení X. 509 pomocí vlastního modulu hardwarového zabezpečení (HSM) a sady SDK pro zařízení jazyka C pro Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b178aa4a524cb7fcc85c7fc68ac5f772747787a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052359"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Kurz: zřízení více zařízení X. 509 pomocí skupin registrace

V tomto kurzu se naučíte zřídit skupiny zařízení IoT, které k ověřování používají certifikáty X. 509. Ukázkový kód zařízení ze [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) se spustí na vašem vývojovém počítači, aby se simulovalo zřizování zařízení X. 509. Na skutečných zařízeních se kód zařízení nasadí a spustí ze zařízení IoT.

Před pokračováním v tomto kurzu se ujistěte, že jste aspoň dokončili kroky v části [nastavení IoT Hub Device Provisioning Service s Azure Portal](quick-setup-auto-provision.md) . Pokud nejste obeznámeni s procesem autozřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . 

Služba Azure IoT Device Provisioning podporuje dva typy registrace pro zřizování zařízení:

* [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
* [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

Tento kurz je podobný jako v předchozích kurzech, které demonstrují, jak pomocí skupin registrací zřizovat sady zařízení. V tomto kurzu se ale místo symetrických klíčů použijí certifikáty X. 509. V předchozích kurzech v této části najdete jednoduchý přístup pomocí [symetrických klíčů](./concepts-symmetric-key-attestation.md).

Tento kurz předvádí [vlastní ukázku modulu HSM](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) , která poskytuje zástupnou implementaci pro propojení s zabezpečeným úložištěm na základě hardwaru. Modul hardwarového [zabezpečení (HSM)](./concepts-service.md#hardware-security-module) se používá k zabezpečenému hardwarovému úložišti tajných kódů zařízení. Modul hardwarového zabezpečení (HSM) je možné použít s symetrickým klíčem, certifikátem X. 509 nebo ověřováním pomocí čipu TPM k zajištění zabezpečeného úložiště tajných kódů. Hardwarové úložiště tajných kódů zařízení se nevyžaduje, ale důrazně se doporučuje chránit citlivé informace, jako je privátní klíč certifikátu zařízení.


V tomto kurzu provedete následující cíle:

> [!div class="checklist"]
> * Vytvořte řetěz certifikátů s důvěryhodným pro uspořádání sady zařízení pomocí certifikátů X. 509.
> * Dokončete důkaz o vlastnictví s podpisovým certifikátem použitým v řetězu certifikátů.
> * Vytvoří registraci nové skupiny, která používá řetěz certifikátů.
> * Nastavení vývojového prostředí pro zřizování zařízení pomocí kódu ze [sady Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Zajistěte, aby zařízení používalo řetěz certifikátů s ukázkovým modulem hardwarového zabezpečení (HSM) v sadě SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Následující požadavky jsou určené pro vývojové prostředí systému Windows, které se používá k simulaci zařízení. Informace o systému Linux nebo macOS najdete v příslušné části [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) v dokumentaci k sadě SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 se zapnutou úlohou [vývoj desktopových aplikací v jazyce C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Podporují se také sady Visual Studio 2015 a Visual Studio 2017. 

    V tomto článku se používá Visual Studio k vytvoření ukázkového kódu zařízení, který se nasadí do zařízení IoT.  To neznamená, že se na samotném zařízení vyžaduje Visual Studio.

* Nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/)

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Příprava vývojového prostředí sady Azure IoT C SDK

V této části připravíte vývojové prostředí použité k sestavení [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sada SDK obsahuje ukázkový kód a nástroje, které používají zařízení X. 509 se zřizováním pomocí DPS.

1. Stáhněte si [sestavovací systém cmake](https://cmake.org/download/).

    **Před** zahájením instalace je důležité, aby byly na vašem počítači nainstalovány požadavky sady Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) a ["vývoj desktopových aplikací s C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ) `CMake` . Jakmile jsou požadované součásti k dispozici a stažený soubor je ověřený, nainstalujte sestavovací systém CMake.

2. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady Azure IoT C SDK.

3. Otevřete prostředí příkazového řádku nebo Git Bash. Spuštěním následujících příkazů naklonujte nejnovější verzi úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) . Použijte značku, kterou jste našli v předchozím kroku, jako hodnotu `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

4. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. `cmake`Adresář, který jste vytvořili, bude obsahovat vlastní ukázku HSM a ukázkový kód pro zřizování zařízení, který používá vlastní modul hardwarového zabezpečení, poskytuje ověřování X. 509. 

    Spuštěním následujícího příkazu v `cmake` adresáři sestavíte verzi sady SDK, která je specifická pro vaši vývojovou platformu. Sestavení bude obsahovat odkaz na vlastní ukázku HSM. 

    Když zadáte cestu použitou `-Dhsm_custom_lib` níže, nezapomeňte použít cestu relativní k `cmake` adresáři, který jste dříve vytvořili. Relativní cesta uvedená níže je pouze příklad.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Pokud `cmake` nenajde váš kompilátor C++, můžou se při spuštění výše uvedeného příkazu zobrazit chyby sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Po úspěšném sestavení se ve vašem adresáři vygeneruje řešení Visual Studio `cmake` . Poslední pár výstupních řádků vypadá podobně jako v následujícím výstupu:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Vytvoření řetězu certifikátů X. 509

V této části vygenerujete řetěz certifikátů X. 509 tří certifikátů pro testování jednotlivých zařízení pomocí tohoto kurzu. Certifikáty budou mít následující hierarchii.

![Kurz pro řetěz certifikátů zařízení](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Kořenový certifikát](concepts-x509-attestation.md#root-certificate): dokončete [kontrolu nad](how-to-verify-certificates.md) tím, že budete chtít ověřit kořenový certifikát. Toto ověření umožní službě DPS důvěřovat tomuto certifikátu a ověřit certifikáty podepsané tímto certifikátem.

[Zprostředkující certifikát](concepts-x509-attestation.md#intermediate-certificate): je běžné pro použití zprostředkujících certifikátů k logickému seskupení zařízení podle produktů, divizí a dalších kritérií. V tomto kurzu se použije řetěz certifikátů složený z jednoho zprostředkujícího certifikátu. Zprostředkující certifikát bude podepsán kořenovým certifikátem. Tento certifikát se taky použije ve skupině pro registraci vytvořené v DPS k logickému seskupení sady zařízení. Tato konfigurace umožňuje spravovat celou skupinu zařízení, která mají certifikáty zařízení podepsaná stejným zprostředkujícím certifikátem. Můžete vytvořit skupiny registrací a povolit nebo zakázat skupinu zařízení. Další informace o tom, jak zakázat skupinu zařízení, najdete v tématu Zakázání [certifikátu X. 509 zprostředkující nebo kořenové certifikační autority pomocí skupiny](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group) registrací.

[Certifikáty zařízení](concepts-x509-attestation.md#end-entity-leaf-certificate): certifikáty zařízení (list) budou podepsány zprostředkujícím certifikátem a budou uloženy v zařízení spolu s jeho privátním klíčem. V ideálním případě budou tyto citlivé položky bezpečně uloženy pomocí modulu hardwarového zabezpečení (HSM). Každé zařízení bude při pokusu o zřízení předložit svůj certifikát a soukromý klíč spolu s řetězem certifikátů. 

#### <a name="create-root-and-intermediate-certificates"></a>Vytvoření kořenových a zprostředkujících certifikátů

Vytvoření kořenové a zprostředkující části řetězu certifikátů:

1. Otevřete příkazový řádek Git bash. Kroky 1 a 2 proveďte pomocí pokynů prostředí bash, které se nacházejí v tématu [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    Tím se vytvoří pracovní adresář pro skripty certifikátu a vygeneruje se ukázkový kořenový a zprostředkující certifikát pro řetěz certifikátů pomocí OpenSSL. 
    
2. Všimněte si, že výstup ukazuje umístění kořenového certifikátu podepsaného svým držitelem. Tento certifikát [prochází ověřením vlastnictví a později](how-to-verify-certificates.md) se ověří vlastnictví.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. Všimněte si, že výstup ukazuje umístění zprostředkujícího certifikátu, který je podepsán nebo vydán kořenovým certifikátem. Tento certifikát se použije se skupinou pro registraci, kterou vytvoříte později.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>Vytvoření certifikátů zařízení

Vytvoření certifikátů zařízení podepsaných zprostředkujícím certifikátem v řetězu:

1. Spuštěním následujícího příkazu vytvořte nový certifikát zařízení nebo listu s názvem subjektu, který udělíte jako parametr. Použijte příklad názvu subjektu uvedeného v tomto kurzu `custom-hsm-device-01` . Tento název subjektu bude ID zařízení pro vaše zařízení IoT. 

    > [!WARNING]
    > Nepoužívejte název subjektu s mezerami. Tento název subjektu představuje ID zařízení pro zřízené zařízení IoT. Musí splňovat pravidla pro ID zařízení. Další informace najdete v tématu [Vlastnosti identity zařízení](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Všimněte si následujícího výstupu, který ukazuje, kde se nachází nový certifikát zařízení. Certifikát zařízení je podepsaný (vystavený) zprostředkujícím certifikátem.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. Spuštěním následujícího příkazu vytvořte úplný soubor PEM řetězu certifikátů, který obsahuje nový certifikát zařízení pro `custom-hsm-device-01` .

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    Použijte textový editor a otevřete soubor řetězu certifikátů, *./certs/New-Device-01-Full-Chain.CERT.pem*. Text řetězu certifikátů obsahuje úplný řetěz všech tří certifikátů. Tento text budete používat jako řetěz certifikátů v části vlastní kód zařízení HSM dále v tomto kurzu pro `custom-hsm-device-01` .

    Text celého řetězce má následující formát:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. Všimněte si, že privátní klíč nového certifikátu zařízení je zapsaný do *./Private/New-Device.Key.pem*. Přejmenujte tento soubor klíče *./Private/New-Device-01.Key.pem* pro `custom-hsm-device-01` zařízení. Během zřizování bude zařízení potřebovat text pro tento klíč. Text se přidá do vlastního příkladu HSM později.

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > Text pro certifikáty obsahuje jenom informace o veřejném klíči. 
    >
    > Zařízení ale musí mít také přístup k privátnímu klíči pro certifikát zařízení. To je nezbytné, protože zařízení musí při pokusu o zřízení provést ověření pomocí tohoto klíče za běhu. Citlivost tohoto klíče je jedním z hlavních důvodů, proč se doporučuje používat hardwarové úložiště v reálném modulu HSM k zabezpečení privátních klíčů.

4. Opakujte kroky 1-3 pro druhé zařízení s ID zařízení `custom-hsm-device-02` . Pro toto zařízení použijte následující hodnoty:

    |   Description                 |  Hodnota  |
    | :---------------------------- | :--------- |
    | Název subjektu                  | `custom-hsm-device-02` |
    | Úplný soubor řetězu certifikátů   | *./certs/new-device-02-full-chain.cert.pem* |
    | Název souboru privátního klíče          | *Private/New-Device-02. Key. pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>Ověřit vlastnictví kořenového certifikátu

1. Pomocí směrů z [registrace veřejné části certifikátu X. 509 a získání ověřovacího kódu](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)nahrajte kořenový certifikát ( `./certs/azure-iot-test-only.root.ca.cert.pem` ) a získejte ověřovací kód z DPS.

2. Jakmile budete mít ověřovací kód z DPS pro kořenový certifikát, spusťte následující příkaz z pracovního adresáře skriptu certifikátu pro vygenerování ověřovacího certifikátu.
 
    Ověřovací kód, který je zde uveden, je pouze příklad. Použijte kód, který jste vygenerovali z DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Tento skript vytvoří certifikát podepsaný kořenovým certifikátem s názvem subjektu nastaveným na ověřovací kód. Tento certifikát umožňuje DPS, abyste ověřili, že máte přístup k privátnímu klíči kořenového certifikátu. Všimněte si umístění ověřovacího certifikátu ve výstupu skriptu. Tento certifikát je vygenerovaný ve `.pfx` formátu.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Jak je uvedeno v [části nahrání podepsaného ověřovacího certifikátu](how-to-verify-certificates.md#upload-the-signed-verification-certificate), nahrajte ověřovací certifikát a kliknutím na **ověřit** v DPS dokončete důkaz o vlastnictví kořenového certifikátu.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Aktualizace úložiště certifikátů na zařízeních se systémem Windows

Na zařízeních s jiným systémem než Windows můžete řetěz certifikátů předat z kódu jako úložiště certifikátů.

V zařízeních se systémem Windows je nutné přidat podpisové certifikáty (kořenové a zprostředkující) do [úložiště certifikátů](/windows/win32/secauthn/certificate-stores)systému Windows. V opačném případě nebudou podpisové certifikáty přeneseny do DPS pomocí zabezpečeného kanálu s protokolem TLS (Transport Layer Security).

> [!TIP]
> Místo zabezpečeného kanálu (Schannel) se sadou C SDK je taky možné použít OpenSSL. Další informace o použití OpenSSL najdete v tématu [použití OpenSSL v sadě SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

Postup přidání podpisových certifikátů do úložiště certifikátů v zařízeních se systémem Windows:

1. V příkazovém řádku Git bash přejděte do `certs` podadresáře, který obsahuje vaše podpisové certifikáty, a převeďte je na následujícím `.pfx` způsobem.

    kořenový certifikát:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    zprostředkující certifikát:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Klikněte pravým tlačítkem myši na tlačítko **Start** systému Windows. Pak klikněte levým na **Spustit**. Zadejte *certmgr. MCS* a kliknutím na **OK** spusťte modul snap-in konzoly MMC Správce certifikátů.

3. V části Správce certifikátů v části **Certifikáty – aktuální uživatel** klikněte na **Důvěryhodné kořenové certifikační autority**. Pak v nabídce klikněte na **Akce**  >  **všechny úkoly**  >  **importovat** do importu `root.pfx` .

    * Ujistěte se, že vyhledáváte pomocí souboru **Personal Information Exchange (. pfx)** .
    * Použijte `1234` jako heslo.
    * Umístěte certifikát do úložiště certifikátů **Důvěryhodné kořenové certifikační autority** .

4. V části Správce certifikátů v části **Certifikáty – aktuální uživatel** klikněte na **zprostředkující certifikační autority**. Pak v nabídce klikněte na **Akce**  >  **všechny úkoly**  >  **importovat** do importu `intermediate.pfx` .

    * Ujistěte se, že vyhledáváte pomocí souboru **Personal Information Exchange (. pfx)** .
    * Použijte `1234` jako heslo.
    * Umístěte certifikát do úložiště certifikátů **zprostředkující certifikační autority** .

Podpisové certifikáty jsou teď důvěryhodné na zařízení se systémem Windows a úplný řetěz se dá přenést do DPS.



## <a name="create-an-enrollment-group"></a>Vytvoření skupiny registrace

1. Přihlaste se k Azure Portal, v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete svou službu Device Provisioning.

2. Vyberte kartu **spravovat registrace** a pak v horní části vyberte tlačítko **Přidat skupinu** registrací.

3. Na panelu **Přidat skupinu** registrací zadejte následující informace a potom stiskněte tlačítko **Uložit** .

      ![Přidání skupiny registrace pro ověření identity X. 509 na portálu](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Pole        | Hodnota           |
    | :----------- | :-------------- |
    | **Název skupiny** | V tomto kurzu zadejte **vlastní-HSM-x509-Devices** . |
    | **Typ ověření identity** | Vybrat **certifikát** |
    | **Zařízení IoT Edge** | Vybrat **hodnotu false** |
    | **Typ certifikátu** | Vybrat **zprostředkující certifikát** |
    | **Primární soubor certifikátu. pem nebo. cer** | Přejít na mezikrok, který jste vytvořili dříve (*./certs/Azure-IoT-test-Only.Intermediate.CERT.pem*) |


## <a name="configure-the-provisioning-device-code"></a>Konfigurace kódu zřizovacího zařízení

V této části aktualizujete ukázkový kód s informacemi o instanci služby Device Provisioning. Pokud je zařízení ověřené, bude přiřazeno ke službě IoT Hub propojené s instancí služby Device Provisioning konfigurovanou v této části.

1. V Azure Portal vyberte kartu **Přehled** vaší služby Device Provisioning a poznamenejte si hodnotu **_Rozsah ID_** .

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Spusťte sadu Visual Studio a otevřete nový soubor řešení, který byl vytvořen v `cmake` adresáři, který jste vytvořili v kořenovém adresáři úložiště Git Azure-IoT-SDK-c. Soubor řešení je pojmenován `azure_iot_sdks.sln` .

3. V Průzkumník řešení pro Visual Studio přejděte do **Provisioning_Samples > prov_dev_client_sample > zdrojové soubory** a otevřete *prov_dev_client_sample. c*.

4. Najděte konstantu `id_scope` a nahraďte její hodnotu hodnotou **Rozsah ID**, kterou jste si zkopírovali. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. Ve stejném souboru vyhledejte definici funkce `main()`. Ujistěte se, že `hsm_type` je proměnná nastavena na hodnotu `SECURE_DEVICE_TYPE_X509` , jak je uvedeno níže.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Klikněte pravým tlačítkem na projekt **prov\_dev\_client\_sample** a vyberte **Nastavit jako spouštěný projekt**.


## <a name="configure-the-custom-hsm-stub-code"></a>Konfigurace kódu vlastní zástupné procedury HSM

V závislosti na hardwaru se liší specifika interakce s aktuálním zabezpečeným úložištěm založeným na hardwaru. V důsledku toho se řetězy certifikátů používané simulovanými zařízeními v tomto kurzu pevně zakódované ve vlastním kódu stub pro vlastní modul HSM. Ve scénáři reálného světa by byl řetěz certifikátů uložený ve skutečném hardwaru HSM, aby bylo zajištěno lepší zabezpečení citlivých informací. Metody podobné zástupným procedurám používaným v této ukázce by pak byly implementovány pro čtení tajných kódů z daného hardwarového úložiště. 

I když není potřebný hardware HSM, doporučuje se chránit citlivé informace, jako je privátní klíč certifikátu. Pokud se v ukázce volá skutečný modul HSM, privátní klíč se ve zdrojovém kódu nevyskytuje. Klíč ve zdrojovém kódu zpřístupňuje klíč každému, kdo může zobrazit kód. To se provádí jenom v tomto článku, abyste mohli pomoct s učením.

Pokud chcete aktualizovat vlastní kód pro zástupné procedury HSM, aby se simulovala identita zařízení s ID `custom-hsm-device-01` , proveďte následující kroky:

1. V Průzkumník řešení pro Visual Studio přejděte do **Provisioning_Samples > custom_hsm_example > zdrojové soubory** a otevřete *custom_hsm_example. c*.

2. Aktualizujte hodnotu řetězce `COMMON_NAME` řetězcové konstanty pomocí běžného názvu, který jste použili při generování certifikátu zařízení.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. Ve stejném souboru musíte aktualizovat řetězcovou hodnotu `CERTIFICATE` konstantního řetězce pomocí textu řetězu certifikátů, který jste uložili v souboru *./certs/New-Device-01-Full-Chain.CERT.pem* po vygenerování certifikátů.

    Syntaxe textu certifikátu musí následovat po vzorci, který neobsahuje nadbytečné mezery ani se neanalyzuje v rámci sady Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Správná aktualizace této řetězcové hodnoty v tomto kroku může být velmi zdlouhavá a může se jednat o chybu. Pokud chcete vygenerovat správnou syntaxi na příkazovém řádku Git bash, zkopírujte a vložte následující příkazy prostředí bash do příkazového řádku Git bash a stiskněte klávesu **ENTER**. Tyto příkazy vygenerují syntaxi `CERTIFICATE` hodnoty řetězcové konstanty.

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Zkopírujte a vložte text výstupního certifikátu pro novou konstantní hodnotu. 


4. Ve stejném souboru musí být řetězcová hodnota `PRIVATE_KEY` konstanty také aktualizována pomocí privátního klíče pro certifikát zařízení.

    Syntaxe textu privátního klíče musí následovat po vzorci, který neobsahuje nadbytečné mezery ani se neanalyzuje v rámci sady Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Správná aktualizace této řetězcové hodnoty v tomto kroku může být také velmi zdlouhavá a může se jednat o chybu. Pokud chcete vygenerovat správnou syntaxi na příkazovém řádku Git bash, zkopírujte a vložte následující příkazy prostředí bash a stiskněte klávesu **ENTER**. Tyto příkazy vygenerují syntaxi `PRIVATE_KEY` hodnoty řetězcové konstanty.

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Zkopírujte a vložte výstupní text privátního klíče pro novou konstantní hodnotu. 

5. Uložte *custom_hsm_example. c*.

6. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** a spusťte řešení. Po zobrazení výzvy k opětovnému sestavení projektu vyberte **Ano** pro opětovné sestavení projektu před spuštěním.

    Následující výstup je příkladem simulovaného zařízení, které se `custom-hsm-device-01` úspěšně spouští a připojuje se ke službě zřizování. Zařízení se přiřadilo ke službě IoT Hub a zaregistrovalo se:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a vyberte kartu **zařízení IoT** . Po úspěšném zřízení zařízení X. 509 do centra se jeho ID zařízení zobrazí v okně **zařízení IoT** se *stavem* **povoleno**. Možná budete muset stisknout tlačítko **aktualizovat** v horní části. 

    ![Vlastní zařízení HSM je zaregistrované ve službě IoT Hub.](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. Opakujte kroky 1-7 pro druhé zařízení s ID zařízení `custom-hsm-device-02` . Pro toto zařízení použijte následující hodnoty:

    |   Description                 |  Hodnota  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | Úplný řetěz certifikátů        | Vygenerujte text pomocí `input="./certs/new-device-02-full-chain.cert.pem"` |
    | Privátní klíč                   | Vygenerujte text pomocí `input="./private/new-device-02.key.pem"` |

    Následující výstup je příkladem simulovaného zařízení, které se `custom-hsm-device-02` úspěšně spouští a připojuje se ke službě zřizování. Zařízení se přiřadilo ke službě IoT Hub a zaregistrovalo se:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Až dokončíte testování a prozkoumáte ukázku klienta tohoto zařízení, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v tomto kurzu.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete **spravovat registrace** pro vaši službu a pak vyberte kartu **skupiny** registrací. Zaškrtněte políčko vedle *názvu skupiny* zařízení, kterou jste vytvořili v tomto kurzu, a potom v horní části podokna klikněte na tlačítko **Odstranit** . 
1. Klikněte na **certifikáty** v DPS. Pro každý certifikát, který jste nahráli a ověřili v tomto kurzu, klikněte na certifikát a kliknutím na tlačítko **Odstranit** ho odeberte.
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete **zařízení IoT** pro vaše centrum. Zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v tomto kurzu. Klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zřídili zařízení X. 509 pomocí vlastního modulu HARDWAROVÉho zabezpečení ve službě IoT Hub. Další informace o tom, jak zřídit zařízení IoT pro více Center, najdete v dalším kurzu. 

> [!div class="nextstepaction"]
> [Kurz: zřízení zařízení napříč centrem IoT s vyrovnáváním zatížení](tutorial-provision-multiple-hubs.md)