---
title: Zřizování služby - TPM ověření zařízení Azure IoT Hub
description: Tento článek obsahuje koncepční přehled toku TPM ověření pomocí služby zřizování zařízení IoT.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 90f41e56f8e95584959576d3e5ad837f4774048a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629078"
---
# <a name="tpm-attestation"></a>Osvědčení TPM

Služba zřizování zařízení IoT Hub je služba Pomocník pro IoT Hub, který použijete ke konfiguraci zařízení nula touch zřizování do zadané služby IoT hub. Se službou zřizování zařízení můžete zřídit miliony zařízení zabezpečeným způsobem.

Tento článek popisuje proces ověření identity při použití [TPM](./concepts-device.md). Čip TPM zkratka Trusted Platform Module a je typu modulu hardwarového zabezpečení (HSM). Tento článek předpokládá použití diskrétní, firmwaru, nebo integrovaný TPM. Software emulované čipy TPM jsou vhodné pro vytváření prototypů nebo testování, ale jejich neposkytuje stejnou úroveň zabezpečení jako diskrétní, firmwaru, nebo provést integrované čipy TPM. Nedoporučujeme používání softwaru čipy TPM v produkčním prostředí. [Další informace](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) o typech čipy TPM.

Tento článek platí pouze pro zařízení čip TPM 2.0 pomocí klíče podporu HMAC a jejich ověřovacího klíče. Není nastaven pro zařízení pro ověřování pomocí certifikátů X.509. Čip TPM je napříč celým průmyslem, standard ISO z Trusted Computing Group a si můžete přečíst více o čipu TPM na [dokončení specifikace TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) nebo [specifikace ISO/IEC 11889](https://www.iso.org/standard/66510.html). Tento článek také předpokládá, že jste obeznámeni s páry klíčů veřejný a privátní a jak se používají pro šifrování.

Zařízení zařízení zřizování služby sady SDK zpracovat vše, který je popsaný v tomto článku pro vás. Není nutné, abyste mohli implementovat žádné další, pokud používáte sady SDK v zařízeních. Tento článek vám pomůže pochopit koncepčně co se děje s čip TPM zabezpečení při zřizuje vaše zařízení a proč je tak zabezpečení.

## <a name="overview"></a>Přehled

Čipy TPM použít takzvaný ověřovací klíč (EK) jako zabezpečené kořenové důvěryhodnosti. Ověřovací klíč je jedinečné pro TPM a změnou v podstatě do nové změny zařízení.

Neexistuje jiný typ klíče, že čipy TPM mají, názvem kořenový klíč úložiště (SRK). SRK může být generována vlastníka čipu TPM po trvá vlastnictví čipu TPM. Převzetí vlastnictví čipu TPM je způsob TPM specifické o tom, že "někdo nastaví heslo modul hardwarového zabezpečení." Pokud k novému vlastníkovi se prodává TPM zařízení, může trvat nový vlastník vlastnictví čipu TPM generovat nové SRK. Nové generace SRK zajistí, že předchozího vlastníka nelze použít čip TPM. Protože SRK je jedinečné pro vlastníka čipu TPM, SRK slouží k zapečetění do čipu TPM, samotné daného vlastníka data. SRK poskytuje izolovaného prostoru pro vlastníka k ukládání svých klíčů a poskytuje revocability přístup, pokud se prodává zařízení nebo TPM. Je třeba Přesun do nového úklidové: převzetí vlastnictví je změna zámky na dveře a zničení všech nábytek zanechaný předchozí vlastníky (SRK), ale nelze změnit na adresu úklidové (EK).

Jakmile zařízení byla nastavena a připravena k použití, bude mít EK i SRK k dispozici pro použití.

![Převzetí vlastnictví čipu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

OneNote na převzetí vlastnictví čipu TPM: převzetí vlastnictví čipu TPM, závisí na celou řadu věcí, včetně výrobce čipu TPM, sadu nástrojů TPM používá a operačního systému zařízení. Postupujte podle pokynů relevantní pro váš systém převzít vlastnictví.

Služba zřizování zařízení používá veřejnou část EK (EK_pub) k identifikaci a registraci zařízení. Výrobce zařízení můžete číst EK_pub během výrobce nebo konečné testování a odeslání EK_pub zřizování službě tak, aby zařízení bude rozpoznán, když se připojuje ke zřízení. Službu zřizování zařízení nekontroluje SRK nebo vlastníka, takže "zrušení" čipu TPM vymaže data zákazníků, ale ověřovací klíč (a další data od dodavatele) je zachována a zařízení bude rozpoznán stále pomocí služby zřizování zařízení, když se připojuje ke zřízení.

## <a name="detailed-attestation-process"></a>Podrobné ověření procesu

Pokud zařízení s čipem TPM poprvé připojí ke službě zřizování zařízení, službu nejprve zkontroluje zadaný EK_pub proti EK_pub uložený v seznamu registrace. Pokud EK_pubs neshodují, není povoleno zřízení zařízení. Pokud EK_pubs neodpovídají, služby pak vyžaduje zařízení k prokázání vlastnictví soukromá část EK prostřednictvím nonce výzvy, což je zabezpečený výzvy používá k prokázání identity. Službu zřizování zařízení generuje hodnotu nonce a pak ji zašifruje pomocí SRK a pak EK_pub, které jsou poskytovány zařízení během volání počáteční registrace. Čip TPM vždy udržuje soukromá část ověřovací klíč zabezpečení. To zabraňuje padělání a zajišťuje, aby se tokeny SAS bezpečně připravené povolená zařízení.

Projděme proces ověření podrobně.

### <a name="device-requests-an-iot-hub-assignment"></a>Zařízení vyžaduje přiřazení IoT Hub

Nejprve zařízení připojí ke službě zřizování zařízení a požadavky na zřízení. Při tom zařízení poskytuje služby s jeho ID registrace, rozsah ID a EK_pub a SRK_pub z čipu TPM. Služba šifrované hodnotu nonce předá zpět do zařízení a požádá zařízení k dešifrování tuto a použít pro přihlášení znovu připojit a dokončení zřizování tokenu SAS.

![Požadavky na zařízení zřizování](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce výzvy

Zařízení přebírá hodnotu nonce a používá privátní části EK a SRK k dešifrování tuto do čipu TPM; pořadí nonce šifrování delegáti vztah důvěryhodnosti z ověřovací klíč, který se nedá změnit, SRK, ve kterém můžete změnit, pokud nový vlastník trvá vlastnictví čipu TPM.

![Dešifrování tuto](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Ověřit tuto a přijímat pověření

Zařízení můžete přihlásit pomocí dešifrovaný hodnotu nonce tokenu SAS a znovu vytvořit připojení k službě zřizování zařízení, pomocí podepsaný token SAS. Služba s otázkou hodnotu Nonce dokončit, umožňuje zařízení zřídit.

![Zařízení se obnoví připojení k distribučních bodů k ověření vlastnictví EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Další postup

Nyní se zařízení připojuje ke službě IoT Hub a přiblížení zabezpečené ve znalostní bázi, že vaše zařízení klíče jsou bezpečně uložené. Teď, když víte, jak službu zřizování zařízení bezpečně ověří identitu zařízení pomocí čipu TPM, projděte si další informace v následujících článcích:

* [Další informace o všech koncepty v automatické zřizování](./concepts-auto-provisioning.md)
* [Začněte používat automatické zřizování](./quick-setup-auto-provision.md) pomocí sady SDK, která se postará o toku.
