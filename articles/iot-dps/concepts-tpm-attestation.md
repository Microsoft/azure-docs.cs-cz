---
title: Služba zřizování zařízení služby Azure IoT Hub – atestace čipu TPM
description: Tento článek obsahuje koncepční přehled toku osvědčení Čipu TPM pomocí služby DPS (IoT Device Provisioning Service).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975274"
---
# <a name="tpm-attestation"></a>Osvědčení TPM

Služba zřizování zařízení služby IoT Hub je pomocná služba pro službu IoT Hub, kterou používáte ke konfiguraci zřizování zařízení bez dotykového ovládání do určeného centra IoT Hub. Se službou Zřizování zařízení můžete zřídit miliony zařízení bezpečným způsobem.

Tento článek popisuje proces ověření identity při použití [čipu TPM](./concepts-device.md). TPM je zkratka pro Trusted Platform Module a je typem modulu hardwarového zabezpečení (HSM). Tento článek předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Softwarově emulované tpms jsou vhodné pro prototypování nebo testování, ale neposkytují stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované TPM. Nedoporučujeme používat software TPM ve výrobě. Další informace o typech tpms naleznete [v tématu Stručný úvod do čipu TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Tento článek je relevantní pouze pro zařízení používající čip TPM 2.0 s podporou klíče HMAC a jejich klíči pro potvrzení. Není určen pro zařízení používající certifikáty X.509 pro ověřování. Čip TPM je průmyslový standard ISO od skupiny Trusted Computing Group a o čipu TPM si můžete přečíst více na [kompletní specifikaci TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) nebo [specifikaci ISO/IEC 11889](https://www.iso.org/standard/66510.html). Tento článek také předpokládá, že jste obeznámeni s páry veřejného a soukromého klíče a jak se používají pro šifrování.

Sady SDK zařízení device Provisioning Service zpracovávají vše, co je popsáno v tomto článku. Pokud na svých zařízeních používáte sady SDK, není nutné implementovat nic dalšího. Tento článek vám pomůže koncepčně pochopit, co se děje s bezpečnostním čipem Čip tpm, když vaše zařízení obsahuje a proč je tak bezpečné.

## <a name="overview"></a>Přehled

Tpms použít něco, co nazývá ověřovací klíč (EK) jako zabezpečený kořen důvěryhodnosti. EK je jedinečný pro Čip TPM a jeho změna v podstatě změní zařízení na nové.

Existuje jiný typ klíče, který mají TPMs, volal klíč kořenového úložiště (SRK). SRK může být generovánvlastníkem čipu TPM poté, co převezme vlastnictví čipu TPM. Převzetí vlastnictví čipu TPM je specifický způsob čipu TPM, který říká "někdo nastaví heslo k přístupu k speciálnímu zabezpečení". Pokud je zařízení TPM prodáno novému vlastníkovi, může nový vlastník převzít vlastnictví čipu TPM a vygenerovat novou srk. Nová generace SRK zajišťuje, že předchozí vlastník nemůže čip TPM používat. Vzhledem k tomu, že srk je jedinečný pro vlastníka čipu TPM, lze ji použít k zapečetění dat do samotného čipu TPM pro tohoto vlastníka. SRK poskytuje izolovanému prostoru pro vlastníka uložit své klíče a poskytuje přístup odvolatelnost, pokud je zařízení nebo TPM prodává. Je to jako přestěhovat se do nového domu: převzetí vlastnictví mění zámky na dveřích a ničí veškerý nábytek, který zanechali předchozí majitelé (SRK), ale nemůžete změnit adresu domu (EK).

Jakmile je zařízení nastaveno a připraveno k použití, bude mít k dispozici ek i SRK.

![Převzetí vlastnictví čipu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Jedna poznámka o převzetí vlastnictví čipu TPM: Převzetí vlastnictví čipu TPM závisí na mnoha věcech, včetně výrobce čipu TPM, sady používaných nástrojů Čipu TPM a operačního serveru zařízení. Postupujte podle pokynů týkajících se vašeho systému převzít vlastnictví.

Služba zřizování zařízení používá veřejnou část EK (EK_pub) k identifikaci a registraci zařízení. Dodavatel zařízení může číst EK_pub během výroby nebo závěrečné zkoušky a nahrát EK_pub do zřizovací služby tak, aby zařízení bude rozpoznáno, když se připojí k zřizování. Služba zřizování zařízení nekontroluje srk nebo vlastníka, takže "vymazání" čipu TPM vymaže zákaznická data, ale EK (a další data dodavatele) je zachována a zařízení bude stále rozpoznáno službou Device Provisioning Service, když se připojí k poskytování.

## <a name="detailed-attestation-process"></a>Podrobný proces atestace

Když se zařízení s čipem TPM poprvé připojí ke službě Zřizování zařízení, služba nejprve zkontroluje poskytovanou EK_pub proti EK_pub uloženým v seznamu registrací. Pokud se EK_pubs neshodují, zařízení není povoleno zřídit. Pokud EK_pubs se shodují, služba pak vyžaduje, aby zařízení prokázalo vlastnictví soukromé části EK prostřednictvím výzvy nonce, což je bezpečná výzva používaná k prokázání identity. Služba zřizování zařízení generuje nonce a pak šifruje s SRK a pak EK_pub, které jsou poskytovány zařízením během počáteční registrace volání. Čip TPM vždy udržuje soukromou část EK zabezpečenou. Tím zabráníte padělání a zajistíte, že tokeny SAS jsou bezpečně zřizovány pro autorizovaná zařízení.

Pojďme si podrobně projít procesem atestace.

### <a name="device-requests-an-iot-hub-assignment"></a>Zařízení požaduje přiřazení služby IoT Hub

Nejprve se zařízení připojí ke službě Zřizování zařízení a požádá o zřízení. Přitom zařízení poskytuje službě své ID registrace, rozsah ID a EK_pub a SRK_pub z čipu TPM. Služba předá šifrované nonce zpět do zařízení a požádá zařízení k dešifrování nonce a použít k podepsání tokenu SAS pro opětovné připojení a dokončení zřizování.

![Zřizování požadavků na zařízení](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce výzva

Zařízení převezme nonce a použije soukromé části EK a SRK k dešifrování nonce do čipu TPM; pořadí nonce šifrování deleguje vztah důvěryhodnosti z EK, což je neměnné, na SRK, které se může změnit, pokud nový vlastník převezme vlastnictví čipu TPM.

![Dešifrování nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Ověření nonce a přijetí přihlašovacích údajů

Zařízení pak může podepsat token SAS pomocí dešifrovaného nonce a obnovit připojení ke službě device provisioning service pomocí podepsaného tokenu SAS. Po dokončení výzvy Nonce umožňuje služba zařízení zřídit.

![Zařízení obnoví připojení ke službě Device Provisioning Service za účelem ověření vlastnictví EK.](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Další kroky

Teď se zařízení připojí k IoT Hubu a budete mít s vědomím, že klíče vašich zařízení jsou bezpečně uložené. Teď, když víte, jak služba zřizování zařízení bezpečně ověřuje identitu zařízení pomocí čipu TPM, podívejte se na následující články, kde se dozvíte další:

* [Informace o všech konceptech v automatickém zřizování](./concepts-auto-provisioning.md)
* [Můžete začít používat automatické zřizování](./quick-setup-auto-provision.md) pomocí sad SDK se postaráte o tok.
