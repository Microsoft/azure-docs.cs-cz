---
title: Co je riziko? Azure AD Identity Protection
description: Vysvětlení rizika v azure ad identity ochrany
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
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253686"
---
# <a name="what-is-risk"></a>Co je riziko?

Detekce rizik v Azure AD Identity Protection zahrnují všechny identifikované podezřelé akce související s uživatelskými účty v adresáři.

Ochrana identity poskytuje organizacím přístup k výkonným prostředkům, aby tyto podezřelé akce rychle viděly a reagovaly na ně. 

![Přehled zabezpečení zobrazující rizikové uživatele a přihlášení](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Typy rizik a detekce

Existují dva typy **rizika Uživatel** a **přihlášení** a dva typy detekce nebo výpočtu **v reálném čase** a **offline**.

### <a name="user-risk"></a>Riziko uživatele

Riziko uživatele představuje pravděpodobnost, že je ohrožena daná identita nebo účet. 

Tato rizika se počítají offline pomocí interních a externích zdrojů analýzy hrozeb společnosti Microsoft, včetně výzkumných pracovníků v oblasti zabezpečení, odborníků v oblasti vymáhání práva, bezpečnostních týmů společnosti Microsoft a dalších důvěryhodných zdrojů.

| Detekce rizik | Popis |
| --- | --- |
| Uniklá pověření | Tento typ zjišťování rizika označuje, že byla nevrácena platná pověření uživatele. Když počítačoví zločinci ohrožují platná hesla legitimních uživatelů, často tyto přihlašovací údaje sdílejí. Toto sdílení se obvykle provádí zveřejněním na tmavém webu, vkládáním stránek nebo obchodováním a prodejem pověření na černém trhu. Když služba microsoft leaked credentials získává přihlašovací údaje uživatelů z tmavého webu, vkládání webů nebo jiných zdrojů, jsou kontrolovány proti aktuálníplatná pověření uživatelů Azure AD najít platné shody. |
| Azure AD threat intelligence | Tento typ zjišťování rizik a označuje aktivitu uživatele, která je pro daného uživatele neobvyklá nebo je konzistentní se známými vzory útoků založenými na interních a externích zdrojích analýzy hrozeb společnosti Microsoft. |

### <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizován vlastníkem identity. 

Tato rizika lze vypočítat v reálném čase nebo vypočítat offline pomocí interních a externích zdrojů analýzy hrozeb společnosti Microsoft, včetně výzkumných pracovníků v oblasti zabezpečení, odborníků v oblasti vymáhání práva, bezpečnostních týmů společnosti Microsoft a dalších důvěryhodných zdrojů.

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Anonymní IP adresa | Reálný čas | Tento typ detekce rizik označuje přihlášení z anonymní IP adresy (například prohlížeče Tor nebo anonymní VPN). Tyto IP adresy jsou obvykle používány subjekty, kteří chtějí skrýt své přihlašovací telemetrie (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivý záměr. |
| Atypické cestování | Offline | Tento typ zjišťování rizika identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde alespoň jedno z umístění může být také atypické pro uživatele, vzhledem k minulému chování. Mezi několika dalšími faktory tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a čas, který by uživateli trvalo, než cestoval z prvního místa do druhého, což naznačuje, že jiný uživatel používá stejné Pověření. <br><br> Algoritmus ignoruje zjevné "falešně pozitivní výsledky", které přispívají k nemožným podmínkám cestování, jako jsou virtuální ny a místa pravidelně používaná ostatními uživateli v organizaci. Systém má počáteční období učení nejdříve 14 dnů nebo 10 přihlášení, během kterého se dozví chování nového uživatele přihlášení. |
| Ip adresa propojená s malwarem | Offline | Tento typ detekce rizika označuje přihlášení z IP adres infikovaných malwarem, o kterých je známo, že aktivně komunikuje se serverem robotů. Tato detekce je určena korelacemi IP adres zařízení uživatele s IP adresami, které byly v kontaktu se serverem robotů, když byl robotový server aktivní. |
| Neznámé přihlašovací vlastnosti | Reálný čas | Tento typ detekce rizik a považuje historii přihlášení v minulosti (IP, Zeměpisná šířka / zeměpisná délka a ASN) za účelem hledání neobvyklých přihlášení. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje tato "známá" umístění. Zjišťování rizika se aktivuje, když dojde k přihlášení z umístění, které ještě není v seznamu známých umístění. Nově vytvořené uživatelé budou v "režimu učení" po dobu, ve které neznámé přihlašovací vlastnosti detekce rizik a detekce rizik bude vypnuta, zatímco naše algoritmy se učí chování uživatele. Doba výuky režimu je dynamický a závisí na tom, kolik času trvá algoritmus shromáždit dostatek informací o vzorcích přihlášení uživatele. Minimální doba trvání je pět dní. Uživatel se může po dlouhé době nečinnosti vrátit do režimu učení. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého místa. <br><br> Toto zjišťování také spouštějíme pro základní ověřování (nebo starší protokoly). Vzhledem k tomu, že tyto protokoly nemají moderní vlastnosti, jako je například ID klienta, je omezená telemetrie ke snížení falešně pozitivních hodnot. Doporučujeme našim zákazníkům přejít na moderní ověřování. |
| Admin potvrdil, že uživatel byl ohrožen. | Offline | Toto zjištění znamená, že správce vybral v uživatelském rozhraní rizikových uživatelů nebo pomocí rozhraní API rizikových uživatelů možnost Potvrdit ohrožení zabezpečení uživatele. Chcete-li zjistit, který správce potvrdil tento uživatel ohrožen, zkontrolujte historii rizik uživatele (pomocí uživatelského rozhraní nebo rozhraní API). |
| Škodlivá IP adresa | Offline | Toto zjištění označuje přihlášení ze škodlivé adresy IP. Adresa IP je považována za škodlivou na základě vysoké míry selhání z důvodu neplatných pověření přijatých z adresy IP nebo jiných zdrojů reputace IP adresy. |
| Podezřelá pravidla manipulace s doručenou poštou | Offline | Toto zjištění je zjištěno [microsoft cloud app security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Toto zjišťování profily vašeho prostředí a aktivuje výstrahy, když podezřelá pravidla, která odstraňují nebo přesunout zprávy nebo složky jsou nastaveny na doručené poště uživatele. To může znamenat, že je ohrožen účet uživatele, že zprávy jsou záměrně skryté a že poštovní schránka se používá k distribuci nevyžádané pošty nebo malwaru ve vaší organizaci. |
| Neuskutečnitelná cesta | Offline | Toto zjištění je zjištěno [microsoft cloud app security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Toto zjištění identifikuje dvě aktivity uživatele (je jedna nebo více relací) pocházející z geograficky vzdálených míst v časovém období kratším, než je doba, po kterou by uživatel mohl cestovat z prvního místa do druhého, což znamená, že že jiný uživatel používá stejná pověření. |

### <a name="other-risk-detections"></a>Další detekce rizik

| Detekce rizik | Typ detekce | Popis |
| --- | --- | --- |
| Bylo zjištěno další riziko | V reálném čase nebo offline | Tato detekce indikuje, že byla zjištěna jedna z výše uvedených detekcí prémií. Vzhledem k tomu, že detekce premium jsou viditelné jenom pro zákazníky Azure AD Premium P2, mají název "další zjištěné riziko" pro zákazníky bez licencí Azure AD Premium P2. |

## <a name="next-steps"></a>Další kroky

- [Dostupné zásady ke zmírnění rizik](concept-identity-protection-policies.md)

- [Přehled zabezpečení](concept-identity-protection-security-overview.md)
