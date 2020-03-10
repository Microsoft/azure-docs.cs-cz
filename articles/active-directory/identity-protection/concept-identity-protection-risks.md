---
title: Co je riziko? Azure AD Identity Protection
description: Vysvětlení rizika v Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9815ff08296b38c39e732c08619042659504462
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892911"
---
# <a name="what-is-risk"></a>Co je riziko?

Detekce rizik v Azure AD Identity Protection zahrnuje všechny identifikované podezřelé akce týkající se uživatelských účtů v adresáři.

Identity Protection poskytuje organizacím přístup k výkonným prostředkům, abyste mohli rychle sledovat tyto podezřelé akce a reagovat na ně. 

![Přehled zabezpečení znázorňující rizikové uživatele a přihlášení](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Typy a detekce rizik

Existují dva typy rizikového **uživatele** a **přihlášení** a dva typy detekce nebo výpočtu v **reálném čase** a v **režimu offline**.

### <a name="user-risk"></a>Riziko uživatele

Riziko uživatele představuje pravděpodobnost zneužití dané identity nebo účtu. 

Tato rizika se počítají v režimu offline pomocí interních a externích zdrojů informací o hrozbách Microsoftu, včetně výzkumných pracovníků v oblasti zabezpečení, odborníků na prosazování zákonů, týmů zabezpečení v Microsoftu a dalších důvěryhodných zdrojů.
''
| Detekce rizik | Descript'on |
| --- | --- |
| Nevrácená pověření | Tento typ detekce rizika označuje, že došlo k úniku platných přihlašovacích údajů uživatele. Pokud cybercriminals ohrozit platná hesla legitimních uživatelů, často tyto přihlašovací údaje sdílejí. Toto sdílení se obvykle provádí publikováním veřejně na tmavém webu, vložením webů nebo obchodováním a prodejem přihlašovacích údajů na černém trhu. Když služba nevrácených přihlašovacích údajů od Microsoftu Získá přihlašovací údaje uživatele z tmavého webu, vloží weby nebo jiné zdroje, vyhledá platné shody pomocí aktuálních platných přihlašovacích údajů uživatelů Azure AD. |
| Analýza hrozeb v Azure AD | Tento typ detekce rizika označuje aktivitu uživatele, která je pro daného uživatele neobvyklá nebo je konzistentní se známými vzorci útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu. |

### <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity. 

Tato rizika se dají vypočítat v reálném čase nebo vypočítat v režimu offline pomocí interních a externích zdrojů informací o hrozbách Microsoftu, včetně bezpečnostních výzkumníků, profesionálů v oblasti vynucování zákonů, týmů zabezpečení v Microsoftu a dalších důvěryhodných zdrojů.

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Anonymní IP adresa | V reálném čase | Tento typ detekce rizika označuje přihlášení z anonymní IP adresy (například samoobslužný prohlížeč nebo anonymní síť VPN). Tyto IP adresy obvykle používají aktéri, kteří chtějí skrýt svou telemetrii přihlašování (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivý záměr. |
| Neobvyklé cestování | Offline | Tento typ detekce rizika identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde aspoň jedno z umístění může být pro uživatele neobvyklá, a to s ohledem na minulé chování. Kromě několika dalších faktorů tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a časem, kdy by uživatel musel přejít z prvního umístění do druhé, což značí, že jiný uživatel používá stejné. přihlašovací údaje. <br><br> Algoritmus ignoruje zjevné "falešně pozitivní", které přispívají k nemožným cestovním podmínkám, jako jsou sítě VPN a lokality pravidelně používané jinými uživateli v organizaci. Systém má počáteční období učení nejstarších 14 dní nebo 10 přihlášení, během kterých se učí chování přihlášení nového uživatele. |
| Propojená IP adresa pro malware | Offline | Tento typ detekce rizika označuje přihlášení z IP adres nakažených malwarem, u kterých je známo, že aktivně komunikuje se serverem robot. Tato detekce je určena korelacemi IP adres zařízení uživatele proti IP adresám, které se nacházely v kontaktu se serverem robota, zatímco byl server robot aktivní. |
| Neznámou vlastnost přihlášení | V reálném čase | Tento typ detekce rizika považuje předchozí historii přihlášení (IP, zeměpisná šířka/délka a ASN), aby vyhledal neobvyklé přihlášení. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje se za "známá" umístění. Zjišťování rizik se aktivuje, když se přihlásí z umístění, které ještě není v seznamu známých umístění. Nově vytvoření uživatelé budou v "výukovém režimu" po určitou dobu, během které se neznámé detekce rizik při neznámých vlastnostech přihlášení vypne, zatímco se naše algoritmy budou učit chování uživatele. Doba trvání výukového režimu je dynamická a závisí na tom, kolik času má algoritmus získat dostatek informací o vzorech přihlášení uživatele. Minimální doba trvání je pět dní. Uživatel se může po dlouhou době nečinnosti vrátit do výukového režimu. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého umístění. <br><br> Tuto detekci také spouštíme pro základní ověřování (nebo starší protokoly). Vzhledem k tomu, že tyto protokoly nemají moderní vlastnosti, jako je například ID klienta, je k dispozici omezená telemetrie k omezení falešně pozitivních hodnot. Zákazníkům doporučujeme, aby přešli na moderní ověřování. |
| Správce potvrzuje ohrožení zabezpečení uživatele. | Offline | Tato detekce indikuje, že správce v uživatelském rozhraní rizikové uživatele nebo pomocí rozhraní riskyUsers API vybral možnost potvrdit zneužití uživatele. Pokud chcete zjistit, který správce potvrdil ohrožení tohoto uživatele, Zkontrolujte historii rizika uživatele (prostřednictvím uživatelského rozhraní nebo rozhraní API). |
| Škodlivá IP adresa | Offline | Tato detekce indikuje přihlášení ze škodlivé IP adresy. IP adresa je považována za škodlivou na základě vysoké míry selhání kvůli neplatným přihlašovacím údajům z IP adresy nebo jiných zdrojů reputace IP. |
| Podezřelá pravidla pro manipulaci s doručenou poštou | Offline | Tato detekce je zjištěna [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Tato detekce profily prostředí a aktivují upozornění, když v doručené poště uživatele jsou nastavená podezřelého pravidla, která odstranit nebo přesunout zprávy nebo složky. To může znamenat, že došlo k ohrožení zabezpečení účtu uživatele, že zprávy jsou záměrně skryté a že se poštovní schránka používá k distribuci spamu nebo malwaru ve vaší organizaci. |
| Neuskutečnitelná cesta | Offline | Tato detekce je zjištěna [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Tato detekce identifikuje dvě aktivity uživatele (je jeden nebo více relací) pocházející z geograficky vzdáleném umístění v časovém období kratší než čas ho by trvalo cestují z první umístění do druhého, která uživateli že je jiný uživatel pomocí stejných přihlašovacích údajů. |

### <a name="other-risk-detections"></a>Další detekce rizik

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Bylo zjištěno další riziko. | V reálném čase nebo offline | Toto zjištění indikuje, že bylo zjištěno jedno z výše uvedených detekcí Premium. Vzhledem k tomu, že zjišťování úrovně Premium je viditelné pouze pro zákazníky Azure AD Premium P2, jsou pro zákazníky s názvem "dodatečné riziko zjištěny" pro zákazníky bez licencí Azure AD Premium P2. |

## <a name="next-steps"></a>Další kroky

- [Dostupné zásady pro zmírnění rizik](concept-identity-protection-policies.md)

- [Přehled zabezpečení](concept-identity-protection-security-overview.md) ""'