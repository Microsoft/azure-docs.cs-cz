---
title: Přehled služby Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: V tomto přehledu se dozvíte, co služba Azure Active Directory Domain Services poskytuje a jak ji používat ve vaší organizaci k poskytování služeb identit aplikacím a službám v cloudu.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cf961b00c4823d0c9efcf44b0db7bb75a80d3dfa
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474335"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co je služba Azure Active Directory Domain Services?

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, protokol LDAP (LDAP) a ověřování protokolem Kerberos / NTLM, které je plně kompatibilní se službou Windows Server Active Directory. Tyto služby domény můžete používat bez nutnosti nasazovat, spravovat a opravovat řadiče domény v cloudu. Azure AD DS integruje s vaším stávajícím tenantem Azure AD, což umožňuje uživatelům přihlásit pomocí jejich stávající přihlašovací údaje. Existující skupiny a uživatelské účty můžete také použít k zabezpečení přístupu k prostředkům, což poskytuje plynulejší výtah a přesun místních prostředků do Azure.

Azure AD DS replikuje informace o identitě z Azure AD, takže spolupracuje s klienty Azure AD, které jsou jenom pro cloud nebo synchronizované s místním prostředím Služby Active Directory Domain Services (AD DS). Stejná sada funkcí Azure AD DS existují pro obě prostředí.

* Pokud máte existující místní prostředí služby AD DS, můžete synchronizovat informace o uživatelském účtu a poskytnout tak uživatelům konzistentní identitu.
* Pro cloudová prostředí nepotřebujete tradiční místní prostředí služby AD DS k používání centralizovaných služeb identit azure ad ds.

Následující video poskytuje přehled o tom, jak se Azure AD DS integruje s vašimi aplikacemi a úlohami za účelem poskytování služeb identity v cloudu:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Běžné způsoby poskytování řešení identit v cloudu

Při migraci existujících úloh do cloudu mohou aplikace s informacemi o adresářích používat protokol LDAP pro přístup pro čtení nebo zápis do místního adresáře služby AD DS. Aplikace spuštěné na Windows Serveru se obvykle nasazují na virtuálnípočítače (VM) pro připojení k doméně, takže je lze bezpečně spravovat pomocí zásad skupiny. K ověření koncových uživatelů mohou být aplikace také závislé na ověřování integrovaném systému Windows, například ověřování protokolem Kerberos nebo NTLM.

Správci IT často používají jedno z následujících řešení k poskytování služby identit aplikacím, které běží v Azure:

* Nakonfigurujte připojení VPN mezi úlohami, které běží v Azure, a místním prostředím služby AD DS.
    * Místní řadiče domény pak poskytují ověřování prostřednictvím připojení VPN.
* Vytvořte repliky řadičů domény pomocí virtuálních počítačů Azure (VM) k rozšíření domény nebo doménové struktury služby AD DS z místního prostředí.
    * Řadiče domény, které běží na virtuálních počítačích Azure, poskytují ověřování a replikují informace o adresáři mezi místním prostředím služby AD DS.
* Nasaďte v Azure samostatné prostředí služby AD DS pomocí řadičů domény, které běží na virtuálních počítačích Azure.
    * Řadiče domény, které běží na virtuálních počítačích Azure, poskytují ověřování, ale nejsou k dispozici žádné informace o adresáři replikované z místního prostředí služby AD DS.

Díky těmto přístupům připojení VPN k místnímu adresáři činí aplikace zranitelnými vůči přechodným síťovým závadám nebo výpadkům. Pokud nasadíte řadiče domény pomocí virtuálních počítačů v Azure, it tým musí spravovat virtuální počítače, pak zabezpečit, opravit, monitorovat, zálohovat a řešit jejich řešení.

Azure AD DS nabízí alternativy k potřebě vytvořit připojení VPN zpět do místního prostředí Služby AD DS nebo spustit a spravovat virtuální počítače v Azure za účelem poskytování služeb identity. Jako spravovaná služba Azure AD DS snižuje složitost a vytváří integrované řešení identit pro hybridní i cloudová prostředí.

## <a name="azure-ad-ds-features-and-benefits"></a>Funkce a výhody Azure AD DS

Chcete-li poskytovat služby identit aplikací a virtuálních počítačů v cloudu, Azure AD DS je plně kompatibilní s tradiční prostředí služby AD DS pro operace, jako je připojení k doméně, zabezpečené LDAP (LDAPS), zásady skupiny, správa DNS a ldap vazba a podpora čtení. Podpora zápisu LDAP je dostupná pro objekty vytvořené ve spravované doméně Azure AD DS, ale ne prostředky synchronizované ze služby Azure AD. Následující funkce služby Azure AD DS zjednodušují operace nasazení a správy:

* **Zjednodušené prostředí nasazení:** Azure AD DS je povolené pro vašeho klienta Azure AD pomocí jediného průvodce na webu Azure Portal.
* **Integrovaná s Azure AD:** Uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné z vašeho klienta Azure AD. Noví uživatelé, skupiny nebo změny atributů z vašeho klienta Azure AD nebo místního prostředí služby AD DS se automaticky synchronizují s Azure AD DS.
    * Účty v externích adresářích propojených s vaší službou Azure AD nejsou ve službě Azure AD DS k dispozici. Přihlašovací údaje nejsou k dispozici pro tyto externí adresáře, takže nelze synchronizovat do spravované domény Azure AD DS.
* **Použijte firemní přihlašovací údaje/hesla:** Hesla pro uživatele ve službě Azure AD DS jsou stejná jako ve vašem tenantovi Azure AD. Uživatelé můžou používat svá podniková pověření k počítačům s připojením k doméně, interaktivnímu přihlášení nebo přes vzdálenou plochu a ověřování ve spravované doméně Azure AD DS.
* **Ověřování ntlm a kerberos:** S podporou ověřování NTLM a Kerberos můžete nasadit aplikace, které jsou závislé na ověřování integrovaném se systémem Windows.
* **Vysoká dostupnost:** Azure AD DS zahrnuje více řadičů domény, které poskytují vysokou dostupnost pro vaši spravovanou doménu. Tato vysoká dostupnost zaručuje provozuslužbu a odolnost vůči selhání.
    * V oblastech, které podporují [zóny dostupnosti Azure][availability-zones], jsou tyto řadiče domény také distribuovány napříč zónami pro další odolnost proti chybám.

Některé klíčové aspekty spravované domény Azure AD DS zahrnují následující:

* Spravovaná doména Azure AD DS je samostatná doména. Není rozšíření místní domény.
    * V případě potřeby můžete vytvořit jednosměrné odchozí vztahy důvěryhodnosti doménové struktury z Azure AD DS do místního prostředí služby AD DS. Další informace naleznete v [tématu koncepty doménové struktury prostředků a funkce pro Azure AD DS][ forest-trusts].
* Váš IT tým nemusí spravovat, opravovat nebo monitorovat řadiče domény pro tuto spravovanou doménu Azure AD DS.

Pro hybridní prostředí, která běží službu AD DS místně, není nutné spravovat replikaci služby AD do spravované domény Služby Azure AD DS. Uživatelské účty, členství ve skupinách a přihlašovací údaje z místního adresáře se synchronizují do Služby Azure AD prostřednictvím [služby Azure AD Connect][azure-ad-connect]. Tyto uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné v rámci spravované domény Služby Azure AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Jak Azure AD DS funguje?

K poskytování služeb identity Azure vytvoří instanci služby AD DS ve virtuální síti podle vašeho výběru. Na pozadí se vytvoří dvojice řadičů domény Windows Serveru, které běží na virtuálních počítačích Azure. Tyto řadiče domény není nutné spravovat, konfigurovat ani aktualizovat. Platforma Azure spravuje řadiče domény jako součást služby Azure AD DS.

Spravovaná doména Azure AD DS je nakonfigurovaná tak, aby prováděla jednosměrnou synchronizaci ze služby Azure AD a poskytovala přístup k centrální sadě uživatelů, skupin a přihlašovacích údajů. Prostředky můžete vytvořit přímo ve spravované doméně Azure AD DS, ale nejsou synchronizovány zpět do Azure AD. Aplikace, služby a virtuální počítače v Azure, které se připojují k této virtuální síti, pak můžou používat běžné funkce služby AD DS, jako je připojení k doméně, zásady skupiny, protokol LDAP a ověřování protokolem Kerberos / NTLM.

V hybridním prostředí s místním prostředím Služby AD DS azure [ad connect][azure-ad-connect] synchronizuje informace o identitě s Azure AD, který se pak synchronizuje s Azure AD DS.

![Synchronizace ve službě Azure AD Domain Services se službou Azure AD a místní službou Active Directory Domain Services pomocí služby AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Chcete-li zobrazit Azure AD DS v akci, podívejme se na několik příkladů:

* [Azure AD DS pro hybridní organizace](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS pro organizace pouze pro cloud](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS pro hybridní organizace

Mnoho organizací provozuje hybridní infrastrukturu, která zahrnuje cloudové i místní aplikační úlohy. Starší aplikace migrované do Azure jako součást strategie výtahu a směny mohou používat tradiční připojení LDAP k poskytování informací o identitě. Pro podporu této hybridní infrastruktury lze informace o identitě z místního prostředí služby AD DS synchronizovat s klientem Azure AD. Azure AD DS pak poskytuje tyto starší aplikace v Azure se zdrojem identity, bez nutnosti konfigurovat a spravovat připojení aplikací zpět k místním adresářovým službám.

Podívejme se na příklad pro Litware Corporation, hybridní organizace, která provozuje místní prostředky i prostředky Azure:

![Služba Azure Active Directory Domain Services pro hybridní organizaci, která zahrnuje místní synchronizaci](./media/overview/synced-tenant.png)

* Aplikace a úlohy serveru, které vyžadují služby domény, se nasazují ve virtuální síti v Azure.
    * To může zahrnovat starší aplikace migrované do Azure jako součást strategie výtahu a směny.
* Aby bylo účelem synchronizace informací o identitě z jejich místního adresáře do jejich tenanta Azure AD, litware corporation nasazuje [Azure AD Connect][azure-ad-connect].
    * Synchronizované informace o identitě zahrnují uživatelské účty a členství ve skupinách.
* It tým Litware umožňuje Azure AD DS pro jejich tenanta Azure AD v této virtuální síti nebo partnerské virtuální síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure AD DS, jako je připojení k doméně, čtení LDAP, vazba LDAP, ověřování NTLM a Kerberos a zásady skupiny.

> [!IMPORTANT]
> Azure AD Connect by se měl nainstalovat a nakonfiguroval jenom pro synchronizaci s místními prostředími služby AD DS. Není podporováno instalace Azure AD Connect ve spravované doméně Azure AD DS k synchronizaci objektů zpět do Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS pro organizace pouze pro cloud

Klient Azure AD pouze pro cloud nemá místní zdroj identity. Uživatelské účty a členství ve skupinách se například vytvářejí a spravují přímo ve službě Azure AD.

Teď se podívejme na příklad pro Contoso, organizaci určenou pouze pro cloud, která používá Azure AD pro identitu. Všechny identity uživatelů, jejich přihlašovací údaje a členství ve skupinách se vytvářejí a spravují ve službě Azure AD. Neexistuje žádná další konfigurace Azure AD Connect pro synchronizaci informací o identitě z místního adresáře.

![Služba Azure Active Directory Domain Services pro organizaci určenou pouze pro cloud bez místní synchronizace](./media/overview/cloud-only-tenant.png)

* Aplikace a úlohy serveru, které vyžadují služby domény, se nasazují ve virtuální síti v Azure.
* It tým společnosti Contoso umožňuje Azure AD DS pro jejich klienta Azure AD v této virtuální síti nebo v partnerské síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure AD DS, jako je připojení k doméně, čtení LDAP, vazba LDAP, ověřování NTLM a Kerberos a zásady skupiny.

## <a name="next-steps"></a>Další kroky

Další informace o Azure AD DS porovnává s jinými řešeními identit a jak funguje synchronizace, najdete v následujících článcích:

* [Porovnání Azure AD DS s Azure AD, službou Active Directory Domain Services na virtuálních počítačích Azure a místními službami Active Directory Domain Services][compare]
* [Zjistěte, jak se služba Azure AD Domain Services synchronizuje s adresářem Azure AD][synchronization]

Chcete-li začít, [vytvořte spravovanou doménu Azure AD DS pomocí webu Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
