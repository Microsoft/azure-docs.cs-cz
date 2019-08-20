---
title: 'Azure Active Directory Domain Services: Synchronizace ve spravovaných doménách | Microsoft Docs'
description: Pochopení synchronizace ve spravované doméně Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617105"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizace ve spravované doméně Azure AD Domain Services
Následující diagram znázorňuje, jak synchronizace funguje v Azure AD Domain Services spravovaných doménách.

![Synchronizace v Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronizace z místního adresáře do tenanta Azure AD
K synchronizaci uživatelských účtů, členství ve skupinách a hodnot hash přihlašovacích údajů pro vašeho tenanta Azure AD se používá Azure AD Connect synchronizace. Budou synchronizovány atributy uživatelských účtů, například hlavní název uživatele (UPN) a místní identifikátor zabezpečení (SID). Použijete-li Azure AD Domain Services, jsou všechny hodnoty hash přihlašovacích údajů vyžadované pro ověřování protokolem NTLM a Kerberos také synchronizovány s vaším klientem služby Azure AD.

Pokud nakonfigurujete zpětný zápis, změny, ke kterým došlo v adresáři služby Azure AD, se synchronizují zpátky do místní služby Active Directory. Pokud například změníte heslo pomocí samoobslužné správy hesel služby Azure AD, změněné heslo se aktualizuje ve vaší místní doméně AD.

> [!NOTE]
> Vždy používejte nejnovější verzi Azure AD Connect, abyste měli jistotu, že máte opravy pro všechny známé chyby.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronizace z vašeho tenanta Azure AD do spravované domény
Uživatelské účty, členství ve skupinách a hodnoty hash přihlašovacích údajů se synchronizují z vašeho tenanta Azure AD s vaší Azure AD Domain Services spravovanou doménou. Tento proces synchronizace je automatický. Tento proces synchronizace není nutné konfigurovat, monitorovat ani spravovat. Počáteční synchronizace může trvat několik hodin, a to v závislosti na počtu objektů v adresáři služby Azure AD. Po dokončení počáteční synchronizace bude trvat přibližně 20-30 minut, než se změny provedené ve službě Azure AD aktualizují ve spravované doméně. Tento interval synchronizace se vztahuje na změny hesla nebo změny atributů provedené ve službě Azure AD.

Proces synchronizace je také jednosměrný/jednosměrný v podstatě. Vaše spravovaná doména je z velké části jen pro čtení, s výjimkou jakýchkoli vlastních organizačních jednotek, které vytvoříte. Proto nemůžete měnit atributy uživatele, hesla uživatelů ani členství ve skupinách ve spravované doméně. V důsledku toho nebudete moct zpětně synchronizovat změny ze spravované domény zpátky do svého tenanta služby Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizace z místního prostředí s více doménovými strukturami
Mnoho organizací má poměrně složitou místní infrastrukturu identity skládající se z několika doménových struktur účtů. Azure AD Connect podporuje synchronizaci uživatelů, skupin a hodnot hash přihlašovacích údajů z prostředí s více doménovými strukturami do vašeho tenanta Azure AD.

Naopak váš tenant Azure AD je mnohem jednodušší a plochý obor názvů. Aby uživatelé mohli spolehlivě přistupovat k aplikacím zabezpečeným službou Azure AD, vyřešte konflikty hlavního názvu uživatele v různých uživatelských účtech v různých doménových strukturách. Vaše Azure AD Domain Services spravovaná doména nese pro vašeho tenanta Azure AD blízkou podobu. Ve spravované doméně se zobrazí struktura ploché organizační jednotky. Všechny uživatelské účty a skupiny se ukládají v kontejneru AADDC Users, navzdory synchronizaci z různých místních domén nebo doménových struktur. Je možné, že jste nakonfigurovali hierarchickou strukturu organizačních jednotek místně. Vaše spravovaná doména má stále jednoduchou strukturu ploché organizační jednotky.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Vyloučení – co není synchronizované s vaší spravovanou doménou
Následující objekty nebo atributy se nesynchronizují s vaším klientem služby Azure AD nebo vaší spravovanou doménou:

* **Vyloučené atributy:** Můžete vyloučit určité atributy z synchronizace s vaším klientem Azure AD z místní domény pomocí Azure AD Connect. Tyto vyloučené atributy nejsou ve spravované doméně k dispozici.
* **Zásady skupiny:** Zásady skupiny nakonfigurované ve vaší místní doméně se nesynchronizují do vaší spravované domény.
* **Sdílená složka SYSVOL:** Podobně se obsah sdílené složky SYSVOL v místní doméně nesynchronizuje do spravované domény.
* **Objekty počítače:** Objekty počítačů pro počítače připojené k vaší místní doméně se nesynchronizují do vaší spravované domény. Tyto počítače nemají vztah důvěryhodnosti se spravovanou doménou a patří pouze do vaší místní domény. V spravované doméně najdete objekty počítače jenom pro počítače, které jste explicitně připojili k doméně spravované doméně.
* **Atributy SidHistory pro uživatele a skupiny:** Identifikátory SID primárních uživatelů a primární skupiny z vaší místní domény se synchronizují do vaší spravované domény. Stávající atributy SidHistory pro uživatele a skupiny se ale nesynchronizují z vaší místní domény do spravované domény.
* **Struktury organizačních jednotek (OU):** Organizační jednotky definované v místní doméně se nesynchronizují do vaší spravované domény. Ve spravované doméně jsou dva předdefinované organizační jednotky. Ve výchozím nastavení má vaše spravovaná doména strukturu ploché organizační jednotky. Ve spravované doméně se ale můžete rozhodnout [vytvořit vlastní organizační jednotku](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Způsob synchronizace specifických atributů do spravované domény
Následující tabulka uvádí některé běžné atributy a popisuje, jak jsou synchronizovány do spravované domény.

| Atribut ve spravované doméně | Source | Poznámky |
|:--- |:--- |:--- |
| HLAVNÍ NÁZEV UŽIVATELE |Atribut hlavního názvu uživatele (UPN) ve vašem tenantovi Azure AD |Atribut hlavního názvu uživatele z vašeho tenanta Azure AD se synchronizuje s vaší spravovanou doménou. Nejspolehlivějším způsobem, jak se přihlašovat ke spravované doméně, je používat hlavní název uživatele (UPN). |
| SAMAccountName |Atribut mailNickname uživatele v tenantovi Azure AD nebo automaticky generovaný |Z atributu mailNickname ve vašem tenantovi služby Azure AD je zdrojový atribut SAMAccountName. Pokud má více uživatelských účtů stejný atribut mailNickname, je automaticky vygenerována vlastnost SAMAccountName. Pokud je předpona mailNickname nebo hlavního názvu uživatele (UPN) delší než 20 znaků, automaticky se vygeneruje hodnota SAMAccountName, aby splňovala omezení 20 znaků u atributů SAMAccountName. |
| Hesla |Heslo uživatele z vašeho tenanta Azure AD |Hodnoty hash přihlašovacích údajů vyžadované pro ověřování protokolem NTLM nebo Kerberos (označované taky jako doplňkové přihlašovací údaje) se synchronizují z vašeho tenanta Azure AD. Pokud je váš tenant služby Azure AD synchronizovaný tenant, jsou tyto přihlašovací údaje zdrojové z vaší místní domény. |
| Identifikátor SID primárního uživatele/skupiny |Automaticky generované |Ve spravované doméně se automaticky generuje primární identifikátor SID pro účty uživatelů a skupin. Tento atribut se neshoduje s identifikátorem SID primárního uživatele nebo skupiny objektu ve vaší místní doméně AD. Tato neshoda je způsobená tím, že spravovaná doména má jiný obor názvů SID než vaše místní doména. |
| Historie identifikátorů SID pro uživatele a skupiny |Identifikátor SID místního primárního uživatele a skupiny |Atribut SidHistory pro uživatele a skupiny ve spravované doméně je nastavený tak, aby odpovídal odpovídajícímu identifikátoru SID primárního uživatele nebo skupiny v místní doméně. Tato funkce pomáhá zjednodušit přezvednutí místních aplikací do spravované domény, protože nemusíte znovu vytvářet prostředky ACL. |

> [!NOTE]
> **Přihlaste se ke spravované doméně pomocí formátu UPN:** U některých uživatelských účtů ve spravované doméně se může automaticky generovat atribut SAMAccountName. Pokud má více uživatelů stejný atribut mailNickname nebo uživatelé mají příliš dlouhé předpony hlavního názvu uživatele (UPN), mohou být pro tyto uživatele automaticky vygenerovány hodnoty SAMAccountName pro tyto uživatele. Proto formát SAMAccountName (například ' CONTOSO\dee ') není vždy spolehlivým způsobem, jak se přihlásit k doméně. Automaticky vygenerovaný parametr SAMAccountName uživatele se může lišit od předpony hlavního názvu uživatele (UPN). K spolehlivému přihlášení ke spravované doméně použijte formátdee@contoso.comUPN (například).

### <a name="attribute-mapping-for-user-accounts"></a>Mapování atributů uživatelských účtů
Následující tabulka ukazuje, jak se konkrétní atributy pro uživatelské objekty v tenantovi Azure AD synchronizují s odpovídajícími atributy ve spravované doméně.

| Atribut uživatele v tenantovi Azure AD | Atribut uživatele ve spravované doméně |
|:--- |:--- |
| accountEnabled |stav skupiny (nastavení nebo vymazání bitu ACCOUNT_DISABLED) |
| city |l |
| krajin |co |
| Oddělení |Oddělení |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| pracovní funkce |název |
| e-mailu |e-mailu |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (někdy může být generováno automaticky) |
| Mobilní zařízení |Mobilní zařízení |
| objektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Čísel |
| passwordPolicies |stav skupiny (nastavení nebo vymazání bitu DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| PSČ |PSČ |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Příjmení |sériové číslo |
| telephoneNumber |telephoneNumber |
| userPrincipalName (Hlavní název uživatele) |userPrincipalName (Hlavní název uživatele) |

### <a name="attribute-mapping-for-groups"></a>Mapování atributů pro skupiny
Následující tabulka ukazuje, jak jsou konkrétní atributy pro objekty skupin ve vašem tenantovi Azure AD synchronizované s odpovídajícími atributy ve spravované doméně.

| Atribut Group v tenantovi Azure AD | Atribut Group ve spravované doméně |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (někdy může být generováno automaticky) |
| e-mailu |e-mailu |
| mailNickname |msDS-AzureADMailNickname |
| objektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Čísel |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizace hodnoty hash hesel a posouzení zabezpečení
Když povolíte Azure AD Domain Services, adresář služby Azure AD vygeneruje a uloží hodnoty hash hesel ve formátech kompatibilních s protokolem NTLM & Kerberos. 

Pro existující cloudové uživatelské účty, protože Azure AD nikdy neukládá hesla k nešifrovaným textům, nedají se automaticky vygenerovat tyto hodnoty hash. Proto společnost Microsoft vyžaduje, [aby uživatelé cloudových aplikací obnovili nebo změnili hesla](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby bylo možné generovat a ukládat hodnoty hash hesel v Azure AD. Pro všechny cloudové uživatelské účty vytvořené v Azure AD po povolení Azure AD Domain Services se generují hodnoty hash hesel a ukládají se do formátů kompatibilních s protokolem NTLM a Kerberos. 

U uživatelských účtů synchronizovaných z místní služby AD pomocí Azure AD Connect synchronizace je potřeba [nakonfigurovat Azure AD Connect pro synchronizaci hodnot hash hesel ve formátech kompatibilních s protokoly NTLM a Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Hodnoty hash hesla kompatibilní s protokolem NTLM a Kerberos se vždycky ukládají šifrovaným způsobem ve službě Azure AD. Tyto hodnoty hash jsou zašifrované tak, aby k dešifrovacím klíčům měli přístup jenom Azure AD Domain Services. Žádná jiná služba ani součást ve službě Azure AD nemá přístup k dešifrovacím klíčům. Šifrovací klíče jsou jedinečné pro každého tenanta Azure AD. Azure AD Domain Services synchronizuje hodnoty hash hesla do řadičů domény pro spravovanou doménu. Tyto hodnoty hash hesla se ukládají a zabezpečují na těchto řadičích domény podobně jako ukládání a zabezpečení hesel na řadičích domény se systémem Windows Server AD. Disky pro tyto spravované řadiče domény jsou v klidovém stavu šifrované.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekty, které nejsou synchronizované s vaším klientem služby Azure AD ze spravované domény
Jak je popsáno v předchozí části tohoto článku, neprovádí se žádná synchronizace ze spravované domény zpátky do vašeho tenanta Azure AD. Ve spravované doméně se můžete rozhodnout [vytvořit vlastní organizační jednotku (OU)](create-ou.md) . Dále můžete vytvořit další organizační jednotky, uživatele, skupiny nebo účty služeb v rámci těchto vlastních organizačních jednotek. Žádný z objektů vytvořených v rámci vlastních organizačních jednotek se nesynchronizuje zpátky do vašeho tenanta Azure AD. Tyto objekty jsou k dispozici pouze pro použití v rámci spravované domény. Proto tyto objekty nejsou viditelné pomocí rutin Azure AD PowerShellu, Azure AD Graph API nebo pomocí uživatelského rozhraní pro správu Azure AD.

## <a name="related-content"></a>Související obsah
* [Scénáře nasazení – Azure AD Domain Services](scenarios.md)
* [Síťové požadavky pro Azure AD Domain Services](network-considerations.md)
* [Začínáme s Azure AD Domain Services](tutorial-create-instance.md)
