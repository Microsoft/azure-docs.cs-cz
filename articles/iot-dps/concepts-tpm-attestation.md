---
title: Azure IoT Hub Device Provisioning Service – ověření identity čipem TPM
description: Tento článek obsahuje přehled toku TPM ověření pomocí služby IoT Device Provisioning.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: cb763327eb292feb9d58fb21b1ca808a3f2909aa
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058051"
---
# <a name="tpm-attestation"></a>Osvědčení TPM

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, který použijete ke konfiguraci plně automatizované zřizování zařízení pro určité Centrum IoT. Ve službě Device Provisioning Service můžete zřizovat miliony zařízení bezpečným způsobem.

Tento článek popisuje postup ověření identity, při použití [TPM](./concepts-device.md). TPM zkratka Trusted Platform Module a k typu modulu hardwarového zabezpečení (HSM). Tento článek předpokládá používáte samostatné, firmwaru, nebo integrované čipu TPM. Software emulované čipy TPM se dobře hodí pro vytváření prototypů nebo testování, ale se nebude poskytovat stejnou úroveň zabezpečení jako samostatné, firmwaru, nebo proveďte integrované čipy TPM. Nedoporučujeme používání softwaru čipy TPM v produkčním prostředí. Další informace o typech čipy TPM, najdete v části [A stručný úvod do čipu TPM](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Tento článek platí pouze pro zařízení TPM 2.0 pomocí HMAC podporu klíčů a jejich ověřovací klíče. Není pro zařízení pomocí certifikátů X.509 pro ověřování. Čip TPM je globální standard ISO od Trusted Computing Group a další informace o čipu TPM v [kompletní specifikace TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) nebo [specifikace ISO/IEC 11889](https://www.iso.org/standard/66510.html). Tento článek také předpokládá, že máte zkušenosti s párů veřejných a privátních klíčů a jak se používají pro šifrování.

Zařízení služby Azure Device Provisioning sady SDK zpracovat vše, který je popsaný v tomto článku pro vás. Není nutné pro vás k implementaci žádné další, pokud používáte sady SDK na vašich zařízeních. Tento článek vám pomůže pochopit koncepčně co se děje s čipem TPM zabezpečení při zabezpečit vaše zařízení předpisy a proč je to.

## <a name="overview"></a>Přehled

Čipy TPM použijte ověřovací klíč (EK) s názvem jako zabezpečeného kořenového vztahu důvěryhodnosti. Ověřovací klíč je jedinečné pro TPM a to v podstatě změna zařízení do nové.

Existuje jiný typ klíče, že čipy TPM mají, názvem úložiště kořenového klíče (SRK Key). SRK mohou být generovány podle vlastníka čipu TPM, když ji převezme vlastnictví čipu TPM. Převzetí vlastnictví čip TPM je tak, jak TPM specifické o tom, že "uživatel nastaví heslo na modul hardwarového zabezpečení." Pokud zařízení TPM se prodává na nového vlastníka, nový vlastník může převzít vlastnictví generovat nové SRK čipu TPM. Nová generace SRK zajišťuje že předchozího vlastníka nelze použít čip TPM. Vzhledem k tomu, SRK jsou jedinečné pro vlastníka čipu TPM, SRK umožňuje data zapečetění do čipu TPM, samotný pro tohoto vlastníka. SRK poskytuje izolovaný prostor pro vlastníka k uložení svých klíčů a poskytuje revocability přístup v případě, že se prodává TPM na zařízení. Je to jako přesun do nového house: převzetí vlastnictví je změna zámky u dveří a zničení všech nábytek zanechaný předchozí vlastníky (SRK), ale nemůže změnit adresu house (EK).

Po nastavení zařízení a připravené k použití, bude mít EK a SRK k dispozici pro použití.

![Převzetí vlastnictví čip TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

OneNote na převzetí vlastnictví čipu TPM: převzetí vlastnictví čip TPM závisí na mnoha faktorech, včetně výrobce čipu TPM, sadu nástrojů pro TPM se používají a operační systém zařízení. Postupujte podle pokynů relevantní pro váš systém převzít vlastnictví.

Služby Device Provisioning Service používá veřejnou část EK (EK_pub) k identifikaci a registrace zařízení. Výrobce zařízení můžete přečíst EK_pub při výrobě nebo konečné testování a nahrát EK_pub ke službě zřizování tak, aby zařízení bude rozpoznán, když se připojuje ke zřízení. Služby Device Provisioning nekontroluje SRK nebo vlastník, takže "vymazání" čipu TPM vymaže zákaznická data, ale klíče (a další data dodavatele) je zachována a zařízení bude stále rozpoznán ve službě Device Provisioning při připojení ke zřízení.

## <a name="detailed-attestation-process"></a>Podrobné ověření procesu

Když s čipem TPM zařízení poprvé připojí ke službě Device Provisioning, službu nejprve zkontroluje zadaná EK_pub proti EK_pub uložené v seznamu registrací. Pokud EK_pubs neshodují, není povoleno zřízení zařízení. Pokud se shodují EK_pubs, služba pak vyžaduje, aby zařízení prokázat, že vlastnictví soukromá část EK prostřednictvím nonce výzvy, která výzvou je i zabezpečení používá k prokázání identity. Služby Device Provisioning generuje hodnotu nonce a pak ji zašifruje pomocí SRK a pak EK_pub, které jsou k dispozici v zařízení během volání počáteční registraci. Čip TPM vždy udržuje části privátního klíče zabezpečené. To zabraňuje padělání a zajistí, že tokeny SAS bezpečně připravené autorizovaných zařízení.

Projděme si podrobně proces ověření.

### <a name="device-requests-an-iot-hub-assignment"></a>Zařízení vyžaduje přiřazení služby IoT Hub

Nejprve zařízení připojí ke službě Device Provisioning a požadavky na zřízení funkce. Přitom zařízení poskytuje službu s jeho ID registrace, rozsahem ID EK_pub a SRK_pub čipu TPM. Služba šifrovaná hodnota nonce předá zpět do zařízení a žádostí zařízení a dešifrovat hodnotu nonce, který budete používat k podpisu tokenu SAS, který chcete znovu připojit a zřizování dokončit.

![Požadavky na zařízení zřizování](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce výzvy

Zařízení přebírá hodnotu nonce a dešifrovat hodnotu nonce do čipu TPM; pomocí soukromé části SRK a EK pořadí nonce šifrování delegáti vztah důvěryhodnosti z EK, který je neměnný, SRK, který může změnit v případě, že nový vlastník převezme vlastnictví čipu TPM.

![Hodnota nonce dešifrování](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Ověřte hodnotu nonce a přijímat přihlašovací údaje

Zařízení můžete podepsat token SAS pomocí dešifrovaný nonce a znovu vytvořit připojení ke službě Device Provisioning pomocí podepsaný token SAS. Služba s otázkou, Hodnota Nonce dokončeno, umožňuje zařízení pro zřízení.

![Zařízení se obnoví připojení do služby Device Provisioning můžete ověřit vlastnictví EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Další postup

Teď se zařízení připojuje ke službě IoT Hub a umístíte zabezpečení ve znalostní bázi, že vaše zařízení klíče jsou bezpečně uložené. Teď, když víte, jak do služby Device Provisioning bezpečně ověří identitu zařízení pomocí čipu TPM, přečtěte si další informace v následujících článcích:

* [Seznamte se s koncepty automatického zřizování](./concepts-auto-provisioning.md)
* [Začínáme s využitím automatického zřizování](./quick-setup-auto-provision.md) pomocí sad SDK, která se stará o toku.
