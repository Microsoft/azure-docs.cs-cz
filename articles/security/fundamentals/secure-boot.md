---
title: Zabezpečené spouštění firmwaru – zabezpečení Azure
description: Technický přehled zabezpečeného spouštění firmwaru Azure
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557663"
---
# <a name="secure-boot"></a>Zabezpečené spouštění

Zabezpečené spouštění je funkce [rozhraní UEFI (Unified Extensible Firmware Interface)](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI), která před načtením vyžaduje, aby byly před načtením ověřeny všechny firmware a softwarové komponenty nízké úrovně. Při spuštění nástroje se zabezpečeným spouštěním UEFI kontroluje podpis každého spouštěcího softwaru, včetně ovladačů firmwaru UEFI (označuje se také jako možnosti ROM), aplikací rozhraní EFI (Extensible Firmware Interface) a ovladačů a binárních souborů operačního systému. Pokud signatury platí nebo důvěřuje výrobci OEM (Original Equipment Manufacturer), spustí se počítač a firmware poskytne řízení operačnímu systému.

## <a name="components-and-process"></a>Komponenty a procesy

Zabezpečené spouštění závisí na těchto důležitých součástech:

- Klíč platformy (PK) – naváže vztah důvěryhodnosti mezi vlastníkem platformy (Microsoft) a firmwarem. Veřejná polovina je PKpub a soukromá polovina je PKpriv.
- Databáze klíčů pro zápis klíčů (KEK) – vytvoří vztah důvěryhodnosti mezi operačním systémem a firmwarem platformy. Veřejná polovina je KEKpub a soukromá polovina je KEKpriv.
- Signature Database (DB) – obsahuje hodnoty Digest pro důvěryhodné podepsané (veřejné klíče a certifikáty) modulů firmwaru a kódu softwaru autorizované k interakci s firmwarem platforem.
- Odvolaný podpis databáze (DBX) – obsahuje odvolané výtahy kódových modulů, které byly identifikovány jako škodlivé, zranitelné, napadené nebo nedůvěryhodné. Pokud je hodnota hash v signatuře DB a v databázi odvolaných podpisů, má databáze odvolaných podpisů stejný předchůdce.

Následující obrázek a proces vysvětlují, jak jsou tyto komponenty aktualizovány:

![Diagram, který zobrazuje součásti zabezpečeného spuštění.](./media/secure-boot/secure-boot.png)

Výrobce OEM ukládá v době výroby výtahy zabezpečeného spuštění na nestálé paměti RAM počítače (NV – RAM).

1. Signatura databáze (DB) se naplní pomocí podepisujících nebo obrazových hodnot hash aplikací UEFI, zavaděčů operačního systému (například zavaděče operačního systému Microsoft nebo správce spouštění) a důvěryhodných ovladačů rozhraní UEFI.
2. Odvolané databáze (DBX) se naplní pomocí algoritmů Digest modulů, které už nejsou důvěryhodné.
3. Databáze klíče pro zápis klíčů (KEK) je naplněná pomocí podpisových klíčů, které se dají použít k aktualizaci databáze signatur a odvolaných podpisů databáze. Databáze je možné upravovat prostřednictvím aktualizací, které jsou podepsané správným klíčem nebo prostřednictvím aktualizací, fyzicky přítomný autorizovaný uživatel pomocí nabídek firmwaru.
4. Poté, co byly přidány databáze DB, dbx a KEK a dokončena finální ověření a testování firmwaru, výrobce OEM zamkne firmware z úprav a vygeneruje klíč platformy (PK). PK lze použít k podepsání aktualizací KEK nebo k vypnutí zabezpečeného spouštění.

V každé fázi procesu spouštění jsou vypočítány výtahy firmwaru, zaváděcího programu, operačního systému, ovladačů jádra a dalších artefaktů spouštěcího řetězu a jsou porovnány s přijatelnými hodnotami. Není povoleno načíst firmware a software, které jsou zjištěny jako nedůvěryhodné. Proto je možné zablokovat vkládání malwaru na nízké úrovni nebo malwarové útoky před spuštěním.

## <a name="secure-boot-on-the-azure-fleet"></a>Zabezpečené spouštění v rámci loďstva Azure
V dnešní době je každý počítač, který je připojen a nasazený do výpočetního loďstva Azure pro hostování úloh zákazníků, dodáván z podlahových podlah s povoleným zabezpečeným spouštěním. Cílené nástroje a procesy jsou v každé fázi v hardwarovém sestavení a kanálu pro integraci, aby se zajistilo, že se povolení zabezpečeného spouštění nevrátí buď omylem, nebo škodlivým záměrem.

Ověření, zda jsou hodnoty Digest DB a DBX správné, zajistí:

- Zaváděcí program pro spouštění se nachází v jedné z položek databáze.
- Podpis zaváděcího programu pro spouštění je platný.
- Spuštění hostitele s důvěryhodným softwarem

 Ověřením signatur KEKpub a PKpub můžeme potvrdit, že pouze důvěryhodné strany mají oprávnění pro úpravu definic, které software považuje za důvěryhodný. A konečně tím, že zajistíte, že zabezpečené spouštění je aktivní, můžeme ověřit, jestli se tyto definice vynutily.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co máme k zajištění integrity a zabezpečení platformy, najdete v těchto tématech:

- [Zabezpečení firmwaru](firmware.md)
- [Měřené ověření spouštění a ověřování hostitele](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)
- [Zabezpečení hypervisoru](hypervisor.md)