---
title: Azure IoT Hub Device Provisioning Service – ověření identity čipem TPM
description: Tento článek obsahuje koncepční přehled toku ověření čipem TPM pomocí služby IoT Device Provisioning (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531589"
---
# <a name="tpm-attestation"></a>Osvědčení TPM

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, která slouží ke konfiguraci zařízení s nulovým dotykem pro zadané centrum IoT. Služba Device Provisioning Service umožňuje bezpečným způsobem zřizovat miliony zařízení.

Tento článek popisuje proces ověření identity při použití čipu TPM (Trusted Platform Module). ČIP TPM je typ modulu hardwarového zabezpečení (HSM). V tomto článku se předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Software emulující čipy TPM je vhodný pro vytváření prototypů nebo testování, ale neposkytuje stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované čipy TPM. Nedoporučujeme používat software čipy TPM v produkčním prostředí. Další informace o typech čipy TPM najdete v tématu [stručný úvod do čipu TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Tento článek se týká jenom zařízení, která používají TPM 2,0 s podporou klíčů HMAC a jejich ověřovacími klíči. Nejedná se o zařízení, která používají certifikáty X. 509 pro ověřování. TPM je podniková, standardně založená na standardech ISO od skupiny důvěryhodných Computingů a další informace o TPM najdete na [kompletní specifikaci tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) nebo [specifikaci ISO/IEC 11889](https://www.iso.org/standard/66510.html). Tento článek také předpokládá, že jste obeznámeni s dvojicemi veřejného a privátního klíče a jak se používají pro šifrování.

Sady SDK zařízení služby Device Provisioning zpracovávají všechno popsané v tomto článku. Pokud používáte sady SDK na svých zařízeních, není potřeba nic dalšího implementovat. Tento článek vám pomůže pochopit, co se v čipu TPM zabezpečení nachází v případě, že vaše zařízení splňuje požadavky a proč je tak zabezpečené.

## <a name="overview"></a>Přehled

Čipy TPM jako zabezpečený kořen důvěry použijte něco jako ověřovací klíč (EK). Klíč EK je jedinečný pro čip TPM a změna v podstatě změní zařízení na nový.

Existuje jiný typ klíče, který čipy TPM má, označovaný jako kořenový klíč úložiště (SRK). Modul SRK může být vytvořen vlastníkem čipu TPM poté, co převezme vlastnictví čipu TPM. Převzetí vlastnictví čipu TPM je způsobem, který je specifický pro čip TPM. "někdo nastaví heslo v modulu HSM". Pokud se zařízení TPM prodává novému vlastníkovi, může nový vlastník převzít vlastnictví čipu TPM, aby vygeneroval nový modul SRK. Nová generace SRK zajišťuje, že předchozí vlastník nemůže čip TPM použít. Vzhledem k tomu, že je modul SRK jedinečný pro vlastníka čipu TPM, lze použít modul SRK k zapečetění dat do samotného čipu TPM pro daného vlastníka. Modul SRK poskytuje izolovaný prostor pro ukládání klíčů a poskytuje přístup revocability, pokud se zařízení nebo TPM prodává. Vypadá to jako při přesunu do nové domu: převzetí vlastnictví mění zámky na dveřích a zničí veškerý nábytek vlevo předchozími vlastníky (SRK), ale nemůžete změnit adresu House (EK).

Jakmile je zařízení nastavené a připravené k použití, bude mít k dispozici jak EK, tak i SRK.

![Převzetí vlastnictví čipu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Jedna Poznámka k převzetí vlastnictví čipu TPM: přebírání vlastnictví čipu TPM závisí na mnoha věcí, včetně výrobce čipu TPM, používané sady nástrojů TPM a operačního systému zařízení. Pokud chcete převzít vlastnictví, postupujte podle pokynů, které jsou důležité pro váš systém.

Služba Device Provisioning používá veřejnou část EK (EK_pub) k identifikaci a registraci zařízení. Dodavatel zařízení může EK_pub během výroby nebo finálního testování a nahrajte EK_pub do služby zřizování, aby se zařízení rozpoznalo při připojení ke zřízení. Služba Device Provisioning nekontroluje modul pro vytváření dat nebo vlastníka, takže když "maže", čip TPM vymaže zákaznická data, ale v případě, že se připojí ke zřízení, zařízení bude i nadále rozpoznáno službou Device Provisioning.

## <a name="detailed-attestation-process"></a>Podrobný proces ověření identity

Když se zařízení s čipem TPM poprvé připojí ke službě Device Provisioning, služba nejdřív zkontroluje zadaný EK_pub proti EK_pub uloženému v seznamu registrací. Pokud se EK_pubs neshodují, zařízení se nepovoluje zřídit. Pokud EK_pubs souhlasí, služba pak vyžaduje, aby zařízení prokázalo vlastnictví soukromé části EK prostřednictvím výzvy pomocí hodnoty nonce, což je zabezpečená výzva, která se používá k prokázání identity. Služba Device Provisioning vygeneruje hodnotu nonce a pak ji zašifruje s modulem SRK a potom EK_pub, které jsou v zařízení během počátečního volání registrace k dispozici. ČIP TPM vždycky udržuje soukromou část zabezpečení EK. Tím zabráníte padělání a zajistíte, aby se tokeny SAS bezpečně zřídily autorizovaným zařízením.

Pojďme si podrobně projít proces ověření identity.

### <a name="device-requests-an-iot-hub-assignment"></a>Zařízení požádá o přiřazení IoT Hub.

Nejdřív se zařízení připojí ke službě Device Provisioning a žádostem o zřízení. V takovém případě zařízení poskytuje službu s ID registrace, rozsahem ID a EK_pub a SRK_pub z čipu TPM. Služba předá zašifrovanou hodnotu NONCE zpátky do zařízení a požádá zařízení, aby dešifroval hodnotu nonce a použila ho k podepsání tokenu SAS pro opětovné připojení a dokončení zřizování.

![Zřizování požadavků na zařízení](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Hodnota nonce – výzva

Zařízení přebírá hodnoty nonce a používá privátní části EK a SRK k dešifrování hodnoty nonce do čipu TPM. pořadí šifrování hodnoty nonce deleguje vztah důvěryhodnosti od typu EK, který je neproměnlivý, na modul SRK, který se může změnit, pokud nový vlastník převezme vlastnictví čipu TPM.

![Dešifrování hodnoty nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Ověření hodnoty nonce a přijetí přihlašovacích údajů

Zařízení pak může podepsat token SAS pomocí dešifrované hodnoty nonce a znovu navázat spojení se službou Device Provisioning pomocí podepsaného tokenu SAS. Po dokončení výzvy k vystavení hodnoty nonce služba umožní zařízení zřídit.

![Zařízení znovu naváže připojení ke službě Device Provisioning a ověří vlastnictví EK.](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Další kroky

Nyní se zařízení připojí k IoT Hub a zůstane v bezpečí, že jsou klíče zařízení bezpečně uložené. Když teď víte, jak služba Device Provisioning bezpečně ověřuje identitu zařízení pomocí čipu TPM, Projděte si následující články, kde najdete další informace:

* [Přečtěte si o konceptech zřizování](about-iot-dps.md#provisioning-process)
* Začněte [používat Automatické zřizování](./quick-setup-auto-provision.md) pomocí sad SDK, aby se tento tok mohl postarat.
