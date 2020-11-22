---
ms.openlocfilehash: 46c27fa2073ea1178fdf01ea3cbf96fe86954a6a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255100"
---
V tomto článku jsou uvedené verze a funkce Azure Active Directory Connect zřizovacího agenta, který byl vydán. Tým Azure AD pravidelně aktualizuje agenta zřizování o nové funkce a funkce. Agent zřizování se aktualizuje automaticky, když se uvolní nová verze. 

Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Stav verze

Úterý XX, 2020: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Podpora pro [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Podpora skupin až do velikosti 1500 členů (během rozdílové synchronizace)
* Podpora velkých skupin s velikostí členů až 15 000
* Vylepšení počáteční synchronizace
* Rozšířené podrobné protokolování
* Úvod do [modulu PowerShellu pro AADCloudSyncTools](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Pevná omezení, která umožňují instalaci agenta na jiný než anglický Server
* Podpora pro filtrování KOSMETICE jenom pro objekty v oboru (původně se jednalo o synchronizaci hodnot hash hesel pro všechny objekty)
* Opravili jsme problém s nevracením paměti v agentovi.
* Vylepšené protokoly zřizování


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stav verze

4. prosince 2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Zahrnuje podporu [Azure AD Connectho zřizování cloudu](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) pro synchronizaci dat uživatelů, kontaktů a skupin z místní služby Active Directory do Azure AD.


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


