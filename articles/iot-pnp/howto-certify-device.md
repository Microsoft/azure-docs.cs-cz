---
title: Jak certifikovat technologie Plug and Play zařízení IoT | Microsoft Docs
description: Jako tvůrce zařízení se naučíte spouštět testy a odesílat zařízení k certifikaci.
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6aa4273933190ccfe495bcaf243ee15a5ce823fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577641"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Certifikace zařízení IoT Plug and Play

Program certifikace zařízení IoT Plug and Play zahrnuje nástroje umožňující zkontrolovat, jestli zařízení splňuje požadavky na certifikaci IoT Plug and Play. Nástroje také umožňují organizacím zajistit povědomí o dostupnosti jejich zařízení technologie Plug and Play IoT. Tato certifikovaná zařízení jsou přizpůsobená pro řešení IoT a umožňují zkrátit dobu uvedení na trh.

V tomto článku se dozvíte, jak:

- Instalace rozšíření nástroje příkazového řádku Azure IoT pro Azure CLI
- Spuštění testů technologie Plug and Play IoT k ověření aplikace zařízení ve fázi vývoje  
- K ověření aplikace zařízení použijte portál zařízení s certifikací Azure.

## <a name="prepare-your-device"></a>Příprava zařízení

Kód aplikace, který běží na vašem technologie Plug and Play IoT, musí:

- Připojte se k Azure IoT Hub pomocí [služby Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).
- Použijte [konvence pro technologii IoT Plug and Play](concepts-convention.md) k implementaci telemetrie, vlastností a příkazů.

Aplikace je software, který se instaluje samostatně z operačního systému, nebo je zahrnutý do balíčku s operačním systémem v imagi firmwaru, který je na zařízení spuštěný.

Proces certifikace ověřuje, zda je zařízení v IoT technologie Plug and Play kompatibilní, tím, že ověřuje, že implementace zařízení odpovídá telemetrie, vlastnostem a příkazům definovaným v modelu zařízení [DTDL (Digital revalids Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) a že je model dostupný v [úložišti veřejného modelu Azure IoT](concepts-model-repository.md).

Abyste splnili požadavky na certifikaci, vaše zařízení musí:

- Připojí se k Azure IoT Hub pomocí [DPS](../iot-dps/about-iot-dps.md).
- Implementací telemetrie, vlastností nebo příkazů podle konvence technologie Plug and Play IoT.
- Popište interakce zařízení s modelem [DTDL v2](https://aka.ms/dtdl) .
- Publikování modelu a všech požadovaných rozhraní v [úložišti veřejného modelu Azure IoT](https://devicemodels.azureiotsolutions.com/)
- Odešlete ID modelu během [registrace DPS](concepts-developer-guide-device-csharp.md#dps-payload) v datové části zřizování DPS.
- Během [připojení MQTT](concepts-developer-guide-device-csharp.md#model-id-announcement)oznamuje ID modelu.

## <a name="test-with-the-azure-iot-extension-cli"></a>Testování s využitím rozhraní příkazového řádku pro rozšíření Azure CLI

[Rozšíření Azure IoT CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest&preserve-view=true) umožňuje ověřit, že implementace zařízení odpovídá modelu předtím, než odešlete zařízení pro certifikaci prostřednictvím portálu zařízení s certifikací Azure.

Následující kroky ukazují, jak připravit a spustit ověřovací testy pomocí rozhraní příkazového řádku:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Instalace rozšíření Azure IoT pro rozhraní příkazového řádku Azure

Podívejte se na pokyny k instalaci a nastavte [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) ve vašem prostředí.

Pokud chcete nainstalovat rozšíření Azure IoT, spusťte následující příkaz:

```azurecli
az extension add --name azure-iot
```

Další informace najdete v tématu [Azure CLI pro Azure IoT](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest&preserve-view=true).

### <a name="create-a-new-product-test"></a>Vytvořit nový test produktu

Následující příkaz vytvoří test pomocí DPS s metodou ověření identity symetrického klíče:

- Vytvoří nový produkt, který se má otestovat, a vygeneruje konfiguraci testu. Výstup zobrazuje informace DPS, které zařízení musí používat ke zřizování: primární klíč, ID zařízení a obor ID.
- Určuje složku se soubory DTDL, které popisují váš model.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Když použijete rozhraní příkazového řádku, musíte se [Přihlásit](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) ke svému předplatnému.

Výstup JSON z příkazu obsahuje `primaryKey` , `registrationId` a, `scopeID` který se použije při připojení zařízení.

Očekávaný výstup:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Připojení zařízení

Pomocí předchozího příkazu pro připojení zařízení k instanci testovacího IoT Hub použijte výstup informací DPS.

### <a name="manage-and-configure-the-product-tests"></a>Správa a konfigurace testů produktů

Když je zařízení připojené a připravené k interakci se službou IoT Hub, vygenerujte soubor konfigurace testu produktu. Postup vytvoření souboru:

- Použijte test `id` z výstupu předchozího příkazu.
- `--wait`K získání testovacího případu použijte parametr.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Očekávaný výstup:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

Můžete použít `az iot product test case update` příkaz pro úpravu konfiguračního souboru testu.

### <a name="run-the-tests"></a>Spuštění testů

Po vygenerování konfigurace testu je dalším krokem spuštění testů. `devicetestId`Pro spuštění testů použijte stejný z předchozích příkazů jako parametr. Zkontrolujte výsledky testu a ujistěte se, že všechny testy mají stav `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Příklad výstupu testovacího běhu

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testování s využitím portálu Microsoft Azure Certified for IoT

Následující kroky ukazují, jak používat [portál zařízení s certifikací Azure](https://aka.ms/acdp) ke zprovoznění, registraci podrobností produktů, odeslání příručky Začínáme a spuštění testů certifikace.

### <a name="onboarding"></a>Onboarding

Pokud chcete používat [certifikační portál](https://aka.ms/acdp), musíte použít Azure Active Directory ze svého pracovního nebo školního tenanta.

Pokud chcete publikovat modely do úložiště veřejného modelu Azure IoT, váš účet musí být členem [Microsoft Partner Network](https://partner.microsoft.com). Systém kontroluje, zda ID Microsoft Partner Network existuje a účet je plně prověřené před publikováním do katalogu zařízení.

### <a name="company-profile"></a>Profil společnosti

Svůj profil společnosti můžete spravovat z levé navigační nabídky. Profil společnosti zahrnuje adresu URL, e-mailovou adresu a logo společnosti. Smlouvu o programu je třeba přijmout na této stránce před spuštěním jakýchkoli operací certifikace.

Informace o profilu společnosti se používají v popisu zařízení, které se v katalogu zařízení prezentuje.

### <a name="create-new-project"></a>Vytvoření nového projektu

Chcete-li certifikovat zařízení, je nutné nejprve vytvořit nový projekt.

Přejděte na [portál pro certifikaci](https://aka.ms/acdp). Na stránce **projekty** vyberte *+ vytvořit nový projekt*. Pak zadejte název projektu, název zařízení a vyberte třídu zařízení.

Informace o produktu, které poskytnete během procesu certifikace, spadají do čtyř kategorií:

- Informace o zařízení. Shromažďuje informace o zařízení, jako je název, popis, certifikace a operační systém.
- Příručka **Začínáme** . Před publikováním zařízení je třeba odeslat průvodce jako dokument PDF, který bude schválen správcem systému.
- Podrobnosti o marketingu. Poskytněte marketingové informace připravené pro zákazníky k vašemu zařízení. Informace o marketingu zahrnují popis, fotografii a distributory.
- Další oborové certifikace. V této volitelné části můžete zadat další informace o všech dalších certifikátech, které zařízení získalo.

### <a name="connect-and-test"></a>Připojit a otestovat

Krok připojit a test zkontroluje, jestli zařízení splňuje požadavky na certifikaci technologie Plug and Play IoT.

Existují tři kroky, které je třeba provést:

1. Připojte a vyhledejte rozhraní. Zařízení se musí připojit k certifikační službě Azure IoT prostřednictvím DPS. Vyberte metodu ověřování (X. 509 certifikát, symetrický klíč nebo modul Trusted Platform Module), abyste mohli používat a aktualizovat aplikaci pro zařízení pomocí informací DPS.
1. Zkontrolujte rozhraní. Zkontrolujte rozhraní a ujistěte se, že každý z nich má vstupy datové části, které jsou vhodné pro testování.
1. Napaden. Systém testuje jednotlivé modely zařízení, aby zkontroloval, jestli telemetrie, vlastnosti a příkazy popsané v modelu dodržují konvence technologie Plug and Play IoT. Po dokončení testu vyberte odkaz **Zobrazit protokoly** a zobrazte telemetrii ze zařízení a nezpracovaná data odesílaná do IoT Hub vlastností zařízení.

### <a name="submit-and-publish"></a>Odeslat a publikovat

Poslední požadovaná fáze je odeslat projekt ke kontrole. Tento krok oznamuje členovi týmu zařízení Certified v Azure, aby zkontroloval úplnost projektu, včetně podrobností o zařízení a marketingu a úvodní příručce. Člen týmu vás může kontaktovat na e-mailové adrese společnosti, která byla dříve k dispozici s dotazy nebo upravit žádosti před schválením.

Pokud vaše zařízení vyžaduje další ruční ověření v rámci certifikace, obdržíte v tomto okamžiku oznámení.

Když je zařízení certifikováno, můžete se rozhodnout publikovat podrobnosti o produktu v katalogu zařízení s certifikací Azure pomocí funkce **publikovat do katalogu** na stránce souhrnu produktu.

## <a name="next-steps"></a>Další kroky

Když se teď odeslání zařízení dokončí, můžete se obrátit na tým certifikace zařízení v, [iotcert@microsoft.com](mailto:iotcert@microsoft.com) abyste pokračovali v dalších krocích, které zahrnují ověření členství Microsoft Partner Network a kontrolu Úvodní příručky. Pokud jsou všechny požadavky splněné, můžete si vybrat, že vaše zařízení je součástí [katalogu zařízení Certified for Azure IoT](https://aka.ms/devicecatalog).
