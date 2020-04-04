---
title: Nejčastější dotazy týkající se místní ochrany heslem Azure AD
description: Zkontrolujte nejčastější dotazy týkající se ochrany heslem služby Azure AD v místním prostředí služby Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16537ba72eb9f11abd8de47256150818c121a140
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652644"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD Ochrana heslem místní nejčastější dotazy

Tato část obsahuje odpovědi na mnoho běžně kladených otázek týkajících se ochrany heslem Azure AD.

## <a name="general-questions"></a>Obecné otázky

**Otázka: Jaké pokyny by měly být poskytnuty uživatelům o tom, jak vybrat bezpečné heslo?**

Aktuální pokyny společnosti Microsoft k tomuto tématu naleznete na následujícím odkazu:

[Pokyny pro hesla společnosti Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Otázka: Je místní ochrana heslem Azure AD podporovaná v neveřejných cloudech?**

Ne – místní ochrana heslem Azure AD je podporovaná jenom ve veřejném cloudu. Pro neveřejnou dostupnost cloudu nebylo oznámeno žádné datum.

Portál Azure AD umožňuje úpravy místní konfigurace "Ochrana heslem pro službu Active Directory systému Windows Server" i v neveřejných cloudech; tyto změny budou přetrvávat, ale jinak se nikdy neprojeví. Registrace místních proxy agentů nebo doménových struktur není podporována při použití pověření neveřejného cloudu a všechny takové pokusy o registraci se vždy nezdaří.

**Otázka: Jak můžu použít výhody ochrany heslem Azure AD na podmnožinu mých místních uživatelů?**

Není podporováno. Po nasazení a povolení azure ad ochrana heslem nediskriminuje – všichni uživatelé obdrží stejné výhody zabezpečení.

**Otázka: Jaký je rozdíl mezi změnou hesla a nastavením hesla (nebo resetováním)?**

Změna hesla je, když uživatel zvolí nové heslo po prokázání, že mají znalosti staré heslo. Například změna hesla je to, co se stane, když se uživatel přihlásí k systému Windows a pak je vyzván k výběru nového hesla.

Sada hesel (někdy označovaná jako resetování hesla) je, když správce nahradí heslo účtu novým heslem, například pomocí nástroje pro správu uživatelů a počítačů služby Active Directory. Tato operace vyžaduje vysokou úroveň oprávnění (obvykle Domain Admin) a osoba provádějící operaci obvykle nemá znalosti starého hesla. Scénáře technické podpory často provádějí sady hesel, například při pomoci uživateli, který zapomněl své heslo. Zobrazí se také události nastavení hesla při vytvoření zcela nového uživatelského účtu poprvé s heslem.

Zásady ověření hesla se chovají stejně bez ohledu na to, zda se provádí změna nebo nastavení hesla. Služba Agent pro ochranu hesel Azure AD zaznamenává různé události, aby vás informovala, zda byla provedena změna nebo nastavení hesla.  Viz [Monitorování a protokolování ochrany heslem Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Otázka: Proč jsou duplicitní události odmítnutí hesla zaznamenány při pokusu o nastavení slabého hesla pomocí modulu snap-in Správa uživatelů a počítačů služby Active Directory?**

Modul snap-in Správa uživatelů a počítačů služby Active Directory se nejprve pokusí nastavit nové heslo pomocí protokolu Kerberos. Po selhání se modul snap-in pokusí nastavit heslo pomocí staršího protokolu (SAM RPC) (použité konkrétní protokoly nejsou důležité). Pokud nové heslo je považováno za slabé službou Azure AD Password Protection, toto chování modulu snap-in bude mít za následek dvě sady událostí obnovení hesla odmítnutí jsou protokolovány.

**Otázka: Proč jsou události ověření hesla služby Azure AD zaznamenány s prázdným uživatelským jménem?**

Služba Active Directory podporuje možnost otestovat heslo a zjistit, zda předá aktuální požadavky domény na složitost hesla, například pomocí rozhraní [API NetValidatePasswordPolicy.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Při ověření hesla tímto způsobem testování zahrnuje také ověření pomocí hesel filtr dll založené produkty, jako je například Ochrana heslem Azure AD – ale uživatelská jména předaná dané heslo filtru dll bude prázdný. V tomto scénáři Azure AD Password Protection bude stále ověřit heslo pomocí aktuálně v effect zásady hesla a vydá zprávu protokolu událostí zachytit výsledek, ale zpráva protokolu událostí bude mít prázdná pole uživatelského jména.

**Otázka: Je podporována instalace Azure AD Password Protection vedle sebe s jinými produkty založenými na filtru hesel?**

Ano. Podpora pro více registrovaných dll filtru hesel je základní funkce systému Windows a není specifická pro Azure AD Password Protection. Všechny registrované dll filtru hesel musí souhlasit před přijetím hesla.

**Otázka: Jak můžu nasadit a nakonfigurovat Azure AD Password Protection v mém prostředí Služby Active Directory bez použití Azure?**

Není podporováno. Azure AD Password Protection je funkce Azure, která podporuje rozšíření do místního prostředí služby Active Directory.

**Otázka: Jak lze upravit obsah zásad na úrovni služby Active Directory?**

Není podporováno. Zásady lze spravovat pouze pomocí portálu Azure AD. Viz také předchozí otázka.

**Otázka: Proč je pro replikaci sysvol vyžadováno dfsr?**

Frs (předchůdce technologie DFSR) má mnoho známých problémů a je zcela nepodporovaný v novějších verzích služby Windows Server Active Directory. Nulové testování ochrany heslem Azure AD se bude provádět na konfigurovaných doménách frs.

Další informace naleznete v následujících článcích:

[Případ pro migraci replikace sysvol do dfsr](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Konec je blízko pro FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Pokud vaše doména ještě nepoužívá DFSR, musíte ji migrovat, abyste před instalací služby Azure AD Password Protection používali dfsr. Další informace naleznete v následujícím odkazu:

[Průvodce migrací replikace SYSVOL: Frs na replikaci distribuovaného systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Software Azure AD Password Protection DC Agent se v současné době nainstaluje do řadičů domény v doménách, které stále používají frs pro replikaci sysvol, ale software nebude fungovat správně v tomto prostředí. Mezi další negativní vedlejší účinky patří, že se jednotlivé soubory nepodaří replikovat, a postupy obnovení sysvol, které se zdají být úspěšné, ale tiše nedaří replikovat všechny soubory. Měli byste migrovat doménu používat DFSR co nejdříve, a to jak pro vlastní výhody DFSR a také odblokovat nasazení Azure AD Password Protection. Budoucí verze softwaru budou automaticky zakázány při spuštění v doméně, která stále používá FRS.

**Otázka: Kolik místa na disku vyžaduje funkce na sdílené sysvol domény?**

Přesné využití místa se liší, protože závisí na faktorech, jako je počet a délka zakázaných tokenů v globálním seznamu zakázaných položek společnosti Microsoft a vlastní seznam pro jednotlivé klienty a režie šifrování. Obsah těchto seznamů pravděpodobně v budoucnu poroste. S ohledem na to je rozumné očekávání, že tato funkce bude potřebovat alespoň pět (5) megabajtů místa na sdílené sysvol domény.

**Otázka: Proč je k instalaci nebo upgradu softwaru agenta řadiče domény nutné restartovat počítač?**

Tento požadavek je způsoben základní chování systému Windows.

**Otázka: Existuje nějaký způsob, jak nakonfigurovat agenta řadiče domény pro použití určitého proxy serveru?**

Ne. Vzhledem k tomu, že proxy server je bezstavový, není důležité, který konkrétní proxy server se používá.

**Otázka: Je v pořádku nasadit službu Proxy ochrany hesel Azure AD vedle jiných služeb, jako je Azure AD Connect?**

Ano. Služba Proxy ochrany heslem Azure AD a Azure AD Connect by nikdy neměly být v konfliktu přímo mezi sebou.

Bohužel byla nalezena nekompatibilita mezi verzí služby Microsoft Azure AD Connect Agent Updater, která je nainstalovaná softwarem proxy azure ad password protection, a verzí služby nainstalované softwarem [proxy aplikace Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Tato nekompatibilita může způsobit, že služba Agent Updater nebude moci kontaktovat Azure kvůli aktualizacím softwaru. Doporučujeme instalovat proxy server Azure AD Password Protection Proxy a proxy aplikace Azure Active Directory do stejného počítače.

**Otázka: V jakém pořadí by měly být nainstalovány a registrovány agenti dc a proxy?**

Jakékoli pořadí instalace agenta proxy, instalace agenta řadiče domény, registrace doménové struktury a registrace proxy je podporováno.

**Otázka: Mám být znepokojen výkon hit na mé řadiče domény z nasazení této funkce?**

Služba Azure AD Password Protection DC Agent by neměla významně ovlivnit výkon řadiče domény v existujícím nasazení služby Active Directory.

Pro většinu operací se změnou hesla nasazení služby Active Directory je malá část celkového zatížení na daném řadiči domény. Jako příklad si představte doménu služby Active Directory s 10000 uživatelskými účty a zásadou MaxPasswordAge nastavenou na 30 dní. V průměru se této doméně každý den zobrazí 10000/30=~333 operací změny hesla, což je menší počet operací i pro jeden řadič domény. Zvažte potenciální nejhorší scénář: Předpokládejme, že tyto změny hesla ~333 na jednom řadiči domény byly provedeny během jedné hodiny. Například tento scénář může nastat, když mnoho zaměstnanců všichni přijdou do práce v pondělí ráno. Dokonce i v takovém případě se stále díváme na ~ 333/60 minut = šest změn hesla za minutu, což opět není významné zatížení.

Pokud jsou však aktuální řadiče domény již spuštěny na úrovních s omezeným výkonem (například maximální množství s ohledem na procesor, místo na disku, vstupně-to disk atd.), je vhodné před nasazením této funkce přidat další řadiče domény nebo rozšířit dostupné místo na disku. Viz také výše uvedená otázka týkající se využití místa na disku sysvol výše.

**Otázka: Chci otestovat Azure AD Password Protection na několik řadičů domény v mé doméně. Je možné vynutit změny hesla uživatele k použití těchto konkrétních řadičů domény?**

Ne. Operační systém Windows řídí, který řadič domény se používá při změně hesla uživatelem. Řadič domény je vybrán na základě faktorů, jako je přiřazení lokality služby Active Directory a podsítě, konfigurace sítě specifické pro prostředí atd. Azure AD Password Protection neřídí tyto faktory a nemůže ovlivnit, který řadič domény je vybrán pro změnu hesla uživatele.

Jedním ze způsobů, jak částečně dosáhnout tohoto cíle by bylo nasazení Azure AD Password Protection na všech řadičích domény v dané lokalitě služby Active Directory. Tento přístup bude poskytovat přiměřené pokrytí pro klienty systému Windows, které jsou přiřazeny k této lokalitě, a proto také pro uživatele, kteří se přihlašují k těmto klientům a mění jejich hesla.

**Otázka: Pokud nainstaluji službu Agent dc agent ad ochrany hesel Azure AD pouze na primární řadič domény (PDC), budou chráněny také všechny ostatní řadiče domény v doméně?**

Ne. Při změně hesla uživatele na daném řadiči domény bez primárního řadiče domény je heslo ve veřejném textu nikdy odesláno do primárního řadiče domény (tato myšlenka je běžné nesprávné vnímání). Jakmile je nové heslo přijato v daném řadiči domény, tento řadič domény používá toto heslo k vytvoření různých hash tohoto hesla specifických pro protokol ověřování a poté tyto zachycování zajistných hesel v adresáři zachová. Heslo ve zprávě o nepořádku není trvalé. Aktualizované hashe jsou pak replikovány do primárního řadiče domény. Uživatelská hesla mohou být v některých případech změněna přímo na primárním řadiči domény, opět v závislosti na různých faktorech, jako je topologie sítě a návrh lokality služby Active Directory. (Viz předchozí otázka.)

V souhrnu je nutné nasazení služby Azure AD Password Protection DC Agent na Primárním řadiči domény k dosažení 100% zabezpečení funkce v celé doméně. Nasazení funkce na Primárním řadiči domény neposkytuje jenom výhody zabezpečení Azure AD Password Protection pro žádné jiné řadiče domény v doméně.

**Otázka: Proč vlastní inteligentní uzamčení nefunguje ani po instalaci agentů v místním prostředí služby Active Directory?**

Vlastní inteligentní uzamčení je podporováno jenom ve službě Azure AD. Změny vlastního nastavení inteligentního uzamčení na portálu Azure AD nemají žádný vliv na místní prostředí služby Active Directory, a to ani s nainstalovanými agenty.

**Otázka: Je sada managementu Nástroje pro správu nástroje System Center Operations Manager dostupná pro ochranu heslem služby Azure AD?**

Ne.

**Otázka: Proč Azure AD stále odmítá slabá hesla, i když jsem nakonfiguroval zásady v režimu auditování?**

Režim auditování je podporován pouze v místním prostředí služby Active Directory. Azure AD je implicitně vždy v režimu "vynucovat", když vyhodnocuje hesla.

**Otázka: Moji uživatelé uvidí tradiční chybovou zprávu systému Windows, když je heslo odmítnuto službou Azure AD Password Protection. Je možné přizpůsobit tuto chybovou zprávu tak, aby uživatelé věděli, co se skutečně stalo?**

Ne. Chybová zpráva, kterou uživatelé vidí, když je heslo odmítnuto řadičem domény, je řízena klientským počítačem, nikoli řadičem domény. K tomuto chování dochází, pokud je heslo odmítnuto výchozími zásadami hesel služby Active Directory nebo řešením založeným na filtru hesel, jako je ochrana heslem Azure AD.

## <a name="additional-content"></a>Další obsah

Následující odkazy nejsou součástí základní dokumentace Azure AD Password Protection, ale může být užitečným zdrojem dalšíinformace o funkci.

[Azure AD Ochrana heslem je teď obecně k dispozici!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Průvodce ochranou proti phishingu e-mailů – část 15: Implementace služby ochrany heslem služby Microsoft Azure AD (také pro místní stránku!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Ochrana heslem a inteligentní uzamčení jsou teď ve veřejné verzi Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>K dispozici je školení podpory microsoft premier\unified

Pokud máte zájem dozvědět se více o službě Azure AD Password Protection a nasadit ji ve vašem prostředí, můžete využít proaktivní službu Microsoftu, která je dostupná pro zákazníky se smlouvou Premier nebo Unified support. Služba se nazývá Azure Active Directory: Password Protection. Další informace vám poskytne technický správce účtu.

## <a name="next-steps"></a>Další kroky

Pokud máte místní otázku ochrany heslem Azure AD, která zde není zodpovězena, odešlete položku zpětné vazby níže – děkuji!

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
