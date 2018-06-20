---
title: 'Služba Azure AD Domain Services: Porovnání Azure AD Domain Services na řadiče domény DIY | Microsoft Docs'
description: Porovnání Azure Active Directory Domain Services na řadiče domény DIY
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
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: 550354ceab3026cfd724cd77c3266b22682e4431
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212567"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Rozhodování, pokud služba Azure AD Domain Services je nejvhodnější pro váš případ použití
S Azure AD Domain Services můžete nasadit úlohy ve službách infrastruktury Azure, aniž by museli starosti údržbu infrastruktury identity v Azure. Tato spravované služby se liší od typické nasazení systému Windows Server Active Directory, které můžete nasadit a spravovat sami. Služba nasazení je snadné a poskytuje automatizované stavu monitorování a nápravu. Jsme neustále vyvíjí službu, kterou chcete přidat podporu pro nejčastější scénáře nasazení.

Chcete-li rozhodnout, zda chcete používat Azure AD Domain Services doporučujeme následující materiálu čtení:

* Zobrazit seznam [funkce nabízené službou Azure AD Domain Services](active-directory-ds-features.md).
* Zkontrolujte běžné [scénáře nasazení služby Azure AD Domain Services](active-directory-ds-scenarios.md).
* Nakonec [porovnat Azure AD Domain Services samoobslužné možnost AD](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Porovnání služby Azure AD Domain Services k doméně DIY AD v Azure
Následující tabulka vám pomůže zjistit, mezi pomocí Azure AD Domain Services a správu vlastní infrastrukturu AD v Azure.

| **Funkce** | **Služba Azure AD Domain Services** | **'Samoobslužné' AD ve virtuálních počítačích Azure** |
| --- |:---:|:---:|
| [**Spravované služby**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Bezpečné nasazení**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Správce musí k zabezpečení nasazení. |
| [**DNS server**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(spravované služby) |**&#x2713;** |
| [**Oprávnění správce domény nebo Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Připojení k doméně**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Ověřování v doméně pomocí protokolu NTLM a Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Omezené delegování protokolu Kerberos**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|na základě prostředků|na základě prostředků & založené na klientech|
| [**Vlastní struktury organizační jednotky**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Rozšíření schématu**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Vztahy důvěryhodnosti domény nebo doménové struktuře AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Čtení protokolu LDAP**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Zabezpečený LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Zápis LDAP**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Zásady skupiny**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Geograficky distribuovaná nasazení**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Spravované služby
Microsoft spravuje domén Azure AD Domain Services. Nemusíte si dělat starosti o opravy, aktualizace, monitorování, zálohování a zajištění dostupnosti vaší domény. Tyto úlohy správy jsou nabízena jako služba Microsoft Azure pro vaše spravované domény.

#### <a name="secure-deployments"></a>Bezpečné nasazení
Spravované doméně je bezpečně uzamčené dle doporučení zabezpečení společnosti Microsoft pro nasazení AD. Tato doporučení kmen z tým produktu AD desetiletí prostředí technici a podpůrné AD nasazení. Pro samoobslužné nasazení musíte provést kroky nasazení specifické uzamknout, dolů nebo zabezpečení vašeho nasazení.

#### <a name="dns-server"></a>DNS server
Spravované doméně služby Azure AD Domain Services zahrnuje spravované služby DNS. Členové skupiny "Správci AAD řadič domény, můžete spravovat DNS na spravované domény. Členové této skupiny mají úplná oprávnění správy služby DNS pro spravovanou doménu. Správa služby DNS lze provést použitím ' DNS konzoly pro správu' součástí balíčku vzdálenou správu serveru (RSAT).
[Další informace](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Oprávnění domény nebo správce podnikové sítě
Na spravované doméně služby AAD-DS nejsou nabízí tyto zvýšená oprávnění. Aplikace, které vyžadují tyto zvýšená oprávnění nemůže být nasazena ve službě AAD DS spravované domény. Je k dispozici pro členy skupiny delegovanou správu volat 'správci AAD řadič domény, menší podmnožinu oprávnění správce. Tato oprávnění zahrnují oprávnění pro konfiguraci DNS, nakonfigurovat zásady skupiny, získání oprávnění správce na počítačích připojených k doméně atd.

#### <a name="domain-join"></a>Připojení k doméně
Virtuální počítače můžete připojit k spravované doméně podobné jak připojit počítače k doméně služby AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Ověřování v doméně pomocí protokolu NTLM a Kerberos
S Azure AD Domain Services můžete svoje podnikové přihlašovací údaje k ověřování pro spravované domény. Přihlašovací údaje jsou synchronizovány s klientovi Azure AD. U synchronizovaného klientů Azure AD Connect zajistí, že změny provedené přihlašovací údaje v místě jsou synchronizovány do Azure AD. Při instalaci samoobslužné domény budete muset nastavit domény AD vztah důvěryhodnosti s místní AD pro uživatele k ověřování pro svoje podnikové přihlašovací údaje. Alternativně můžete nastavení replikace AD zajistit, že uživatelská hesla synchronizovat pro virtuální počítače řadiče domény Azure.

#### <a name="kerberos-constrained-delegation"></a>Omezené delegování protokolu Kerberos
Nemáte oprávnění správce domény na spravované doméně služby Active Directory Domain Services. Proto se nedají konfigurovat na základě účtu (tradiční) omezené delegování protokolu Kerberos. Můžete ale nakonfigurovat bezpečnější omezené delegování na základě prostředků.
[Další informace](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Vlastní struktury organizační jednotky
Členové skupiny "Správci AAD řadič domény, můžete vytvořit vlastní organizační jednotky v rámci spravované domény. Uživatelé, kteří vytváří vlastní organizační jednotky jsou udělena úplná oprávnění správce v organizační jednotce.
[Další informace](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Rozšíření schématu
Nelze rozšířit schéma základní spravované domény služby Azure AD Domain Services. Aplikace, které jsou závislé na rozšíření schématu AD (například nové atributy v rámci objektu uživatele) proto nelze zrušit a zapuštěno AAD DS domény.

#### <a name="ad-domain-or-forest-trusts"></a>Domény služby AD nebo vztahy důvěryhodnosti doménové struktury
Spravované domény nelze konfigurovat nastavení vztahy důvěryhodnosti (příchozí nebo odchozí) s ostatními doménami. Scénáře nasazení doménové struktury prostředků proto nelze použít Azure AD Domain Services. Podobně nasazení, kde nechcete synchronizovat hesla ke službě Azure AD nelze použít Azure AD Domain Services.

#### <a name="ldap-read"></a>LDAP pro čtení
Spravované doméně podporuje čtení úlohy LDAP. Proto můžete nasadit aplikace, které provádějí operace čtení LDAP proti spravované domény.

#### <a name="secure-ldap"></a>Protokol Secure LDAP
Můžete konfigurovat Azure AD Domain Services zajistit zabezpečený přístup protokolu LDAP k vaší spravované domény, včetně přes internet.
[Další informace](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Zápis LDAP
Spravované doméně je jen pro čtení pro uživatelské objekty. Proto aplikace, které provádějí LDAP operace zápisu u atributů objektu uživatele ve spravované doméně nefungují. Kromě toho uživatelská hesla nelze změnit z v rámci spravované domény. Dalším příkladem může být úprava členství ve skupině nebo skupinu atributů v rámci spravované domény, což není povoleno. Ale některé změny atributů uživatele nebo hesla provedené ve službě Azure AD (prostřednictvím portálu prostředí PowerShell nebo Azure) nebo místní AD se synchronizují k spravované doméně AAD DS.

#### <a name="group-policy"></a>Zásady skupiny
Je integrované objektu zásad skupiny každý pro kontejnery "AADDC počítače" a "Uživatelé AADDC". Tyto integrované objekty zásad skupiny ke konfigurování zásad skupiny můžete přizpůsobit. Členy skupiny "Správci AAD řadič domény, můžete také vytvořit vlastní objekty zásad skupiny a propojit je s existující organizační jednotky (včetně vlastní organizační jednotky).
[Další informace](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Rozptýlená v geograficky nasazení
Spravované domény služby Azure AD Domain Services jsou k dispozici v jedné virtuální sítě v Azure. Pro scénáře, které vyžadují řadiče domény být k dispozici v několika oblastech Azure po celém světě nastavení řadiče domény ve virtuálních počítačích Azure IaaS může být lepší alternativou.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Možnosti nasazení 'Samoobslužné' (DIY) AD
Může mít nasazení případy použití, kde je nutné některé funkce nabízené instalace systému Windows Server AD. V těchto případech zvažte jednu z následujících možností samoobslužné (DIY):

* **Samostatné doméně cloudu:** můžete nastavit samostatnou cloudovou doménu pomocí Azure virtuální počítače, které jsou nakonfigurované jako řadiče domény. Tato infrastruktura Neintegruje s místní prostředí AD. Tato možnost by vyžadovaly jinou sadu 'cloudu přihlašovací údaje' pro přihlášení nebo správu virtuálních počítačů v cloudu.
* **Nasazení doménové struktury prostředků:** můžete nastavit domény v topologii doménové struktury prostředků pomocí Azure virtuální počítače nakonfigurované jako řadiče domény. Dále je nutné nakonfigurovat vztah důvěryhodnosti služby AD s místními prostředí AD. Připojení k doméně počítače (virtuální počítače Azure) můžete do této doménové struktury prostředku v cloudu. Ověření uživatele se stane buď přes připojení VPN nebo ExpressRoute do místního adresáře.
* **Rozšířit vaše místní domény do Azure:** virtuální sítě Azure můžete připojit k místní síti pomocí připojení VPN nebo ExpressRoute. Toto nastavení umožňuje připojit k místní virtuální počítače Azure AD. Další alternativou je zvýšení úrovně řadičů domény repliky vaší místní domény v Azure jako virtuální počítač. Potom ji můžete nastavit pro replikaci přes připojení VPN nebo ExpressRoute do místního adresáře. V tomto režimu nasazení efektivně rozšiřuje vaše místní domény do Azure.

> [!NOTE]
> Může určit, že je DIY možnost lépe vhodné pro vaše nasazení případy použití. Vezměte v úvahu [sdílení zpětné vazby](active-directory-ds-contact-us.md) a pomoci tak pochopit, co by funkce nápovědy v budoucnu zvolíte Azure AD Domain Services. Tato zpětná vazba pomůže nám momentální službu tak, aby lépe vyhovovalo vašim potřebám nasazení a případy použití.
>
>

Jsme publikovali [pokyny pro nasazení systému Windows Server Active Directory ve virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) pomohou usnadnit DIY instalace.

## <a name="related-content"></a>Související obsah
* [Funkce – Azure AD Domain Services](active-directory-ds-features.md)
* [Scénáře nasazení – Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
