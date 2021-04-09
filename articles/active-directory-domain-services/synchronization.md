---
title: Jak funguje synchronizace v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak proces synchronizace funguje pro objekty a přihlašovací údaje z tenanta Azure AD nebo místního prostředí Active Directory Domain Services do Azure Active Directory Domain Services spravované domény.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: justinha
ms.openlocfilehash: 779fd9bbfdd35061867fecc8d4be62b8768f43f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933300"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Způsob synchronizace objektů a přihlašovacích údajů ve spravované doméně Azure Active Directory Domain Services

Objekty a přihlašovací údaje ve spravované doméně Azure Active Directory Domain Services (Azure služba AD DS) se dají místně vytvořit v doméně nebo synchronizovat z klienta služby Azure Active Directory (Azure AD). Při prvním nasazení služby Azure služba AD DS se konfiguruje Automatická Jednosměrná synchronizace, která se spustí pro replikaci objektů z Azure AD. Tato Jednosměrná synchronizace dál běží na pozadí, aby se zajistila aktuálnost spravované domény Azure služba AD DS se všemi změnami ze služby Azure AD. Z Azure služba AD DS zpět do Azure AD nedochází k žádné synchronizaci.

V hybridním prostředí se můžou objekty a přihlašovací údaje z místní služba AD DS domény synchronizovat s Azure AD pomocí Azure AD Connect. Jakmile jsou tyto objekty úspěšně synchronizovány do Azure AD, automatické synchronizace na pozadí pak tyto objekty a přihlašovací údaje zpřístupní aplikacím, které používají spravovanou doménu.

Pokud je Prem služba AD DS a služba Azure AD je nakonfigurovaná pro federované ověřování pomocí služby AD FS, není v Azure DS dostupná žádná (aktuální/platná) hodnota hash hesla. Uživatelské účty Azure AD vytvořené před implementací dodaného ověřováním můžou mít starou hodnotu hash hesla, ale pravděpodobně neodpovídají hodnotě hash hesla on-Prem. Proto Azure služba AD DS nebude moci ověřit přihlašovací údaje uživatelů.

Následující diagram znázorňuje, jak synchronizace funguje mezi Azure služba AD DS, Azure AD a volitelným místním prostředím služba AD DS:

![Přehled synchronizace pro Azure AD Domain Services spravovanou doménu](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronizace z Azure AD do Azure služba AD DS

Uživatelské účty, členství ve skupinách a hodnoty hash přihlašovacích údajů se synchronizují jedním ze způsobů z Azure AD až do Azure služba AD DS. Tento proces synchronizace je automatický. Tento proces synchronizace nemusíte konfigurovat, monitorovat ani spravovat. Počáteční synchronizace může trvat několik hodin, a to v závislosti na počtu objektů v adresáři služby Azure AD. Po dokončení počáteční synchronizace se změny provedené ve službě Azure AD, jako je třeba heslo nebo atributy, automaticky synchronizují do Azure služba AD DS.

Když se ve službě Azure AD vytvoří uživatel, nebude se synchronizovat do Azure služba AD DS, dokud nezmění heslo ve službě Azure AD. Tento proces změny hesla způsobí, že se ve službě Azure AD vygenerují a ukládají hodnoty hash hesel pro ověřování pomocí protokolu Kerberos a NTLM. Hodnoty hash hesla jsou potřeba k úspěšnému ověření uživatele v Azure služba AD DS.

Proces synchronizace je jedním ze způsobů a jednosměrný podle návrhu. Z Azure služba AD DS zpátky do Azure AD se nevrátí žádná zpětná synchronizace změn. Spravovaná doména je převážně určena jen pro čtení, s výjimkou vlastních organizačních jednotek, které můžete vytvořit. V rámci spravované domény nemůžete měnit atributy uživatele, hesla uživatelů ani členství ve skupinách.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronizace atributů a mapování na Azure služba AD DS

Následující tabulka uvádí některé běžné atributy a způsob jejich synchronizace s služba AD DS Azure.

| Atribut v Azure služba AD DS | Zdroj | Poznámky |
|:--- |:--- |:--- |
| UPN | Atribut *hlavního názvu uživatele (UPN)* v TENANTOVI Azure AD | Atribut UPN z tenanta Azure AD se synchronizuje se službou Azure služba AD DS. Nejspolehlivějším způsobem, jak se přihlásit ke spravované doméně, je používání hlavního názvu uživatele (UPN). |
| SAMAccountName | Uživatelský atribut *mailNickname* v TENANTOVI Azure AD nebo se automaticky vygeneroval. | Atribut *sAMAccountName* je od atributu *MailNickname* v tenantovi Azure AD zdrojový. Pokud má více uživatelských účtů stejný atribut *mailNickname* , pak se účet *sAMAccountName* automaticky vygeneruje. Pokud je předpona *mailNickname* nebo *hlavního názvu uživatele (UPN)* delší než 20 znaků, hodnota *sAMAccountName* se automaticky vygeneruje tak, aby splňovala omezení 20 znaků u atributů *sAMAccountName* . |
| Hesla | Heslo uživatele z tenanta Azure AD | Starší hodnoty hash hesel vyžadované pro ověřování protokolem NTLM nebo Kerberos se synchronizují z tenanta Azure AD. Pokud je tenant Azure AD nakonfigurovaný pro hybridní synchronizaci pomocí Azure AD Connect, hodnoty hash hesel se naúčtují z místního prostředí služba AD DS. |
| Identifikátor SID primárního uživatele/skupiny | Automaticky generované | Primární identifikátor SID pro účty uživatele nebo skupiny se automaticky vygeneruje v Azure služba AD DS. Tento atribut se neshoduje s identifikátorem SID primárního uživatele nebo skupiny objektu v místním služba AD DSovém prostředí. Tato neshoda je způsobená tím, že spravovaná doména má jiný obor názvů SID než místní doména služba AD DS. |
| Historie identifikátorů SID pro uživatele a skupiny | Identifikátor SID místního primárního uživatele a skupiny | Atribut *SIDHistory* pro uživatele a skupiny v Azure služba AD DS je nastavený tak, aby odpovídal PŘÍSLUŠNÉmu identifikátoru SID primárního uživatele nebo skupiny v místním prostředí služba AD DS. Tato funkce pomáhá zajistit přezvednutí místních aplikací do Azure služba AD DS, protože nemusíte znovu vytvářet prostředky ACL. |

> [!TIP]
> **Přihlaste se ke spravované doméně pomocí formátu UPN** .  `AADDSCONTOSO\driley` U některých uživatelských účtů ve spravované doméně se může automaticky generovat atribut sAMAccountName, například. Automaticky generovaná hodnota *sAMAccountName* uživatele se může lišit od PŘEDPONY názvu UPN, takže není vždy spolehlivým způsobem, jak se přihlásit.
>
> Například pokud má více uživatelů stejný atribut *mailNickname* nebo mají více uživatelů delší předpony hlavního názvu uživatele (UPN), mohou být pro tyto uživatele automaticky vygenerovány hodnoty *sAMAccountName* pro tyto uživatele. K `driley@aaddscontoso.com` spolehlivému přihlášení ke spravované doméně použijte formát UPN, například.

### <a name="attribute-mapping-for-user-accounts"></a>Mapování atributů uživatelských účtů

Následující tabulka ukazuje, jak se konkrétní atributy pro uživatelské objekty ve službě Azure AD synchronizují s odpovídajícími atributy v Azure služba AD DS.

| Atribut uživatele v Azure AD | Atribut uživatele v Azure služba AD DS |
|:--- |:--- |
| accountEnabled |stav skupiny (nastavení nebo vymazání bitu ACCOUNT_DISABLED) |
| city |l |
| company |Společnosti |
| country |co |
| Oddělení |Oddělení |
| displayName |displayName |
| Zaměstnance |Zaměstnance |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| pošta |pošta |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (někdy může být generováno automaticky) |
| manager |manager |
| mobil |mobil |
| objektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Čísel |
| passwordPolicies |stav skupiny (nastavení nebo vymazání bitu DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName (Hlavní název uživatele) |userPrincipalName (Hlavní název uživatele) |

### <a name="attribute-mapping-for-groups"></a>Mapování atributů pro skupiny

Následující tabulka ukazuje, jak se konkrétní atributy pro objekty skupin v Azure AD synchronizují s odpovídajícími atributy v Azure služba AD DS.

| Atribut Group v Azure AD | Atribut Group v Azure služba AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (někdy může být generováno automaticky) |
| pošta |pošta |
| mailNickname |msDS-AzureADMailNickname |
| objektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Čísel |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronizace z místního služba AD DS do Azure AD a Azure služba AD DS

Azure AD Connect slouží k synchronizaci uživatelských účtů, členství ve skupinách a hodnot hash přihlašovacích údajů z místního prostředí služba AD DS do Azure AD. Jsou synchronizovány atributy uživatelských účtů, například hlavní název uživatele (UPN) a místní identifikátor zabezpečení (SID). Pokud se chcete přihlásit pomocí služba AD DS Azure, synchronizují se starší hodnoty hash hesel vyžadované pro ověřování protokolem NTLM a Kerberos taky do Azure AD.

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Instalace Azure AD Connect ve spravované doméně není podporovaná pro synchronizaci objektů zpět do Azure AD.

Pokud nakonfigurujete zpětný zápis, změny z Azure AD se synchronizují zpátky do místního prostředí služba AD DS. Pokud uživatel například změní heslo pomocí samoobslužné správy hesel služby Azure AD, heslo se aktualizuje zpátky v místním prostředí služba AD DS.

> [!NOTE]
> Vždy používejte nejnovější verzi Azure AD Connect, abyste měli jistotu, že máte opravy pro všechny známé chyby.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizace z místního prostředí s více doménovými strukturami

Mnoho organizací má poměrně komplexní místní služba AD DS prostředí, které zahrnuje několik doménových struktur. Azure AD Connect podporuje synchronizaci uživatelů, skupin a hodnot hash přihlašovacích údajů z prostředí s více doménovými strukturami do Azure AD.

Azure AD má mnohem jednodušší a plochý obor názvů. Aby uživatelé mohli spolehlivě přistupovat k aplikacím zabezpečeným službou Azure AD, vyřešte konflikty hlavního názvu uživatele v různých uživatelských účtech v různých doménových strukturách. Spravované domény používají strukturu ploché organizační jednotky, která je podobná službě Azure AD. Všechny uživatelské účty a skupiny se ukládají v kontejneru *AADDC Users* , i když se synchronizují z různých místních domén nebo doménových struktur, i když jste místně nakonfigurovali hierarchickou strukturu organizačních jednotek. Spravovaná doména sloučí všechny hierarchické struktury organizační jednotky.

Jak už bylo popsáno dříve, neexistuje žádná synchronizace z Azure služba AD DS zpět do Azure AD. V Azure můžete [vytvořit vlastní organizační jednotku (OU)](create-ou.md) služba AD DS a pak uživatelé, skupiny nebo účty služeb v těchto vlastních organizačních jednotkách. Žádný z objektů vytvořených ve vlastních organizačních jednotkách se nesynchronizuje zpátky do Azure AD. Tyto objekty jsou k dispozici pouze v rámci spravované domény a nejsou viditelné pomocí rutin služby Azure AD PowerShell, Microsoft Graph rozhraní API nebo pomocí uživatelského rozhraní pro správu služby Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Co se nesynchronizuje do Azure služba AD DS

Následující objekty nebo atributy nejsou synchronizované z místního prostředí služba AD DS do služby Azure AD nebo Azure služba AD DS:

* **Vyloučené atributy:** Můžete vyloučit určité atributy z synchronizace do Azure AD z místního prostředí služba AD DS pomocí Azure AD Connect. Tyto vyloučené atributy nejsou pak dostupné v Azure služba AD DS.
* **Zásady skupiny:** Zásady skupiny nakonfigurované v místních služba AD DS prostředí se nesynchronizují do Azure služba AD DS.
* **Složka SYSVOL:** Obsah složky *SYSVOL* v místním služba AD DSovém prostředí se nesynchronizuje do Azure služba AD DS.
* **Objekty počítače:** Objekty počítačů pro počítače připojené k místnímu služba AD DS prostředí nejsou synchronizované s Azure služba AD DS. Tyto počítače nemají vztah důvěryhodnosti se spravovanou doménou a patří do místního služba AD DSho prostředí. V Azure služba AD DS se zobrazují jenom objekty počítačů pro počítače, které mají explicitně připojené domény ke spravované doméně.
* **Atributy SIDHistory pro uživatele a skupiny:** Identifikátory SID primárních uživatelů a primární skupiny z místního prostředí služba AD DS se synchronizují do Azure služba AD DS. Stávající atributy *SIDHistory* pro uživatele a skupiny se ale nesynchronizují z místního prostředí služba AD DS do Azure služba AD DS.
* **Struktury organizačních jednotek (OU):** Organizační jednotky definované v místních služba AD DS prostředí se nesynchronizují se službou Azure služba AD DS. V Azure jsou k dispozici dvě integrované organizační jednotky, služba AD DS – jeden pro uživatele a jeden pro počítače. Spravovaná doména má strukturu ploché organizační jednotky. [Ve spravované doméně se můžete rozhodnout vytvořit vlastní organizační jednotku](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizace hodnoty hash hesel a posouzení zabezpečení

Když povolíte Azure služba AD DS, vyžaduje se starší hodnoty hash hesla pro ověřování NTLM a Kerberos. Služba Azure AD neukládá hesla k nešifrovaným textům, takže tyto hodnoty hash není možné automaticky vygenerovat pro existující uživatelské účty. Po vygenerování a uložení se hodnoty hash hesla kompatibilní s protokolem NTLM a Kerberos vždycky ukládají šifrovaným způsobem ve službě Azure AD.

Šifrovací klíče jsou pro každého tenanta Azure AD jedinečné. Tyto hodnoty hash jsou šifrované, takže přístup k dešifrovacím klíčům má jenom Azure služba AD DS. Žádná jiná služba ani součást ve službě Azure AD nemá přístup k dešifrovacím klíčům.

Starší hodnoty hash hesel se pak z Azure AD synchronizují do řadičů domény pro spravovanou doménu. Disky pro tyto spravované řadiče domény v Azure služba AD DS jsou zašifrované v klidovém stavu. Tyto hodnoty hash hesla se ukládají a zabezpečují na těchto řadičích domény podobně jako při ukládání a zabezpečení hesel v místním služba AD DS prostředí.

Pro cloudová prostředí Azure AD [musí uživatelé resetovat nebo změnit heslo](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby se vygenerovaly a uložily hodnoty hash hesel v Azure AD. Pro všechny cloudové uživatelské účty vytvořené v Azure AD po povolení Azure AD Domain Services se generují hodnoty hash hesel a ukládají se do formátů kompatibilních s protokolem NTLM a Kerberos. Všechny uživatelské účty v cloudu musí změnit heslo, než se synchronizují do Azure služba AD DS.

U hybridních uživatelských účtů synchronizovaných z místního prostředí služba AD DS pomocí Azure AD Connect musíte [nakonfigurovat Azure AD Connect pro synchronizaci hodnot hash hesel ve formátech kompatibilních s protokoly NTLM a Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Další kroky

Další informace o konkrétních informacích o synchronizaci hesel najdete v tématu [Jak funguje synchronizace hodnot hash hesel s Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Pokud chcete začít s Azure služba AD DS, [vytvořte spravovanou doménu](tutorial-create-instance.md).
