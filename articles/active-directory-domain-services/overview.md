---
title: Přehled Azure Active Directory Domain Services | Microsoft Docs
description: V tomto přehledu se dozvíte, co Azure Active Directory Domain Services poskytuje a jak ho ve vaší organizaci používat k poskytování služeb identit aplikacím a službám v cloudu.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 921266b78b82364b4610dcd74b6ee16ee44cb060
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617246"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co je Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos nebo NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby budete používat bez nutnosti nasazovat, spravovat a opravovat řadiče domény v cloudu. Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD, který umožňuje uživatelům přihlásit se pomocí jejich stávajících přihlašovacích údajů. Pomocí existujících skupin a uživatelských účtů taky můžete zabezpečit přístup k prostředkům, což poskytuje plynulý přenos místních prostředků do Azure.

Azure služba AD DS replikuje informace o identitě z Azure AD, takže funguje s klienty Azure AD, které jsou jenom pro Cloud, nebo synchronizované s místním prostředím Active Directory Domain Services (služba AD DS). Pokud máte existující místní služba AD DS prostředí, můžete synchronizovat informace o uživatelském účtu a zajistit tak konzistentní identitu pro uživatele. Pro cloudová prostředí nepotřebujete tradiční místní služba AD DS prostředí, abyste mohli používat centralizované služby identit Azure služba AD DS. Pro obě prostředí existují stejné sady funkcí Azure služba AD DS.

Následující video poskytuje přehled o tom, jak se Azure služba AD DS integruje s vašimi aplikacemi a úlohami a poskytuje služby identity v cloudu:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Běžné způsoby poskytování řešení identity v cloudu

Když migrujete stávající úlohy do cloudu, aplikace podporující adresáře můžou používat protokol LDAP pro přístup pro čtení nebo zápis do místního adresáře služba AD DS. Aplikace, které běží na Windows serveru, se obvykle nasazují při připojení k doméně, aby je bylo možné bezpečně spravovat pomocí Zásady skupiny. K ověřování koncových uživatelů se můžou aplikace spoléhat i na integrované ověřování Windows, jako je například ověřování pomocí protokolu Kerberos nebo NTLM.

Správci IT často používají jedno z následujících řešení k poskytování služby identity aplikacím, které běží v Azure:

* Nakonfigurujte připojení VPN typu Site-to-site mezi úlohami spuštěnými v Azure a místním prostředím služba AD DS.
* Pomocí virtuálních počítačů Azure můžete k rozšiřování služba AD DS domény nebo doménové struktury vytvořit repliku řadičů domény.
* Nasazení samostatného služba AD DSho prostředí v Azure pomocí řadičů domény, které běží na virtuálních počítačích Azure.

Díky těmto přístupům sítě VPN k místnímu adresáři připojovat aplikace k přechodným síťovým histogramu nebo výpadkům sítě. Pokud nasadíte řadiče domény pomocí virtuálních počítačů v Azure, virtuální počítače IT týmu musí spravovat, zabezpečovat, opravovat, monitorovat, zálohovat a řešit potíže.

Azure služba AD DS nabízí alternativy k nutnosti vytvářet připojení VPN zpátky do místního prostředí služba AD DS nebo spouštět a spravovat virtuální počítače v Azure za účelem poskytování služeb identit. Služba Azure služba AD DS jako spravovaná služba zjednodušuje vytváření řešení integrované identity pro hybridní i cloudové prostředí.

## <a name="azure-ad-ds-features-and-benefits"></a>Funkce a výhody pro Azure služba AD DS

Aby služby identit poskytovaly aplikace a virtuální počítače v cloudu, služba Azure služba AD DS je plně kompatibilní s tradičním služba AD DSm prostředím pro operace, jako je připojení k doméně, zabezpečení LDAP (LDAPs), Zásady skupiny a správa DNS, a podpora čtení a vazeb LDAP. Podpora zápisu LDAP je k dispozici pro objekty vytvořené ve spravované doméně služby Azure služba AD DS, ale ne prostředky synchronizované z Azure AD. Následující funkce Azure služba AD DS zjednodušují operace nasazení a správy:

* **Prostředí pro zjednodušené nasazení:** Služba Azure služba AD DS je povolená pro vašeho tenanta Azure AD pomocí jediného průvodce v Azure Portal.
* **Integrace se službou Azure AD:** Uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné z vašeho tenanta Azure AD. Noví uživatelé, skupiny nebo změny atributů z vašeho tenanta Azure AD nebo místního prostředí služba AD DS jsou automaticky synchronizovány do Azure služba AD DS.
* **Použijte své podnikové přihlašovací údaje nebo hesla:** Hesla pro uživatele v tenantovi Azure AD fungují s Azure služba AD DS. Uživatelé můžou používat své podnikové přihlašovací údaje k počítačům připojeným k doméně, přihlašovat se interaktivně nebo přes vzdálenou plochu a ověřovat je pomocí spravované domény Azure služba AD DS.
* **Ověřování protokolem NTLM a Kerberos:** Díky podpoře ověřování protokolem NTLM a protokolu Kerberos můžete nasazovat aplikace, které spoléhají na ověřování integrované v systému Windows.
* **Vysoká dostupnost:** Azure služba AD DS zahrnuje několik řadičů domény, které poskytují vysokou dostupnost vaší spravované domény. Tato vysoká dostupnost zajišťuje dostupnost a odolnost služby při selhání.

Mezi klíčové aspekty spravované domény Azure služba AD DS patří následující:

* Spravovaná doména Azure služba AD DS je samostatná doména. Nejedná se o rozšíření místní domény.
* Váš IT tým nemusí spravovat, opravovat ani monitorovat řadiče domény pro tuto spravovanou doménu Azure služba AD DS.

Pro hybridní prostředí, která běží služba AD DS místně, nemusíte spravovat replikaci AD do spravované domény Azure služba AD DS. Uživatelské účty, členství ve skupinách a přihlašovací údaje z místního adresáře se synchronizují do Azure AD prostřednictvím Azure AD Connect. Tyto uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné v rámci spravované domény Azure služba AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Jak funguje Azure služba AD DS?

Pokud chcete poskytovat služby identit, Azure vytvoří ve virtuální síti podle vašeho výběru dostupnou služba AD DS spravovanou doménu. Redundance je poskytována na pozadí a bez nutnosti spravovat, zabezpečit nebo aktualizovat, a to prostřednictvím dvojice řadičů domény Windows serveru. Spravovaná doména Azure služba AD DS je nakonfigurovaná tak, aby prováděla jednosměrnou synchronizaci z Azure AD za účelem poskytnutí přístupu k centrální skupině uživatelů, skupin a přihlašovacích údajů. Prostředky můžete vytvořit přímo ve spravované doméně Azure služba AD DS, ale nesynchronizují se zpátky do Azure AD. Aplikace, služby a virtuální počítače v Azure, které se připojují k této virtuální síti, můžou použít běžné funkce služba AD DS, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos/NTLM. V hybridním prostředí s místním prostředím služba AD DS [Azure AD Connect][azure-ad-connect] synchronizuje informace o identitě s Azure AD.

![Synchronizace v Azure AD Domain Services s Azure AD a místními Active Directory Domain Services pomocí služby AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Pokud se chcete podívat na Azure služba AD DS v akci, Podívejme se na několik příkladů:

* [Azure služba AD DS pro hybridní organizace](#azure-ad-ds-for-hybrid-organizations)
* [služba AD DS Azure pro cloudové organizace](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure služba AD DS pro hybridní organizace

Řada organizací spouští hybridní infrastrukturu, která zahrnuje úlohy cloudových i místních aplikací. Starší verze aplikací migrované do Azure v rámci strategie výtahu a posunutí můžou pořád používat tradiční připojení LDAP k poskytování informací o identitě. Pro podporu této hybridní infrastruktury je možné synchronizovat informace o identitě z místního prostředí Active Directory Domain Services (služba AD DS) do tenanta služby Azure AD. Azure služba AD DS pak může poskytnout tyto starší verze aplikací v Azure se zdrojem identity, aniž by bylo nutné konfigurovat a spravovat připojení aplikací zpátky do místních adresářových služeb.

Pojďme se podívat na příklad pro společnost Litware Corporation, hybridní organizaci, která spouští místní i prostředky Azure:

![Azure Active Directory Domain Services pro hybridní organizaci, která zahrnuje místní synchronizaci](./media/overview/synced-tenant.png)

* Úlohy aplikací a serverů, které vyžadují službu Domain Services, se nasazují ve virtuální síti v Azure.
    * To může zahrnovat starší aplikace migrované do Azure v rámci strategie navýšení a posunutí.
* Pokud chcete synchronizovat informace o identitě z místního adresáře do svého tenanta Azure AD, Litware Corporation nasadí [Azure AD Connect][azure-ad-connect].
    * Mezi informace o identitě, která se synchronizují, patří uživatelské účty a členství ve skupinách.
* Litware IT tým umožňuje služba AD DS Azure AD pro svého tenanta Azure AD v této nebo partnerské virtuální síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure služba AD DS, jako je připojení k doméně, čtení LDAP, vázání LDAP, ověřování NTLM a Kerberos, a Zásady skupiny.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>služba AD DS Azure pro cloudové organizace

Pouze cloudový tenant Azure AD nemá místní zdroj identity. Uživatelské účty a členství ve skupinách jsou například vytvářeny a spravovány v Azure AD.

Teď se podíváme na příklad pro společnost Contoso, jenom pro cloudovou organizaci, která pro identitu používá jenom Azure AD. V Azure AD se vytvářejí a spravují všechny identity uživatelů, jejich přihlašovací údaje a členství ve skupinách. Neexistuje žádná další konfigurace Azure AD Connect k synchronizaci jakýchkoli informací o identitě z místního adresáře.

![Azure Active Directory Domain Services pro cloudovou organizaci bez místní synchronizace](./media/overview/cloud-only-tenant.png)

* Úlohy aplikací a serverů, které vyžadují službu Domain Services, se nasazují ve virtuální síti v Azure.
* Tým IT společnosti Contoso povoluje Azure služba AD DS pro svého tenanta Azure AD v této nebo partnerské virtuální síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure služba AD DS, jako je připojení k doméně, čtení LDAP, vázání LDAP, ověřování NTLM a Kerberos, a Zásady skupiny.

## <a name="next-steps"></a>Další kroky

Další informace o Azure služba AD DS porovnání s dalšími řešeními identit a jak funguje synchronizace najdete v následujících článcích:

* [Porovnání Azure služba AD DS s Azure AD, Active Directory Domain Services na virtuálních počítačích Azure a Active Directory Domain Services místních][compare]
* [Informace o tom, jak se Azure AD Domain Services synchronizuje s adresářem služby Azure AD][synchronization]

Začněte tím, [že pomocí Azure Portal vytvoříte spravovanou doménu Azure služba AD DS][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-hybrid-identity.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
