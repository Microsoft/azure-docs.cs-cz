---
title: Připojení zařízení v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 5f9f8be81c5b90ff5e7172b2aba41a108afc64bd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126837"
---
# <a name="get-connected-to-azure-iot-central"></a>Připojení ke službě Azure IoT Central

*Tento článek se týká operátorů a vývojářů zařízení.*

Tento článek popisuje možnosti připojení vašich zařízení k aplikaci Azure IoT Central.

Obvykle musíte zařízení v aplikaci zaregistrovat předtím, než se bude moci připojit. IoT Central ale podporují scénáře, kdy [se zařízení můžou připojit bez prvotní registrace](#connect-without-registering-devices).

IoT Central používá ke správě procesu připojení [službu Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) . Zařízení se nejdřív připojí ke koncovému bodu DPS, aby načetlo informace, které potřebuje pro připojení k vaší aplikaci. Interně používá vaše aplikace IoT Central službu IoT Hub ke zpracování připojení zařízení. Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Aby bylo možné zabezpečit komunikaci mezi zařízením a vaší aplikací, IoT Central podporuje jak certifikáty sdílených přístupových podpisů (SAS), tak certifikáty X. 509. V produkčním prostředí se doporučují certifikáty X. 509.

Tento článek popisuje následující případy použití:

- [Připojení jednoho zařízení pomocí SAS](#connect-a-single-device)
- [Připojení zařízení ve velkém měřítku pomocí SAS](#connect-devices-at-scale-using-sas)
- [Připojení zařízení ve velkém měřítku pomocí certifikátů X. 509](#connect-devices-using-x509-certificates) – doporučený postup pro produkční prostředí.
- [Připojit zařízení bez jejich prvotní registrace](#connect-without-registering-devices)
- [Připojit zařízení, která používají DPS pro jednotlivé registrace](#individual-enrollment-based-device-connectivity)
- [Automatické přidružení zařízení k šabloně zařízení](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Připojení jednoho zařízení

Tento přístup je užitečný při experimentování s IoT Central nebo testováním zařízení. K připojení zařízení k aplikaci IoT Central můžete použít klíče SAS pro připojení zařízení z vaší aplikace IoT Central. Zkopírujte _klíč SAS zařízení_ z informací o připojení pro registrované zařízení:

![Klíče SAS pro jednotlivá zařízení](./media/concepts-get-connected/single-device-sas.png)

Další informace najdete v kurzu [Vytvoření a připojení klientské aplikace Node.js k vaší aplikaci Azure IoT Central](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Připojení zařízení ve velkém měřítku pomocí SAS

Pokud chcete zařízení připojit k IoT Central škálování pomocí klíčů SAS, musíte se zaregistrovat a pak nastavit zařízení:

### <a name="register-devices-in-bulk"></a>Hromadně registrovat zařízení

Pokud chcete v aplikaci IoT Central zaregistrovat velký počet zařízení, použijte k [importu ID zařízení a názvů zařízení](howto-manage-devices.md#import-devices)soubor CSV.

Chcete-li načíst informace o připojení pro importovaná zařízení, [exportujte soubor CSV z aplikace IoT Central](howto-manage-devices.md#export-devices). Exportovaný soubor CSV obsahuje ID zařízení a klíče SAS.

### <a name="set-up-your-devices"></a>Nastavení zařízení

Pomocí informací o připojení ze souboru exportu v kódu zařízení můžete zařízením umožnit připojení a posílání dat do aplikace IoT Central. Pro vaši aplikaci budete taky potřebovat **obor ID** DPS. Tuto hodnotu najdete ve **správě > připojení zařízení**.

> [!NOTE]
> Pokud se chcete dozvědět, jak můžete zařízení připojit, aniž byste je nejdřív zaregistrovali v IoT Central, přečtěte si téma [připojit bez první registrace zařízení](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Připojení zařízení pomocí certifikátů X. 509

V produkčním prostředí je použití certifikátů X. 509 doporučeným mechanismem ověřování zařízení pro IoT Central. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](../../iot-hub/iot-hub-x509ca-overview.md).

Připojení zařízení s certifikátem X. 509 do vaší aplikace:

1. Vytvořte *skupinu registrace* , která používá typ ověření identity **(X. 509)** .
2. Přidejte a ověřte zprostředkující nebo kořenový certifikát X. 509 ve skupině pro registraci.
3. Zaregistrujte a připojte zařízení, která používají listové certifikáty X. 509 vygenerované z kořenového nebo zprostředkujícího certifikátu ve skupině pro registraci.

### <a name="create-an-enrollment-group"></a>Vytvoření skupiny registrace

[Skupina registrace](../../iot-dps/concepts-service.md#enrollment) je skupina zařízení, která sdílejí stejný typ ověření identity. Tyto dva podporované typy ověření identity jsou certifikáty X. 509 a SAS:

- V rámci skupiny pro registraci X. 509 se všechna zařízení, která se připojují k IoT Central, používají listy, které jsou vygenerované z kořenového nebo zprostředkujícího certifikátu v rámci skupiny registrací.
- V rámci skupiny registrací SAS se všechna zařízení, která se připojují k IoT Central používají token SAS generovaný z tokenu SAS ve skupině pro registraci.

Dvě výchozí skupiny registrací v každé aplikaci IoT Central jsou skupiny registrací SAS – jeden pro zařízení IoT a jeden pro Azure IoT Edge zařízení. Pokud chcete vytvořit skupinu registrace X. 509, přejděte na stránku **připojení zařízení** a vyberte **+ Přidat skupinu**registrací:

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="Přidat snímek skupiny registrace X. 509":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>Přidání a ověření kořenového nebo zprostředkujícího certifikátu X. 509

Postup přidání a ověření kořenového nebo zprostředkujícího certifikátu do skupiny registrací:

1. Přejděte do skupiny registrace X. 509, kterou jste právě vytvořili. Máte možnost Přidat jak primární, tak sekundární certifikáty X. 509. Vyberte **+ Spravovat primární**.

1. Na **stránce primární certifikát**Nahrajte svůj primární certifikát X. 509. Toto je váš kořenový nebo zprostředkující certifikát:

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="Přidat snímek skupiny registrace X. 509":::

1. Kliknutím na možnost **Generovat ověřovací kód** vygenerujte kód pro nástroj, který používáte k vygenerování ověřovacího certifikátu. Pak vyberte **ověřit** a odešlete ověřovací certifikát.

1. Po úspěšném ověření se zobrazí následující potvrzení:

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="Přidat snímek skupiny registrace X. 509":::

Ověření vlastnictví certifikátu zajišťuje, že uživatel, který certifikát nahrává, má privátní klíč certifikátu.

Pokud dojde k narušení zabezpečení nebo pokud je váš primární certifikát nastavený na vypršení platnosti, použijte sekundární certifikát k omezení výpadků. Během aktualizace primárního certifikátu můžete nadále zřizovat zařízení pomocí sekundárního certifikátu.

### <a name="register-and-connect-devices"></a>Registrace a připojení zařízení

Pokud chcete zařízení hromadně propojit pomocí certifikátů X. 509, nejdřív zařízení zaregistrujte do aplikace pomocí souboru CSV [a importujte identifikátory zařízení a názvy zařízení](howto-manage-devices.md#import-devices). Všechna ID zařízení by měla být malá.

Pomocí kořenového nebo zprostředkujícího certifikátu, který jste nahráli do vaší skupiny registrací X. 509, vygenerujte na svých zařízeních hlavní certifikáty X. 509. Jako **Device ID** `CNAME` hodnotu v listových certifikátech použijte ID zařízení. Váš kód zařízení potřebuje hodnotu **rozsahu ID** vaší aplikace, **ID zařízení**a odpovídající certifikát zařízení.

#### <a name="sample-device-code"></a>Ukázkový kód zařízení

Následující ukázka v [sadě Azure IoT Node.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) ukazuje, jakým způsobem klient Node.js zařízení používá k registraci v aplikaci IoT Central listový certifikát X. 509 a DPS:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Ekvivalentní ukázku jazyka C najdete v tématu [prov_dev_client_sample. C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) v [sadě SDK pro zřizování zařízení Azure IoT c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Pouze pro účely testování

Pro účely testování můžete pomocí následujících nástrojů generovat certifikáty root, zprostředkujícího a zařízení:

- [Nástroje pro sadu SDK zařízení pro zřizování zařízení Azure IoT](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): kolekce Node.jsch nástrojů, které můžete použít ke generování a ověření certifikátů a klíčů X. 509.
- Pokud používáte zařízení DevKit, tento [Nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) vygeneruje certifikát certifikační autority, který můžete přidat do vaší aplikace IoT Central pro ověření certifikátů.
- [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): kolekce skriptů PowerShellu a bash pro:
  - Vytvořte řetěz certifikátů.
  - Uložte certifikáty jako soubory. cer a nahrajte je do vaší aplikace IoT Central.
  - K vygenerování ověřovacího certifikátu použijte ověřovací kód z aplikace IoT Central.
  - Vytvořte listové certifikáty pro vaše zařízení pomocí ID zařízení jako parametru nástroje.

## <a name="connect-without-registering-devices"></a>Připojit bez registrace zařízení

Dříve popsané scénáře vyžadují, abyste před připojením zaregistrovali zařízení v aplikaci. IoT Central taky umožňuje výrobcům OEM hromadně vyrábět zařízení, která se můžou připojit bez prvotní registrace. Výrobce OEM vygeneruje vhodné přihlašovací údaje pro zařízení a nakonfiguruje zařízení v továrně. Když zákazník zařízení poprvé zapne, připojí se k DPS, který pak zařízení automaticky připojí ke správné aplikaci IoT Central. Operátor IoT Central musí schválit zařízení před tím, než začne odesílat data do aplikace.

Tok se mírně liší v závislosti na tom, jestli zařízení používají tokeny SAS nebo certifikáty X. 509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Připojení zařízení, která používají tokeny SAS bez registrace

1. Zkopírujte primární klíč skupiny ze skupiny pro zápis **SAS-IoT-Device** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Přidat snímek skupiny registrace X. 509":::

1. Pomocí `az iot central device compute-device-key` příkazu vygenerujte klíče SAS zařízení. Použijte primární klíč skupiny z předchozího kroku. ID zařízení musí být malá písmena:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Výrobce OEM pokaždé zařízení pokaždé, když má ID zařízení, vygenerovanou klíč SAS zařízení a hodnotu **rozsahu ID** aplikace.

1. Když přepnete zařízení do zařízení, nejprve se připojí k DPS, aby se načetly informace o registraci IoT Central.

    Zařízení zpočátku má na stránce **zařízení** **nepřiřazený** stav zařízení a není přiřazený k šabloně zařízení. Na stránce **zařízení** **migrujte** zařízení do příslušné šablony zařízení. Zřizování zařízení je teď hotové, stav zařízení se teď **zřídí**a zařízení může začít odesílat data.

    Na stránce **> pro správu připojení zařízení** , možnost **automatického schvalování** určuje, jestli je potřeba zařízení před zahájením odesílání dat ručně schválit.

    > [!NOTE]
    > Informace o tom, jak automaticky přidružit zařízení k šabloně zařízení, najdete v tématu [automatické přidružení zařízení k šabloně zařízení](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Připojení zařízení, která používají certifikáty X. 509 bez registrace

1. [Vytvořte skupinu](#create-an-enrollment-group) registrací a potom do aplikace IoT Central [přidejte a ověřte kořenový nebo zprostředkující certifikát X. 509](#add-and-verify-a-root-or-intermediate-x509-certificate) .

1. Vygenerujte list – certifikáty pro vaše zařízení pomocí kořenového nebo zprostředkujícího certifikátu, který jste přidali do aplikace IoT Central. Použijte ID zařízení malými písmeny jako `CNAME` v listových certifikátech.

1. Výrobce OEM pokaždé zařízení pokaždé, když má ID zařízení, vygenerovaný listový certifikát X. 509 a hodnotu **rozsahu ID** aplikace.

1. Když přepnete zařízení do zařízení, nejprve se připojí k DPS, aby se načetly informace o registraci IoT Central.

    Zařízení zpočátku má na stránce **zařízení** **nepřiřazený** stav zařízení a není přiřazený k šabloně zařízení. Na stránce **zařízení** **migrujte** zařízení do příslušné šablony zařízení. Zřizování zařízení je teď hotové, stav zařízení se teď **zřídí**a zařízení může začít odesílat data.

    Na stránce **> pro správu připojení zařízení** , možnost **automatického schvalování** určuje, jestli je potřeba zařízení před zahájením odesílání dat ručně schválit.

    > [!NOTE]
    > Informace o tom, jak automaticky přidružit zařízení k šabloně zařízení, najdete v tématu [automatické přidružení zařízení k šabloně zařízení](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Jednotlivá připojení zařízení založená na registraci

Pro zákazníky připojující zařízení, která mají vlastní ověřovací přihlašovací údaje, použijte jednotlivé registrace. Jednotlivá registrace je záznam pro jedno zařízení, které se smí připojit. Jednotlivé registrace můžou jako mechanismus ověřování použít buď certifikáty na listech X. 509, nebo tokeny SAS (z modulu fyzického nebo virtuální důvěryhodné platformy). ID zařízení (označované také jako ID registrace) v individuální registraci je alfanumerické a malé písmeno a může obsahovat spojovníky. Další informace najdete v tématu [DPS – jednotlivý zápis](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Když vytvoříte jednotlivou registraci zařízení, bude mít přednost před výchozími možnostmi registrace skupin ve vaší aplikaci IoT Central.

### <a name="create-individual-enrollments"></a>Vytvořit jednotlivé registrace

IoT Central podporuje následující mechanismy ověřování identity pro jednotlivé registrace:

- **Ověření identity symetrického klíče:** Symetrický ověření identity je jednoduchý přístup k ověřování zařízení s instancí DPS. Pokud chcete vytvořit jednotlivou registraci s použitím symetrických klíčů, otevřete stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus zadejte **sdílený přístupový podpis (SAS)** . Zadejte primární a sekundární klíč kódovaný v kódování Base64 a uložte provedené změny. Připojte zařízení pomocí **oboru ID**, **ID zařízení**a primárního nebo sekundárního klíče.

    > [!TIP]
    > Pro účely testování můžete použít **OpenSSL** k vygenerování klíčů kódovaných v kódování Base64: `openssl rand -base64 64`

- **Certifikáty X. 509:** Pokud chcete vytvořit jednotlivou registraci pomocí certifikátů X. 509, otevřete stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus zvolte **certifikáty (X. 509)** . Certifikáty zařízení používané s jednotlivou položkou registrace mají požadavek, aby byl Vydavatel a předmět CN nastaven na ID zařízení.

    > [!TIP]
    > Pro účely testování můžete použít [Nástroje pro sadu SDK zařízení pro zřizování zařízení Azure IoT pro Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) k vygenerování certifikátu podepsaného svým držitelem: `node create_test_cert.js device "mytestdevice"`

- **Ověření identity čipu TPM (Trusted Platform Module):** [Čip TPM](../../iot-dps/concepts-tpm-attestation.md) je typ modulu hardwarového zabezpečení. Používání čipu TPM je jedním z nejbezpečnější způsobů, jak připojit zařízení. V tomto článku se předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Software emulující čipy TPM je vhodný pro vytváření prototypů nebo testování, ale neposkytuje stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované čipy TPM. Nepoužívejte software čipy TPM v produkčním prostředí. Pokud chcete vytvořit jednotlivou registraci, která používá čip TPM, otevřete stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus vytvořte **TPM** . Zadejte ověřovací klíč čipu TPM a uložte informace o připojení zařízení.

## <a name="automatically-associate-with-a-device-template"></a>Automaticky přidružit k šabloně zařízení

Jednou z klíčových funkcí IoT Central je možnost přidružit šablony zařízení automaticky k připojení zařízení. Spolu s přihlašovacími údaji zařízení můžou zařízení odeslat **CapabilityModelId** jako součást volání registrace zařízení. **CapabilityModelID** je název URN, který identifikuje model schopností, který zařízení implementuje. Aplikace IoT Central může použít **CapabilityModelID** k identifikaci šablony zařízení, která se má použít, a pak automaticky přiřadí zařízení k šabloně zařízení. Proces zjišťování funguje takto:

1. Pokud je šablona zařízení již publikována v aplikaci IoT Central, je zařízení přidruženo k šabloně zařízení.
1. V případě zařízení, která jsou předem certifikována technologie Plug and Play, se šablona zařízení načte z veřejného úložiště, pokud už není publikovaná v IoT Central aplikaci.

Následující fragmenty kódu ukazují formát další datové části, kterou musí zařízení poslat během výzvy k registraci DPS, aby se automatické přidružení fungovalo.

Jedná se o formát zařízení, která používají všeobecně dostupnou sadu SDK pro zařízení:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Jedná se o formát zařízení pomocí sady SDK pro zařízení s veřejnou sadou Preview:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Možnost **automatického schvalování** na stránce **Správa > připojení zařízení** musí být povolená, aby se zařízení automaticky připojovala, zjistila šablonu zařízení a začali odesílat data.

## <a name="device-status-values"></a>Hodnoty stavu zařízení

Když se reálné zařízení připojí k vaší aplikaci IoT Central, změní se stav zařízení takto:

1. Stav zařízení se **zaregistruje**jako první. Tento stav znamená, že se zařízení vytvoří v IoT Central a má ID zařízení. Zařízení je zaregistrováno v těchto případech:
    - Na stránce **zařízení** se přidá nové reálné zařízení.
    - Na stránce **zařízení** se přidá sada zařízení pomocí **importu** .

1. Stav zařízení se změní na **zřízený** , když se zařízení, které je připojené k vaší aplikaci IoT Central s platnými přihlašovacími údaji, dokončí krok zřizování. V tomto kroku zařízení používá DPS k automatickému načítání připojovacího řetězce z IoT Hub používaného vaší aplikací IoT Central. Zařízení se teď může připojit k IoT Central a začít odesílat data.

1. Operátor může blokovat zařízení. Když je zařízení blokované, nemůže odesílat data do vaší aplikace IoT Central. Blokovaná zařízení mají stav **blokováno**. Předtím, než bude moct pokračovat v odesílání dat, musí operátor resetovat zařízení. Když operátor odblokuje zařízení, stav se vrátí do předchozí hodnoty, **zaregistrováno** nebo **zřízeno**.

1. Pokud stav zařízení čeká na **schválení**, znamená to, že je možnost **automatického schvalování** zakázaná. Operátor musí explicitně schválit zařízení před tím, než začne zasílat data. Zařízení, která nejsou zaregistrovaná ručně na stránce **zařízení** , ale jsou připojená s platnými přihlašovacími údaji, budou mít stav zařízení **čekající na schválení**. Operátoři můžou tato zařízení schválit ze stránky **zařízení** pomocí tlačítka **schválit** .

1. Pokud je stav zařízení **Nepřidruženo**, znamená to, že zařízení připojující se k IoT Central nemá přidruženou šablonu zařízení. K této situaci obvykle dochází v následujících situacích:

    - Sada zařízení se přidá na stránce **zařízení** pomocí **importu** bez zadání šablony zařízení.
    - Zařízení se zaregistrovalo ručně na stránce **zařízení** bez zadání šablony zařízení. Zařízení se pak připojí s platnými přihlašovacími údaji.  

    Operátor může přidružit zařízení k šabloně zařízení ze stránky **zařízení** pomocí tlačítka **migrovat** .

## <a name="best-practices"></a>Osvědčené postupy

Nezachovejte ani do mezipaměti připojovacího řetězce zařízení, který funkce DPS vrátí při prvním připojení zařízení. Pokud chcete zařízení znovu připojit, Projděte si standardní tok registrace zařízení, abyste získali správný připojovací řetězec zařízení. Pokud zařízení připojovací řetězec ukládá do mezipaměti, je software zařízení v případě, že IoT Central aktualizuje základní službu Azure IoT Hub, kterou používá, v případě, že je v provozu.

## <a name="sdk-support"></a>Podpora SDK

Sady SDK pro zařízení Azure nabízí nejjednodušší způsob implementace kódu zařízení. K dispozici jsou následující sady SDK pro zařízení:

- [Azure IoT SDK pro C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK pro Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK pro Javu](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení IoT Hub

Všechna komunikace zařízení s IoT Hub používá následující možnosti připojení IoT Hub:

- [Zasílání zpráv ze zařízení do cloudu](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Vlákna zařízení](../../iot-hub/iot-hub-devguide-device-twins.md)

Následující tabulka shrnuje, jak se funkce IoT Central zařízení Azure namapují k funkcím IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Zasílání zpráv ze zařízení do cloudu |
| Vlastnost | Nedokončené hlášené vlastnosti zařízení |
| Vlastnost (zapisovatelná) | Požadované a hlášené vlastnosti vlákna zařízení |
| Příkaz | Přímé metody |

### <a name="protocols"></a>Protokoly

Sady SDK pro zařízení podporují následující síťové protokoly pro připojení ke službě IoT Hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto rozdílových protokolech a doprovodnéch materiálech k výběru jednoho najdete v tématu [Volba komunikačního protokolu](../../iot-hub/iot-hub-devguide-protocols.md).

Pokud zařízení nemůže používat žádné z podporovaných protokolů, můžete k převodu protokolu použít Azure IoT Edge. IoT Edge podporuje další postupy na základě inteligentních funkcí, které umožňují přesměrování zpracování na hranici z aplikace Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data vyměňované mezi zařízeními a vaším IoT Central Azure jsou šifrovaná. IoT Hub ověří všechny požadavky ze zařízení, které se připojuje k jakémukoli koncovému bodu IoT Hub pro zařízení. Aby se zabránilo výměně přihlašovacích údajů po kabelech, zařízení používá k ověření podepsané tokeny. Další informace najdete v tématu [řízení přístupu k IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
- Zjistěte, jak [v aplikaci IoT Central Azure definovat nový typ zařízení IoT](./howto-set-up-template.md) .
- Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md)