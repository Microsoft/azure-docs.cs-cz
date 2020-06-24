---
title: Koncepty doménové struktury prostředků pro Azure AD Domain Services | Microsoft Docs
description: Seznamte se s tím, co je doménová struktura prostředků v Azure Active Directory Domain Services a jak mají prospěch pro vaši organizaci v hybridním prostředí s omezenými možnostmi ověřování uživatelů nebo problémy zabezpečení.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 6f34ba9b9ebdc395338ef65b696fa9748417e20e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734907"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Koncepty a funkce doménové struktury prostředků pro Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje přihlašovací prostředí pro starší místní obchodní aplikace. Uživatelé, skupiny a hodnoty hash hesel místních i cloudových uživatelů se synchronizují do spravované domény Azure služba AD DS. Tato synchronizovaná hodnota hash hesla dává uživatelům jedinou sadu přihlašovacích údajů, které můžou používat pro místní služba AD DS, Office 365 a Azure Active Directory.

I když zabezpečení a poskytuje další výhody zabezpečení, některé organizace nemůžou synchronizovat hodnoty hash hesel uživatelů do služby Azure AD nebo Azure služba AD DS. Uživatelé v organizaci nemusí znát heslo, protože používají jenom ověřování pomocí čipové karty. Tato omezení brání některým organizacím v používání Azure služba AD DS k přenesení a posunutí místních klasických aplikací do Azure.

Pro řešení těchto potřeb a omezení můžete vytvořit spravovanou doménu, která používá doménovou strukturu prostředků. Tento koncepční článek vysvětluje, co jsou doménové struktury a jak důvěřují jiným prostředkům, aby poskytovala zabezpečenou metodu ověřování. Doménové struktury prostředků Azure služba AD DS jsou momentálně ve verzi Preview.

> [!IMPORTANT]
> Doménové struktury prostředků Azure služba AD DS v současné době nepodporují Azure HDInsight ani soubory Azure. Výchozí doménové struktury uživatelů Azure služba AD DS podporují obě tyto další služby.

## <a name="what-are-forests"></a>Co jsou doménové struktury?

*Doménová struktura* je logická konstrukce, kterou používá Active Directory Domain Services (služba AD DS) k seskupení jedné nebo více *domén*. Domény pak uchovávají objekty pro uživatele nebo skupiny a poskytují ověřovací služby.

V Azure služba AD DS doménová struktura obsahuje jenom jednu doménu. Místní doménové struktury služba AD DS často obsahují mnoho domén. Ve velkých organizacích, zejména po fúzích a akvizicích, můžete mít několik místních doménových struktur, které každý z nich obsahuje víc domén.

Ve výchozím nastavení je spravovaná doména vytvořena jako doménová struktura *uživatelů* . Tento typ doménové struktury synchronizuje všechny objekty z Azure AD, včetně všech uživatelských účtů vytvořených v místním služba AD DS prostředí. Uživatelské účty se můžou přímo ověřovat proti spravované doméně, třeba pro přihlášení k virtuálnímu počítači připojenému k doméně. Doménová struktura uživatelů funguje, když je možné synchronizovat hodnoty hash hesla a uživatelé nepoužívají exkluzivní metody přihlašování, jako je ověřování pomocí čipové karty.

V doménové struktuře *prostředků* Azure služba AD DS se uživatelé ověřují pomocí jednosměrné *důvěryhodnosti* doménové struktury ze své místní služba AD DS. S tímto přístupem se uživatelské objekty a hodnoty hash hesel nesynchronizují do Azure služba AD DS. Uživatelské objekty a přihlašovací údaje existují pouze v místních služba AD DS. Tento přístup umožňuje podnikům hostovat prostředky a aplikační platformy v Azure, které jsou závislé na klasických ověřováních, jako jsou protokoly LDAP, Kerberos nebo NTLM, ale všechny problémy s ověřováním nebo obavy se odeberou. Doménové struktury prostředků Azure služba AD DS jsou momentálně ve verzi Preview.

Doménové struktury prostředků také poskytují schopnost nasouvat a přesouvat aplikace po jednotlivých součástech najednou. Mnohé starší verze místních aplikací jsou vícevrstvé, často se používají webový server nebo front-end a mnoho součástí souvisejících s databází. Tyto vrstvy usnadňují navýšení a posunutí celé aplikace do cloudu v jednom kroku. Pomocí doménových struktur prostředků můžete svou aplikaci nasunout do cloudu v rámci postupného přístupu, což usnadňuje přesun vaší aplikace do Azure.

## <a name="what-are-trusts"></a>Co jsou vztahy důvěryhodnosti?

Organizace, které mají více než jednu doménu často potřebují uživatele pro přístup ke sdíleným prostředkům v jiné doméně. Přístup k těmto sdíleným prostředkům vyžaduje, aby se uživatelé v jedné doméně ověřovali v jiné doméně. Aby bylo možné zajistit tyto možnosti ověřování a autorizace mezi klienty a servery v různých doménách, musí mezi těmito dvěma doménami existovat *vztah důvěryhodnosti* .

U vztahů důvěryhodnosti domén ověřovací mechanismy pro každou doménu důvěřují ověřováním, které přicházejí z jiné domény. Vztahy důvěryhodnosti poskytují řízený přístup ke sdíleným prostředkům v doméně prostředků ( *důvěřující* doména) tím, že ověřují příchozí požadavky na ověření pocházející od důvěryhodné autority ( *důvěryhodné* domény). Vztahy důvěryhodnosti fungují jako mosty, které umožňují pouze ověřené žádosti o ověření mezi doménami.

Způsob, jakým vztah důvěryhodnosti projde požadavky na ověření, závisí na tom, jak je nakonfigurován. Vztahy důvěryhodnosti lze nakonfigurovat jedním z následujících způsobů:

* **Jednosměrné** – poskytuje přístup z důvěryhodné domény k prostředkům v důvěřující doméně.
* **Obousměrný** – poskytuje přístup z každé domény k prostředkům v druhé doméně.

Vztahy důvěryhodnosti je také možné nakonfigurovat tak, aby zpracovávala další vztahy důvěryhodnosti jedním z následujících způsobů:

* **Nepřenosná** – vztah důvěryhodnosti existuje jenom mezi dvěma doménami Trust partner.
* **Přenosný** -důvěryhodný se automaticky rozšiřuje na všechny ostatní domény, které partner důvěřuje.

V některých případech se vztahy důvěryhodnosti automaticky vytvoří při vytváření domén. Jindy, musíte zvolit typ vztahu důvěryhodnosti a výslovně vytvořit příslušné vztahy. Konkrétní typy vztahů důvěryhodnosti a struktura těchto vztahů důvěryhodnosti závisí na tom, jak je adresářová služba Active Directory organizována, a zda různé verze systému Windows v síti koexistovat.

## <a name="trusts-between-two-forests"></a>Vztahy důvěryhodnosti mezi dvěma doménovými strukturami

Důvěryhodnost domén v rámci jedné doménové struktury můžete roztáhnout do jiné doménové struktury tak, že ručně vytvoříte jednosměrný nebo obousměrný vztah důvěryhodnosti doménové struktury. Vztah důvěryhodnosti doménové struktury je tranzitivní vztah důvěryhodnosti, který existuje jenom mezi kořenovou doménou struktury a druhou kořenovou doménou doménové struktury.

* Jednosměrná důvěryhodnost doménové struktury umožňuje všem uživatelům v jedné doménové struktuře důvěřovat všem doménám v jiné doménové struktuře.
* Obousměrná důvěryhodnost domén tvoří vztah mezi doménami v obou doménových strukturách s tranzitivní důvěryhodností.

Přenositelnost vztahů důvěryhodnosti doménové struktury je omezená na tyto dva partnery doménové struktury. Vztah důvěryhodnosti doménové struktury se nerozšiřuje na další doménové struktury, které jsou pro některé z partnerů důvěryhodné.

![Diagram vztahu důvěryhodnosti doménové struktury z Azure služba AD DS do místního služba AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

V závislosti na struktuře služby Active Directory organizace můžete vytvořit různé konfigurace doménových a doménových vztahů důvěryhodnosti. Azure služba AD DS podporuje jenom jednosměrný vztah důvěryhodnosti mezi doménovými strukturami. V této konfiguraci můžou prostředky v Azure služba AD DS důvěřovat všem doménám v místní doménové struktuře.

## <a name="supporting-technology-for-trusts"></a>Podpůrná technologie pro vztahy důvěryhodnosti

Vztahy důvěryhodnosti využívají různé služby a funkce, jako je třeba DNS k vyhledání řadičů domény v partnerských doménových strukturách. Vztahy důvěryhodnosti také závisí na ověřovacích protokolech NTLM a Kerberos a v mechanismech ověřování a řízení přístupu na základě systému Windows, které vám pomůžou zajistit zabezpečenou komunikační infrastrukturu napříč doménami a doménovými strukturami Active Directory. Následující služby a funkce vám pomůžou podpořit úspěšné vztahy důvěryhodnosti.

### <a name="dns"></a>DNS

Služba AD DS potřebuje DNS pro umístění řadiče domény (DC) a pojmenování. K úspěšnému fungování služba AD DS je k dispozici následující Podpora služby DNS:

* Služba překladu názvů umožňující síťovým hostitelům a službám vyhledat řadiče domény.
* Struktura pojmenování, která umožňuje podniku odrážet svou organizační strukturu v názvech svých domén adresářových služeb.

Obvykle je nasazen obor názvů domény DNS, který odráží služba AD DS obor názvů domény. Pokud již existuje obor názvů DNS před nasazením služba AD DS, je obor názvů DNS obvykle rozdělený pro službu Active Directory a je vytvořena subdoména DNS a delegování pro kořenovou strukturu doménové struktury služby Active Directory. Pro každou podřízenou doménu služby Active Directory se pak přidaly další názvy domén DNS.

Služba DNS se používá také k podpoře umístění řadičů domény služby Active Directory. Zóny DNS se naplní záznamy prostředků DNS, které umožňují síťovým hostitelům a službám vyhledat řadiče domény služby Active Directory.

### <a name="applications-and-net-logon"></a>Aplikace a přihlášení k síti

Obě aplikace i služba přihlášení k síti jsou součástí modelu Windows Distributed Security Channel. Aplikace integrované s Windows serverem a službou Active Directory používají ověřovací protokoly ke komunikaci se službou přihlašování k síti, aby bylo možné navázat zabezpečenou cestu, přes kterou může ověřování probíhat.

### <a name="authentication-protocols"></a>Protokoly pro ověřování

Řadiče domény služby Active Directory ověřují uživatele a aplikace pomocí jednoho z následujících protokolů:

* **Ověřovací protokol Kerberos verze 5**
    * Protokol Kerberos verze 5 je výchozí protokol ověřování používaný místními počítači, na kterých běží Windows, a podporuje operační systémy třetích stran. Tento protokol je zadaný v dokumentu RFC 1510 a je plně integrovaný se službou Active Directory, protokolem SMB (Server Message Block), HTTP a vzdáleným voláním procedur (RPC) a také klientskými a serverovými aplikacemi, které používají tyto protokoly.
    * Když se použije protokol Kerberos, server se nemusí připojit k řadiči domény. Místo toho klient obdrží lístek pro server tím, že si ho požádá z řadiče domény v doméně účtu serveru. Server potom ověří lístek bez konzultace jakékoli jiné autority.
    * Pokud některý počítač zahrnutý v transakci nepodporuje protokol Kerberos verze 5, použije se protokol NTLM.

* **Protokol ověřování NTLM**
    * Protokol NTLM je klasický protokol ověřování sítě používaný staršími operačními systémy. Z důvodu kompatibility je používá doména služby Active Directory ke zpracování požadavků na ověření v síti, které pocházejí z aplikací navržených pro starší klienty a servery se systémem Windows a operačních systémů třetích stran.
    * Pokud je protokol NTLM použit mezi klientem a serverem, server musí kontaktovat službu ověřování v doméně na řadiči domény za účelem ověření přihlašovacích údajů klienta. Server ověřuje klienta předáním přihlašovacích údajů klienta na řadič domény v doméně klientského účtu.
    * Pokud je mezi dvěma doménami služby Active Directory nebo doménovou strukturou propojena důvěryhodnost, je možné směrovat požadavky na ověření pomocí těchto protokolů, aby poskytovaly přístup k prostředkům v obou doménových strukturách.

## <a name="authorization-and-access-control"></a>Autorizace a řízení přístupu

Technologie autorizace a vztahu důvěryhodnosti společně spolupracují na poskytování zabezpečené komunikační infrastruktury napříč doménami nebo doménovými strukturami služby Active Directory. Autorizace určuje, jakou úroveň přístupu má uživatel k prostředkům v doméně. Vztahy důvěryhodnosti usnadňují ověřování uživatelů mezi doménami, protože poskytují cestu k ověřování uživatelů v jiných doménách, takže jejich požadavky na sdílené prostředky v těchto doménách mohou být autorizovány.

V případě, že je žádost o ověření vytvořená v důvěřující doméně ověřena důvěryhodnou doménou, bude předána cílovému prostředku. Cílový prostředek pak určuje, jestli se má autorizovat konkrétní žádost vytvořená uživatelem, službou nebo počítačem v důvěryhodné doméně na základě konfigurace řízení přístupu.

Vztahy důvěryhodnosti poskytují tento mechanismus k ověřování žádostí o ověření, které jsou předány důvěřující doméně. Mechanismy řízení přístupu na počítači prostředků určují konečnou úroveň přístupu uděleného žadateli v důvěryhodné doméně.

## <a name="next-steps"></a>Další kroky

Další informace o vztahu důvěryhodnosti najdete v tématu [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

Pokud chcete začít s vytvářením spravované domény Azure služba AD DS pomocí doménové struktury prostředků, přečtěte si téma [Vytvoření a konfigurace spravované domény azure služba AD DS][tutorial-create-advanced]. Pak můžete [vytvořit odchozí vztah důvěryhodnosti doménové struktury k místní doméně (Preview)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
