---
title: Jak synchronizace funguje ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak funguje proces synchronizace pro objekty a přihlašovací údaje z klienta Azure AD nebo místního prostředí služby Active Directory Domain Services do spravované domény služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639915"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizace objektů a přihlašovacích údajů ve spravované doméně služby Azure AD Domain Services

Objekty a přihlašovací údaje ve spravované doméně služby Azure Active Directory Domain Services (AD DS) lze vytvořit místně v rámci domény nebo synchronizovat z klienta Azure Active Directory (Azure AD). Při prvním nasazení Služby Azure AD DS se nakonfiguruje automatická jednosměrná synchronizace a spustí se replikace objektů z Azure AD. Tato jednosměrná synchronizace nadále běží na pozadí, aby byla spravovaná doména Azure AD DS aktuální s případnými změnami ze služby Azure AD. Žádná synchronizace dochází z Azure AD DS zpět do Služby Azure AD.

V hybridním prostředí lze objekty a přihlašovací údaje z místní domény služby AD DS synchronizovat do služby Azure AD pomocí služby Azure AD Connect. Jakmile jsou tyto objekty úspěšně synchronizovány do Služby Azure AD, automatická synchronizace na pozadí pak zpřístupní tyto objekty a přihlašovací údaje aplikacím pomocí spravované domény Azure AD DS.

Následující diagram znázorňuje, jak funguje synchronizace mezi Azure AD DS, Azure AD a volitelným místním prostředím služby AD DS:

![Přehled synchronizace pro spravovanou doménu služby Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronizace z Azure AD na Azure AD DS

Uživatelské účty, členství ve skupinách a hash přihlašovacích údajů jsou synchronizovány jedním způsobem z Azure AD do Azure AD DS. Tento proces synchronizace je automatický. Tento proces synchronizace není nutné konfigurovat, monitorovat ani spravovat. Počáteční synchronizace může trvat několik hodin na několik dní, v závislosti na počtu objektů v adresáři Azure AD. Po dokončení počáteční synchronizace se změny provedené ve službě Azure AD, jako jsou změny hesla nebo atributů, automaticky synchronizují do služby Azure AD DS.

Když se uživatel vytvoří ve službě Azure AD, nejsou synchronizovány s Azure AD DS, dokud nezmění své heslo ve službě Azure AD. Tento proces změny hesla způsobí, že hodnot hash hesel pro ověřování protokolů Kerberos a NTLM se vygenerují a uloží ve službě Azure AD. K úspěšnému ověření uživatele ve službě Azure AD DS jsou potřeba hashe hesla.

Proces synchronizace je jedním ze způsobů / jednosměrný podle návrhu. Neexistuje žádná zpětná synchronizace změn z Azure AD DS zpět do Služby Azure AD. Spravovaná doména Azure AD DS je do značné míry jen pro čtení s výjimkou vlastních vou, které můžete vytvořit. V rámci spravované domény Azure AD DS nelze provádět změny atributů uživatelů, uživatelských hesel nebo členství ve skupinách.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronizace a mapování atributů do služby Azure AD DS

V následující tabulce jsou uvedeny některé běžné atributy a způsob jejich synchronizace s Azure AD DS.

| Atribut ve službě Azure AD DS | Zdroj | Poznámky |
|:--- |:--- |:--- |
| UPN | Atribut *UPN* uživatele v tenantu Azure AD | Atribut UPN z klienta Azure AD se synchronizuje jako je do Služby Azure AD DS. Nejspolehlivější způsob, jak se přihlásit ke spravované doméně Azure AD DS, je použití hlavního názvu domény. |
| Samaccountname | Atribut *mailNickname* uživatele v tenantu Azure AD nebo automaticky vygenerovaný | Atribut *SAMAccountName* pochází z atributu *mailNickname* v tenantovi Azure AD. Pokud více uživatelských účtů má stejný atribut *mailNickname,* je automaticky vygenerován *název SAMAccountName.* Pokud je *předpona e-mailu uživatelePřezdívka* nebo *UPN* delší než 20 znaků, je *název SAMAccountName* automaticky vygenerován tak, aby splňoval limit 20 znaků atributů *SAMAccountName.* |
| Hesla | Heslo uživatele z klienta Azure AD | Starší hodnotu hashe hesla vyžadovaná pro ověřování NTLM nebo Kerberos jsou synchronizovány z klienta Azure AD. Pokud je klient Azure AD nakonfigurovaný pro hybridní synchronizaci pomocí služby Azure AD Connect, tyto hodnoty hashe hesla jsou získávány z místního prostředí služby AD DS. |
| Sid primárního uživatele/skupiny | Automaticky generované | Primární SID pro účty uživatelů/skupin se automaticky generuje ve službě Azure AD DS. Tento atribut neodpovídá primárnímu identifikátoru SID uživatele nebo skupiny objektu v místním prostředí služby AD DS. Tento nesoulad je, protože spravovaná doména Azure AD DS má jiný obor názvů SID než místní doména služby AD DS. |
| Historie SID pro uživatele a skupiny | Místní primární uživatel a sid skupiny | Atribut *SidHistory* pro uživatele a skupiny ve službě Azure AD DS je nastaven tak, aby odpovídal odpovídajícímu primárnímu identifikátoru SID uživatele nebo skupiny v místním prostředí služby AD DS. Tato funkce pomáhá usnadnit zvedací a přesun místních aplikací do služby Azure AD DS, protože není nutné znovu prostředky acl. |

> [!TIP]
> **Přihlášení ke spravované doméně pomocí formátu UPN** Atribut *SAMAccountName,* například `AADDSCONTOSO\driley`, může být automaticky generován pro některé uživatelské účty ve spravované doméně Azure AD DS. Automaticky generovaný *název SAMAccountName* uživatelů se může lišit od předpony UPN, takže není vždy spolehlivý způsob přihlášení.
>
> Například pokud více uživatelů má stejný *mailNickname* atribut nebo uživatelé mají příliš dlouhé UPN předpony, *SAMAccountName* pro tyto uživatele může být automaticky generovány. Pomocí formátu HLAVNÍHO názvu `driley@aaddscontoso.com`domény, jako je například , spolehlivé přihlášení ke spravované doméně Azure AD DS.

### <a name="attribute-mapping-for-user-accounts"></a>Mapování atributů pro uživatelské účty

Následující tabulka ukazuje, jak se synchronizují konkrétní atributy pro uživatelské objekty ve službě Azure AD s odpovídajícími atributy ve službě Azure AD DS.

| Atribut uživatele ve službě Azure AD | Atribut uživatele ve službě Azure AD DS |
|:--- |:--- |
| účetPovolený |userAccountControl (nastaví nebo vymaže ACCOUNT_DISABLED bit) |
| city |l |
| country |Co |
| Oddělení |Oddělení |
| displayName |displayName |
| facsimileČíslo telefonu |facsimileČíslo telefonu |
| givenName |givenName |
| název úlohy |title |
| pošta |pošta |
| přezdívka |přezdívka msDS-AzureADMail |
| přezdívka |SAMAccountName (může být někdy automaticky generován) |
| mobil |mobil |
| Objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| zásady hesel |userAccountControl (nastaví nebo vymaže DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postalcode |Postalcode |
| preferredLanguage |preferredLanguage |
| state |st |
| Streetaddress |Streetaddress |
| surname |sn |
| phoneČíslo |phoneČíslo |
| userPrincipalName (Hlavní název uživatele) |userPrincipalName (Hlavní název uživatele) |

### <a name="attribute-mapping-for-groups"></a>Mapování atributů pro skupiny

Následující tabulka ukazuje, jak se synchronizují konkrétní atributy pro objekty skupiny ve službě Azure AD s odpovídajícími atributy ve službě Azure AD DS.

| Atribut skupiny ve službě Azure AD | Atribut skupiny ve službě Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (může být někdy automaticky generován) |
| pošta |pošta |
| přezdívka |přezdívka msDS-AzureADMail |
| Objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronizace z místníslužby AD DS do Azure AD a Azure AD DS

Azure AD Connect se používá k synchronizaci uživatelských účtů, členství ve skupinách a hash přihlašovacích údajů z místního prostředí služby AD DS do služby Azure AD. Jsou synchronizovány atributy uživatelských účtů, jako je hlavní název uživatele a místní identifikátor zabezpečení (SID). Chcete-li se přihlásit pomocí Služby Azure AD DS, starší nastavení hash hesel vyžadovaná pro ověřování NTLM a Kerberos jsou také synchronizovány do Služby Azure AD.

> [!IMPORTANT]
> Azure AD Connect by se měl nainstalovat a nakonfiguroval jenom pro synchronizaci s místními prostředími služby AD DS. Není podporováno instalace Azure AD Connect ve spravované doméně Azure AD DS k synchronizaci objektů zpět do Azure AD.

Pokud nakonfigurujete zpětný zápis, změny z Azure AD jsou synchronizovány zpět do místního prostředí služby AD DS. Například pokud uživatel změní své heslo pomocí samoobslužné správy hesel Azure AD, heslo se aktualizuje zpět v místním prostředí služby AD DS.

> [!NOTE]
> Vždy používejte nejnovější verzi Azure AD Connect, abyste zajistili, že máte opravy pro všechny známé chyby.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizace z místního prostředí s více doménovými strukturami

Mnoho organizací má poměrně složité místní prostředí ad ds, které zahrnuje více doménových struktur. Azure AD Connect podporuje synchronizaci uživatelů, skupin a hodnot hash pověření z prostředí s více doménovými strukturami do Azure AD.

Azure AD má mnohem jednodušší a plochý obor názvů. Chcete-li uživatelům umožnit spolehlivý přístup k aplikacím zabezpečeným službou Azure AD, vyřešte konflikty upn napříč uživatelskými účty v různých doménových strukturách. Spravované domény Azure AD DS používají strukturu ploché ou, podobně jako Azure AD. Všechny uživatelské účty a skupiny jsou uloženy v kontejneru *AADDC Users,* přestože jsou synchronizovány z různých místních domén nebo doménových struktur, i když jste nakonfigurovali hierarchickou strukturu ou místně. Spravovaná doména Azure AD DS sloučí všechny hierarchické struktury ou.

Jak již bylo podrobně popsáno, neexistuje žádná synchronizace z Azure AD DS zpět do Služby Azure AD. Můžete [vytvořit vlastní organizační jednotky (OU)](create-ou.md) v Azure AD DS a potom uživatelé, skupiny nebo účty služeb v rámci těchto vlastních organizačních jednotek. Žádný z objektů vytvořených ve vlastních operačních službách jsou synchronizovány zpět do služby Azure AD. Tyto objekty jsou dostupné jenom v rámci spravované domény Azure AD DS a nejsou viditelné pomocí rutin prostředí Azure AD PowerShell, rozhraní Microsoft Graph API nebo pomocí ustálené oblasti správy Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Co není synchronizované s Azure AD DS

Následující objekty nebo atributy nejsou synchronizovány z místního prostředí služby AD DS do služby Azure AD nebo Azure AD DS:

* **Vyloučené atributy:** Můžete vyloučit určité atributy ze synchronizace na Azure AD z místního prostředí Služby AD DS pomocí Azure AD Connect. Tyto vyloučené atributy pak nejsou k dispozici ve službě Azure AD DS.
* **Zásady skupiny:** Zásady skupiny nakonfigurované v místním prostředí služby AD DS nejsou synchronizovány s Azure AD DS.
* **Sysvol složka:** Obsah složky *Sysvol* v místním prostředí služby AD DS není synchronizován se službou Azure AD DS.
* **Počítačové objekty:** Počítačové objekty pro počítače spojené s místním prostředím služby AD DS nejsou synchronizovány s Azure AD DS. Tyto počítače nemají vztah důvěryhodnosti se spravovanou doménou Azure AD DS a patří jenom do místního prostředí služby AD DS. Ve službě Azure AD DS se zobrazí pouze počítačové objekty pro počítače, které mají explicitně doménové připojení ke spravované doméně.
* **Atributy SidHistory pro uživatele a skupiny:** Primární uživatel a primární skupiny SID z místního prostředí služby AD DS jsou synchronizovány do služby Azure AD DS. Existující atributy *SidHistory* pro uživatele a skupiny se však nesynchronizují z místního prostředí služby AD DS do služby Azure AD DS.
* **Struktury organizačních jednotek (OU):** Organizační jednotky definované v místním prostředí služby AD DS se nesynchronizují s Azure AD DS. Ve službě Azure AD DS jsou dvě předdefinované hlavní sady – jedna pro uživatele a jedna pro počítače. Spravovaná doména Azure AD DS má strukturu ploché ou. Můžete vytvořit [vlastní individuální oužinu ve spravované doméně](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizace hodnot hash hesel a důležité informace o zabezpečení

Když povolíte Azure AD DS, jsou vyžadovány starší nastavení hash hesla pro ověřování NTLM + Kerberos. Azure AD neukládá hesla ve stavu prostého textu, takže tyto hashe nelze automaticky generovat pro existující uživatelské účty. Po vygenerování a uložení jsou hashe kompatibilní s ntlm a kerberos hesla vždy uloženy šifrovaným způsobem ve službě Azure AD.

Šifrovací klíče jsou jedinečné pro každého klienta Azure AD. Tyto hashes jsou šifrovány tak, že pouze Azure AD DS má přístup k dešifrovací klíče. Žádná jiná služba nebo součást ve službě Azure AD nemá přístup k dešifrovacím klíčům.

Starší nastavení hashe hesla se pak synchronizují ze služby Azure AD do řadičů domény pro spravovanou doménu Azure AD DS. Disky pro tyto spravované řadiče domény ve službě Azure AD DS jsou šifrované v klidovém stavu. Tato hash hesel jsou uložena a zabezpečena v těchto řadičích domény podobná způsobu ukládání a zabezpečení hesel v místním prostředí služby AD DS.

Pro prostředí Azure AD pouze pro cloud [uživatelé musí resetovat nebo změnit své heslo,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) aby se požadované zabezpečení hesel vygenerovalo a ukládaly ve službě Azure AD. Pro všechny cloudové uživatelské účet vytvořený ve službě Azure AD po povolení služby Azure AD Domain Services se vygenerují a ukládají ve formátech kompatibilních s protokoly NTLM a Kerberos. Všechny uživatelské účty v cloudu musí změnit své heslo před jejich synchronizací do Služby Azure AD DS.

Pro hybridní uživatelské účty synchronizované z místního prostředí služby AD DS pomocí služby Azure AD Connect je nutné [nakonfigurovat službu Azure AD Connect tak, aby synchronizovala hodnothasy hesel ve formátech kompatibilních s protokoly NTLM a Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Další kroky

Další informace o specifikách synchronizace hesel najdete v tématu [Jak funguje synchronizace hash hesel s Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Pokud chcete začít s Azure AD DS, [vytvořte spravovanou doménu](tutorial-create-instance.md).
