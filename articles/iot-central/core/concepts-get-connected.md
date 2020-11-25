---
title: Připojení zařízení v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 10/22/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 1a050daa3a4b3ae9be5ef40961c40adaa90dc72b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121817"
---
# <a name="get-connected-to-azure-iot-central"></a>Připojení ke službě Azure IoT Central

*Tento článek se týká operátorů a vývojářů zařízení.*

Tento článek popisuje, jak se zařízení připojují k aplikaci Azure IoT Central. Předtím, než může zařízení vyměňovat data s IoT Central, musí:

- *Ověřování*. Ověřování pomocí aplikace IoT Central používá buď _token sdíleného přístupového podpisu (SAS)_ nebo _certifikát X. 509_. V produkčním prostředí se doporučují certifikáty X. 509.
- *Zaregistrujte* se. Zařízení musí být zaregistrovaná v aplikaci IoT Central. Registrovaná zařízení můžete zobrazit na stránce **zařízení** v aplikaci.
- *Přidružte se k šabloně zařízení*. V aplikaci IoT Central definují šablony zařízení uživatelské rozhraní, které operátory používají k zobrazení a správě připojených zařízení.

IoT Central podporuje tyto dva scénáře registrace zařízení:

- *Automatická registrace*. Zařízení se zaregistruje automaticky při prvním připojení. Díky tomuto scénáři můžou výrobci OEM hromadně vyrábět zařízení, která se můžou připojit bez registrace. Výrobce OEM vygeneruje vhodné přihlašovací údaje pro zařízení a nakonfiguruje zařízení v továrně. Volitelně můžete vyžadovat, aby před zahájením odesílání dat mohl operátor schválit zařízení. Tento scénář vyžaduje, abyste ve své aplikaci nakonfigurovali _registraci skupiny_ X. 509 nebo SAS.
- *Ruční registrace*. Operátory buď zaregistrují jednotlivá zařízení na stránce **zařízení** , nebo [naimportujte soubor CSV](howto-manage-devices.md#import-devices) pro hromadnou registraci zařízení. V tomto scénáři můžete použít _registraci skupiny_ x. 509 nebo SAS nebo _registraci_ do skupin x. 509 nebo SAS.

Zařízení, která se připojují k IoT Central by měla splňovat *konvence technologie Plug and Play IoT*. Jednou z těchto konvencí je, že zařízení má poslat _ID modelu_ modelu zařízení, které implementuje při připojení. ID modelu umožňuje aplikaci IoT Central přidružit zařízení ke správné šabloně zařízení.

IoT Central používá ke správě procesu připojení [službu Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) . Zařízení se nejdřív připojí ke koncovému bodu DPS, aby načetlo informace, které potřebuje pro připojení k vaší aplikaci. Interně používá vaše aplikace IoT Central službu IoT Hub ke zpracování připojení zařízení. Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Tento článek popisuje následující kroky připojení zařízení:

- [Registrace skupiny X. 509](#x509-group-enrollment)
- [Registrace skupiny SAS](#sas-group-enrollment)
- [Jednotlivé registrace](#individual-enrollment)
- [Registrace zařízení](#device-registration)
- [Přidružení zařízení k šabloně zařízení](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Registrace skupiny X. 509

V produkčním prostředí je použití certifikátů X. 509 doporučeným mechanismem ověřování zařízení pro IoT Central. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](../../iot-hub/iot-hub-x509ca-overview.md).

Připojení zařízení s certifikátem X. 509 do vaší aplikace:

1. Vytvořte *skupinu registrace* , která používá typ ověření identity **(X. 509)** .
1. Přidejte a ověřte zprostředkující nebo kořenový certifikát X. 509 ve skupině pro registraci.
1. Vygenerujte listový certifikát z kořenového nebo zprostředkujícího certifikátu ve skupině pro registraci. Odeslat listový certifikát ze zařízení, když se připojí k vaší aplikaci.

Další informace najdete v tématu [Postup připojení zařízení pomocí certifikátů X. 509.](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Pouze pro účely testování

Pro účely testování můžete pomocí následujících nástrojů generovat certifikáty root, zprostředkujícího a zařízení:

- [Nástroje pro sadu SDK zařízení pro zřizování zařízení Azure IoT](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): kolekce Node.jsch nástrojů, které můžete použít ke generování a ověření certifikátů a klíčů X. 509.
- Pokud používáte zařízení DevKit, tento [Nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) vygeneruje certifikát certifikační autority, který můžete přidat do vaší aplikace IoT Central pro ověření certifikátů.
- [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): kolekce skriptů PowerShellu a bash pro:
  - Vytvořte řetěz certifikátů.
  - Uložte certifikáty jako soubory. cer a nahrajte je do vaší aplikace IoT Central.
  - K vygenerování ověřovacího certifikátu použijte ověřovací kód z aplikace IoT Central.
  - Vytvořte listové certifikáty pro vaše zařízení pomocí ID zařízení jako parametru nástroje.

## <a name="sas-group-enrollment"></a>Registrace skupiny SAS

Připojení zařízení k klíči SAS zařízení k vaší aplikaci:

1. Vytvořte *skupinu registrace* , která používá typ ověření identity **sdíleného přístupového podpisu (SAS)** .
1. Z registrační skupiny zkopírujte primární nebo sekundární klíč skupiny.
1. Pomocí rozhraní příkazového řádku Azure vygenerujte klíč zařízení z klíče skupiny:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Použijte generovaný klíč zařízení, když se zařízení připojí k vaší IoT Central aplikaci.

## <a name="individual-enrollment"></a>Jednotlivé registrace

Zákazníci připojující zařízení, u kterých má každá z nich vlastní ověřovací přihlašovací údaje, používají jednotlivé registrace. Jednotlivá registrace je záznam pro jedno zařízení, které se smí připojit. Jednotlivé registrace můžou jako mechanismus ověřování použít buď certifikáty na listech X. 509, nebo tokeny SAS (z modulu fyzického nebo virtuální důvěryhodné platformy). ID zařízení může obsahovat písmena, číslice a `-` znak. Další informace najdete v tématu [DPS – jednotlivý zápis](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Když vytvoříte jednotlivou registraci zařízení, bude mít přednost před výchozími možnostmi registrace skupin ve vaší aplikaci IoT Central.

### <a name="create-individual-enrollments"></a>Vytvořit jednotlivé registrace

IoT Central podporuje následující mechanismy ověřování identity pro jednotlivé registrace:

- **Ověření identity symetrického klíče:** Symetrický ověření identity je jednoduchý přístup k ověřování zařízení s instancí DPS. Pokud chcete vytvořit jednotlivou registraci s použitím symetrických klíčů, otevřete na zařízení stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus zadejte **sdílený přístupový podpis (SAS)** . Zadejte primární a sekundární klíč kódovaný v kódování Base64 a uložte provedené změny. Připojte zařízení pomocí **oboru ID**, **ID zařízení** a primárního nebo sekundárního klíče.

    > [!TIP]
    > Pro účely testování můžete použít **OpenSSL** k vygenerování klíčů kódovaných v kódování Base64: `openssl rand -base64 64`

- **Certifikáty X. 509:** Pokud chcete vytvořit jednotlivou registraci pomocí certifikátů X. 509, otevřete stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus zvolte **certifikáty (X. 509)** . Certifikáty zařízení používané s jednotlivou položkou registrace mají požadavek, aby byl Vydavatel a předmět CN nastaven na ID zařízení.

    > [!TIP]
    > Pro účely testování můžete použít [Nástroje pro sadu SDK zařízení pro zřizování zařízení Azure IoT pro Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) k vygenerování certifikátu podepsaného svým držitelem: `node create_test_cert.js device "mytestdevice"`

- **Ověření identity čipu TPM (Trusted Platform Module):** [Čip TPM](../../iot-dps/concepts-tpm-attestation.md) je typ modulu hardwarového zabezpečení. Používání čipu TPM je jedním z nejbezpečnější způsobů, jak připojit zařízení. V tomto článku se předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Software emulující čipy TPM je vhodný pro vytváření prototypů nebo testování, ale neposkytuje stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované čipy TPM. Nepoužívejte software čipy TPM v produkčním prostředí. Pokud chcete vytvořit jednotlivou registraci, která používá čip TPM, otevřete stránku **připojení zařízení** , jako způsob připojení vyberte **jednotlivou registraci** a jako mechanismus vytvořte **TPM** . Zadejte ověřovací klíč čipu TPM a uložte informace o připojení zařízení.

## <a name="device-registration"></a>Registrace zařízení

Předtím, než se zařízení může připojit k aplikaci IoT Central, musí být zaregistrované v aplikaci:

- Zařízení se můžou při prvním připojení automaticky zaregistrovat. Pokud chcete použít tuto možnost, musíte použít buď [registraci skupiny X. 509](#x509-group-enrollment) , nebo [registraci skupiny SAS](#sas-group-enrollment).
- Operátor může importovat soubor CSV pro hromadnou registraci seznamu zařízení v aplikaci.
- Operátor může ručně zaregistrovat jednotlivá zařízení na stránce **zařízení** v aplikaci.

IoT Central umožňuje výrobcům OEM využít hromadnou výrobu zařízení, která se můžou registrovat automaticky. Výrobce OEM vygeneruje vhodné přihlašovací údaje pro zařízení a nakonfiguruje zařízení v továrně. Když zákazník zařízení poprvé zapne, připojí se k DPS, který pak zařízení automaticky připojí ke správné aplikaci IoT Central. Volitelně můžete vyžadovat, aby před zahájením odesílání dat do aplikace operátor schválil zařízení.

> [!TIP]
> Na stránce **> pro správu připojení zařízení** , možnost **automatického schvalování** určuje, jestli má operátor ručně schválit zařízení, než může začít odesílat data.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Automatická registrace zařízení, která používají certifikáty X. 509

1. Vygenerujte list – certifikáty pro vaše zařízení pomocí kořenového nebo zprostředkujícího certifikátu, který jste přidali do vaší [skupiny pro registraci X. 509](#x509-group-enrollment). Použijte ID zařízení jako `CNAME` v listových certifikátech. ID zařízení může obsahovat písmena, číslice a `-` znak.

1. Jako výrobce OEM, zablikají každé zařízení s ID zařízení, vygenerovaným listem X. 509 list-Certificate a hodnotou **oboru ID** aplikace. Kód zařízení by měl také odeslat ID modelu modelu zařízení, který implementuje.

1. Když přepnete zařízení, nejprve se připojí k DPS, aby se načetly informace o IoT Central připojení.

1. Zařízení používá informace z DPS k připojení a registraci v aplikaci IoT Central.

IoT Central aplikace používá ID modelu odesílané zařízením k [přidružení registrovaného zařízení k šabloně zařízení](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Automaticky registrovat zařízení, která používají tokeny SAS

1. Zkopírujte primární klíč skupiny ze skupiny pro zápis **SAS-IoT-Device** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Skupinový primární klíč ze skupiny SAS-IoT-Devices skupina pro registraci":::

1. Pomocí `az iot central device compute-device-key` příkazu vygenerujte klíče SAS zařízení. Použijte primární klíč skupiny z předchozího kroku. ID zařízení může obsahovat písmena, číslice a `-` znak:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Jako výrobce OEM, zablikají každé zařízení s ID zařízení, vygenerovanou klíč SAS zařízení a hodnotou **rozsahu ID** aplikace. Kód zařízení by měl také odeslat ID modelu modelu zařízení, který implementuje.

1. Když přepnete zařízení do zařízení, nejprve se připojí k DPS, aby se načetly informace o registraci IoT Central.

1. Zařízení používá informace z DPS k připojení a registraci v aplikaci IoT Central.

IoT Central aplikace používá ID modelu odesílané zařízením k [přidružení registrovaného zařízení k šabloně zařízení](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Předem hromadně registrovat zařízení

Pokud chcete v aplikaci IoT Central zaregistrovat velký počet zařízení, použijte k [importu ID zařízení a názvů zařízení](howto-manage-devices.md#import-devices)soubor CSV.

Pokud vaše zařízení k ověřování používají tokeny SAS, [exportujte soubor CSV z aplikace IoT Central](howto-manage-devices.md#export-devices). Exportovaný soubor CSV obsahuje ID zařízení a klíče SAS.

Pokud vaše zařízení používají k ověřování certifikáty X. 509, vygenerujte na svých zařízeních hlavní certifikát X. 509 a pomocí kořenového nebo zprostředkujícího certifikátu, který jste nahráli do vaší skupiny pro registraci X. 509. Použijte ID zařízení, která jste importovali jako `CNAME` hodnotu v listových certifikátech.

Zařízení musí pro svou aplikaci používat hodnotu **rozsahu ID** a při připojování odesílat ID modelu.

> [!TIP]
> Hodnotu **Rozsah ID** můžete najít v části **Správa > připojení zařízení**.

### <a name="register-a-single-device-in-advance"></a>Předem Zaregistrujte jedno zařízení.

Tento přístup je užitečný při experimentování s IoT Central nebo testováním zařízení. Vyberte **+ Nová** na stránce **zařízení** a zaregistrujte jednotlivá zařízení. K připojení zařízení k aplikaci IoT Central můžete použít klíče SAS pro připojení zařízení. Zkopírujte _klíč SAS zařízení_ z informací o připojení pro registrované zařízení:

![Klíče SAS pro jednotlivá zařízení](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Přidružení zařízení k šabloně zařízení

IoT Central automaticky přidružuje zařízení k šabloně zařízení, když se zařízení připojí. Zařízení pošle ID modelu při připojení. IoT Central používá ID modelu k identifikaci šablony zařízení pro konkrétní model zařízení. Proces zjišťování funguje takto:

1. Pokud je šablona zařízení již publikována v aplikaci IoT Central, je zařízení přidruženo k šabloně zařízení.
1. Pokud už šablona zařízení není v aplikaci IoT Central publikovaná, IoT Central v úložišti veřejného modelu vyhledá model zařízení. Pokud IoT Central najde model, použije ho k vygenerování základní šablony zařízení.
1. Pokud IoT Central nenajde model v úložišti veřejného modelu, zařízení se označí jako **Nepřidruženo**. Operátor může vytvořit šablonu zařízení pro zařízení a pak migrovat nepřidružené zařízení do nové šablony zařízení.

## <a name="device-status-values"></a>Hodnoty stavu zařízení

Když se reálné zařízení připojí k vaší aplikaci IoT Central, změní se stav zařízení takto:

1. Stav zařízení se **zaregistruje** jako první. Tento stav znamená, že se zařízení vytvoří v IoT Central a má ID zařízení. Zařízení je zaregistrováno v těchto případech:
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

Nezachovejte ani do mezipaměti připojovacího řetězce zařízení, který funkce DPS vrátí při prvním připojení zařízení. Pokud chcete zařízení znovu připojit, Projděte si standardní tok registrace zařízení, abyste získali správný připojovací řetězec zařízení. Pokud zařízení připojovací řetězec ukládá do mezipaměti, software zařízení bude mít riziko, že se zastaralým připojovacím řetězcem pracuje. Pokud IoT Central aktualizuje základní službu Azure IoT Hub, kterou používá, zařízení se zastaralým připojovacím řetězcem se nemůže připojit.

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

Pokud zařízení nemůže používat žádné z podporovaných protokolů, použijte k převodu protokolu Azure IoT Edge. IoT Edge podporuje další inteligentní scénáře pro přesměrování zpracování z aplikace Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data vyměňované mezi zařízeními a vaším IoT Central Azure jsou šifrovaná. IoT Hub ověří všechny požadavky ze zařízení, které se připojuje k jakémukoli koncovému bodu IoT Hub pro zařízení. Aby se zabránilo výměně přihlašovacích údajů po kabelech, zařízení používá k ověření podepsané tokeny. Další informace najdete v tématu [řízení přístupu k IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si ukázkový kód, který ukazuje, jak používat tokeny SAS v [kurzu: vytvoření a připojení klientské aplikace do aplikace Azure IoT Central.](tutorial-connect-device.md)
- Naučte se, jak [připojit zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikaci](how-to-connect-devices-x509.md) .
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
- Zjistěte, jak [v aplikaci IoT Central Azure definovat nový typ zařízení IoT](./howto-set-up-template.md) .
- Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md)
