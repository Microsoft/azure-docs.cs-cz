---
title: Přehled služby Azure Active Directory Domain Services | Microsoft Docs
description: Přehled služby Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 5918791fdbb1fe7bcfe20bb44077f77d938b39b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587738"
---
# <a name="azure-active-directory-ad-domain-services"></a>Doména služby Azure Active Directory (AD)
## <a name="overview"></a>Přehled
Služby infrastruktury Azure umožňují nasadit širokou škálu řešení výpočetní agilní způsobem. Pomocí Azure Virtual Machines, můžete nasadit téměř okamžitě a platíte jenom podle počtu minut. Využitím podpory pro Windows, Linux, SQL Server, Oracle, SAP, IBM a BizTalk, můžete nasadit jakoukoli úlohu, žádný jazyk na téměř jakémkoliv operačním systému. Tyto výhody umožňují migrovat starší verze aplikace nasazené na místě do Azure, uložte na provozní náklady.

Klíčovým prvkem migrace místní aplikace do Azure je zpracování identity potřebám tyto aplikace. Aplikace využívající technologii adresáře může spoléhají na LDAP pro čtení nebo zápis do podnikového adresáře nebo spoléhají na integrované ověřování systému Windows (ověřování protokolu Kerberos nebo NTLM) k ověření koncových uživatelů. Obchodní (LOB) aplikace spuštěné na Windows serveru se obvykle nasazují na počítačích připojených k doméně, aby se dala spravovat, bezpečně pomocí zásad skupiny. Tyto závislosti na infrastruktuře podnikové identitě 'navýšení a shift' v místě aplikací do cloudu, musí být rozpoznány.

Správci často zapnout na jednu z následujících řešení vyhovět potřebám identity jejich aplikace nasazené v Azure:

* Nasazení připojení site-to-site VPN mezi úlohami spuštěnými v služby infrastruktury Azure a firemní adresář místní.
* Rozšiřte podnikové infrastruktury domény nebo doménové struktuře AD nastavení řadičů domény repliky pomocí virtuální počítače Azure.
* Nasazení samostatné doméně v Azure pomocí řadiče domény nasazené jako virtuální počítače Azure.

Všechny tyto přístupy trpí vysoké náklady a režijní náklady na správu. Správci jsou nutné k nasazení řadičů domény pomocí virtuálních počítačů v Azure. Kromě toho potřebují spravovat, zabezpečení, opravy, monitorování, zálohování a řešení těchto virtuálních počítačů. Závislá na připojení k síti VPN do místního adresáře způsobí, že úlohy nasazené v Azure a být zranitelné v důsledku chyb přechodný síťový nebo výpadků. Tyto výpadky sítě zase mít za následek nižší provozu a sníženou spolehlivost pro tyto aplikace.

Jsme chtěli Azure AD Domain Services zajistit alternativu jednodušší.

### <a name="watch-an-introductory-video"></a>Podívejte se na úvodní video

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Představení služby Azure AD Domain Services

Služba Azure AD Domain Services poskytuje spravované doméně služby, jako je například připojení k doméně, ověřování protokolu Kerberos nebo NTLM zásad LDAP, skupiny, které jsou plně kompatibilní s Windows Server Active Directory. Můžete využívat tyto domény služby bez nutnosti nasazení, správě a oprava řadiče domény v cloudu. Služba Azure AD Domain Services se integruje s existující klientovi Azure AD, a tím umožnit uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů. Kromě toho můžete použít existující skupiny a uživatelské účty zabezpečený přístup k prostředkům, čímž zajišťuje hladší 'navýšení a shift' z místních prostředků do služby infrastruktury Azure.

Azure AD Domain Services funkce funguje bezproblémově bez ohledu na to, jestli klientovi Azure AD je jenom pro cloud nebo synchronizované s vaší místní Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services pro organizace, jenom pro cloud

Výhradně cloudový klienta Azure AD (často označované jako "spravované klientů.) nemá žádné místní identity nároky. Jinými slovy uživatelské účty, jejich hesla a členství ve skupinách jsou všechny nativní pro cloud – to znamená, vytvořit a spravovat ve službě Azure AD. Vezměte v úvahu na chvíli že Contoso je čistě cloudové klienta Azure AD. Jak je znázorněno na následujícím obrázku, správce společnosti Contoso nakonfiguroval virtuální sítě ve službách infrastruktury Azure. V této virtuální sítě ve virtuálních počítačích Azure nasazených aplikací a zatížení serveru. Vzhledem k tomu, že Contoso je jenom pro cloud klienta, všechny identity uživatelů, jejich přihlašovacích údajů a členství ve skupinách jsou vytvořit a spravovat ve službě Azure AD.

![Přehled služby Azure AD Domain Services](./media/active-directory-domain-services-overview/aadds-overview.png)

Společnosti Contoso správce IT může povolení služby Azure AD Domain Services pro svého tenanta Azure AD a zvolte chcete zpřístupnit služby domény v této virtuální síti. Po tomto datu Azure AD Domain Services zřizuje spravované domény a zpřístupní ve virtuální síti. Všechny uživatelské účty, členství ve skupinách a k dispozici v klientovi Azure AD společnosti Contoso přihlašovací údaje uživatele jsou taky k dispozici v této doméně nově vytvořený. Tato funkce umožňuje uživatelům v organizaci se přihlásit k doméně pomocí podnikových přihlašovacích – například při vzdáleném připojení k doméně počítače přes vzdálenou plochu. Správci můžou zřizovat přístup k prostředkům v doméně pomocí existující členství ve skupinách. Aplikace nasazené ve virtuálních počítačích ve virtuální síti můžete použít funkce, například připojení k doméně, LDAP pro čtení, LDAP bind, ověřování NTLM a Kerberos a zásad skupiny.

Několik nejdůležitějšími aspekty tohoto spravované domény, který je zajištěný nástrojem Azure AD Domain Services jsou následující:

* Společnosti Contoso správce IT není potřeba spravovat, oprava nebo sledovat tuto doménu nebo všechny řadiče domény pro toto spravované domény.
* Není nutné ke správě replikace služby AD pro tuto doménu. Uživatelské účty, členství ve skupinách a přihlašovacích údajů z klienta Azure AD společnosti Contoso jsou automaticky dostupné v rámci této spravované domény.
* Vzhledem k tomu, že doménu je spravované službou Azure AD Domain Services, Contoso na správce IT nemá oprávnění správce domény nebo správce podnikové sítě v této doméně.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD Domain Services pro hybridní organizace
Organizace s hybridní infrastrukturu IT využívat kombinaci prostředků cloudu a místních prostředků. Tyto organizace synchronizovat informace o identitě ze svého místního adresáře do jejich klienta Azure AD. Jako hybridní organizace podívejte se na migraci více ze své místní aplikace do cloudu, zejména starší verze adresáře aplikací, může být užitečné k nim Azure AD Domain Services.

Litware Corporation nasadil [Azure AD Connect](../active-directory/active-directory-aadconnect.md), k synchronizaci informací o identitě ze svého místního adresáře do jejich klienta Azure AD. Informace o identitě, který je synchronizován se obsahuje uživatelské účty, jejich hodnoty hash přihlašovacích údajů pro ověřování (heslo sync) a členství ve skupinách.

> [!NOTE]
> **Synchronizace hesla je povinné pro hybridní organizace používat Azure AD Domain Services**. Tento požadavek je, protože jsou vyžadované přihlašovací údaje uživatelů ve spravované doméně poskytované Azure AD Domain Services, k ověřování těchto uživatelů prostřednictvím metody ověřování protokolem NTLM nebo Kerberos.
>
>

![Služba Azure AD Domain Services pro Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Předchozí obrázek ukazuje, jak organizace s hybridní infrastrukturu IT, jako je například Litware Corporation, můžete použít Azure AD Domain Services. Aplikace a úlohy serveru, které vyžadují služby domény litware's se nasadí ve virtuální síti ve službách infrastruktury Azure. Litware's správce IT může povolení služby Azure AD Domain Services pro svého tenanta Azure AD a zvolte chcete zpřístupnit spravované domény v této virtuální síti. Protože Litware organizaci s hybridní infrastrukturu IT, uživatelské účty, skupiny a přihlašovací údaje jsou synchronizovány do jejich klienta Azure AD ze svého místního adresáře. Tato funkce umožňuje uživatelům přihlášení k doméně pomocí podnikových přihlašovacích – například při vzdáleném připojení pro počítače připojené k doméně přes vzdálenou plochu. Správci můžou zřizovat přístup k prostředkům v doméně pomocí existující členství ve skupinách. Aplikace nasazené ve virtuálních počítačích ve virtuální síti můžete použít funkce, například připojení k doméně, LDAP pro čtení, LDAP bind, ověřování NTLM a Kerberos a zásad skupiny.

Několik nejdůležitějšími aspekty tohoto spravované domény, který je zajištěný nástrojem Azure AD Domain Services jsou následující:

* Spravované doméně je samostatné domény. Není rozšíření Litware's místní domény.
* Litware's správce IT není potřeba spravovat, opravy, nebo monitorování řadičů domény pro toto spravované domény.
* Není nutné ke správě replikace AD k této doméně. Uživatelské účty, členství ve skupinách a přihlašovacích údajů z místního adresáře Litware's jsou synchronizovány do Azure AD pomocí Azure AD Connect. Tyto uživatelské účty, členství ve skupinách a přihlašovacích údajů jsou automaticky dostupné v rámci spravované domény.
* Vzhledem k tomu, že doménu je spravované službou Azure AD Domain Services, Litware na správce IT nemá oprávnění správce domény nebo správce podnikové sítě v této doméně.

## <a name="benefits"></a>Výhody
S Azure AD Domain Services můžete využívat následující výhody:

* **Jednoduché** – můžete vyhovět potřebám identity virtuální počítače nasazené služby infrastruktury Azure s jenom několik jednoduchých kliknutí. Není nutné k nasazení a správě infrastruktury identit v Azure nebo instalační program připojit zpět k vaší místní infrastruktury identity.
* **Integrované** – Azure AD Domain Services se úzce integruje se klientovi služby Azure AD. Teď můžete použít Azure AD integrované podnikové cloudové adresáře, který je určen pro potřeby moderních aplikací a tradiční aplikace využívající technologii directory.
* **Kompatibilní** – Azure AD Domain Services je založený na úrovni infrastruktury Principy enterprise systému Windows Server Active Directory. Aplikace můžete tedy spoléhat na vyšší úroveň kompatibility s funkcemi, Windows Server Active Directory. Ne všechny funkce, které jsou k dispozici v systému Windows Server AD jsou aktuálně dostupné v Azure AD Domain Services. K dispozici funkce jsou však kompatibilní s odpovídající funkce systému Windows Server AD, které byste tedy spoléhat na ve vaší místní infrastruktuře. Možnosti připojení LDAP, protokolu Kerberos, NTLM, zásad skupiny a domény tvoří vyspělá nabídky, které byly testovány a vylepšení v různých verzích systému Windows Server.
* **Nákladově efektivní** – službou Azure AD Domain Services, se můžete vyhnout spojené se správou infrastruktury identity pro podporu tradiční aplikace využívající technologii directory zatížení infrastruktury a správu. Můžete přesunout tyto aplikace službám infrastruktury Azure a využívat větší úsporu na provozní náklady.


## <a name="next-steps"></a>Další postup
### <a name="learn-more-about-azure-ad-domain-services"></a>Další informace o Azure AD Domain Services
* [Funkce](active-directory-ds-features.md)
* [Scénáře nasazení](active-directory-ds-scenarios.md)
* [Zjistěte, pokud Azure AD Domain Services vyhovuje případy použití](active-directory-ds-comparison.md)
* [Pochopit, jak Azure AD Domain Services synchronizován s adresářem služby Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Začínáme s Azure AD Domain Services
* [Povolení služby Azure AD Domain Services pomocí portálu Azure](active-directory-ds-getting-started.md)
