---
title: Zabezpečení zařízení Microsoft Azure Data Box – přehled | Microsoft Docs
description: Popisuje Azure Data Box funkce zabezpečení v zařízení, službě a datech, která se nachází v Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655488"
---
# <a name="azure-data-box-security-and-data-protection"></a>Zabezpečení a ochrana dat v zařízení Azure Data Box

Data Box představuje bezpečné řešení pro přenos dat díky tomu, že prohlížet, upravovat a mazat data mohou jen oprávněné entity. Tento článek popisuje funkce zabezpečení zařízení Azure Data Box, které pomáhají chránit všechny komponenty řešení Data Box i v něm uložená data.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Tok dat přes jednotlivé komponenty

Řešení Microsoft Azure Data Box se skládá ze čtyř hlavních komponent, které spolu navzájem spolupracují:

- **Služba Azure Data Box hostovaná v Azure** – služba pro správu, kterou použijete pro vytváření pořadí a konfiguraci zařízení a sledování průběhu operací.
- **Zařízení Data Box** – zařízení pro přenos, které vám bude doručeno za účelem přenosu vašich místních dat do Azure.
- **Klienti/hostitelé připojení k zařízení** – klienti ve vaší infrastruktuře připojující se k zařízení Data Box a obsahující data, která je třeba přenést a chránit.
- **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží. Toto umístění obvykle je účet úložiště propojený s prostředkem Azure Data Box, který jste vytvořili.

Následující diagram znázorňuje tok dat prostřednictvím Azure Data Box řešení z místního prostředí do Azure a různé funkce zabezpečení, které jsou k disřadě jako data v rámci řešení. Tento tok je určen pro pořadí importu Data Box.

![Data Box import zabezpečení](media/data-box-security/data-box-security-import.png)

Následující diagram je určen pro pořadí exportu Data Box.

![Data Box exportovat zabezpečení](media/data-box-security/data-box-security-export.png)

Jak data přecházejí přes toto řešení, události se zaznamenávají a generují se protokoly. Další informace najdete tady:

- [Sledování a protokolování událostí pro Azure Data box importu objednávek](data-box-logs.md).
- [Sledování a protokolování událostí pro Azure Data Box exportní objednávky](data-box-export-logs.md)

## <a name="security-features"></a>Funkce zabezpečení

Data Box představuje bezpečné řešení pro přenos dat díky tomu, že prohlížet, upravovat a mazat data mohou jen oprávněné entity. Bezpečnostní funkce tohoto řešení se týkají disku a související služby a zajišťují maximální bezpečí uložených dat.

### <a name="data-box-device-protection"></a>Ochrana zařízení Data Box

Zařízení Data Box je chráněno následujícími funkcemi.

- Odolné pouzdro zařízení, které odolá nárazům, nešetrné přepravě a vnějším podmínkám. 
- Hardwarová a softwarová detekce nedovolené manipulace, která zabrání dalšímu používání zařízení.
- Běží pouze na softwaru specifickém pro Data Box.
- Spouští se v uzamčeném stavu.
- Řídí přístup zařízení prostřednictvím klíče k odemknutí zařízení. Tento klíč je chráněn šifrovacím klíčem. K ochraně klíče můžete použít svůj vlastní klíč spravovaný zákazníkem. Další informace najdete v tématu [použití klíčů spravovaných zákazníkem v Azure Key Vault pro Azure Data box](data-box-customer-managed-encryption-key-portal.md).
- Přístupové přihlašovací údaje ke kopírování na zařízení a ze zařízení. Každý přístup k stránce s **přihlašovacími údaji zařízení** v Azure Portal se zaznamená do protokolu [aktivit](data-box-logs.md#query-activity-logs-during-setup).
- Pro přístup k zařízení a sdílení můžete použít vlastní hesla. Další informace najdete v tématu [kurz: Azure Data box Order](data-box-deploy-ordered.md).

### <a name="establish-trust-with-the-device-via-certificates"></a>Navázání vztahu důvěryhodnosti se zařízením pomocí certifikátů

Data Box zařízení vám umožňuje přenášet vlastní certifikáty a instalovat je, které se použijí pro připojení k místnímu webovému uživatelskému rozhraní a úložišti objektů BLOB. Další informace najdete v tématu [použití vlastních certifikátů se zařízeními data box a data box Heavy](data-box-bring-your-own-certificates.md).

### <a name="data-box-data-protection"></a>Ochrana dat na zařízení Data Box

Data, která se na zařízení Data Box ukládají a která se z něho kopírují, jsou chráněna následujícími funkcemi:

- 256bitové šifrování AES pro neaktivní uložená data. V prostředí s vysokou úrovní zabezpečení můžete použít softwarové šifrování s dvojitým zabezpečením. Další informace najdete v tématu [kurz: Azure Data box Order](data-box-deploy-ordered.md).
- Pro přenášená data je možné použít šifrované protokoly. K ochraně dat při kopírování z vašich datových serverů doporučujeme použít protokol SMB 3,0 se šifrováním.
- Po dokončení nahrávání do Azure se zabezpečí mazání dat ze zařízení. Výmaz dat je v souladu s pokyny v [dodatku A pro jednotky pevného disku ATA v NIST standardech 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). Událost mazání dat je zaznamenána v [historii objednávky](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Ochrana služby Data Box

Služba Data Box Disk je chráněná následujícími funkcemi.

- Přístup ke službě Data Box vyžaduje, aby vaše organizace měla předplatné Azure, které zahrnuje Data Box. Vaše předplatné určuje funkce, které máte na webu Azure Portal dostupné.
- Protože služba Data Box je hostovaná v Azure, je chráněna funkcemi zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- Přístup k pořadí Data Box lze řídit pomocí rolí Azure. Další informace najdete v tématu [nastavení řízení přístupu pro pořadí data box](data-box-logs.md#set-up-access-control-on-the-order) .
- Služba Data Box ukládá heslo k odemknutí, které se používá k odemknutí zařízení ve službě.
- Služba Data Box ukládá údaje o objednávce a stavu přenosu v úložišti služby. Tyto informace se odstraní při smazání objednávky.

## <a name="managing-personal-data"></a>Správa osobních údajů

Azure Data Box shromažďuje a zobrazuje osobní údaje v následujících klíčových instancích služby:

- **Nastavení oznámení** – při vytvoření objednávky můžete nastavit e-mailovou adresu uživatele v rámci nastavení oznámení. Tyto údaje může prohlížet pouze správce. Tyto údaje se odstraní, jakmile úloha dosáhne konečného stavu nebo když objednávku smažete.

- **Podrobnosti objednávky** – po vytvoření objednávky se v Azure Portal uloží dodací adresa, e-mail a kontaktní informace uživatelů. Uložené údaje zahrnují:

  - Jméno kontaktu
  - Telefonní číslo
  - E-mail
  - Ulice a číslo
  - City (Město)
  - PSČ
  - Stav
  - Provincie/kraj/oblast
  - Číslo účtu dopravce
  - Sledovací číslo zásilky

    Údaje objednávky služba Data Box smaže po dokončení úlohy nebo když objednávku smažete.

- **Dodací adresa** – po objednání služba Data Box předá dodací adresu externímu dopravci, jako je například UPS nebo DHL. 

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Referenční pokyny pro zabezpečení

V zařízení Data Box jsou implementovány následující pokyny pro zabezpečení:

|Pokyn   |Description   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Ochrana před vodou a prachem         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Odolnost před nešetrným zacházením při přepravě          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Bezpečná aktualizace firmwaru         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Ochrana dat         |
|Dodatek A, pro jednotky pevného disku ATA v [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Sanitizace dat         |

## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky zařízení Data Box](data-box-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-limits.md).
- Rychle nasaďte [Azure Data Box](data-box-quickstart-portal.md) na webu Azure Portal.
