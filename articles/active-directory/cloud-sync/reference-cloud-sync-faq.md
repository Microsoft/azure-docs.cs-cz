---
title: Nejčastější dotazy k synchronizaci cloudu Azure AD Connect
description: V tomto dokumentu jsou popsány nejčastější dotazy k synchronizaci cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613237"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Nejčastější dotazy k synchronizaci cloudu Azure Active Directory Connect

Přečtěte si o nejčastějších dotazech ke službě Azure Active Directory (Azure AD) Connect Cloud Sync.

## <a name="general-installation"></a>Obecná instalace

**Otázka: jak často je cloudová synchronizace spuštěna?**

Zřizování cloudu se plánuje spouštět každé 2 minuty. Každé 2 minuty se všechny změny hodnot hash uživatelů, skupin a hesel zřídí do Azure AD.

**Otázka: zobrazení chyb synchronizace hodnot hash hesel při prvním spuštění. Proč?**

To se očekává. Příčinou chyb je objekt uživatele, který není ve službě Azure AD přítomen. Po zřízení uživatele v Azure AD by se měly hodnoty hash hesel zřídit v následném spuštění. Počkejte na několik spuštění a potvrďte, že synchronizace hodnot hash hesel už neobsahuje chyby.

**Otázka: co se stane, pokud instance služby Active Directory obsahuje atributy, které nejsou podporovány cloudovou synchronizací (např. rozšíření adresáře)?**

Zřizování cloudu se spustí a zřídí podporované atributy. Nepodporované atributy se nezřídí do Azure AD. Zkontrolujte přípony adresářů ve službě Active Directory a ujistěte se, že tyto atributy nepotřebujete k toku do služby Azure AD. Pokud je vyžadován jeden nebo více atributů, zvažte použití Azure AD Connect synchronizaci nebo přesunutí požadovaných informací do jednoho z podporovaných atributů (například atributy rozšíření 1-15).

**Otázka: Jaký je rozdíl mezi Azure AD Connect synchronizace a cloudovou synchronizací?**

Při Azure AD Connect synchronizaci se zřizování spouští na místním serveru synchronizace. Konfigurace je uložená na místním serveru synchronizace. Díky Azure AD Connect synchronizaci cloudu se konfigurace zřizování ukládá v cloudu a běží v cloudu jako součást služby Azure AD Provisioning. 

**Otázka: mohu použít synchronizaci cloudu pro synchronizaci z více doménových struktur služby Active Directory?**

Ano. Zřizování cloudu se dá použít k synchronizaci z několika doménových struktur služby Active Directory. V prostředí s více doménovými strukturami musí být všechny odkazy (například správce) v doméně.  

**Otázka: jak se Agent aktualizuje?**

Agenti jsou automaticky upgradováni společností Microsoft. Pro IT tým to snižuje zatížení, které musí testovat a ověřovat nové verze agentů. 

**Otázka: mohu zakázat automatický upgrade?**

Neexistuje žádný podporovaný způsob, jak automatický upgrade zakázat.

**Otázka: mohu změnit zdrojovou kotvu pro cloudovou synchronizaci?**

Ve výchozím nastavení používá synchronizace v cloudu službu MS-DS-konzistence-GUID s Fallback jako zdrojový kotvu. Neexistuje žádný podporovaný způsob, jak změnit zdrojové ukotvení.

**Otázka: při použití synchronizace cloudu se zobrazují nové instanční objekty s názvy domén služby AD. Očekává se?**

Ano, synchronizace v cloudu vytvoří instanční objekt pro konfiguraci zřizování s názvem domény jako hlavní název služby. Neprovádějte žádné změny v konfiguraci instančního objektu.

**Otázka: co se stane, když se synchronizovaný uživatel při příštím přihlášení vyžaduje, aby změnil heslo?**

Pokud je synchronizace hodnot hash hesel povolená v synchronizaci cloudu a synchronizovaný uživatel musí změnit heslo při příštím přihlášení v místní službě AD, cloudová synchronizace nezřídí hodnotu hash hesla "ke změně" na službu Azure AD. Jakmile uživatel změní heslo, bude hodnota hash hesla uživatele zřízená ze služby AD do služby Azure AD.

**Otázka: podporuje synchronizace v cloudu zpětný zápis ms-DS-consistencyGUID pro libovolný objekt?**

Ne, synchronizace v cloudu nepodporuje zpětný zápis ms-DS-consistencyGUID pro žádný objekt (včetně objektů uživatele). 

**Otázka: zřizování uživatelů pomocí cloudové synchronizace. Odstranil (a) jsem konfiguraci. Proč se stále zobrazují staré synchronizované objekty ve službě Azure AD?** 

Při odstranění konfigurace neodstraní cloudová synchronizace automaticky synchronizované objekty ve službě Azure AD. Aby se zajistilo, že nemáte staré objekty, změňte obor konfigurace na prázdnou skupinu nebo organizační jednotky. Jakmile zřizování spustí a vyčistí objekty, zakažte a odstraňte konfiguraci. 

**Otázka: co znamená, že systém Exchange Hybrid není podporován?**

Funkce hybridního nasazení serveru Exchange umožňuje souběžnou existenci poštovních schránek Exchange místních i v Microsoft 365. Azure AD Connect synchronizuje konkrétní sadu atributů z Azure AD zpátky do místního adresáře.  Agent zřizování cloudu v současné době nesynchronizuje tyto atributy zpátky do místního adresáře, takže není podporován jako náhrada za Azure AD Connect.

**Otázka: mohu nainstalovat agenta zřizování cloudu na Windows serveru Core?**

Ne, instalace agenta na Server Core není podporovaná.

**Otázka: mohu použít pracovní server s agentem zřizování cloudu?**

Ne, pracovní servery se nepodporují.

**Otázka: mohu synchronizovat uživatelské účty typu Host?**

Ne, synchronizace uživatelských účtů typu Host není podporována.

**Otázka: když přesunete uživatele z organizační jednotky, která je vymezená pro cloudovou synchronizaci, do organizační jednotky, která je vymezená pro Azure AD Connect, co se stane?**

Uživatel se odstraní a znovu vytvoří.  Přesunutí uživatele z organizační jednotky, která je v oboru pro cloudovou synchronizaci, se zobrazí jako operace odstranění.  Pokud se uživatel přesune do organizační jednotky, která je spravovaná pomocí Azure AD Connect, bude se znovu zřídit do Azure AD a vytvoří se nový vytvořený uživatel.

**Otázka: Pokud přejmenujete nebo přesunete organizační jednotku, která je v oboru pro filtr Cloud Sync, co se stane s uživatelem vytvořeným ve službě Azure AD?**

Ničeho.  Pokud je organizační jednotka přejmenována nebo přesunuta, uživatelé nebudou smazáni.

**Otázka: Azure AD Connect cloudová synchronizace podporuje velké skupiny?**

Ano. Dnes podporujeme až 50 tis členy skupiny synchronizované pomocí filtrování oboru organizačních jednotek. Když ale použijete filtrování oboru skupiny, doporučujeme, abyste zachovali velikost skupiny méně než 1500 členů. Důvodem je, že i když můžete synchronizovat velkou skupinu jako součást filtru oboru skupiny, když přidáte členy do této skupiny podle dávek většího než 1500, synchronizace rozdílů se nezdaří. 

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
