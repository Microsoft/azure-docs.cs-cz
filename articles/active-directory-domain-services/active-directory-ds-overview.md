---
title: Přehled služby Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Přehled služby Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: ergreenl
ms.openlocfilehash: ed89954836fe262ba60f7401bd7b040e87b60101
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215180"
---
# <a name="azure-active-directory-ad-domain-services"></a>Doména služby Azure Active Directory (AD)
## <a name="overview"></a>Přehled
Infrastruktura Azure zlepšuje vám umožňují nasazovat širokou škálu výpočetních řešení promptně. S Azure Virtual Machines můžete řešení nasazovat prakticky okamžitě a platíte jenom za využívané minuty. S využitím podpory pro Windows, Linux, SQL Server, Oracle, IBM, SAP a BizTalk, můžete nasadit jakýkoli úkol, libovolný jazyk, a na téměř jakýkoli operační systém. Mezi tyto výhody umožňují snadno migrovat starší verze aplikace nasazené v místním do Azure, a Šetřete na provozní výdaje.

Klíčový aspekt migraci místních aplikací do Azure je zpracování identity požadavky těchto aplikací. Aplikací podporujících adresářové služby mohou spoléhat na LDAP pro čtení nebo zápis do podnikového adresáře nebo využívají integrované ověřování Windows (ověřování protokolu Kerberos nebo NTLM) k ověření koncových uživatelů. Obchodní aplikace (LOB) běžící na Windows serveru se obvykle nasazují na počítačích připojených k doméně, aby bylo možné je spravovat pomocí zásad skupiny. Pro "lift and shift" u místních aplikací do cloudu nutné vyřešit tyto závislosti v infrastruktuře podnikové identity.

Správci často zapnout na jednu z následujících řešení, abyste vyhověli potřebám identity aplikací nasazených v Azure:

* Připojení VPN typu site-to-site mezi úlohy probíhající ve službách infrastruktury Azure a podnikový adresář v místním nasazení.
* Rozšiřte podnikové infrastruktury domény nebo doménové struktuře AD nastavení repliky řadičů domény pomocí služby Azure virtual machines.
* Nasazení samostatné domény v Azure pomocí řadiče domény nasazené jako virtuální počítače Azure.

Všechny tyto přístupy trpí vysoké náklady a režijní náklady na správu. Správci jsou nutné k nasazení řadičů domény pomocí virtuálních počítačů v Azure. Kromě toho potřebují spravovat, zabezpečit, opravy, monitorování, zálohování a řešení těchto virtuálních počítačů. Závislost na připojení VPN v místním adresáři způsobí, že úloh nasazených v Azure a být zranitelné v důsledku přechodných síťových chyb a méně výpadků. Tyto výpadky sítě zase mít za následek nižší dostupnost a sníženou spolehlivost pro tyto aplikace.

Jsme navrhovali Azure AD Domain Services poskytují jednodušší alternativu.

### <a name="watch-an-introductory-video"></a>Podívejte se na úvodní video

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Úvod do služby Azure AD Domain Services

Azure AD Domain Services poskytuje služby spravované domény, jako je například připojení k doméně, skupiny zásad, LDAP, Kerberos nebo NTLM, které jsou plně kompatibilní s Windows Server Active Directory. Můžete využívat tyto domény služby bez nutnosti nasazovat, spravovat a oprava řadiče domény v cloudu. Azure AD Domain Services se integruje s existujícím tenantovi Azure AD a tím umožnit uživatelům přihlášení pomocí svých podnikových přihlašovacích údajů. Kromě toho vám pomůže stávajících skupin a uživatelské účty zabezpečení přístupu k prostředkům, čímž zajišťuje hladší "lift a shift" on-premises prostředků se službami infrastruktury Azure.

Funkce Azure AD Domain Services funguje bez problémů bez ohledu na to, jestli je váš tenant Azure AD výhradně cloudový nebo synchronizované s místní Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services u výhradně cloudového organizací

Výhradně cloudového tenanta Azure AD (označovaný také jako "spravované tenantů") nemá žádné místní identity nároky na místo. Jinými slovy uživatelské účty, jejich hesla a členství ve skupinách jsou všechny nativní pro cloud – to znamená, vytvářet a spravovat ve službě Azure AD. Vezměte v úvahu na chvíli zastavíme Contoso je výhradně cloudového tenanta Azure AD. Jak je znázorněno na následujícím obrázku, správce společnosti Contoso má nakonfigurovanou virtuální síť ve službách infrastruktury Azure. Aplikace a úlohy serveru jsou nasazené v této virtuální síti ve službě Azure virtual machines. Protože Contoso je výhradně cloudový tenant, všechny identity uživatelů, jejich přihlašovacích údajů a členství ve skupinách se vytvářet a spravovat ve službě Azure AD.

![Přehled služby Azure Active Directory Domain Services](./media/active-directory-domain-services-overview/aadds-overview.png)

Společnosti Contoso IT správce může povolit Azure AD Domain Services pro svého tenanta Azure AD a zvolit, jestli služby domain services k dispozici v této virtuální síti. Po tomto datu Azure AD Domain Services zřizuje spravované domény a zpřístupňuje je ve virtuální síti. Všechny uživatelské účty, členství ve skupinách a přihlašovacích údajů uživatele ve společnosti Contoso tenantovi Azure AD k dispozici jsou také k dispozici v tomto nově vytvořené domény. Tato funkce umožňuje uživatelům v organizaci přihlásit k doméně pomocí podnikových přihlašovacích údajů – například při vzdáleném připojení k doméně počítače přes vzdálenou plochu. Správci můžou zřizovat přístup k prostředkům v doméně pomocí existujícího členství ve skupinách. Aplikace nasazenými na virtuálních počítačích ve virtuální síti můžou využívat funkce, jako je připojení k doméně, LDAP pro čtení, LDAP bind, ověřování protokolů NTLM a Kerberos a zásad skupiny.

Několik nejdůležitějšími aspekty spravované domény, zřízenou se službou Azure AD Domain Services jsou následující:

* Společnosti Contoso IT správce není potřeba spravovat, opravovat nebo sledovat tuto doménu nebo všechny řadiče domény pro tuto spravovanou doménu.
* Není nutné ke správě replikace služby AD pro tuto doménu. Uživatelské účty, členství ve skupinách a přihlašovacích údajů ve společnosti Contoso tenantovi Azure AD jsou automaticky dostupné v rámci této spravované doméně.
* Vzhledem k tomu, že doménu je spravované službou Azure AD Domain Services, Contoso na správce IT nemá oprávnění správce domény nebo správce podnikové sítě v této doméně.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD Domain Services pro hybridní organizace
Organizace s hybridní infrastruktury IT využívat kombinaci prostředků cloudu a místních prostředků. Tyto organizace synchronizovat informace o identitě ze svého místního adresáře do svého tenanta Azure AD. Jak hybridní organizace vypadat více migraci místních aplikací do cloudu, zejména starší verze adresářové aplikace, může být užitečné k nim Azure AD Domain Services.

Společnost litware má nasazený [Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md), synchronizovat informace o identitě ze svého místního adresáře do svého tenanta Azure AD. Informace o identitě, který je synchronizován obsahuje uživatelské účty, jejich hodnoty hash přihlašovacích údajů pro ověřování (synchronizace hodnot hash hesel) a členství ve skupinách.

> [!NOTE]
> **Synchronizace hodnot hash hesel je povinné pro hybridní organizace používat službu Azure AD Domain Services**. Tento požadavek je vzhledem k tomu, že jsou vyžadována pověření uživatelů ve spravované doméně poskytovaných službou Azure AD Domain Services k ověřování těchto uživatelů prostřednictvím metody pro ověřování protokolů NTLM nebo Kerberos.
>
>

![Azure AD Domain Services pro společnost Litware](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Na předchozím obrázku ukazuje, jak organizace s hybridní infrastrukturu IT, jako jsou společnosti Litware, můžete použít Azure AD Domain Services. Společnosti Litware aplikací a úloh serveru, které vyžadují služby domain services se nasadí ve virtuální síti ve službách infrastruktury Azure. Společnosti Litware správce IT můžete povolit Azure AD Domain Services pro svého tenanta Azure AD a zvolit, jestli spravovaná doména k dispozici v této virtuální síti. Protože společnost Litware organizaci s hybridní infrastrukturu IT, uživatelské účty, skupiny a přihlašovací údaje jsou synchronizovány do svého tenanta Azure AD ze svého místního adresáře. Tato funkce umožňuje uživatelům umožní přihlásit k doméně pomocí podnikových přihlašovacích údajů – například při vzdálené připojení pro počítače připojené k doméně přes vzdálenou plochu. Správci můžou zřizovat přístup k prostředkům v doméně pomocí existujícího členství ve skupinách. Aplikace nasazenými na virtuálních počítačích ve virtuální síti můžou využívat funkce, jako je připojení k doméně, LDAP pro čtení, LDAP bind, ověřování protokolů NTLM a Kerberos a zásad skupiny.

Několik nejdůležitějšími aspekty spravované domény, zřízenou se službou Azure AD Domain Services jsou následující:

* Spravovaná doména se samostatné domény. Není rozšířením společnosti Litware místní domény.
* Společnosti Litware správce IT není nutné spravovat, opravovat ani monitorování řadičů domény pro tuto spravovanou doménu.
* Není nutné ke správě replikace AD k této doméně. Uživatelské účty, členství ve skupinách a přihlašovacích údajů z místního adresáře společnosti Litware jsou synchronizovány do Azure AD prostřednictvím služby Azure AD Connect. Tyto uživatelské účty, členství ve skupinách a přihlašovací údaje jsou automaticky dostupné v rámci spravované domény.
* Vzhledem k tomu, že doménu je spravované službou Azure AD Domain Services, Litware od správce IT nemá oprávnění správce domény nebo správce podnikové sítě v této doméně.

## <a name="benefits"></a>Výhody
Díky službě Azure AD Domain Services budete moct využívat následující výhody:

* **Jednoduché** – nemůže splnit potřeby identity virtuálních počítačů nasazených služeb infrastruktury Azure s několika jednoduchými kliknutími. Není potřeba nasazovat a spravovat infrastrukturu identit v Azure nebo instalační program připojit zpět k vaší místní infrastruktury identit.
* **Integrované** – Azure AD Domain Services se úzce integruje se tenanta Azure AD. Teď můžete použít Azure AD jako integrované cloudové podnikové adresáře, který určeného pro potřeby moderních aplikací i tradičních aplikací podporujících adresářové služby.
* **Kompatibilní** – Azure AD Domain Services je postavená na infrastrukturu na podnikové úrovni zajistí prověřený podnikový systém Windows Server Active Directory. Proto se vaše aplikace můžete spolehnout na vyšší úroveň kompatibility s funkcemi systému Windows Server Active Directory. Ne všechny funkce dostupné v systému Windows Server AD jsou nyní dostupné v Azure AD Domain Services. K dispozici funkce jsou však kompatibilní s odpovídající funkce systému Windows Server AD, které se spoléháte na místní infrastrukturu. Možnosti připojení k protokolu LDAP, Kerberos, NTLM, zásad skupiny a domény tvoří až po zralé nabídka, která prošla testováním a vylepšili prostřednictvím různých verzí Windows serveru.
* **Nákladově efektivní** – s Azure AD Domain Services, se můžete vyhnout zatížení infrastruktury a správy, který je spojen se správou infrastruktury identity, které tradičních aplikací podporujících adresářové služby. Můžete přesunout tyto aplikace se službami infrastruktury Azure a těžit z větší úspory na provozní výdaje.


## <a name="next-steps"></a>Další postup
### <a name="learn-more-about-azure-ad-domain-services"></a>Další informace o službě Azure AD Domain Services
* [Funkce](active-directory-ds-features.md)
* [Scénáře nasazení](active-directory-ds-scenarios.md)
* [Zjistěte, pokud Azure AD Domain Services vyhovuje případy použití](active-directory-ds-comparison.md)
* [Vysvětlení, jak Azure AD Domain Services synchronizován s adresářem Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Začínáme s Azure AD Domain Services
* [Povolení služby Azure AD Domain Services pomocí webu Azure portal](active-directory-ds-getting-started.md)
