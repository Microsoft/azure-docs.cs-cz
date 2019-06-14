---
title: Ochrana hesel Azure AD – nejčastější dotazy – Azure Active Directory on-premises
description: Místní ochrana hesel Azure AD – nejčastější dotazy
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
ms.openlocfilehash: 3bd117b79c2d103225e8f1f29b63eb6ae341031d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917662"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD ochrana heslem v místním – nejčastější dotazy

## <a name="general-questions"></a>Obecné otázky

**Otázka: O tom, jak vybrat zabezpečené heslo jaké by měly mít uživatelé?**

Aktuální pokyny Microsoftu k tomuto tématu najdete na následující odkaz:

[Pokyny Microsoftu heslo](https://www.microsoft.com/en-us/research/publication/password-guidance)

**Otázka: Je místní ochrana hesel Azure AD nepodporuje v jiných veřejných cloudech?**

Ne – místní ochrana hesel Azure AD je podporován pouze ve veřejném cloudu. Žádné datum oznámil dostupnost bez veřejného cloudu.

**Otázka: Jak můžu použít výhody ochrany hesel služby Azure AD na určitou podskupinu uživatelů v místním?**

Nepodporuje se. Jakmile nasazená a aktivovaná, nebude rozlišení ochrana hesel Azure AD – všichni uživatelé obdrží stejné výhody.

**Otázka: Jaký je rozdíl mezi změnu hesla a nastavení hesla (nebo reset)?**

Změna hesla se, když uživatel vybere nové heslo po prokázání, že budou mít znalosti o staré heslo. Například to je, co se stane, když uživatel přihlásí do Windows a pak bude muset zvolit nové heslo.

Nastavení hesla (říká se jim resetování hesla) je, když správce nahrazuje heslo pro účet s novým heslem, například pomocí nástroje správy Active Directory Users and Computers. Tato operace vyžaduje vysokou úroveň oprávnění (obvykle správce domény) a osoba provádějící operaci obvykle nemá žádné znalosti o staré heslo. Scénáře technické podpory to často udělat, například když pomáhám uživatel, který se zapomněl svoje heslo. Zobrazí se také, že události, kdy je úplně nový uživatelský účet se vytváří první přihlášení pomocí hesla nastavené heslo.

Zásady ověření hesla se chová stejně bez ohledu na to, jestli se provádí změny hesla nebo set. Služba agenta ochrany DC hesel služby Azure AD protokolovat různé události do poznáte, zda změna hesla nebo bylo provedeno operaci set.  Zobrazit [ochrana hesel Azure AD, monitorování a protokolování](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Otázka: Je podporováno nainstalovat ochranu hesel služby Azure AD souběžně s jinými produkty pomocí filtru hesla?**

Ano. Podpora více knihovny DLL filtru registrované heslo je základní funkcí Windows a nezávislé na ochranu hesel služby Azure AD. Všechny knihovny DLL filtru registrované hesla musí souhlasit, před přijetím heslo.

**Otázka: Jak můžete nasadit a nakonfigurovat ochranu hesel služby Azure AD v Moje prostředí služby Active Directory bez použití Azure?**

Nepodporuje se. Azure AD hesla Protection je funkce služby Azure, která podporuje se rozšíří do prostředí místní služby Active Directory.

**Otázka: Jak můžete upravit obsah zásad na úrovni služby Active Directory?**

Nepodporuje se. Zásady je možné spravovat jenom pomocí portálu pro správu Azure AD. Také najdete v předchozí otázce.

**Otázka: Proč je DFSR se vyžaduje k replikování adresáře sysvol?**

Služba replikace souborů (technologie předchůdce do DFSR) má mnoho známé problémy a není zcela podporována v novějších verzích Windows Server Active Directory. Nulové testování ochrana hesel Azure AD se nevytvoří FRS nakonfigurované domény.

Další informace najdete v následujících článcích:

[Platí pro migraci replikace adresáře sysvol pro službu DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Je konec Nigh u služby FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Otázka: Ve sdílené složce sysvol domény kolik místa na disku vyžaduje funkci?**

Využití přesné místo se liší, protože závisí na faktorech, jako je počet a délku zakázané tokeny v seznamu globální zakázané Microsoft a vlastní seznam na tenanta a režijní náklady na šifrování. Obsah těchto seznamů je pravděpodobně růst v budoucnu. To na paměti je předpokládat, že tato funkce bude nutné aspoň pět (5) MB místa u sdílené složky sysvol domény.

**Otázka: Proč je třeba restartovat počítač pro instalaci nebo upgrade softwaru agenta řadiče domény?**

Tento požadavek je způsobeno jádra Windows chování.

**Otázka: Existuje nějaký způsob, jak nakonfigurovat agenta do řadiče domény použít konkrétní proxy server?**

Ne. Protože je Bezstavová proxy server, není důležité který konkrétní proxy server se používá.

**Otázka: Je možné nasadit službu Proxy ochrana hesel Azure AD vedle dalších služeb, jako je například Azure AD Connect?**

Ano. Služba Proxy ochrana hesel Azure AD a Azure AD Connect by nikdy konfliktu navzájem přímo.

**Otázka: V jakém pořadí by řadiče domény agenty a servery proxy instalaci a registraci?**

Žádné řazení instalace agenta Proxy, instalace agenta pro řadič domény, registrace doménové struktury a registrace proxy serveru je podporována.

**Otázka: Zajímat o výkonu, klikněte na Moje řadičů domény z nasazení této funkce?**

Služba agenta ochrany DC hesel služby Azure AD by neměl výrazně ovlivnit výkon řadiče domény v existující nasazení služby Active Directory v pořádku.

Pro většinu nasazení hesla služby Active Directory změnu operace jsou malou část celkové zatížení na žádném řadiči dané domény. Jako příklad imagine domény služby Active Directory s 10000 uživatelské účty a MaxPasswordAge zásady nastavené na 30 dnů. V průměru tuto doménu uvidí 10000 30 = ~ 333 jejich změn každý den, což je menší počet operací pro ještě jeden řadič domény. Vezměte v úvahu potenciální nejhorší scénář: Předpokládejme, že tyto ~ 333 změny hesla na jednoho řadiče domény se provádí za jednu hodinu. Tento scénář může například dojít při mnoho pracovníků všechny přijdou do práce v pondělí ráno. Dokonce i v tomto případě stále bychom ~333/60 minut = šest změny hesla za minutu, která znovu není významné zatížení.

Nicméně pokud vaše aktuální řadiče domény už běží na úrovních výkonu limited (například maxed navýšení kapacity s ohledem na využití procesoru, místo na disku, disk vstupně-výstupních operací, atd.), se doporučuje přidejte další řadiče domény nebo rozšíření volného místa na disku před nasazení této funkce. Viz také otázku sysvol využití místa na disku výše uvedených výše.

**Otázka: Chcete otestovat ochranu hesel služby Azure AD na několika řadičů domény ve své doméně. Je možné vynutit změny hesel uživatelů používat tyto konkrétní řadiče domény?**

Ne. Ovládací prvky Windows klientský operační systém, který řadič domény se používá, když uživatel změní své heslo. Řadič domény je vybrána na základě faktorů, jako je například přiřazení lokality a podsítě služby Active Directory, konfigurace specifických pro prostředí sítě atd. Azure AD hesla ochrany neřídí tyto faktory a nemůžete ovlivnit, který řadič domény je se rozhodli změnit heslo uživatele.

Jedním ze způsobů částečně dosažení tohoto cíle může být nasazení ochrany hesel služby Azure AD na všechny řadiče domény v dané lokalitě služby Active Directory. Tento přístup přináší přiměřené pokrytí pro klienty Windows, které jsou přiřazeny k této lokalitě, a proto také pro uživatele, které jsou protokolování do těchto klientů a měnit heslo.

**Otázka: Pokud mám nainstalovat službu agenta ochrany DC hesel služby Azure AD na jenom primární řadič domény (PDC), budou všechny ostatní řadiče domény v doméně také chráněné?**

Ne. Při změně uživatelského hesla na řadič domény dané bez-primárního řadiče domény, textové heslo nikdy neodesílá do primárního řadiče domény (Tato myšlenka je běžné chybné vnímání). Po přijetí nové heslo na řadiči domény se daný řadiče domény používá toto heslo pro vytvoření různých ověřování protokolu konkrétní hodnoty hash hesla a poté opakuje tyto hodnoty hash v adresáři. Heslo nešifrovaný text není trvalý. Aktualizované hodnoty hash se pak replikují do primární řadič domény. V některých případech může být uživatelská hesla změněn přímo na primární řadič domény, znovu v závislosti na různých faktorech, například síťové topologie a návrh lokality služby Active Directory. (Viz předchozí otázce.)

Stručně řečeno nasazení službu agenta ochrany DC hesel služby Azure AD na primární řadič domény se vyžaduje k dosažení 100 % pokrytí funkce zabezpečení napříč doménou. Nasazení funkce na primární řadič domény pouze neposkytuje výhody zabezpečení ochrany hesel služby Azure AD pro všechny řadiče domény v doméně.

**Otázka: Je k dispozici sady System Center Operations Manager management pack pro ochranu hesel služby Azure AD?**

Ne.

**Otázka: Proč Azure stále zamítá Slabá hesla i v případě, že konfigurace zásad v režimu auditování?**

Režim auditování je podporována pouze v místním prostředí služby Active Directory. Azure je implicitně vždy v režimu "Vynutit" když je vyhodnocen jako hesla.

## <a name="additional-content"></a>Další obsah

Na následujících odkazech nejsou součástí základní dokumentace ke službě ochrana hesel Azure AD, ale může být užitečné zdroje dalších informací o funkci.

[Ochrana hesel Azure AD je teď obecně dostupná!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-mailu Průvodce ochrany Phishing – část 15: Implementace služby ochrany hesel služby Microsoft Azure AD (v místním příliš!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD ochrana heslem a inteligentním uzamčením jsou nyní ve verzi Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Školení podpory Premier\Unified Microsoftu k dispozici

Pokud vás zajímají další informace o ochraně hesel služby Azure AD a jeho nasazení ve vašem prostředí, můžete využít výhod proaktivní služby Microsoftu k dispozici pro zákazníky se smlouvu o podpoře Premier nebo Unified. Služba se nazývá Azure Active Directory: Ochrana heslem. Další informace vám poskytne Technical Account Manager.

## <a name="next-steps"></a>Další postup

Pokud máte dotaz ochrana hesel Azure AD s místními, který jsem zde nenalezl odpověď, odešlete názor níže – Děkujeme!

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
