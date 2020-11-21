---
title: Co je riziko? Azure AD Identity Protection
description: Vysvětlení rizika v Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0da8755199645b12e9c6ed75a42881fe86e70b5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023033"
---
# <a name="what-is-risk"></a>Co je riziko?

Detekce rizik v Azure AD Identity Protection zahrnuje všechny identifikované podezřelé akce týkající se uživatelských účtů v adresáři.

Identity Protection poskytuje organizacím přístup k výkonným prostředkům, abyste mohli rychle sledovat tyto podezřelé akce a reagovat na ně. 

![Přehled zabezpečení znázorňující rizikové uživatele a přihlášení](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection generuje detekci rizik jenom v případě, že se používají správné přihlašovací údaje. Pokud se při přihlášení použijí nesprávné přihlašovací údaje, nepředstavuje riziko zneužití přihlašovacích údajů.

## <a name="risk-types-and-detection"></a>Typy a detekce rizik

Existují dva typy rizikového **uživatele** a **přihlášení** a dva typy detekce nebo výpočtu v **reálném čase** a v **režimu offline**.

Zjišťování v reálném čase se nemusí zobrazit v hlášení po dobu pěti až deseti minut. Offline detekce se nemusí zobrazit v hlášení po dobu dvou až dvaceti čtyř hodin.

### <a name="user-risk"></a>Riziko uživatele

Riziko uživatele představuje pravděpodobnost zneužití dané identity nebo účtu. 

Tato rizika se počítají v režimu offline pomocí interních a externích zdrojů informací o hrozbách Microsoftu, včetně výzkumných pracovníků v oblasti zabezpečení, odborníků na prosazování zákonů, týmů zabezpečení v Microsoftu a dalších důvěryhodných zdrojů.

| Detekce rizik | Popis |
| --- | --- |
| Uniklé přihlašovací údaje | Tento typ detekce rizika označuje, že došlo k úniku platných přihlašovacích údajů uživatele. Pokud cybercriminals ohrozit platná hesla legitimních uživatelů, často tyto přihlašovací údaje sdílejí. Toto sdílení se obvykle provádí publikováním veřejně na tmavém webu, vložením webů nebo obchodováním a prodejem přihlašovacích údajů na černém trhu. Když služba nevrácených přihlašovacích údajů od Microsoftu Získá přihlašovací údaje uživatele z tmavého webu, vloží weby nebo jiné zdroje, vyhledá platné shody pomocí aktuálních platných přihlašovacích údajů uživatelů Azure AD. Další informace o nevrácených přihlašovacích údajích najdete v tématu [Nejčastější dotazy](#common-questions). |
| Analýza hrozeb v Azure AD | Tento typ detekce rizika označuje aktivitu uživatele, která je pro daného uživatele neobvyklá nebo je konzistentní se známými vzorci útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu. |

### <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity. 

Tato rizika se dají vypočítat v reálném čase nebo vypočítat v režimu offline pomocí interních a externích zdrojů informací o hrozbách Microsoftu, včetně bezpečnostních výzkumníků, profesionálů v oblasti vynucování zákonů, týmů zabezpečení v Microsoftu a dalších důvěryhodných zdrojů.

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Anonymní IP adresa | Reálný čas | Tento typ detekce rizika označuje přihlášení z anonymní IP adresy (například samoobslužný prohlížeč nebo anonymní síť VPN). Tyto IP adresy obvykle používají aktéri, kteří chtějí skrýt svou telemetrii přihlašování (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivý záměr. |
| Neobvyklá cesta | Offline | Tento typ detekce rizika identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde aspoň jedno z umístění může být pro uživatele neobvyklá, a to s ohledem na minulé chování. Kromě několika dalších faktorů tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a časem, kdy by uživatel musel cestovat z prvního umístění do druhé, což značí, že stejný přihlašovací údaj používá jiný uživatel. <br><br> Algoritmus ignoruje zjevné "falešně pozitivní", které přispívají k nemožným cestovním podmínkám, jako jsou sítě VPN a lokality pravidelně používané jinými uživateli v organizaci. Systém má počáteční období učení nejstarších 14 dní nebo 10 přihlášení, během kterých se učí chování přihlášení nového uživatele. |
| Propojená IP adresa pro malware | Offline | Tento typ detekce rizika označuje přihlášení z IP adres nakažených malwarem, u kterých je známo, že aktivně komunikuje se serverem robot. Tato detekce je určena korelacemi IP adres zařízení uživatele proti IP adresám, které se nacházely v kontaktu se serverem robota, zatímco byl server robot aktivní. |
| Neznámé vlastnosti přihlášení | Reálný čas | Tento typ detekce rizika považuje předchozí historii přihlášení (IP, zeměpisná šířka/délka a ASN), aby vyhledal neobvyklé přihlášení. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje se za "známá" umístění. Zjišťování rizik se aktivuje, když se přihlásí z umístění, které ještě není v seznamu známých umístění. Nově vytvoření uživatelé budou v "výukovém režimu" po určitou dobu, během které se neznámé detekce rizik při neznámých vlastnostech přihlášení vypne, zatímco se naše algoritmy budou učit chování uživatele. Doba trvání výukového režimu je dynamická a závisí na tom, kolik času má algoritmus získat dostatek informací o vzorech přihlášení uživatele. Minimální doba trvání je pět dní. Uživatel se může po dlouhou době nečinnosti vrátit do výukového režimu. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého umístění. <br><br> Tuto detekci také spouštíme pro základní ověřování (nebo starší protokoly). Vzhledem k tomu, že tyto protokoly nemají moderní vlastnosti, jako je například ID klienta, je k dispozici omezená telemetrie k omezení falešně pozitivních hodnot. Zákazníkům doporučujeme, aby přešli na moderní ověřování. |
| Správce potvrzuje ohrožení zabezpečení uživatele. | Offline | Tato detekce indikuje, že správce v uživatelském rozhraní rizikové uživatele nebo pomocí rozhraní riskyUsers API vybral možnost potvrdit zneužití uživatele. Pokud chcete zjistit, který správce potvrdil ohrožení tohoto uživatele, Zkontrolujte historii rizika uživatele (prostřednictvím uživatelského rozhraní nebo rozhraní API). |
| Škodlivá IP adresa | Offline | Tato detekce indikuje přihlášení ze škodlivé IP adresy. IP adresa je považována za škodlivou na základě vysoké míry selhání kvůli neplatným přihlašovacím údajům z IP adresy nebo jiných zdrojů reputace IP. |
| Podezřelá pravidla pro manipulaci s doručenou poštou | Offline | Tato detekce je zjištěna [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Toto zjišťování profiluje vaše prostředí a aktivuje výstrahy v případě, že jsou v doručené poště uživatele podezřelá pravidla, která odstraňují nebo přesunují zprávy nebo složky. Tato detekce může znamenat, že došlo k ohrožení zabezpečení účtu uživatele, že zprávy jsou záměrně skryté a že se poštovní schránka používá k distribuci spamu nebo malwaru ve vaší organizaci. |
| Sprej hesla | Offline | Útok na postřik hesla je v případě, kdy je více uživatelských jmen napadeno pomocí běžných hesel v rámci sjednoceného hrubou silou, k získání neoprávněného přístupu. Toto zjišťování rizik se aktivuje, když se provedl útok na spreje hesla. |
| Neuskutečnitelná cesta | Offline | Tato detekce je zjištěna [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Toto zjišťování identifikuje dvě uživatelské aktivity (Jedná se o jednu nebo víc relací) pocházející z geograficky vzdálených umístění v časovém období kratším než čas, kdy by uživatel musel použít stejné přihlašovací údaje jako jiný uživatel. |

### <a name="other-risk-detections"></a>Další detekce rizik

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Bylo zjištěno další riziko. | V reálném čase nebo offline | Toto zjištění indikuje, že bylo zjištěno jedno z výše uvedených detekcí Premium. Vzhledem k tomu, že zjišťování úrovně Premium je viditelné pouze pro zákazníky Azure AD Premium P2, jsou pro zákazníky s názvem "dodatečné riziko zjištěny" pro zákazníky bez licencí Azure AD Premium P2. |

## <a name="common-questions"></a>Časté dotazy

### <a name="risk-levels"></a>Úrovně rizika

Identity Protection kategorizuje riziko do tří úrovní: nízká, střední a vysoká. Když konfigurujete [vlastní zásady ochrany identit](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies#custom-conditional-access-policy), můžete ji také nakonfigurovat tak, aby se aktivovala **bez úrovně rizika** . Žádné riziko znamená, že neexistuje žádná aktivní indikace ohrožení identity uživatele.

Zatímco společnost Microsoft neposkytuje konkrétní informace o tom, jakým způsobem je riziko vypočítáno, říkáme, že každá úroveň přináší větší jistotu, že se uživateli nebo přihlášení naruší zabezpečení. Například něco jako jedna instance neznámého přihlašovacího oprávnění pro uživatele nemusí být ohroženo jako nevrácená pověření pro jiného uživatele.

### <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel

Detekce rizik, jako jsou nevrácené přihlašovací údaje a sprej hesla, vyžadují přítomnost hodnot hash hesla, aby mohlo probíhat detekce. Další informace o synchronizaci hodnot hash hesel najdete v článku [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Kde Microsoft hledá nevrácené přihlašovací údaje?

Společnost Microsoft nalezne nevrácená pověření na nejrůznějších místech, včetně:

- Veřejné vkládání webů, jako je například pastebin.com a paste.ca, kde špatné aktéri obvykle účtují takový materiál. Toto umístění je neplatných aktérů "první zastavení na své oblasti hledání, aby mohl najít odcizené přihlašovací údaje.
- Donucovací orgány pro zákony.
- Další skupiny Microsoftu, které provádějí tmavé webové výzkumné účely.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Proč nevidím žádné nevrácené přihlašovací údaje?

Nevrácená pověření jsou zpracována kdykoli, společnost Microsoft najde novou, veřejně dostupnou dávku. Z důvodu citlivého charakteru jsou nevrácená pověření po zpracování odstraněna krátce. Po povolení synchronizace hodnot hash hesel (KOSMETICE) se zpracují jenom nová nevrácená pověření pro vašeho tenanta. Nepovedlo se ověřit předchozí nalezené páry přihlašovacích údajů. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Nedostali jsme žádné události rizika nevrácených přihlašovacích údajů za chvíli?

Pokud jste neviděli žádné rizikové události přihlašovacích údajů, z tohoto důvodu je to z následujících důvodů:

- Pro vašeho tenanta nemáte povolený KOSMETICE.
- Společnost Microsoft nenalezla žádné páry nevrácených přihlašovacích údajů, které by odpovídaly vašim uživatelům.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Jak často Microsoft zpracovává nové přihlašovací údaje?

Přihlašovací údaje se zpracovávají ihned po jejich nalezení, normálně ve více dávkách za den.

## <a name="next-steps"></a>Další kroky

- [Dostupné zásady pro zmírnění rizik](concept-identity-protection-policies.md)
- [Přehled zabezpečení](concept-identity-protection-security-overview.md)
