---
title: Postup pro Azure – Použití jiného modulu hardwarového zabezpečení pomocí klientské sady SDK služby Device Provisioning v Azure
description: Postup pro Azure – Použití jiného modulu hardwarového zabezpečení pomocí klientské sady SDK služby Device Provisioning v Azure
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 03/28/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: ''
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d392f4a8d935cb37b6f4cfcd69826de58b33880
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk-for-c"></a>Použití různých modulů hardwarového zabezpečení pomocí klientské sady SDK služby Device Provisioning pro C

V tomto článku se dozvíte, jak používat různé [moduly hardwarového zabezpečení (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) pomocí klientské sady SDK služby Device Provisioning pro C. Můžete použít fyzické zařízení nebo simulátor. Zřizovací služba podporuje ověřování pro dva typy mechanismů ověřování: X**.**509 a TPM (Trusted Platform Module).

## <a name="prerequisites"></a>Požadavky

Připravte vývojové prostředí podle části Příprava vývojového prostředí v průvodci [Vytvoření a zřízení simulovaného zařízení](./quick-create-simulated-device.md).

### <a name="choose-a-hardware-security-module"></a>Výběr modulu hardwarového zabezpečení

Jako výrobce zařízení musíte nejprve zvolit moduly hardwarového zabezpečení (HSM) založené na některém z podporovaných typů. V současné době [klientská sada SDK služby Device Provisioning pro C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) poskytuje podporu následujících HSM: 

- [TPM (Trusted Platform Module):](https://en.wikipedia.org/wiki/Trusted_Platform_Module) TPM představuje zavedený standard pro většinu platforem zařízení založených na Windows a také pro několik zařízení založených na Linuxu nebo Ubuntu. Jako výrobce zařízení můžete zvolit tento HSM, pokud vaše zařízení používají některý z následujících operačních systémů a hledáte zavedený standard HSM. V případě čipů TPM můžete zařízení do služby Device Provisioning registrovat pouze jednotlivě. Pro účely vývoje můžete použít simulátor TPM na svém vývojovém počítači s Windows nebo Linuxem.

- [X.509:](https://cryptography.io/en/latest/x509/) HSM založené na X.509 jsou relativně novější čipy. V Microsoft se také pracuje na čipech RIoT nebo DICE, které implementují certifikáty X.509. V případě čipů X.509 můžete registrace zařízení dávkovat na portálu. Podporují také určité operační systémy mimo Windows, jako je například embedOS. Pro účely vývoje klientská sada SDK služby Device Provisioning podporuje simulátor zařízení X.509. 

Další informace najdete v tématu [Koncepty zabezpečení služby IoT Hub Device Provisioning](concepts-security.md). 

## <a name="enable-authentication-for-supported-hsms"></a>Povolení ověřování pro podporované HSM

Než bude možné zaregistrovat fyzické zařízení nebo simulátor na webu Azure Portal, musí pro ně být povolený režim ověřování (X**.**509 nebo TPM). Nejprve přejděte do kořenové složky pro azure-iot-sdk-c. Pak spusťte uvedený příkaz v závislosti na zvoleném režimu ověřování:

### <a name="use-x509-with-simulator"></a>Použití X**.**509 se simulátorem

Součástí zřizovací služby je emulátor DICE (Device Identity Composition Engine), který generuje certifikát X**.**509 pro ověřování zařízení. Pokud chcete povolit ověřování pomocí X**.**509, spusťte následující příkaz: 

```
cmake -Ddps_auth_type=x509 ..
```

Informace o hardwaru s emulátorem DICE najdete [tady](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Použití X**.**509 s hardwarem

Zřizovací službu je možné použít s X**.**509 na jiném hardwaru. K navázání připojení je potřeba rozhraní mezi hardwarem a sadou SDK. Pokud potřebujete informace o rozhraní, obraťte se na výrobce vašeho HSM.

### <a name="use-tpm"></a>Použití TPM

Zřizovací služba se může připojit k hardwarovým čipům TPM pro Windows a Linux pomocí tokenu SAS. Pokud chcete povolit ověřování pomocí TPM, spusťte následující příkaz:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Použití TPM se simulátorem

Pokud nemáte žádné zařízení s čipy TPM, můžete pro účely vývoje v operačním systému Windows použít simulátor. Pokud chcete povolit ověřování pomocí TPM a spustit simulátor TPM, spusťte následující příkaz:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Sestavení sady SDK
Před vytvořením registrace zařízení sestavte sadu SDK.

### <a name="linux"></a>Linux
- Sestavení sady SDK v Linuxu:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Pokud chcete sestavit binární soubory pro ladění, přidejte odpovídající možnost CMake do příkazu pro generování projektu, například:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Pro sestavení sady SDK je k dispozici řada [možností konfigurace nástroje CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html). Například můžete zakázat některou z dostupných sad protokolů přidáním argumentu do příkazu CMake pro generování projektu:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Můžete také sestavit a spustit test jednotek:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Pokud chcete sestavit sadu SDK ve Windows, proveďte následující kroky a vygenerujte soubory projektu:
    - Otevřete Developer Command Prompt for VS2015.
    - Z kořenu úložiště spusťte následující příkazy CMake:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Tento příkaz sestaví knihovny x86. Pokud chcete provést sestavení pro x64, upravte argument generátoru cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Pokud vygenerování projektu proběhne úspěšně, ve složce `cmake` by se měl zobrazit soubor řešení sady Visual Studio (.sln). Sestavení sady SDK:
    - Otevřete v sadě Visual Studio soubor **cmake\azure_iot_sdks.sln** a sestavte ho, **NEBO**
    - Spusťte následující příkaz na příkazovém řádku, který jste použili k vygenerování souborů projektu:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Pokud chcete sestavit binární soubory pro ladění, použijte odpovídající argument MSBuild: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Pro sestavení sady SDK je k dispozici řada možností konfigurace nástroje CMake. Například můžete zakázat některou z dostupných sad protokolů přidáním argumentu do příkazu CMake pro generování projektu:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Můžete také sestavit a spustit test jednotek:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Knihovny, které byste měli zahrnout
- Součástí vaší sady SDK by měly být tyto knihovny:
    - Zřizovací služba: dps_http_transport, dps_client, dps_security_client
    - Zabezpečení služby IoT Hub: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Vytvoření položky registrace zařízení ve službě Device Provisioning

### <a name="tpm"></a>TPM
Pokud používáte TPM, podle pokynů v tématu [Vytvoření a zřízení simulovaného zařízení pomocí služby IoT Hub Device Provisioning](./quick-create-simulated-device.md) vytvořte položku registrace zařízení ve službě Device Provisioning a proveďte simulaci prvního spuštění.

### <a name="x509"></a>X**.**509
1. Pokud chcete zaregistrovat zařízení ve zřizovací službě, je potřeba si u každého zařízení poznamenat Ověřovací klíč a ID registrace, které se zobrazí v nástroji pro zřizování poskytovaném klientskou sadou SDK. Spusťte následující příkaz, který vytiskne kořenový certifikát webu (pro skupiny registrace) a certifikát podpisovatele (pro jednotlivé registrace):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning.
   - X**.**509 – Jednotlivá registrace: V okně s přehledem zřizovací služby vyberte **Správa registrací**. Vyberte kartu **Jednotlivé registrace** a klikněte na tlačítko **Přidat** v horní části. Jako *Mechanismus* ověření identity vyberte **X**.**509** a nahrajte certifikát podpisovatele, jak to okno vyžaduje. Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 
   - X**.**509 – Skupinová registrace: V okně s přehledem zřizovací služby vyberte **Správa registrací**. Vyberte kartu **Skupinové registrace** a klikněte na tlačítko **Přidat** v horní části. Jako *Mechanismus* ověření identity vyberte **X**.**509**, zadejte název skupiny a název certifikátu a nahrajte kořenový certifikát webu, jak to okno vyžaduje. Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

## <a name="enable-authentication-for-custom-tpm-and-x509-devices-optional"></a>Povolení ověřování pro vlastní zařízení TPM a X.509 (volitelné)

> [!NOTE]
> Tato část se vztahuje pouze na zařízení vyžadující podporu vlastní platformy nebo HSM, které se aktuálně nepodporují v klientské sadě SDK služby Device Provisioning pro C.

Nejprve je potřeba vytvořit vlastní knihovnu úložiště HSM:

1. Vytvořte knihovnu pro přístup k vašemu HSM. V tomto projektu je potřeba vytvořit statickou knihovnu, kterou bude využívat sada SDK služby Device Provisioning.

2. Implementujte do své knihovny funkce definované v následujícím souboru hlaviček: 

    - Vlastní TPM: Implementujte vlastní funkce HSM definované v části [Rozhraní HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - Vlastní X.509: Implementujte vlastní funkce HSM definované v části [Rozhraní HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Jakmile se vaše knihovna sama úspěšně sestaví, bude potřeba ji integrovat s klientskou sadou SDK služby Device Provisioning tím, že ji propojíte se svou knihovnou. :

1. Do následujícího příkazu `cmake` zadejte úložiště vlastního HSM na GitHubu, cestu ke knihovně a její název:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Otevřete řešení sady Visual Studio vytvořené nástrojem CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) a sestavte ho. 

    - Proces sestavení zkompiluje knihovnu SDK.
    - Sada SDK se pokusí propojit s vlastním HSM definovaným v příkazu `cmake`.

3. Spuštěním ukázkové aplikace prov_dev_client_ll_sample ve složce Provision_Samples (v cestě `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) ověřte správnou implementaci vašeho HSM.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Připojení ke službě IoT Hub po zřízení

Po zřízení zařízení pomocí zřizovací služby se toto rozhraní API připojí ke službě IoT Hub s využitím režimu ověřování pomocí HSM: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

