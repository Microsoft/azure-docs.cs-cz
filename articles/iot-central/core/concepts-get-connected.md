---
title: Připojení zařízení v Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f37b070c74abd8511fc597f9b159312d91281083
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759109"
---
# <a name="get-connected-to-azure-iot-central"></a>Připojení k Azure IoT Central

*Tento článek se vztahuje na operátory a vývojáře zařízení.*

Tento článek popisuje možnosti pro připojení zařízení k aplikaci Azure IoT Central.

Obvykle je nutné zaregistrovat zařízení v aplikaci před připojením. IoT Central však podporuje scénáře, kde [se zařízení mohou připojit, aniž by byla nejprve zaregistrována](#connect-without-registering-devices).

IoT Central používá [službu Azure IoT Hub Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) ke správě procesu připojení. Zařízení se nejprve připojí ke koncovému bodu DPS, aby načetlo informace, které potřebuje k připojení k aplikaci. Interně vaše aplikace IoT Central používá službu IoT hub ke zpracování připojení zařízení. Použití DPS umožňuje:

- IoT Central pro podporu onboardingu a připojování zařízení ve velkém měřítku.
- Můžete generovat přihlašovací údaje zařízení a konfigurovat zařízení offline bez registrace zařízení prostřednictvím ioT centrální ui.
- K registraci zařízení v IoT Central můžete používat vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími back-office systémy.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

K zabezpečení komunikace mezi zařízením a vaší aplikací podporuje IoT Central certifikáty sdíleného přístupu (SAS) i certifikáty X.509. Certifikáty X.509 se doporučují v produkčním prostředí.

Tento článek popisuje následující případy použití:

- [Připojení jednoho zařízení pomocí SAS](#connect-a-single-device)
- [Připojení zařízení ve velkém měřítku pomocí SAS](#connect-devices-at-scale-using-sas)
- [Připojte zařízení v měřítku pomocí certifikátů X.509](#connect-devices-using-x509-certificates) - doporučený přístup pro produkční prostředí.
- [Připojte zařízení, aniž byste je nejprve zaregistrovali](#connect-without-registering-devices)
- [Připojení zařízení, která používají jednotlivé registrace DPS](#individual-enrollment-based-device-connectivity)
- [Připojení zařízení pomocí funkcí IoT Plug and Play (preview)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Připojení jednoho zařízení

Tento přístup je užitečný, když experimentujete s IoT Central nebo testujete zařízení. Pomocí klíčů SAS připojení zařízení z aplikace IoT Central můžete připojit zařízení k aplikaci IoT Central. Zkopírujte _klíč SAS zařízení_ z informací o připojení registrovaného zařízení:

![Tlačítka SAS pro jednotlivá zařízení](./media/concepts-get-connected/single-device-sas.png)

Další informace najdete v tématu [vytvoření a připojení klientské aplikace Node.js k kurzu aplikace Azure IoT Central.](./tutorial-connect-device.md)

## <a name="connect-devices-at-scale-using-sas"></a>Připojení zařízení ve velkém měřítku pomocí SAS

Chcete-li připojit zařízení k IoT Central ve velkém měřítku pomocí klíčů SAS, musíte zaregistrovat a potom nastavit zařízení:

### <a name="register-devices-in-bulk"></a>Hromadná registrace zařízení

Chcete-li zaregistrovat velký počet zařízení v aplikaci IoT Central, importujte [ID zařízení a názvy zařízení](howto-manage-devices.md#import-devices)pomocí souboru CSV .

Chcete-li načíst informace o připojení pro importovaná zařízení, [exportujte soubor CSV z aplikace IoT Central](howto-manage-devices.md#export-devices). Exportovaný soubor CSV obsahuje ID zařízení a klíče SAS.

### <a name="set-up-your-devices"></a>Nastavení zařízení

Pomocí informací o připojení ze souboru exportu v kódu zařízení umožníte zařízením připojit se a odesílat data do IoT do aplikace IoT Central. Potřebujete také **dps ID obor** pro vaši aplikaci. Tuto hodnotu naleznete v **části Správa připojení > zařízení**.

> [!NOTE]
> Informace o tom, jak můžete připojit zařízení, aniž byste je museli nejdřív zaregistrovat ve službě IoT Central, najdete v [tématu Připojení bez předchozí registrace zařízení](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Připojení zařízení pomocí certifikátů X.509

V produkčním prostředí je použití certifikátů X.509 doporučeným mechanismem ověřování zařízení pro IoT Central. Další informace najdete v [tématu Ověřování zařízení pomocí certifikátů Certifikační autority X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Před připojením zařízení k certifikátu X.509 přidejte a ověřte zprostředkující nebo kořenový certifikát X.509 v aplikaci. Zařízení musí používat certifikáty listu X.509 generované z kořenového nebo zprostředkujícího certifikátu.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Přidání a ověření kořenového nebo zprostředkujícího certifikátu

Přejděte na **název Správce > připojení k zařízení > Správa primárního certifikátu** a přidejte kořenový nebo zprostředkující certifikát X.509, který používáte ke generování certifikátů zařízení.

![Nastavení připojení](media/concepts-get-connected/manage-x509-certificate.png)

Ověření vlastnictví certifikátu zajistí, že osoba, která certifikát nahraje, má soukromý klíč certifikátu. Ověření certifikátu:

  1. Kliknutím na tlačítko vedle **ověřovacího kódu** vygenerujete kód.
  1. Vytvořte ověřovací certifikát X.509 s ověřovacím kódem, který jste vygenerovali v předchozím kroku. Uložte certifikát jako soubor CER.
  1. Nahrajte podepsaný ověřovací certifikát a vyberte **Ověřit**. Certifikát je označen jako **Ověřeno,** když je ověření úspěšné.

Pokud došlo k porušení zabezpečení nebo je nastavena platnost primárního certifikátu, použijte sekundární certifikát ke zkrácení prostojů. Při aktualizaci primárního certifikátu můžete pokračovat v zřizování zařízení používajících sekundární certifikát.

### <a name="register-and-connect-devices"></a>Registrace a připojení zařízení

Chcete-li hromadně připojit zařízení pomocí certifikátů X.509, nejprve zaregistrujte zařízení ve vaší aplikaci pomocí souboru CSV k [importu ID zařízení a názvů zařízení](howto-manage-devices.md#import-devices). ID zařízení by měla být malá písmena.

Pomocí nahraného kořenového nebo zprostředkujícího certifikátu vygenerujte pro svá zařízení listové certifikáty X.509. Jako hodnotu v `CNAME` listových certifikátech použijte **ID zařízení.** Kód zařízení potřebuje hodnotu **oboru ID** pro vaši aplikaci, **ID zařízení**a odpovídající certifikát zařízení.

### <a name="for-testing-purposes-only"></a>Pouze pro účely testování

Pouze pro testování můžete ke generování kořenových, zprostředkujících a prostředků zařízení použít následující nástroje:

- Nástroje pro Azure [IoT Device Provisioning Device SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): kolekce nástrojů Node.js, které můžete použít ke generování a ověřování certifikátů a klíčů X.509.
- Pokud používáte zařízení DevKit, tento [nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) generuje certifikát certifikační autority, který můžete přidat do aplikace IoT Central k ověření certifikátů.
- [Správa testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): kolekce skriptů PowerShella a Bash:
  - Vytvořte řetěz certifikátů.
  - Uložte certifikáty jako soubory CER, které chcete nahrát do aplikace IoT Central.
  - Ke generování ověřovacího certifikátu použijte ověřovací kód z aplikace IoT Central.
  - Vytvořte listové certifikáty pro vaše zařízení pomocí ID zařízení jako parametr u nástroje.

### <a name="further-reference"></a>Další odkaz

- [Ukázková implementace pro RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Ukázkový klient zařízení v C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Připojení bez registrace zařízení

Všechny dříve popsané scénáře vyžadují registraci zařízení v aplikaci před jejich připojením. IoT Central také umožňuje výrobcům OEM sériově vyrábět zařízení, která se mohou připojit, aniž by byla nejprve zaregistrována. Výrobce OEM generuje vhodná pověření zařízení a konfiguruje zařízení ve výrobě. Když zákazník zapne zařízení poprvé, připojí se k DPS, který pak automaticky připojí zařízení ke správné aplikaci IoT Central. Operátor IoT Central musí schválit zařízení před zahájením odesílání dat do aplikace.

Tok se mírně liší v závislosti na tom, zda zařízení používají tokeny SAS nebo certifikáty X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Připojení zařízení, která používají tokeny SAS bez registrace

1. Zkopírujte primární klíč skupiny aplikace IoT Central:

    ![Primární klíč SAS skupiny aplikací](media/concepts-get-connected/group-sas-keys.png)

1. Pomocí nástroje [dps-keygen](https://www.npmjs.com/package/dps-keygen) vygenerujte klíče SAS zařízení. Použijte primární klíč skupiny z předchozího kroku. ID zařízení musí být malá písmena:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. Výrobce OEM bliká na každém zařízení s ID zařízení, klíčem SAS generovaného zařízení a hodnotou **oboru ID** aplikace.

1. Když zapnete zařízení, nejprve se připojí k DPS a načte své registrační informace IoT Central.

    Zařízení má zpočátku stav zařízení **Nepřidružený na** stránce **Zařízení** a není přiřazen k šabloně zařízení. Na stránce **Zařízení** **migrujte** zařízení do příslušné šablony zařízení. Zřizování zařízení je teď **dokončeno,** stav zařízení je teď Zřízený a zařízení může začít odesílat data.

    Na stránce **Správa > připojení zařízení** určuje možnost Automatické **schválení,** zda je třeba zařízení před zahájením odesílání dat ručně schválit.

    > [!NOTE]
    > Informace o automatickém přidružení zařízení k šabloně zařízení najdete v tématu [Připojení zařízení pomocí technologie IoT Plug and Play (náhled).](#connect-devices-with-iot-plug-and-play-preview)

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Připojení zařízení, která používají certifikáty X.509 bez registrace

1. [Přidejte a ověřte kořenový nebo zprostředkující certifikát X.509](#connect-devices-using-x509-certificates) do aplikace IoT Central. (#connect-zařízení-použití-x509-certifikáty)

1. Vygenerujte listové certifikáty pro vaše zařízení pomocí kořenového nebo zprostředkujícího certifikátu, který jste přidali do aplikace IoT Central. Použijte ID malá písmena `CNAME` zařízení jako v listových certifikátech.

1. Výrobce OEM bliká na každém zařízení s ID zařízení, vygenerovaným levým certifikátem X.509 a hodnotou **oboru ID** aplikace.

1. Když zapnete zařízení, nejprve se připojí k DPS a načte své registrační informace IoT Central.

    Zařízení má zpočátku stav zařízení **Nepřidružený na** stránce **Zařízení** a není přiřazen k šabloně zařízení. Na stránce **Zařízení** **migrujte** zařízení do příslušné šablony zařízení. Zřizování zařízení je teď **dokončeno,** stav zařízení je teď Zřízený a zařízení může začít odesílat data.

    Na stránce **Správa > připojení zařízení** určuje možnost Automatické **schválení,** zda je třeba zařízení před zahájením odesílání dat ručně schválit.

    > [!NOTE]
    > Informace o automatickém přidružení zařízení k šabloně zařízení najdete v tématu [Připojení zařízení pomocí technologie IoT Plug and Play (náhled).](#connect-devices-with-iot-plug-and-play-preview)

## <a name="individual-enrollment-based-device-connectivity"></a>Individuální připojení zařízení založené na registraci

Pro zákazníky, kteří připojují zařízení, z nichž každý má svá vlastní ověřovací pověření, použijte jednotlivé registrace. Individuální registrace je položka pro jedno zařízení, které je povoleno připojení. Jednotlivé zápisy můžete použít buď X.509 list ové certifikáty nebo Tokeny SAS (z fyzického nebo virtuálního modulu důvěryhodné platformy) jako mechanismy ověřování. ID zařízení (označované také jako ID registrace) v jednotlivém zápisu je alfanumerické, malé a může obsahovat pomlčky. Další informace naleznete v tématu [DPS individuální zápis](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Když vytvoříte individuální registraci pro zařízení, má přednost před výchozí mise možnosti registrace ve vaší aplikaci IoT Central.

### <a name="creating-individual-enrollments"></a>Vytváření jednotlivých registrací

IoT Central podporuje následující mechanismy ověřování pro jednotlivé zápisy:

- **Symetrické atestace klíče:** Symetrické atestace klíče je jednoduchý přístup k ověřování zařízení s instancí DPS. Chcete-li vytvořit individuální zápis, který používá symetrické klíče, otevřete stránku **Připojení k zařízení,** vyberte **individuální zápis** jako metodu připojení a **sdílený přístupový podpis (SAS)** jako mechanismus. Zadejte základní základní 64 kódované primární a sekundární klíče a uložte změny. Pro připojení zařízení použijte **obor ID**, **ID zařízení**a primární nebo sekundární klíč.

    > [!TIP]
    > Pro testování můžete použít **OpenSSL** ke generování kódovaných klíčů base64:`openssl rand -base64 64`

- **Certifikáty X.509:** Chcete-li vytvořit individuální zápis pomocí certifikátů X.509, otevřete stránku **Připojení k zařízení,** vyberte **individuální zápis** jako metodu připojení a **certifikáty (X.509)** jako mechanismus. Certifikáty zařízení používané s jednotlivou položkou zápisu mají požadavek, aby vystavitel a subjekt cn byly nastaveny na ID zařízení.

    > [!TIP]
    > Pro testování můžete ke generování certifikátu podepsaného svým vlastním podpisem použít [nástroje pro sypovou sadu Zařízení Azure IoT pro node.js:](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)`node create_test_cert.js device "mytestdevice"`

- **Atestace modulu důvěryhodné platformy (TPM):** Čip [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) je typ modulu hardwarového zabezpečení. Použití čipu TPM je jedním z nejbezpečnějších způsobů připojení zařízení. Tento článek předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Softwarově emulované tpms jsou vhodné pro prototypování nebo testování, ale neposkytují stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované TPM. Nepoužívejte softwarové tpms v produkčním prostředí. Chcete-li vytvořit individuální registraci, která používá čip TPM, otevřete stránku **Připojení zařízení,** vyberte jako metodu připojení **individuální zápis** a jako mechanismus **čip TPM.** Zadejte ověřovací klíč čipu TPM a uložte informace o připojení zařízení.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Připojení zařízení pomocí technologie IoT Plug and Play (náhled)

Jednou z klíčových funkcí IoT Plug and Play (preview) s IoT Central je možnost automaticky přidružit šablony zařízení při připojení zařízení. Spolu s pověřenízařízení zařízení nyní můžete odeslat **CapabilityModelId** jako součást volání registrace zařízení. Tato funkce umožňuje IoT Central zjistit a přidružit šablonu zařízení k zařízení. Proces zjišťování funguje takto:

1. Přidruží se k šabloně zařízení, pokud je už publikovaná v aplikaci IoT Central.
1. Načte z veřejného úložiště publikovaných a certifikovaných modelů schopností.

Níže je uveden formát dodatečné hodu, které by zařízení odeslalo během registračního hovoru DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Všimněte si, že možnost **Automatické schválení** při připojení správce **> zařízení** musí být povolena, aby se zařízení automaticky připojovala, zjišťovala šablonu zařízení a začala odesílat data.

## <a name="device-status-values"></a>Hodnoty stavu zařízení

Když se skutečné zařízení připojí k vaší aplikaci IoT Central, jeho stav zařízení se změní následujícím způsobem:

1. Stav zařízení je **nejprve registrován .** Tento stav znamená, že se zařízení vytvoří v IoT Central a má ID zařízení. Zařízení je zaregistrováno, když:
    - Na stránku **Zařízení** je přidáno nové skutečné zařízení.
    - Sada zařízení se přidá pomocí **importu** na stránce **Zařízení.**

1. Stav zařízení se změní na **Zřízené,** když zařízení, které se připojilo k vaší aplikaci IoT Central s platnými přihlašovacími údaji, dokončí krok zřizování. V tomto kroku zařízení používá DPS k automatickému načtení připojovacího řetězce z ioT hubu používaného vaší aplikací IoT Central. Zařízení se teď může připojit k IoT Central a začít odesílat data.

1. Operátor může blokovat zařízení. Když je zařízení zablokované, nemůže odesílat data do vaší aplikace IoT Central. Blokovaná zařízení mají stav **Blokováno**. Před obnovením odesílání dat musí operátor zařízení resetovat. Když operátor odblokuje zařízení, stav se vrátí na jeho předchozí **hodnotu, Registrováno** nebo **Zřízené**.

1. Pokud je stav zařízení **Čeká na schválení**, znamená to, že možnost Automatické **schválení** je zakázána. Operátor musí explicitně schválit zařízení před zahájením odesílání dat. Zařízení, která nejsou na stránce **Zařízení registrována** ručně, ale jsou spojena s platnými přihlašovacími údaji, budou mít stav zařízení **Čekání na schválení**. Operátoři mohou tato zařízení schválit ze stránky **Zařízení** pomocí tlačítka **Schválit.**

1. Pokud je stav zařízení **Nepřidružený**, znamená to, že zařízení, které se připojuje k IoT Central, nemá přidruženou šablonu zařízení. K této situaci obvykle dochází v následujících scénářích:

    - Sada zařízení se přidá pomocí **importu** na stránce **Zařízení** bez zadání šablony zařízení.
    - Zařízení bylo zaregistrováno ručně na stránce **Zařízení** bez zadání šablony zařízení. Zařízení je pak připojeno s platnými přihlašovacími údaji.  

    Operátor může přidružit zařízení k šabloně zařízení ze stránky **Zařízení** pomocí tlačítka **Migrovat.**

## <a name="best-practices"></a>Osvědčené postupy

Nezachovávejte nebo ukládat do mezipaměti připojovací řetězec zařízení, který DPS vrátí při prvním připojení zařízení. Chcete-li zařízení znovu připojit, projděte standardní tok registrace zařízení a získejte správný připojovací řetězec zařízení. Pokud zařízení ukládá připojovací řetězec do mezipaměti, software zařízení narazí na riziko, že bude mít zastaralý připojovací řetězec, pokud IoT Central aktualizuje základní centrum Azure IoT, které používá.

## <a name="sdk-support"></a>Podpora SDK

Sady Azure Device SDK nabízejí nejjednodušší způsob implementace kódu zařízení. K dispozici jsou následující sady SDK:

- [Azure IoT SDK pro C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK pro Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK pro Javu](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení služby IoT Hub

Veškerá komunikace zařízení s službou IoT Hub používá následující možnosti připojení služby IoT Hub:

- [Zasílání zpráv mezi zařízeními a cloudy](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dvojčata zařízení](../../iot-hub/iot-hub-devguide-device-twins.md)

Následující tabulka shrnuje, jak funkce zařízení Azure IoT Central mapují funkce služby IoT Hub na funkce služby IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Zasílání zpráv mezi zařízeními a cloudy |
| Vlastnost | Vlastnosti ohlášené dvojčeti zařízení |
| Vlastnost (zapisovatelná) | Požadované a hlášené vlastnosti dvojčete zařízení |
| Příkaz | Přímé metody |

Další informace o používání sad SDK zařízení najdete v [tématu Připojení zařízení sady DevDiv k aplikaci Azure IoT Central,](howto-connect-devkit.md) například kód.

### <a name="protocols"></a>Protokoly

Sady Device SDK podporují následující síťové protokoly pro připojení k centru IoT:

- MQTT
- AMQP
- HTTPS

Informace o těchto rozdílových protokolech a pokyny k výběru jednoho z nich naleznete v [tématu Volba komunikačního protokolu](../../iot-hub/iot-hub-devguide-protocols.md).

Pokud vaše zařízení nemůže používat žádný z podporovaných protokolů, můžete k převodu protokolu použít Azure IoT Edge. IoT Edge podporuje další scénáře inteligence na hraně k přepětí zpracování na hraniční výhodu z aplikace Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data vyměňovaná mezi zařízeními a vaším Azure IoT Central jsou šifrovaná. IoT Hub ověřuje každý požadavek ze zařízení, které se připojuje k libovolnému koncovému bodu služby IoT Hub směřujícího k zařízení. Aby se zabránilo výměně přihlašovacích údajů po drátě, zařízení používá podepsané tokeny k ověření. Další informace naleznete v tématu [Řízení přístupu k centru IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář zařízení, některé navrhované další kroky jsou:

- Zjistěte, jak [monitorovat připojení zařízení pomocí rozhraní příkazového příkazu Azure](./howto-monitor-devices-azure-cli.md)
- Přečtěte si, jak [definovat nový typ zařízení IoT v aplikaci Azure IoT Central](./howto-set-up-template.md)
- Přečtěte si o [zařízeních Azure IoT Edge a Azure IoT Central](./concepts-iot-edge.md)
