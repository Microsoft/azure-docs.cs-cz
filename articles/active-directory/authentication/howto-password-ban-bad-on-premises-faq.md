---
title: Nejčastější dotazy k ochraně heslem v místní službě Azure AD – Azure Active Directory
description: Nejčastější dotazy k ochraně heslem v místní službě Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1f2e06eb6b5f8d402515ff1c07a4163174495d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666357"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Místní ochrana heslem Azure AD – Nejčastější dotazy

V této části najdete odpovědi na mnoho nejčastějších dotazů týkajících se ochrany heslem Azure AD.

## <a name="general-questions"></a>Obecné otázky

**Otázka: Jaké pokyny by se měly vydávat uživatelům při výběru zabezpečeného hesla?**

Aktuální pokyny Microsoftu k tomuto tématu najdete na následujícím odkazu:

[Pokyny k heslu Microsoftu](https://www.microsoft.com/research/publication/password-guidance)

**Otázka: Podporuje se místní ochrana heslem Azure AD v jiných než veřejných cloudech?**

Žádná ochrana heslem bez místní služby Azure AD je podporována pouze ve veřejném cloudu. Nebylo oznámeno žádné datum pro dostupnost neveřejného cloudu.

**Otázka: Jak můžu využít výhody ochrany hesel Azure AD pro podmnožinu místních uživatelů?**

Nepodporuje se. Po nasazení a povolení ochrany heslem Azure AD nezáleží na tom, že všichni uživatelé získají stejné výhody zabezpečení.

**Otázka: Jaký je rozdíl mezi změnou hesla a nastavením hesla (nebo resetováním)?**

Změna hesla je v případě, že uživatel po prokázání, že má informace o starém hesle, zvolí nové heslo. To je například to, co se stane, když se uživatel přihlásí k Windows, a zobrazí se výzva k výběru nového hesla.

Sada hesel (někdy označované jako resetování hesla) je v případě, že správce nahradí heslo k účtu novým heslem, například pomocí nástroje pro správu Uživatelé a počítače služby Active Directory. Tato operace vyžaduje vysokou úroveň oprávnění (obvykle správce domény) a osoba provádějící tuto operaci obvykle nemá znalosti o původním hesle. Scénáře technické podpory to často dělají, například při pomoci uživateli, který zapomněl heslo. Při prvním vytváření nového uživatelského účtu s heslem se zobrazí také události nastavování hesel.

Zásady ověřování hesla se chovají stejně, bez ohledu na to, jestli se provádí změna nebo nastavení hesla. Služba agenta řadiče domény Azure AD pro ochranu hesel zaznamenává různé události, které vás informují o tom, jestli byla operace změny nebo nastavení hesla hotová.  Viz téma [monitorování a protokolování ochrany heslem služby Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Otázka: Proč se při pokusu o nastavení slabého hesla pomocí modulu snap-in Správa uživatelů a počítačů služby Active Directory zaznamenaly duplicitní události odmítnutí hesla?**

Modul snap-in Správa uživatelů a počítačů služby Active Directory se nejprve pokusí nastavit nové heslo pomocí protokolu Kerberos. Po selhání se modul snap-in pokusí nastavit heslo pomocí staršího protokolu (SAM RPC) (používané konkrétní protokoly nejsou důležité). Pokud se nové heslo považuje za slabé pomocí ochrany heslem Azure AD, bude se jednat o dvě sady událostí odmítnutí resetování hesla.

**Otázka: Proč se události ověřování hesla ochrany heslem služby Azure AD zaprotokolují s prázdným uživatelským jménem?**

Služba Active Directory podporuje možnost testování hesla, aby bylo možné zjistit, zda splňuje aktuální požadavky na složitost hesla domény, například pomocí rozhraní [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API. Když se heslo tímto způsobem ověří, testování zahrnuje taky ověřování v produktech založených na filtrech hesel, jako je třeba ochrana heslem Azure AD. uživatelská jména předaná do dané knihovny DLL filtru hesel ale budou prázdná. V tomto scénáři ochrana heslem Azure AD bude i nadále ověřovat heslo pomocí zásad hesel v současné době a vydá zprávu protokolu událostí pro zachycení výsledku, ale zpráva protokolu událostí bude mít prázdná pole uživatelského jména.

**Otázka: Je podporováno pro instalaci ochrany heslem Azure AD vedle sebe pomocí dalších produktů založených na filtrech hesel?**

Ano. Podpora více registrovaných knihoven DLL filtru hesel je základní funkcí Windows, která není specifická pro ochranu heslem Azure AD. Před přijetím hesla musí všechny knihovny DLL registrovaných filtrů hesel souhlasit.

**Otázka: Jak můžu nasadit a nakonfigurovat ochranu hesel Azure AD v prostředí služby Active Directory bez použití Azure?**

Nepodporuje se. Ochrana heslem Azure AD je funkce Azure, která podporuje rozšíření do místního prostředí Active Directory.

**Otázka: Jak mohu změnit obsah zásady na úrovni služby Active Directory?**

Nepodporuje se. Zásady se dají spravovat jenom pomocí portálu pro správu Azure AD. Podívejte se také na předchozí otázku.

**Otázka: Proč je Služba DFSR nutná pro replikaci adresáře SYSVOL?**

Služba replikace souborů (z předchůdce do DFSR) má mnoho známých problémů a je zcela nepodporovaná v novějších verzích služby Windows Server Active Directory. Žádné testování ochrany heslem Azure AD se provede na doménách konfigurovaných pro službu FRS.

Další informace najdete v následujících článcích:

[Případ migrace replikace adresáře SYSVOL do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Konec je Nigh pro službu FRS.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Otázka: Kolik místa na disku vyžaduje funkce ve sdílené složce SYSVOL v doméně?**

Přesné využití místa se liší, protože závisí na faktorech, jako je počet a délka zakázaných tokenů v seznamu globálních zakázaných společností Microsoft a na vlastní seznam pro jednotlivé klienty, a na režii šifrování. Obsah těchto seznamů se v budoucnu může zvětšovat. V takovém případě je vhodné předpokládat, že tato funkce bude potřebovat nejméně pět (5) MB místa na sdílené složce SYSVOL v doméně.

**Otázka: Proč je k instalaci nebo upgradu softwaru agenta DC nutný Restart?**

Tento požadavek způsobuje základní chování systému Windows.

**Otázka: Existuje nějaký způsob, jak nakonfigurovat agenta řadiče domény pro použití konkrétního proxy server?**

Ne. Vzhledem k tomu, že proxy server je Bezstavová, není důležité použít konkrétní proxy server.

**Otázka: Je v pořádku nasazovat službu proxy ochrany heslem Azure AD vedle sebe s jinými službami, jako je například Azure AD Connect?**

Ano. Služba proxy ochrany heslem Azure AD a Azure AD Connect by nikdy neměly kolidovat přímo.

**Otázka: V jakém pořadí by se měly agenti a proxy servery DC instalovat a registrovat?**

Podporuje se jakékoli pořadí instalace agenta proxy, instalace agenta DC, registrace doménové struktury a registrace proxy serveru.

**Otázka: Mám obavy o úspěšnosti výkonu na řadičích domény z nasazení této funkce?**

Služba agenta řadiče domény služby Azure AD Password Protection by neměla významně ovlivnit výkon řadiče domény v existujícím nasazení služby Active Directory v pořádku.

U většiny nasazení služby Active Directory jsou operace změny hesla malým podílem celkového zatížení na jakémkoli daném řadiči domény. Představte si například doménu služby Active Directory s uživatelskými účty 10000 a zásadami MaxPasswordAge nastavenými na 30 dní. V průměru bude tato doména zobrazovat 10 000/30 = ~ 333 operace změny hesla každý den, což je menší počet operací pro i jeden řadič domény. Vezměte v úvahu možný nejhorší scénář: Předpokládejme, že se na jednom řadiči domény provedly změny hesel 333 za jednu hodinu. K tomuto scénáři může například dojít, když spousta zaměstnanců pracuje na pondělí ráno. I v takovém případě se pořád zobrazujeme přibližně 333/60 minut = šest změn hesel za minutu, což ještě není významné zatížení.

Pokud jsou však stávající řadiče domény již spuštěny na úrovni omezené výkonem (například vyčerpáním z hlediska procesoru, místa na disku, v/v disku atd.), je vhodné přidat další řadiče domény nebo rozšířit dostupné místo na disku. nasazuje se tato funkce. Podívejte se také na otázku výše o využití místa na disku SYSVOL.

**Otázka: Chci otestovat ochranu heslem Azure AD na několika řadičích domény v doméně. Je možné vynutit, aby se změny hesla uživatele používaly pro tyto konkrétní řadiče domény?**

Ne. Klientský operační systém Windows řídí, který řadič domény se používá, když uživatel změní heslo. Řadič domény je vybraný na základě faktorů, jako je například lokalita a přiřazení podsítě služby Active Directory, konfigurace sítě specifická pro prostředí atd. Ochrana heslem Azure AD tyto faktory neřídí a nemůže ovlivnit, který řadič domény je vybraný pro změnu hesla uživatele.

Jedním ze způsobů, jak částečně dosáhnout tohoto cíle, je nasazení ochrany heslem Azure AD na všech řadičích domény v dané lokalitě služby Active Directory. Tento přístup poskytne přiměřené pokrytí pro klienty systému Windows, kteří jsou k této lokalitě přiřazeni, a proto také pro uživatele, kteří se přihlásí do těchto klientů a mění hesla.

**Otázka: Pokud nainstalujete službu agenta řadiče domény služby Azure AD heslem na jenom primární řadič domény, budou se chránit taky všechny ostatní řadiče domény v doméně?**

Ne. Když se heslo uživatele změní na daném řadiči domény, který není řadičem domény, heslo nešifrovaných textů se nikdy nepošle na primární řadič domény (Tento nápad je běžnou přívnímáním). Jakmile se v daném řadiči domény přijme nové heslo, tento řadič domény toto heslo použije k vytvoření různých hash hodnot specifických pro ověřování pro dané heslo a pak tyto hodnoty hash uchovává v adresáři. Heslo nešifrovaných textů není trvalé. Aktualizované hodnoty hash se pak replikují na primární řadič domény. Uživatelská hesla se můžou v některých případech změnit přímo na primárním řadiči domény, a to v závislosti na různých faktorech, jako je síťová topologie a návrh lokality služby Active Directory. (Podívejte se na předchozí otázku.)

V souhrnu je potřeba nasazení služby agenta řadiče domény služby Azure AD Password na primárním řadiči domény dosáhnout 100% pokrytí zabezpečení funkce v rámci domény. Nasazení funkce na primární řadič domény jenom neposkytuje výhody zabezpečení ochrany heslem Azure AD pro žádné jiné řadiče domény v doméně.

**Otázka: Je System Center Operations Manager k dispozici Management Pack ochrany heslem Azure AD?**

Ne.

**Otázka: Proč Azure pořád odmítá slabé heslo, i když jsem nakonfigurovali zásady tak, aby byly v režimu auditování?**

Režim auditování se podporuje jenom v prostředí místní služby Active Directory. V případě, že je vyhodnocena hesla, Azure je implicitně vždy v režimu vynucení.

## <a name="additional-content"></a>Další obsah

Následující odkazy nejsou součástí základní dokumentace k ochraně hesel Azure AD, ale mohou být užitečným zdrojem dalších informací o této funkci.

[Ochrana heslem Azure AD je teď všeobecně dostupná!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Průvodce ochranou útoků phishing – část 15: Implementujte Microsoft Azure AD službu ochrany heslem (pro místní prostředí).](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Ochrana hesel Azure AD a inteligentní uzamčení jsou teď v Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Dostupné školení podpory Microsoft Premier\Unified

Pokud vás zajímá více o ochraně heslem Azure AD a jejich nasazení ve vašem prostředí, můžete využít výhod služby Microsoft Proactive Service dostupné zákazníkům se smlouvou Premier nebo Unified support. Služba se nazývá Azure Active Directory: Ochrana heslem. Další informace vám poskytne váš správce technického účtu.

## <a name="next-steps"></a>Další postup

Pokud máte místní otázku ochrany heslem služby Azure AD, která tady není zodpovězená, pošlete nám níže uvedenou položku zpětné vazby.

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
