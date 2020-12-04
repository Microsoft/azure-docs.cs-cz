---
title: Přehled Azure Active Directory Domain Services | Microsoft Docs
description: V tomto přehledu se dozvíte, co Azure Active Directory Domain Services poskytuje a jak ho ve vaší organizaci používat k poskytování služeb identit aplikacím a službám v cloudu.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2020
ms.author: joflore
ms.custom: contperfq1
ms.openlocfilehash: d8d709590345982479301f25601fae25fa6f4fae
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571857"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co je služba Azure Active Directory Domain Services?

Azure Active Directory Domain Services (služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, protokol LDAP (Lightweight Directory Access Protocol) a ověřování pomocí protokolu Kerberos/NTLM. Tyto doménové služby budete používat bez nutnosti nasazovat, spravovat a opravovat řadiče domény (DCs) v cloudu.

Spravovaná doména Azure služba AD DS umožňuje spouštět starší aplikace v cloudu, které nemůžou používat moderní metody ověřování, nebo kde nechcete, aby se prohledávání adresářů vždy vrátilo do místního prostředí služba AD DS. Tyto starší aplikace můžete z místního prostředí napravit a přesunout do spravované domény, aniž byste museli spravovat služba AD DS prostředí v cloudu.

Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se ke službě a aplikacím připojeným ke spravované doméně pomocí svých stávajících přihlašovacích údajů. K zabezpečení přístupu k prostředkům můžete použít taky existující skupiny a uživatelské účty. Tyto funkce poskytují plynulejší a posunutí místních prostředků do Azure.

> [!div class="nextstepaction"]
> [Pokud chcete začít, vytvořte spravovanou doménu Azure služba AD DS pomocí Azure Portal][tutorial-create]

## <a name="how-does-azure-ad-ds-work"></a>Jak funguje Azure služba AD DS?

Při vytváření spravované domény Azure služba AD DS definujete jedinečný obor názvů. Tento obor názvů je název domény, například *aaddscontoso.com*. Do vybrané oblasti Azure se pak nasadí dva řadiče domény (DCs) Windows serveru. Toto nasazení řadičů domény se označuje jako sada replik.

Tyto řadiče domény nemusíte spravovat, konfigurovat ani aktualizovat. Platforma Azure zpracovává řadiče domény jako součást spravované domény, včetně zálohování a šifrování v klidovém formátu pomocí Azure Disk Encryption.

Spravovaná doména je nakonfigurovaná tak, aby prováděla jednosměrnou synchronizaci z Azure AD za účelem poskytnutí přístupu k centrální skupině uživatelů, skupin a přihlašovacích údajů. Prostředky můžete vytvořit přímo ve spravované doméně, ale nesynchronizují se zpátky do Azure AD. Aplikace, služby a virtuální počítače v Azure, které se připojují ke spravované doméně, pak můžou používat běžné funkce služba AD DS, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos/NTLM.

V hybridním prostředí s místním prostředím služba AD DS [Azure AD Connect][azure-ad-connect] synchronizuje informace o identitě s Azure AD, které jsou pak synchronizovány do spravované domény.

![Synchronizace v Azure AD Domain Services s Azure AD a místními služba AD DS pomocí služby AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure služba AD DS replikuje informace o identitě z Azure AD, takže funguje s klienty Azure AD, které jsou jenom pro Cloud, nebo synchronizované s místním (služba AD DS prostředím). Pro obě prostředí existují stejné sady funkcí Azure služba AD DS.

* Pokud máte existující místní služba AD DS prostředí, můžete synchronizovat informace o uživatelském účtu a zajistit tak konzistentní identitu pro uživatele. Další informace najdete v tématu [jak se objekty a přihlašovací údaje synchronizují ve spravované doméně][synchronization].
* Pro cloudová prostředí nepotřebujete tradiční místní služba AD DS prostředí, abyste mohli používat centralizované služby identit Azure služba AD DS.

Spravovanou doménu můžete rozšířit tak, aby měla více než jednu sadu replik na jeden tenant služby Azure AD. Sady replik se dají přidat do všech partnerských virtuálních sítí v libovolné oblasti Azure, která podporuje Azure služba AD DS. Další sady replik v různých oblastech Azure poskytují geografickou obnovu po havárii pro starší aplikace, pokud oblast Azure přejde do režimu offline. Sady replik jsou momentálně ve verzi Preview. Další informace najdete v tématu [Koncepty a funkce sady replik pro spravované domény][concepts-replica-sets].

Následující video poskytuje přehled o tom, jak se Azure služba AD DS integruje s vašimi aplikacemi a úlohami a poskytuje služby identity v cloudu:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Pokud si chcete prohlédnout scénáře nasazení služby Azure služba AD DS v akci, můžete prozkoumat následující příklady:

* [Azure služba AD DS pro hybridní organizace](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [služba AD DS Azure pro cloudové organizace](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Funkce a výhody pro Azure služba AD DS

Aby služby identit poskytovaly aplikace a virtuální počítače v cloudu, služba Azure služba AD DS je plně kompatibilní s tradičním služba AD DSm prostředím pro operace, jako je připojení k doméně, zabezpečení LDAP (LDAPs), Zásady skupiny, správa DNS a podpora čtení a čtení protokolu LDAP. Podpora zápisu LDAP je dostupná pro objekty vytvořené ve spravované doméně, ale ne prostředky synchronizované z Azure AD.

Pokud chcete získat další informace o možnostech vaší identity, [Porovnejte azure služba AD DS s Azure AD, služba AD DS na virtuálních počítačích Azure a služba AD DS místních][compare].

Následující funkce Azure služba AD DS zjednodušují operace nasazení a správy:

* **Prostředí pro zjednodušené nasazení:** Služba Azure služba AD DS je povolená pro vašeho tenanta Azure AD pomocí jediného průvodce v Azure Portal.
* **Integrace se službou Azure AD:** Uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné z vašeho tenanta Azure AD. Noví uživatelé, skupiny nebo změny atributů z vašeho tenanta Azure AD nebo místního prostředí služba AD DS jsou automaticky synchronizovány do Azure služba AD DS.
    * Účty v externích adresářích propojených s Azure AD nejsou k dispozici v Azure služba AD DS. Přihlašovací údaje nejsou pro tyto externí adresáře k dispozici, proto je nelze synchronizovat do spravované domény.
* **Použijte své podnikové přihlašovací údaje nebo hesla:** Hesla pro uživatele v Azure služba AD DS jsou stejná jako v tenantovi Azure AD. Uživatelé můžou používat své podnikové přihlašovací údaje k počítačům připojeným k doméně, přihlašovat se interaktivně nebo přes vzdálenou plochu a ověřovat vůči spravované doméně.
* **Ověřování protokolem NTLM a Kerberos:** Díky podpoře ověřování protokolem NTLM a protokolu Kerberos můžete nasazovat aplikace, které spoléhají na ověřování integrované v systému Windows.
* **Vysoká dostupnost:** Azure služba AD DS zahrnuje několik řadičů domény, které poskytují vysokou dostupnost vaší spravované domény. Tato vysoká dostupnost zajišťuje dostupnost a odolnost služby při selhání.
    * V oblastech, které podporují [zóny dostupnosti Azure][availability-zones], jsou tyto řadiče domény distribuovány i napříč zónami pro zajištění vyšší odolnosti.
    * [Sady replik][concepts-replica-sets] lze také použít k poskytování geografického zotavení po havárii pro starší aplikace v případě, že oblast Azure přejde do režimu offline.

Mezi klíčové aspekty spravované domény patří následující:

* Spravovaná doména je samostatná doména. Nejedná se o rozšíření místní domény.
    * V případě potřeby můžete vytvořit jednosměrný odchozí vztah důvěryhodnosti doménové struktury z Azure služba AD DS do místního prostředí služba AD DS. Další informace najdete v tématu [Koncepty a funkce doménové struktury prostředků pro Azure služba AD DS][ forest-trusts].
* Váš IT tým nemusí spravovat, opravovat ani monitorovat řadiče domény pro tuto spravovanou doménu.

V hybridních prostředích, která běží služba AD DS místně, nemusíte spravovat replikaci AD do spravované domény. Uživatelské účty, členství ve skupinách a přihlašovací údaje z místního adresáře se synchronizují do Azure AD prostřednictvím [Azure AD Connect][azure-ad-connect]. Tyto uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné v rámci spravované domény.

## <a name="next-steps"></a>Další kroky

Další informace o Azure služba AD DS porovnání s dalšími řešeními identit a jak funguje synchronizace najdete v následujících článcích:

* [Porovnání Azure služba AD DS s Azure AD, Active Directory Domain Services na virtuálních počítačích Azure a Active Directory Domain Services místních][compare]
* [Informace o tom, jak se Azure AD Domain Services synchronizuje s adresářem služby Azure AD][synchronization]
* Informace o tom, jak správce spravované domény, najdete v tématu [koncepty správy pro uživatelské účty, hesla a správu v Azure služba AD DS][administration-concepts].

Začněte [tím, že vytvoříte spravovanou doménu pomocí Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
