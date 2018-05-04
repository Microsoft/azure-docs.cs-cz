---
title: "Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace - identifikace a ověření"
description: "FedRAMP webové aplikace automatizace - identifikace a ověření"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Identifikace a ověření (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 řízení IA-1

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Identifikace a ověřování zásady a postupy

**IA-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásadu identifikace a ověření, která řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění identifikace a zásady ověřování a přidružené mechanismy identifikace a ověřování; kontroluje a aktualizuje aktuální zásady identifikace a ověření [přiřazení: organizace definované frekvence]; Identifikace a ověřování postupů a [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Identifikace a ověření zásad úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 řízení IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identifikace a ověření (organizační uživatele)

**IA-2** informace systému, který jednoznačně identifikuje a ověřuje organizační uživatele (nebo procesy, které jednají jménem organizace uživatelů).

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Účty, které jsou vytvořené tento plán, podle kterého mít jedinečné identifikátory. Integrované účty s nejedinečné identifikátory se zakážou nebo odeberou. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 řízení IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identifikace a ověření (organizační uživatele) | Síťový přístup k privilegovaným účtům

**IA-2 (1)** systém informace implementuje vícefaktorového ověřování pro přístup k síti k privilegovaným účtům.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci vícefaktorového ověřování pro přístup k síti k privilegovaným účtům. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 řízení IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identifikace a ověření (organizační uživatele) | Přístup k síti pro účty bez oprávnění

**IA-2 (2)** systém informace implementuje vícefaktorového ověřování pro přístup k síti pro účty bez oprávnění.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci vícefaktorového ověřování pro přístup k síti pro účty bez oprávnění. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 řízení IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identifikace a ověření (organizační uživatele) | Místní přístup k privilegovaným účtům

**IA-2 (3)** systém informace implementuje vícefaktorového ověřování pro místní přístup k privilegovaným účtům.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníka nemají místní přístup k žádným prostředkům systému v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje místního přístupu, pokud se vyžaduje fyzický přístup. Přístup k místní správce musí použít pouze k řešení potíží v případech, kde členský server dochází k problémům sítě a ověřování v doméně, ale nefunguje. <br /> Azure implementuje vícefaktorového ověřování pro místní přístup přes požadované pro fyzický přístup k prostředí mechanismy řízení přístupu. Místností v datových centrech Azure, které obsahují všechny systémy infrastruktury Azure v rámci hranice systému omezeny prostřednictvím různých mechanismů fyzického zabezpečení, včetně požadavek badging přístup k podnikovým čipových karet a biometrických zařízení. Oba způsoby ověřování se vyžadují pro fyzický přístup okamžiku příjem příchozích dat do datového centra Azure colocations. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 řízení IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identifikace a ověření (organizační uživatele) | Místní přístup k účtům bez oprávnění

**IA-2 (4)** systém informace implementuje vícefaktorového ověřování pro místní přístup k účtům bez oprávnění.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníka nemají místní přístup k žádným prostředkům systému v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure zvažuje všechny účty Microsoft Azure Government používá Microsoft Azure Government pracovníky jako privilegovaný. Vícefaktorové ověřování je implementována pro všechny účty Microsoft Azure Government pracovníky pomocí čipové karty a kódy PIN, která zahrnuje místního přístupu. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 řízení IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identifikace a ověření (organizační uživatele) | Ověření skupiny

**IA-2 (5)** organizace vyžaduje jednotlivce ověření při jednotlivých ověřovací data při zaměstnání ověřovací skupiny.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | U prostředků nasadit pomocí tento plán, podle kterého jsou povolené žádné sdílené nebo skupinové účty. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 řízení IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identifikace a ověření (organizační uživatele) | Síťový přístup k privilegovaným účtům - opětovného přehrání odolná

**IA-2 (8)** systém informace implementuje odolné proti opětovného přehrání ověřovací mechanismy, které pro síťový přístup k privilegovaným účtům.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Přístup k prostředkům nasadit pomocí tento plán, podle kterého je chráněn před útoky před zneužitím integrované funkce protokolu Kerberos Azure Active Directory, služby Active Directory a operačního systému Windows. Ověřovací klient zasílá v ověřování protokolem Kerberos, obsahuje další data, například šifrované seznam adres IP, časová razítka klienta a doba platnosti lístku. Pokud je přehrány paket, zkontroluje se časové razítko. Pokud je starší než časové razítko nebo stejný jako předchozí authenticator, paket byl odmítnut. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 řízení IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identifikace a ověření (organizační uživatele) | Přístup k síti bez oprávnění účtům - opětovného přehrání odolná

**IA-2 (9)** systém informace implementuje odolné proti opětovného přehrání ověřovací mechanismy, které pro přístup k síti pro účty bez oprávnění.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Přístup k prostředkům nasadit pomocí tento plán, podle kterého je chráněn před útoky před zneužitím integrované funkce protokolu Kerberos Azure Active Directory, služby Active Directory a operačního systému Windows. Ověřovací klient zasílá v ověřování protokolem Kerberos, obsahuje další data, například šifrované seznam adres IP, časová razítka klienta a doba platnosti lístku. Pokud je přehrány paket, zkontroluje se časové razítko. Pokud je starší než časové razítko nebo stejný jako předchozí authenticator, paket byl odmítnut. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 řízení IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identifikace a ověření (organizační uživatele) | Vzdálený přístup – samostatný zařízení

**IA-2 (11)** informace systému implementuje vícefaktorového ověřování pro vzdálený přístup na privilegovaný a bez oprávnění účty tak, aby jednoho z faktorů zajišťuje odděleně od systému, aby měli přístup zařízení a zařízení splňuje [Přiřazení: organizace definované sílu mechanismus požadavky].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci vícefaktorového ověřování pro přístup k prostředkům nasazené zákazníka vzdáleně. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 řízení IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identifikace a ověření (organizační uživatele) | Přijetí standardu Piv přihlašovacích údajů

**IA-2 (12)** informace systému přijme a elektronicky ověřuje pověření ověřování osobní Identity (PIV).

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za přijetí a ověření přihlašovacích údajů ověřování osobní Identity (PIV). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 řízení IA-3

#### <a name="device-identification-and-authentication"></a>Identifikace zařízení a ověřování

**IA-3** informace systému jednoznačně identifikuje a ověřuje [přiřazení: konkrétní definované organizace a typech zařízení] před vytvořením [výběr (jeden nebo více): místní; vzdálené; sítě] připojení.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci zařízení identifikace a ověření. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 řízení IA-4.a

#### <a name="identifier-management"></a>Identifikátor správy

**IA-4.a** organizace spravuje informace o systému identifikátory tím přijetí autorizace z [přiřazení: organizace definované pracovníky nebo rolí] přiřadit jednotlivcům, identifikátor skupiny, role nebo zařízení.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu identifikátory (tj, jednotlivce, skupiny, rolí a zařízení) pro prostředky zákazníků. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 řízení IA-4.b

#### <a name="identifier-management"></a>Identifikátor správy

**IA-4.b** organizace spravuje informace o systému identifikátory výběrem identifikátor, který identifikuje jednotlivých uživatelů, skupiny, role nebo zařízení.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého vyzve k zadání při nasazení pro zadaný zákazníka identifikátory pro jednotlivé účty.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 řízení IA-4.c

#### <a name="identifier-management"></a>Identifikátor správy

**IA-4.c** organizace spravuje informace o systému identifikátory přiřazením identifikátor určený jednotlivcům, skupiny, role nebo zařízení.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu identifikátory (tj, jednotlivce, skupiny, rolí a zařízení) pro prostředky zákazníků. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 řízení IA-4.d

#### <a name="identifier-management"></a>Identifikátor správy

**IA-4.d** organizace spravuje informace o systému identifikátory tím, že zabrání opětovnému použití identifikátory pro [přiřazení: organizace definované časové období].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Služba Active Directory a místní účty operačního systému Windows jsou přiřazeny identifikátor jedinečný zabezpečení (SID). Azure Active Directory účty přiřazené globálně jedinečný identifikátor objektu. Tato jedinečné ID se nevztahuje opakované použití. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 řízení IA-4e

#### <a name="identifier-management"></a>Identifikátor správy

**IA-4.e** Že organizace spravuje informace o systému identifikátory zakázáním identifikátor po [přiřazení: organizace definované časové období nečinnosti].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje naplánovanou úlohu pro službu Active Directory automaticky zakázat účty po 35 dní nečinnosti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 řízení IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Identifikátor správy | Určení stavu uživatele

**IA-4 (4)** organizace spravuje individuální identifikátory tím jedinečně identifikující každého uživatele jako [přiřazení: organizace definované vlastnosti identifikaci jednotlivých stav].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Active Directory a podpory služby Active Directory, které označuje dodavatelů, dodavatele a dalších typů uživatele pomocí zásady vytváření názvů u jejich identifikátory. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 řízení IA-5.a

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.a** organizace spravuje ověřovací moduly systému informace kontrolou jako součást počáteční authenticator distribuční, identity jednotlivce, skupiny, role nebo zařízení příjem ověřovacích.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu ověřovací data. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 řízení IA-5.b

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.b** organizace spravuje ověřovací moduly systému informace vytvořením obsah ověřovací počáteční data pro ověřovací data definované v organizaci.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Veškerý obsah počáteční authenticator pro účty vytvořené tento plán, podle kterého splňovat požadavky uvedené v IA-5 (1), ověření při zákazník během nasazení.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 řízení IA-5.c

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.c** organizace spravuje ověřovací informace systému data tím zajistíte, že ověřovací data mají dostatečnou sílu mechanismus pro podle zamýšleného použití.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Ověřovací data používá tento plán, podle kterého splňovaly požadavky pro sílu podle požadavků FedRAMP. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 řízení IA-5

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5** že organizace spravuje ověřovací moduly systému informace o vytvoření a implementace správní postupy pro počáteční authenticator distribuci pro ověřovací data ke ztrátě nebo ohrožení zabezpečení nebo je poškozena a odvolání Ověřovací data.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu ověřovací data. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 řízení IA-5.e

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.e** Organizace spravuje ověřovací moduly systému informace tak, že změníte výchozí obsah ověřovací data před instalací systému informace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Všechny ověřovací data pro součásti tento plán, podle kterého se liší od jejich výchozí hodnoty. Ověřovací data jsou během nasazení tohoto řešení zadaný zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 řízení IA-5.f

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.f** organizace spravuje ověřovací moduly systému informace vytvořením minimální a maximální doba života omezení a opakované použití podmínky pro ověřovací data.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu ověřovací data. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 řízení IA-5.g

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.g** že organizace spravuje ověřovací moduly systému informace o změně nebo aktualizujte ověřovací data [přiřazení: organizace definované časové období podle typu authenticator].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny je vytvořit a nakonfigurovat tak, aby implementace omezení doba platnosti hesla (60 dnů). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 řízení IA-5.h

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.h** že organizace spravuje ověřovací moduly systému informace a obsah ověřovací chrání před neoprávněným sdílením a úpravy.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje Key Vault k ochraně obsahu authenticator před neoprávněným sdílením a úpravy. Následující ověřovací data jsou uložené v Key Vault: Azure heslo pro účet nasadit, heslo správce virtuálního počítače, heslo účtu služby SQL Server. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 řízení IA-5.i

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.i** organizace spravuje ověřovací moduly systému informace vyžadováním jednotlivce provést, a s implementovat zařízení, konkrétní bezpečnostní ochrany k ochraně ověřovací data.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje Key Vault k ochraně obsahu authenticator před neoprávněným sdílením a úpravy. Následující ověřovací data jsou uložené v Key Vault: Azure heslo pro účet nasadit, heslo správce virtuálního počítače, heslo účtu služby SQL Server. Key Vault šifruje klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 řízení IA-5.j

#### <a name="authenticator-management"></a>Ověřovací data správy

**IA-5.j** organizace spravuje ověřovací moduly systému informace změnou ověřovací data pro účty skupiny nebo role, pokud členství těm, které účty změny.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | U prostředků nasadit pomocí tento plán, podle kterého jsou povolené žádné sdílené nebo skupinové účty. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1a"></a>.A NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1) .a** systému informace pro ověřování založené na heslech vynucuje složitost hesla minimální [přiřazení: požadavky organizace definované pro malá a velká písmena, počet znaků, kombinovat velká písmena, malá písmena, číslice a speciální znaky, včetně minimální požadavky pro každý typ].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny je vytvořeno a konfigurován k vynucování požadavky na složitost hesla pro místní účty virtuálního počítače a účty AD.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1b"></a>.B NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1) .b** systému informace pro ověřování založené na heslech vynucuje minimálně tento počet znaků změněné při vytvoření nového hesla: [přiřazení: organizace definované číslo].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání ověřování pomocí hesla v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1c"></a>.C NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1) .c** ukládá informace systému, pro ověřování založené na heslech a přenáší pouze kryptograficky chráněné hesla.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Adresář Azure slouží k zajištění, že jsou všechna hesla kryptograficky chráněné při uložení a přenos. Hesla uložená ve službě Active Directory a místně v nasazené virtuální počítače Windows automaticky rozdělí v rámci fungování funkce integrované zabezpečení. Relace vzdálené plochy ověřování jsou zabezpečené použití protokolu TLS, abyste zajistili, že ověřovací data jsou chráněny při přenosu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1d"></a>.D NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1) .d** systému informace pro ověřování založené na heslech vynucuje heslo minimální a maximální doba života omezení [přiřazení: organizace definované čísla pro maximální doba života minimální, životnost].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny je vytvořit a nakonfigurovat tak, aby vynutit omezení na hesla, které vynucují minimální (1 den) a (60 dnů) maximální doba života omezení pro místní účty a účty AD. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1e"></a>.E NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1).e** Informace o systému, pro ověřování založené na heslech zakáže opakované použití hesla pro [přiřazení: organizace definované číslo] generace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny je vytvořit a nakonfigurovat tak, aby vynutit omezení pro opakované použití podmínky (24 hesla) pro místní účty a účty AD. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-1f"></a>.F NIST 800-53 řízení IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Ověřovací data správy | Ověřování pomocí hesla

**IA-5 (1) .f** systému informace pro ověřování založené na heslech umožňuje použití dočasné heslo pro přihlášení k systému s o okamžitou změnu trvalé heslo.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Active Directory se používá ke správě řízení přístupu k systému informace. Pokaždé, když je původně vytvořen účet, nebo dočasné heslo se vygeneruje, je potřeba, aby uživatel změnit heslo při příštím přihlášení vzhledem k Azure Active Directory. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 Control IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>Ověřovací data správy | Ověřování na základě infrastruktury veřejných klíčů

**IA-5 (2) .a** systému informace pro ověřování na základě infrastruktury veřejných KLÍČŮ ověří certifikace vytváření a ověření certifikační cesta na ukotvení přijatý vztahu důvěryhodnosti, včetně kontroly informace o stavu certifikátu.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání ověřování na základě infrastruktury veřejných KLÍČŮ v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-2b"></a>.B NIST 800-53 řízení IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Ověřovací data správy | Ověřování na základě infrastruktury veřejných klíčů

**IA-5 (2) .b** systému informace pro ověřování na základě infrastruktury veřejných KLÍČŮ vynucuje oprávnění přístupu k odpovídajícího privátního klíče.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání ověřování na základě infrastruktury veřejných KLÍČŮ v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 Control IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>Ověřovací data správy | Ověřování na základě infrastruktury veřejných klíčů

**IA-5 (2) .c** systému informace pro ověřování na základě infrastruktury veřejných KLÍČŮ mapuje ověřenou identitu účtu uživatele nebo skupinu.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání ověřování na základě infrastruktury veřejných KLÍČŮ v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-2d"></a>.D NIST 800-53 řízení IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Ověřovací data správy | Ověřování na základě infrastruktury veřejných klíčů

**IA-5 (2) .d** systému informace pro ověřování na základě infrastruktury veřejných KLÍČŮ implementuje místní mezipaměti dat odvolaných certifikátů pro podporu cesty zjišťování a ověření v případě nemohou přistupovat k informacím odvolání přes síť.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání ověřování na základě infrastruktury veřejných KLÍČŮ v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 řízení IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Ověřovací data správy | Registrace osobní nebo důvěryhodná třetí strany

**IA-5 (3)** organizace vyžaduje, že registrace zpracovat přijímat [přiřazení: organizace definované typy nebo konkrétní ověřovací data] se provést [výběr: osobně; důvěryhodná třetí strana] před [přiřazení: autorita pro registraci definované organizace] s autorizací pomocí [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za registraci ověřovací data. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 řízení IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Ověřovací data správy | Automatizovaná podpora pro určení síly hesla

**IA-5 (4)** organizace využívá automatizované nástroje k určení, pokud jsou vyhovět dostatečně silné heslo ověřovací data [přiřazení: požadavky definované organizace].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Uživatelské účty, které jsou nasazené s Tento plán, podle kterého zahrnují AD a místní uživatelské účty. Obě tyto poskytují mechanismy, které vynutit dodržování předpisů s požadavky na zavedených heslo pro vytvoření počátečního hesla a během změny hesla. Azure Active Directory je automatizovaného nástroje vzhledem k určí, zda heslo ověřovací data dostatečně silné vyhovět délku hesla, složitost, otáčení a doba platnosti omezení nastavené v IA-5 (1). Azure Active Directory zajistí, že splňuje authenticator síly hesla při vytváření těchto standardů. Odběratel určen hesla použít k nasazení tohoto řešení se kontroluje splnění požadavků na sílu hesel. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 řízení IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Ověřovací data správy | Ochranu ověřovací data

**IA-5 (6)** organizace chrání ověřovací data úměrná kategorie zabezpečení informací, ke kterému použití ověřovacích umožňuje přístup.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ochranu ověřovací data. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 řízení IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Ověřovací data správy | Žádné Embedded nezašifrované statické ověřovací data

**IA-5 (7)** organizace zajišťuje, že nezašifrované statické ověřovací data nejsou součástí aplikace nebo skripty přístup nebo uložený na funkční klávesy.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Se nepoužívá nezašifrované statické ověřovací data integrována do aplikací, přístup k skripty nebo funkční klávesy nasadit pomocí tento plán. Každý skript nebo aplikace, která používá ověřovací data zavolá kontejner Azure Key Vault před každou použití. Je auditovat přístup k Azure Key Vault kontejnery, což umožňuje detekci narušení tento zákaz, pokud účet služby se používá pro přístup systému bez odpovídající volání kontejneru Azure Key Vault. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 řízení IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Ověřovací data správy | Více informace o systému účtů

**IA-5 (8)** organizace implementuje [přiřazení: ochrana definované organizace zabezpečení] ke správě riziko ohrožení zabezpečení z důvodu osobu, která má účty na více systémů informace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na ochranu zabezpečení na úrovni podniku spravovat riziko spojené s osobu, která má účty na více systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 řízení IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Ověřovací data správy | Ověřování na základě tokenu hardwaru

**IA-5 (11)** systému informace pro ověřování na základě tokenu hardwaru využívá mechanismy, které odpovídají [přiřazení: požadavky na definované organizace tokenu kvalitu].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využití přenosu mechanismy splňovat požadavky na hardware ověřování na základě tokenu kvality. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 řízení IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Ověřovací data správy | Vypršení platnosti ověřovací data uložená v mezipaměti

**IA-5 (13)** informace systém neumožňuje použití mezipaměti ověřovací data po [přiřazení: organizace definované časové období].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Žádné prostředky nasadit pomocí tento plán, podle kterého jsou nakonfigurována pro použití ověřovací data uložená v mezipaměti. Ověřování pro nasazené virtuální počítače vyžaduje, že ověřovací data se zadá při ověřování. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 řízení IA-6

#### <a name="authenticator-feedback"></a>Ověřovací data zpětné vazby

**IA-6** informace systému skryje zpětnou vazbu o informace o ověřování během procesu ověřování k ochraně informací z možných zneužití nebo používání neoprávněné osoby.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Přístup k prostředkům nasadit pomocí tento plán, podle kterého je pomocí vzdálené plochy a spoléhá na ověřování systému Windows. Výchozí chování relace ověřování systému Windows zakrývá hesla při vstupu během relace ověřování.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 řízení IA-7

#### <a name="cryptographic-module-authentication"></a>Ověřování kryptografického modulu

**IA-7** systém informace implementuje mechanismy pro ověřování, aby kryptografického modulu, které splňují požadavky na příslušných zákonů federal, objednávek vedení, direktivy, zásady, nařízení, standardů a pokyny, například ověřování.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého jsou zaměstnaní ověřování systému Windows, vzdálené plochy a nástroj BitLocker. Tyto součásti lze nakonfigurovat moct spolehnout na FIPS 140 ověřit kryptografických modulů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 řízení IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identifikace a ověření (bez organizační uživatele)

**IA-8** informace systému jednoznačně identifikuje a ověřuje-organizační uživatele (nebo procesy, které jednají jménem-organizační uživatele).

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za identifikace a ověřování – a organizační uživatele, kteří používají zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 řízení IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identifikace a ověření (uživatele – organizační) | Přijetí standardu Piv pověření z jiných úřady

**IA-8 (1)** informace systému přijme a elektronicky ověřuje pověření ověřování osobní Identity (PIV) z jiných federální úřady.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za přijetí a ověření přihlašovacích údajů ověřování osobní Identity (PIV) vydaný jiné federální úřady. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 řízení IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identifikace a ověření (uživatele – organizační) | Přijetí přihlašovacích údajů třetích stran

**IA-8 (2)** informace systému přijímá pouze FICAM schválené pověření třetích stran.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za pouze přijímá pověření třetích stran, které schváleny initiative Federal identitu, přihlašovacích údajů a řízení přístupu (FICAM) důvěřovat Framework řešení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 řízení IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identifikace a ověření (uživatele – organizační) | Použití schválení Ficam produktů

**IA-8 (3)** organizace využívá jenom schválení FICAM informace systému součásti v [přiřazení: systémy informace definované organizace] tak, aby přijímal přihlašovací údaje třetí strany.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání pouze Federal identitu, přihlašovací údaje, a řízení přístupu (FICAM) důvěřovat Framework řešení initiative schválení prostředků pro přijetí přihlašovací údaje třetí strany. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 řízení IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identifikace a ověření (uživatele – organizační) | Použití vydané Ficam profilů

**IA-8 (4)** informace systému vyhovuje vydané FICAM profily.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za odpovídají profily vystavený Federal identitu, přihlašovacích údajů a řízení přístupu (FICAM) důvěřovat Framework řešení initiative. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |
