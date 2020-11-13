---
title: Ověřování firmwaru a ověření identity hostitele – Azure Security
description: Technický přehled ověřování firmwaru Azure s měřením spouštění a ověření identity hostitele.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 73ae811c17a578cafc557b0cda9e98b101dd5c03
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557651"
---
# <a name="measured-boot-and-host-attestation"></a>Měřené ověření spouštění a ověřování hostitele
Tento článek popisuje, jak společnost Microsoft zajišťuje integritu a zabezpečení hostitelů prostřednictvím měřeného spuštění a ověření identity hostitele.

## <a name="measured-boot"></a>Měřené spuštění

Čip TPM ( [Trusted Platform Module](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node) ) je neoprávněně zabezpečená součást auditu, která poskytuje firmware dodaný důvěryhodnou třetí stranou. Protokol konfigurace spouštění obsahuje měření zřetězená algoritmem hash zaznamenaná v registrech konfigurace platforem (PCR) v případě, že hostitel poslední provede sekvenci zavádění. Následující obrázek znázorňuje proces zaznamenávání. Přírůstkové přidání dříve vyhashního měření do hodnoty hash další měření a spuštění algoritmu hash pro sjednocení má za úkol řetězení hodnot hash.

![Diagram, který ukazuje řetězení hodnot hash služby ověření identity hostitele.](./media/measured-boot-host-attestation/hash-chaining.png)

Ověření identity se provádí v případě, že hostitel prokáže svůj stav konfigurace pomocí protokolu konfigurace spuštění (TCGLog). Padělání spouštěcího protokolu je obtížné, protože čip TPM nezveřejňuje své hodnoty PCR Kromě operací čtení a rozšiřování. Kromě toho jsou přihlašovací údaje zadané službou ověření hostitele zapečetěné do konkrétních hodnot PCR. Použití algoritmu hash-řetězování způsobuje neproveditelné falšování nebo nezapečetění přihlašovacích údajů mimo pásmo.

## <a name="host-attestation-service"></a>Služba ověření identity hostitele

Služba ověření identity hostitele je preventivní opatření, které kontroluje, jestli jsou hostitelské počítače důvěryhodné, aby bylo možné pracovat s daty zákazníků nebo úlohami. Služba ověření identity hostuje ověřením prohlášení o dodržování předpisů (ověřitelný důkaz shody hostitele) odesílaných každým hostitelem na zásadu ověření identity (definice stavu zabezpečení). Integrita tohoto systému je zajištěna [kořenovou důvěryhodností](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) poskytovanou čipem TPM.

Služba ověření identity hostitele je k dispozici v každém clusteru Azure v rámci specializovaného uzamknutého prostředí. Uzamčené prostředí obsahuje další služby gatekeeper, které se podílejí na zaváděcím protokolu hostitelského počítače. Infrastruktura veřejných klíčů (PKI) funguje jako prostředník pro ověřování prověřených požadavků na ověření identity a jako vystavitele identity (závisí na úspěšném ověření identity hostitele). Přihlašovací údaje post-Attestation vydané pro hostitele ověřující identity jsou zapečetěné jeho identitou. Pouze žádající hostitel může přihlašovací údaje zapečetit a využít je k získání přírůstkových oprávnění. To brání útokům na střední a falešnou identifikaci.

Pokud hostitel Azure přijde z továrny s chybnou konfigurací zabezpečení nebo je úmyslně poškozen v datacentru, obsahuje jeho TCGLog indikátory ohrožení označenou službou ověření identity hostitele při příštím ověření, což způsobí selhání ověření identity. Selhání ověřování brání službě Azure loďstev v důvěřování problematickém hostiteli. Tato prevence efektivně blokuje veškerou komunikaci do a z hostitele a aktivuje pracovní postup incidentu. Za účelem zjištění hlavních příčin a případného ohrožení bezpečnosti se provádí šetření a podrobná analýza po porážce. Je to až po dokončení analýzy, která je napravovaná, a má příležitost připojit se k Azure loďstva a převzít úlohy pro zákazníky.

Následuje architektura služby ověření identity hostitele v rámci vysoké úrovně:

![Diagram znázorňující architekturu služby ověření identity hostitele.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Měření ověření identity

Následují příklady množství zaznamenaných výsledků v dnešní době.

### <a name="secure-boot-and-secure-boot-keys"></a>Zabezpečené spouštění a klíče zabezpečeného spouštění
Ověřením, že databáze signatur a odvolané signatury databáze jsou správné, služba ověření identity zajišťuje, aby agent klienta posuzuje správný software jako důvěryhodný. Ověřováním podpisů databáze klíčů zápisu veřejných klíčů a klíče veřejné platformy potvrdí služba ověření identity, že pouze důvěryhodné strany mají oprávnění ke změně definicí softwaru, který je považován za důvěryhodný. A konečně tím, že zajistíte, aby zabezpečené spouštění bylo aktivní, Služba ověřování hostitele ověří, že se tyto definice vynutily.

### <a name="debug-controls"></a>Ladit ovládací prvky
Ladicí programy jsou výkonné nástroje pro vývojáře. Přístup neomezený k paměti a dalším příkazům ladění ale může oslabit ochranu dat a integritu systému, pokud je tato úroveň dána nedůvěryhodné straně. Služba ověření identity hostitele zajišťuje, aby při spuštění v produkčním počítači byl zakázán jakýkoli typ ladění.

### <a name="code-integrity"></a>Integrita kódu
[Zabezpečené spouštění](secure-boot.md) UEFI zajišťuje, aby během spouštěcí sekvence mohl běžet jenom důvěryhodný software nízké úrovně. Stejné kontroly se ale také musí použít v prostředí po spuštění pro ovladače a další spustitelné soubory s přístupem v režimu jádra. V tomto případě se k definování ovladačů, binárních souborů a dalších spustitelných souborů považují zásady integrity kódu (CI) zadáním platných a neplatných podpisů. Tyto zásady se vynutily. Porušení zásad generují výstrahy týmu pro odpověď na incidenty zabezpečení k šetření.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co máme k zajištění integrity a zabezpečení platformy, najdete v těchto tématech:

- [Zabezpečení firmwaru](firmware.md)
- [Zabezpečené spouštění](secure-boot.md)
- [Cerberus projektu](project-cerberus.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)
- [Zabezpečení hypervisoru](hypervisor.md)