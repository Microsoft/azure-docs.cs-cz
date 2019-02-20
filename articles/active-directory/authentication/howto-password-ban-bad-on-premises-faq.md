---
title: Místní ochrana hesel Azure AD – nejčastější dotazy
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
ms.openlocfilehash: 8b29e4f6e6e877a3d03ded9edd9e282e0960e177
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414625"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Verze Preview: Azure AD ochrana heslem v místním – nejčastější dotazy

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Obecné otázky

**Otázka: Při dosažení obecné dostupnosti (GA) ochranu hesel služby Azure AD**

Verze GA plánujeme přidat Q1 CY2019 (konce března 2019). Děkujeme, že jste pro každého uživatele, který poskytl zpětnou vazbu na funkci tak, aby datum - Vážíme si ho!

**Otázka: O tom, jak vybrat zabezpečené heslo jaké by měly mít uživatelé?**

Aktuální pokyny Microsoftu k tomuto tématu najdete na následující odkaz:

[Pokyny Microsoftu heslo](https://www.microsoft.com/en-us/research/publication/password-guidance)

**Otázka: Je místní ochrana hesel Azure AD nepodporuje v jiných veřejných cloudech?**

Ne – místní ochrana hesel Azure AD je podporován pouze ve veřejném cloudu. Žádné datum oznámil dostupnost bez veřejného cloudu.

**Otázka: Jak můžu použít výhody ochrany hesel služby Azure AD na určitou podskupinu uživatelů v místním?**

Nepodporuje se. Jakmile nasazená a aktivovaná, ochrana hesel Azure AD není rozlišení – všichni uživatelé obdrží stejné výhody.

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

Využití místa na přesné nelze zadat přesně, protože závisí na faktorech, jako je počet a délku zakázaných hesel tokeny v seznamu zakázaných hesel globální společnosti Microsoft a na tenanta vlastní zakázané seznam hesel a režijní náklady na šifrování. Obsah těchto seznamů je pravděpodobně růst v budoucnu. To na paměti, přiměřené aktuální pravidlem je, že tato funkce bude nutné aspoň pět (5) MB místa u sdílené složky sysvol domény.

**Otázka: Proč je třeba restartovat počítač pro instalaci nebo upgrade softwaru agenta řadiče domény?**

Tento požadavek je způsobeno jádra Windows chování.

**Otázka: Existuje nějaký způsob, jak nakonfigurovat agenta do řadiče domény použít konkrétní proxy server?**

Ne. Mějte na paměti, protože je Bezstavová proxy server, není důležité který konkrétní proxy server se používá.

**Otázka: Je možné nasadit Proxy ochrana hesel Azure AD service-souběžně na počítači s ostatními službami, jako je například Azure AD Connect?**

Ano. Služba Proxy ochrana hesel Azure AD a Azure AD Connect by nikdy konfliktu navzájem přímo.

**Otázka: Zajímat o výkonu, klikněte na Moje řadičů domény z nasazení této funkce?**

Služba agenta ochrany DC hesel služby Azure AD slouží k byly co nejúčinnější a by neměl výrazně ovlivnit výkon řadiče domény v existující nasazení služby Active Directory, která je již dostatečně zdroje.

To je užitečné si uvědomit, že pro většinu nasazení hesla služby Active Directory jsou jejich změn malou část celkové zatížení na všech řadičích domény v dané. Jako příklad imagine domény služby Active Directory s 10000 uživatelské účty a MaxPasswordAge zásady nastavené na 30 dnů. V průměru tuto doménu uvidí 10000 30 = ~ 333 jejich změn každý den, což je menší počet operací pro ještě jeden řadič domény. Vkládání příkladu potenciální nejhorším scénářem případu, Předpokládejme, že tyto ~ 333 změny hesla na jednoho řadiče domény se provádí za jednu hodinu (například v tomto scénáři může dojít při velký počet zaměstnanců všechny přijdou do práce v pondělí ráno); i v tomto případě budete stále se díváme na ~333/60 minuty = šest změny hesla za minutu, která znovu není významné zatížení.

To ale nutné dodat Pokud aktuální řadiče domény už běží na úrovních výkonu limited (například maxed navýšení kapacity s ohledem na využití procesoru, místo na disku, vstupně-výstupních operací disku atd.), bylo by vhodné nasazení dalších řadičů domény a/nebo rozbalte disku místo, před nasazením ochrany hesel služby Azure AD. Viz také otázku sysvol využití místa na disku výše uvedených výše.

**Otázka: Chcete otestovat ochranu hesel služby Azure AD na několika řadičů domény ve své doméně. Je možné vynutit změny hesel uživatelů používat tyto konkrétní řadiče domény?**

Ne. Když uživatel změní heslo, řadič domény, který se používá zvolila Windows klientský operační systém na základě faktorů, jako je například přiřazení lokality a podsítě služby Active Directory, konfigurace specifických pro prostředí sítě atd. Ochrana hesel Azure AD nemá vliv na nebo řídit těmito faktory a proto nemůžete ovlivnit který řadič domény je vybraná změnit heslo daného uživatele.

To ale nutné dodat bude přístup, který se dodává blízko budete jen simulovat tento cíl nasazení hesel služby Azure AD ochrany na všech řadičích domény v dané lokalitě služby Active Directory. Tento přístup přináší rozumně všudypřítomná pokrytí pro Windows klienti přiřazení k lokalitě, a proto také pro uživatele, které jsou protokolování do těchto klientů a měnit heslo.

**Otázka: Pokud mám nainstalovat službu agenta ochrany DC hesel služby Azure AD na jenom primární řadič domény (PDC), budou všechny ostatní řadiče domény v doméně také chráněné?**

Ne. Při změně uživatelského hesla na řadič domény dané bez-primárního řadiče domény, textové heslo nikdy neodesílá do primárního řadiče domény (Tato myšlenka je běžné chybné vnímání). Místo toho po přijetí nové heslo na daný řadič domény se používá toto heslo k vytvoření různých ověřování protokolu konkrétní hodnoty hash hesla řadiče domény a pak opakuje tyto hodnoty hash v adresáři. Ty aktualizovat hodnoty hash se pak replikují do primárního řadiče domény – ale ochrana hesel Azure AD není součástí tohoto procesu. V některých případech může být uživatelská hesla změněn přímo na primární řadič domény, znovu v závislosti na různých faktorech, například síťové topologie a návrh lokality služby Active Directory. (Viz předchozí otázce.)

Slouží ke shrnutí: nasazení služby Azure AD hesla ochrany řadič domény agenta na primární řadič domény se vyžaduje k dosažení 100 % pokrytí funkce zabezpečení, ale který sám o sobě neposkytuje výhody zabezpečení ochrany hesel služby Azure AD pro všechny řadiče domény v doméně.

**Otázka: Je k dispozici sady System Center Operations Manager (SCOM) management pack pro ochranu hesel služby Azure AD?**

Ne.

## <a name="additional-content"></a>Další obsah

Na následujících odkazech nejsou součástí základní dokumentace ke službě ochrana hesel Azure AD, ale může být užitečné zdroje dalších informací o funkci.

[E-mailu Průvodce ochrany Phishing – část 15: Implementace služby ochrany hesel služby Microsoft Azure AD (v místním příliš!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD ochrana heslem a inteligentním uzamčením jsou nyní ve verzi Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="next-steps"></a>Další postup

Pokud máte dotaz ochrana hesel Azure AD s místními, který jsem zde nenalezl odpověď, odešlete názor níže – Děkujeme!

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
