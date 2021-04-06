---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900836"
---
V tomto článku jsou uvedené verze a funkce Azure Active Directory Connect zřizovacího agenta, který byl vydán. Tým Azure AD pravidelně aktualizuje agenta zřizování o nové funkce a funkce. Agent zřizování se aktualizuje automaticky, když se uvolní nová verze. 

Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi.

## <a name="113540"></a>1.1.354.0

20. ledna 2021: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
- Zlepšení prostředí GMSA, včetně podpory pro předem vytvořený účet GMSA
- Podpora [rutin PowerShellu](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) pro instalaci gMSA
- [Podpora](../articles/active-directory/cloud-sync/how-to-install-pshell.md) rozhraní příkazového řádku pro instalaci agenta (bezobslužná instalace)
- Další Diagnostika pro problémy karantény zdroje agenta
- Opravy chyb, které zahrnují snížení využití paměti filtry oboru organizačních jednotek, spuštění KOSMETICE pouze pro uživatele v oboru, při použití oboru organizační jednotky a zpracování vnořených objektů v organizační jednotce. 


### <a name="fixed-issues"></a>Opravené problémy
-    Zabránit karanténě, pokud je skupina oborů mimo rozsah
-   Když jsou nakonfigurované filtry oborů, úloha KOSMETICE se teď funguje jenom pro uživatele v oboru.
-   Agent by během upgradu mohl zablokovat nějakou dobu.
-   Počáteční synchronizace objektů ve vnořených jednotkách při použití oboru organizační jednotky
-   Zvýšit odolnost Repair-AADCloudSyncToolsAccount
-   Zmenšení využití velkých paměťových filtrů oboru organizačních jednotek
-   Pokud členové role obsahují skupinu zabezpečení, kontroler role správce se nezdařila.
-   Opravte problém s oprávněním ke složce GMSA, která znemožňuje obnovení certifikátu agenta.







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stav verze

23. listopadu 2020: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Podpora pro [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Podpora pro skupiny o velikosti menší než 1500 členů během přírůstkového nebo rozdílového synchronizačního cyklu. Tato nastavení platí při použití filtru oboru skupiny.
* Podpora velkých skupin s velikostí členů až 15 000
* Vylepšení počáteční synchronizace
* Rozšířené podrobné protokolování
* Úvod do [modulu PowerShellu pro AADCloudSyncTools](../articles/active-directory/cloud-sync/reference-powershell.md)
* Pevná omezení, která umožňují instalaci agenta na jiný než anglický Server
* Podpora pro filtrování KOSMETICE jenom pro objekty v oboru (původně se jednalo o synchronizaci hodnot hash hesel pro všechny objekty)
* Opravili jsme problém s nevracením paměti v agentovi.
* Vylepšené protokoly zřizování
* Podpora konfigurace [časového limitu připojení LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Podpora konfigurace [dohledávání odkazů](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stav verze

4. prosince 2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Zahrnuje podporu [Azure AD Connect synchronizace cloudu](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) k synchronizaci dat uživatelů, kontaktů a skupin z místní služby Active Directory do Azure AD.


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stav verze

9. září 2019: vydáno pro automatickou aktualizaci

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Možnost konfigurovat další trasování a protokolování pro problémy s agentem zřizování ladění
* Možnost načíst jenom ty atributy služby Azure AD, které jsou nakonfigurované v mapování, aby se zlepšil výkon synchronizace

### <a name="fixed-issues"></a>Opravené problémy

* Opravili jsme chybu, kterou agent přešel do nereagující na stav, pokud došlo k problémům s chybami připojení Azure AD.
* Opravili jsme chybu, která způsobila problémy při čtení binárních dat z Azure Active Directory
* Opravili jsme chybu, která agentovi selhala při obnovení vztahu důvěryhodnosti se službou Cloud Hybrid identity.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stav verze

23. ledna 2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přepracované zřizovacího agenta a architektury konektoru pro zajištění lepšího výkonu, stability a spolehlivosti 
* Zjednodušená konfigurace agenta zřizování pomocí Průvodce instalací na základě uživatelského rozhraní 
* Přidání podpory pro automatické aktualizace agentů