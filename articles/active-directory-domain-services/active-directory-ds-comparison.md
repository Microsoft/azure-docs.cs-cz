---
title: 'Azure AD Domain Services: Porovnání Azure AD Domain Services k řadičům domény rozběhněte zcela | Dokumentace Microsoftu'
description: Porovnání služeb Azure Active Directory Domain Services k řadičům domény SVÉPOMOCÍ
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: acb125ef807193e79e45bca7e217efe3ffb31698
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505946"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Jak určit, pokud Azure AD Domain Services bude vyhovovat vašemu případu použití
S Azure AD Domain Services můžete nasadit úlohy ve službách infrastruktury Azure, aniž byste museli starat o údržbu infrastruktury identity v Azure. Této spravované služby se liší od typické nasazení systému Windows Server Active Directory, která můžete nasadit a spravovat sami. Služba je snadné nasazení a poskytuje automatizované stavu sledování a náprava. Neustále vyvíjíme službu s přidanou podporou pro běžné scénáře nasazení.

Můžete rozhodnout, jestli použít Azure AD Domain Services doporučujeme následující čtení materiálu:

* Zobrazit seznam [funkcí nabízených službou Azure AD Domain Services](active-directory-ds-features.md).
* Zkontrolujte běžné [scénáře nasazení pro Azure AD Domain Services](active-directory-ds-scenarios.md).
* Nakonec [porovnání služby Azure AD Domain Services samoobslužné možnosti AD](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Porovnání služby Azure AD Domain Services k doméně AD rozběhněte zcela v Azure
Následující tabulka vám pomůže rozhodnout mezi pomocí Azure AD Domain Services a správu AD infrastruktury v Azure.

| **Funkce** | **Azure AD Domain Services** | **"Sestavili" AD ve virtuálních počítačích Azure** |
| --- |:---:|:---:|
| [**Spravované služby**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Zabezpečení nasazení**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Správce musí k zabezpečení nasazení. |
| [**DNS server**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(spravovaná služba) |**&#x2713;** |
| [**Oprávnění správce domény nebo Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Připojení k doméně**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Ověřování v doméně pomocí protokolů NTLM a Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Omezené delegování protokolu Kerberos**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|založené na prostředcích|založené na prostředcích & založené na účtu|
| [**Vlastní struktury organizační jednotky**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Rozšíření schématu**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Vztahy důvěryhodnosti domény nebo doménové struktuře AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Přečtěte si LDAP**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Protokol Secure LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Zápis LDAP**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Zásady skupiny**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Geograficky distribuované nasazení**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Spravované služby
Domény Azure AD Domain Services spravuje Microsoft. Není nutné se starat o opravy, aktualizace, monitorování, zálohování a zajištění dostupnosti vaší domény. Tyto úlohy správy jako služby Microsoft Azure nabízí pro spravované domény.

#### <a name="secure-deployments"></a>Zabezpečení nasazení
Spravovaná doména je bezpečně uzamčené podle doporučení zabezpečení od Microsoftu pro nasazení AD. Tato doporučení vyplývají z produktového týmu AD desítky let praxe s technickými a podporuje AD nasazení. Sestavili nasazení musíte provést kroky nasazení specifické pro uzamčení dolů a zabezpečení vašeho nasazení.

#### <a name="dns-server"></a>DNS server
Spravované domény služby Azure AD Domain Services obsahuje spravované služby DNS. Členové skupiny "Správci AAD DC" můžete spravovat DNS ve spravované doméně. Členové této skupiny jsou uvedeny úplná oprávnění pro správu DNS pro spravovanou doménu. Správa služby DNS je možné provádět pomocí "DNS konzoly pro správu' obsažené v balíčku vzdálenou správu serveru (RSAT).
[Další informace](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Oprávnění domény nebo správce podnikové sítě
Tyto zvýšená oprávnění nejsou ve spravované doméně AAD DS nabízejí. Aplikace, které vyžadují zvýšená oprávnění se nedá nasadit ve službě AAD DS spravované domény. Menší podmnožinu oprávnění správce je k dispozici členům skupiny delegovanou správu nazvané "Správci AAD DC". Tato oprávnění zahrnují oprávnění pro konfiguraci DNS, nakonfigurovat zásady skupiny, získat oprávnění správce na počítačích připojených k doméně apod.

#### <a name="domain-join"></a>Připojení k doméně
Připojení virtuálních počítačů do spravované domény, který je podobný jak připojit počítače k doméně AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Ověřování v doméně pomocí protokolů NTLM a Kerberos
S Azure AD Domain Services můžete použít své podnikové přihlašovací údaje k ověření pomocí spravované domény. Přihlašovací údaje se udržovat synchronizované s vašeho tenanta Azure AD. Pro synchronizovaní klienti služby Azure AD Connect zajišťuje, že změny provedené přihlašovací údaje v místním jsou synchronizovány do Azure AD. Při instalaci sestavili domény, budete muset nastavení domény služby AD vztah důvěryhodnosti s místní službou AD pro uživatele k ověření pomocí svých podnikových přihlašovacích údajů. Můžete také budete muset nastavení replikace AD zajistit synchronizovat hesla uživatelů k vašim virtuálním počítačům řadič domény Azure.

#### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos
Nemáte oprávnění ' správce domény na spravované doméně služby Active Directory Domain Services. Proto je nelze nakonfigurovat založené na účtu (tradiční) omezené delegování protokolu Kerberos. Můžete ale nakonfigurovat bezpečnější omezeným delegováním založeným na prostředek.
[Další informace](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Vlastní struktury organizační jednotky
Členové skupiny 'Správci AAD DC' můžete vytvořit vlastní organizační jednotky ve spravované doméně. Uživatelé, kteří vytvářejí vlastní organizační jednotky jsou udělena úplná oprávnění správce v organizační jednotce.
[Další informace](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Rozšíření schématu
Nelze rozšířit základní schéma této spravované doméně služby Azure AD Domain Services. Aplikace, které spoléhají na rozšíření schématu AD (například nové atributy v objektu user) proto nelze zrušeno a přesunuto do služby AAD DS domény.

#### <a name="ad-domain-or-forest-trusts"></a>Domény služby AD nebo vztahy důvěryhodnosti doménové struktury
Spravované domény nejde nakonfigurovat nastavení vztahy důvěryhodnosti (příchozí nebo odchozí) s dalšími doménami. Scénáře nasazení doménové struktury prostředků proto nelze použít Azure AD Domain Services. Podobně nasazení, kde nechcete synchronizovat hesla ke službě Azure AD nemůže použít Azure AD Domain Services.

#### <a name="ldap-read"></a>Čtení protokolu LDAP
Spravovaná doména podporuje čtení úloh protokolu LDAP. Proto můžete nasadit aplikace, které provádějí operace čtení protokolu LDAP pro spravovanou doménu.

#### <a name="secure-ldap"></a>Protokol Secure LDAP
Můžete konfigurovat Azure AD Domain Services k poskytování přístupu k vaší spravované domény, včetně přes internet secure LDAP.
[Další informace](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Zápis LDAP
Spravovaná doména je jen pro čtení pro uživatelské objekty. Proto aplikace, které provádějí operace zápisu LDAP proti atributy objektu uživatele, nebudou fungovat ve spravované doméně. Kromě toho hesla uživatele nejde změnit z v rámci spravované domény. Dalším příkladem může být změny členství ve skupině nebo skupinu atributů v rámci spravované domény, což není povoleno. Ale žádné změny atributy uživatele nebo hesla provedené v Azure AD (prostřednictvím Powershellu nebo Azure portal) nebo místní AD se synchronizují do spravované domény AAD DS.

#### <a name="group-policy"></a>Zásady skupiny
Není k dispozici vestavěné objektu zásad skupiny jednotlivých kontejnerů "Kontejnery počítače AADDC" a "Uživatelé AADDC". Tyto předdefinované objekty zásad skupiny ke konfigurování zásad skupiny můžete přizpůsobit. Členové skupiny 'Správci AAD DC' můžete také vytvořit vlastní objekty zásad skupiny a propojují se s stávajících organizačních jednotek (včetně vlastních OJ).
[Další informace](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Geograficky jsou daleko od sebe nasazení
Spravované domény služby Azure AD Domain Services jsou k dispozici v jedné virtuální sítě v Azure. Pro scénáře, které vyžadují řadiče domény bude k dispozici v několika oblastech Azure po celém světě nastavení řadiče domény na virtuálních počítačích Azure IaaS může být lepší alternativou.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Možnosti nasazení "Sestavili" (DIY) AD
Může mít použití nasazené tam, kde potřebujete některé možnosti nabízené instalace systému Windows Server AD. V těchto případech zvažte jednu z následujících možností sestavili (DIY):

* **Samostatné domény cloudu:** můžete nastavit samostatný cloudové doméně pomocí služby Azure virtual machines, které jsou nakonfigurované jako řadiče domény. Tato infrastruktura nejde integrovat místní prostředí AD. Tato možnost bude vyžadovat jinou sadu 'přihlašovací údaje do cloudu' k přihlášení a správa virtuálních počítačů v cloudu.
* **Nasazení doménové struktury prostředků:** můžete nastavit domény v doménové struktuře topologii prostředků pomocí Azure virtual machines, které jsou nakonfigurované jako řadiče domény. Dále je nutné nakonfigurovat vztah důvěryhodnosti AD s vaší místní prostředí AD. Připojení k doméně počítače (virtuální počítače Azure) můžete do této doménové struktury prostředků v cloudu. Ověření uživatele se stane buď přes připojení VPN/ExpressRoute do místního adresáře.
* **Rozšíření místní domény do Azure:** službě Azure virtual network můžete připojit k místní síti pomocí připojení VPN/ExpressRoute. Toto nastavení umožňuje virtuální počítače Azure k místní AD. Další možností je zvýšení úrovně řadičů domény repliky vaší místní domény v Azure jako virtuální počítač. Vám může potom ho nastavit pro replikaci přes připojení VPN/ExpressRoute do místního adresáře. Tento režim nasazení efektivně rozšiřuje vaši místní doménu do Azure.

> [!NOTE]
> Můžete určit, že rozběhněte zcela možnost je vhodnější pro vaše nasazení případy použití. Vezměte v úvahu [sdílení zpětné vazby](active-directory-ds-contact-us.md) který nám pomůže zjistit, co by funkce pomáhají jste si zvolili Azure AD Domain Services v budoucnu. Tato zpětná vazba nám vyvíjí služby, aby lépe vyhovovala požadavků na nasazení a případy použití.
>
>

Jsme publikovali [pokyny pro nasazení systému Windows Server Active Directory na Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx) abychom usnadnili rozběhněte zcela instalace.

## <a name="related-content"></a>Související obsah
* [Funkce – Azure AD Domain Services](active-directory-ds-features.md)
* [Scénáře nasazení: Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
