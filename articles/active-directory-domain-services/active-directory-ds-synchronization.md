---
title: 'Azure Active Directory Domain Services: Synchronizace ve spravovaných doménách | Dokumentace Microsoftu'
description: Principy synchronizace ve spravované doméně Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: e0fc1b64514adb710ebcbdd417f65e9e3b3b3d66
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155554"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizace ve spravované doméně služby Azure AD Domain Services
Následující diagram znázorňuje, jak funguje synchronizace ve službě Azure AD Domain Services spravované domény.

![Synchronizace ve službě Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronizace z místního adresáře do svého tenanta Azure AD
Synchronizace Azure AD Connect se používá k synchronizaci uživatelských účtů, členství ve skupinách a hodnot hash přihlašovacích údajů do svého tenanta Azure AD. Atributy uživatele, například hlavní název uživatele pro účty a místní SID (security identifier) se synchronizují. Pokud používáte Azure AD Domain Services, hodnoty hash starších verzí přihlašovacích údajů požadovaných pro ověřování protokolů NTLM a Kerberos se synchronizují také do vašeho tenanta Azure AD.

Pokud nakonfigurujete zpětný zápis, změny, ke kterým dochází v adresáři služby Azure AD synchronizovaní zpět na místní Active Directory. Například pokud změníte svoje heslo pomocí služby Azure AD Samoobslužná správa hesel, změny se heslo aktualizuje v místní doméně AD.

> [!NOTE]
> Vždy používejte nejnovější verzi služby Azure AD Connect k zajištění, že máte opravy pro všechny známé chyby.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronizace z vašeho tenanta Azure AD do spravované domény
Uživatelské účty, členství ve skupinách a hodnoty hash přihlašovacích údajů jsou synchronizované z vašeho tenanta Azure AD do spravované domény služby Azure AD Domain Services. Tento proces synchronizace je automatické. Není potřeba konfigurovat, sledovat nebo spravovat proces synchronizace. Počáteční synchronizace může trvat několik hodin pár dní v závislosti na počtu objektů v adresáři služby Azure AD. Po dokončení počáteční synchronizace trvá přibližně 20-30 minutách pro změny provedené ve službě Azure AD aktualizuje ve vaší spravované domény. Tento interval synchronizace se vztahuje na změny hesla nebo změny atributů provedené v Azure AD.

Procesu synchronizace je také více-way/jednosměrnou ze své podstaty. Vaše spravovaná doména je do značné míry jen pro čtení s výjimkou žádné vlastní organizační jednotky, můžete vytvořit. Proto nemůžete provádět změny pro atributy uživatele, uživatelská hesla nebo členství ve skupinách v rámci spravované domény. V důsledku toho neexistuje žádné reverzní synchronizace změn z vaší spravované domény zpět do vašeho tenanta Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizace z více doménovými strukturami v místním prostředí
Řada organizací má poměrně složitá v místní infrastruktuře identity, který se skládá z několika doménových struktur účtů. Podporuje Azure AD Connect synchronizuje uživatele, skupiny nebo hodnoty hash přihlašovacích údajů z prostředí více doménových struktur do svého tenanta Azure AD.

Naproti tomu vašeho tenanta Azure AD je mnohem jednodušší a plochý obor názvů. Pokud chcete povolit uživatelům spolehlivý přístup k aplikacím, které jsou zabezpečené pomocí Azure AD, řešení konfliktů hlavní název uživatele napříč uživatelskými účty v různých doménových strukturách. Vaše nese spravované doméně Azure AD Domain Services zavřete velmi podobný vašeho tenanta Azure AD. Zobrazí ploché struktury organizační jednotky ve spravované doméně. Všechny uživatelské účty a skupiny jsou uloženy v rámci kontejneru "Uživatelé AADDC", bez ohledu na synchronizovaných z různých místních domén a doménových struktur. Jste nakonfigurovali hierarchické organizační jednotky struktury místní. Vaše spravovaná doména má stále jednoduché ploché struktury organizační jednotky.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Vyloučení – co není synchronizována do spravované domény
Následující objekty nebo atributy nejsou synchronizovány do svého tenanta Azure AD nebo ke spravované doméně:

* **Vyloučené atributy:** můžete vyloučit určité atributy z z vaší místní domény pomocí služby Azure AD Connect nesynchronizuje do vašeho tenanta Azure AD. Tyto atributy vyloučené nejsou k dispozici ve vaší spravované doméně.
* **Zásady skupiny:** nakonfigurovaný ve vaší doméně místní zásady skupiny nejsou synchronizovány do spravované domény.
* **Sdílená složka SYSVOL:** Podobně obsah sdílené složky Sysvol v místní doméně nejsou synchronizovány do spravované domény.
* **Objekty počítače:** objekty počítačů pro počítače připojené k místní doméně nejsou synchronizovány do spravované domény. Tyto počítače nemají vztah důvěryhodnosti s vaší spravované domény a patří do vaší místní domény. Ve vaší spravované doméně najít objekty počítače pouze pro počítače, které můžete mít explicitně připojených k doméně do spravované domény.
* **Atributy historie čísel SID pro uživatele a skupiny:** primárního uživatele a skupiny primární SID z vaší místní domény jsou synchronizovány do spravované domény. Ale existující atributy historie čísel SID pro uživatele a skupiny nejsou synchronizované z vaší místní domény do spravované domény.
* **Struktury organizační jednotky (OU):** organizační jednotky, které jsou definovány v místní doméně nejsou synchronizována do spravované domény. Existují dvě předdefinované organizační jednotky ve spravované doméně. Vaše spravovaná doména má ve výchozím nastavení ploché struktury organizační jednotky. Můžete však rozhodnout [vytvořit vlastní organizační jednotky ve spravované doméně](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Jak se konkrétní atributy synchronizují do spravované domény
Následující tabulka uvádí některé běžné atributy a popisuje, jak se synchronizují do spravované domény.

| Atribut ve vaší spravované doméně | Zdroj | Poznámky |
|:--- |:--- |:--- |
| Hlavní název uživatele (UPN) |Atribut uživatele (UPN) ve vašem tenantovi Azure AD |Atribut (UPN) ve svém tenantovi Azure AD se synchronizuje je do spravované domény. Proto je nejspolehlivější způsob, jak se přihlásit ke spravované doméně pomocí hlavní název uživatele. |
| SAMAccountName |MailNickname uživatele atribut ve vašem tenantovi Azure AD nebo automaticky generované |Atribut SAMAccountName pochází z atributu mailNickname ve vašem tenantovi Azure AD. Pokud stejný atribut mailNickname víc uživatelských účtů, SAMAccountName se generuje automaticky. Pokud uživatele mailNickname nebo hlavní název uživatele předpona je delší než 20 znaků, SAMAccountName je automaticky generovaná splňovat limit 20 znaků na atributy SAMAccountName. |
| Hesla |Heslo uživatele z vašeho tenanta Azure AD |Hodnoty hash přihlašovacích údajů požadovaných pro ověřování protokolů NTLM nebo Kerberos (také nazývané dodatečné přihlašovací údaje) jsou synchronizované z vašeho tenanta Azure AD. Pokud váš tenant Azure AD je synchronizovaný tenant, tyto přihlašovací údaje pocházejí z vaší místní domény. |
| Uživatel/identifikátor SID primární skupiny |Automaticky generované |Primární identifikátor SID pro účty uživatele nebo skupiny je automaticky generovaných ve vaší spravované doméně. Tento atribut se neshoduje s primární uživatele/skupiny SID objektu v místní doméně AD. Tato neshoda totiž spravovaná doména má jiný obor názvů SID než vaše místní domény. |
| Historie identifikátorů SID pro uživatele a skupiny |Místní primární uživatele a identifikátor SID skupiny |Atribut historie čísel SID pro uživatele a skupiny ve vaší spravované doméně je nastaven tak, aby odpovídaly odpovídající primární uživatele nebo skupinu SID v místní doméně. Tato funkce pomáhá usnadnit lift and shift místních aplikací do spravované domény, protože není potřeba znovu ACL prostředků. |

> [!NOTE]
> **Přihlaste se k spravované doméně pomocí formátu UPN:** atribut The SAMAccountName pravděpodobně automaticky generované pro některé uživatelské účty ve vaší spravované doméně. Pokud máte více uživatelů stejné atributu mailNickname nebo uživatelé mají příliš dlouhou předpony (UPN), může být automaticky generované SAMAccountName pro tyto uživatele. Proto formátu SAMAccountName (například "CONTOSO100\joeuser') není vždy spolehlivě pro přihlášení k doméně. Automaticky generované SAMAccountName uživatele může lišit od jejich předpona názvu UPN. Použijte formát hlavního názvu uživatele (například "joeuser@contoso100.com") se přihlásit ke spravované doméně spolehlivě.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Mapování atributů pro uživatelské účty
Následující tabulka ukazuje, jak se konkrétní atributy pro uživatelské objekty ve vašem tenantovi Azure AD jsou synchronizovány do odpovídající atributy ve vaší spravované doméně.

| Atribut uživatele ve vašem tenantovi Azure AD | Atribut uživatele ve vaší spravované domény |
|:--- |:--- |
| accountEnabled |userAccountControl (Zapne nebo vypne ACCOUNT_DISABLED bit) |
| city |l |
| Země |co |
| Oddělení |Oddělení |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| pracovní funkce |název |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (může být někdy automaticky vygenerovaný) |
| Mobilní zařízení |Mobilní zařízení |
| ID objektu |msDS-AzureADObjectId |
| OnPremiseSecurityIdentifier |historie čísel SID |
| passwordPolicies |userAccountControl (Zapne nebo vypne DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| PSČ |PSČ |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Příjmení |sériové číslo |
| telephoneNumber |telephoneNumber |
| userPrincipalName (Hlavní název uživatele) |userPrincipalName (Hlavní název uživatele) |

### <a name="attribute-mapping-for-groups"></a>Mapování atributů pro skupiny
Následující tabulka popisuje, jak se konkrétní atributy pro skupinu objektů ve vašem tenantovi Azure AD se synchronizují do odpovídající atributy ve vaší spravované doméně.

| Atribut skupiny ve vašem tenantovi Azure AD | Atribut skupiny ve vaší spravované doméně |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (může být někdy automaticky vygenerovaný) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| ID objektu |msDS-AzureADObjectId |
| OnPremiseSecurityIdentifier |historie čísel SID |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Heslo hash synchronizace a aspekty zabezpečení
Když povolíte službu Azure AD Domain Services, adresáře služby Azure AD vygeneruje a uloží hodnoty hash hesel protokolů NTLM a Kerberos kompatibilní formátů. 

Pro existující cloudové uživatelské účty protože Azure AD nikdy ukládá svá hesla nešifrovaným textem, tyto hodnoty hash nelze vygenerovat automaticky. Proto Microsoft vyžaduje od [cloudu uživatelům resetovat nebo změnit svá hesla](active-directory-ds-getting-started-password-sync.md) v pořadí jejich hodnot hash hesel pro vygenerování a uložené ve službě Azure AD. Pro všechny cloudové uživatelské účty vytvořené ve službě Azure AD po povolení služby Azure AD Domain Services hodnot hash hesel jsou generovány a ukládají v kompatibilní formátech protokolů NTLM a Kerberos. 

Pro uživatelské účty synchronizované z místní AD pomocí služby Azure AD Connect Sync, budete muset [konfigurovat Azure AD Connect na synchronizaci hodnot hash hesel ve formátech protokolů NTLM a Kerberos kompatibilní](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Hodnot hash protokolů NTLM a Kerberos kompatibilní hesel jsou vždy uloženy v zašifrované ve službě Azure AD. Tyto hodnoty hash se zašifrují tak, aby měl přístup k dešifrování klíče pouze Azure AD Domain Services. Žádná jiná služba nebo komponenta ve službě Azure AD nemá přístup k dešifrování klíče. Šifrovací klíče jsou jedinečné tenanta služby na Azure AD. Azure AD Domain Services synchronizaci hodnot hash hesel do řadiče domény vaší spravované domény. Tyto hodnoty hash hesla jsou uložená a zabezpečená na tyto řadiče domény podobné jak hesla jsou uložená a zabezpečená na řadiče domény Windows Server AD. Disky pro tyto řadiče spravované domény se šifrují při nečinnosti.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekty, které nejsou pro vašeho tenanta Azure AD synchronizované z vaší spravované domény
Jak je popsáno v předchozí části tohoto článku, neexistuje žádná synchronizace z vaší spravované domény zpět do vašeho tenanta Azure AD. Můžete se rozhodnout pro [vytvořit vlastní organizační jednotce (OU)](active-directory-ds-admin-guide-create-ou.md) ve vaší spravované doméně. Kromě toho můžete vytvořit další organizační jednotky, uživatele, skupiny nebo účty služby v rámci těchto vlastní organizační jednotky. Žádný z objektů vytvořených ve vlastní organizační jednotky jsou synchronizovány zpět do vašeho tenanta Azure AD. Tyto objekty jsou k dispozici pro použití pouze v rámci vaší spravované domény. Proto tyto objekty nejsou viditelné pomocí rutin Powershellu pro Azure AD, Azure AD Graph API nebo uživatelské rozhraní pro správu Azure AD.

## <a name="related-content"></a>Související obsah
* [Funkce – Azure AD Domain Services](active-directory-ds-features.md)
* [Scénáře nasazení: Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Důležité informace o sítích pro Azure AD Domain Services](active-directory-ds-networking.md)
* [Začínáme s Azure AD Domain Services](active-directory-ds-getting-started.md)
