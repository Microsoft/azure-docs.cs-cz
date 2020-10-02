---
title: Nejčastější dotazy ke zřizování cloudu Azure AD Connect
description: V tomto dokumentu jsou popsány nejčastější dotazy k zřizování cloudu.
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
ms.openlocfilehash: 28fb9da04c9c9d3e98b5226e1aee4cf5dde7183b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628668"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Nejčastější dotazy k zřizování cloudu Azure Active Directory Connect

Přečtěte si o nejčastějších dotazech ke službě Azure Active Directory (Azure AD) Connect Provisioning pro Cloud.

## <a name="general-installation"></a>Obecná instalace

**Otázka: jak často se zřizování cloudu spouští?**

Zřizování cloudu se plánuje spouštět každé 2 minuty. Každé 2 minuty se všechny změny hodnot hash uživatelů, skupin a hesel zřídí do Azure AD.

**Otázka: zobrazení chyb synchronizace hodnot hash hesel při prvním spuštění. Proč?**

To se očekává. Příčinou chyb je objekt uživatele, který není ve službě Azure AD přítomen. Po zřízení uživatele v Azure AD by se měly hodnoty hash hesel zřídit v následném spuštění. Počkejte na několik spuštění a potvrďte, že synchronizace hodnot hash hesel už neobsahuje chyby.

**Otázka: co se stane, pokud instance služby Active Directory obsahuje atributy, které nejsou podporovány zřizováním cloudu (např. rozšíření adresáře)?**

Zřizování cloudu se spustí a zřídí podporované atributy. Nepodporované atributy se nezřídí do Azure AD. Zkontrolujte přípony adresářů ve službě Active Directory a ujistěte se, že tyto atributy nepotřebujete k toku do služby Azure AD. Pokud je vyžadován jeden nebo více atributů, zvažte použití Azure AD Connect synchronizaci nebo přesunutí požadovaných informací do jednoho z podporovaných atributů (například atributy rozšíření 1-15).

**Otázka: Jaký je rozdíl mezi Azure AD Connect synchronizace a zřizování cloudu?**

Při Azure AD Connect synchronizaci se zřizování spouští na místním serveru synchronizace. Konfigurace je uložená na místním serveru synchronizace. Při zřizování Azure AD Connect cloudu se konfigurace zřizování ukládá v cloudu a v rámci služby Azure AD Provisioning je spuštěná v cloudu. 

**Otázka: můžu použít zřizování cloudu pro synchronizaci z více doménových struktur služby Active Directory?**

Ano. Zřizování cloudu se dá použít k synchronizaci z několika doménových struktur služby Active Directory. V prostředí s více doménovými strukturami musí být všechny odkazy (například správce) v doméně.  

**Otázka: jak se Agent aktualizuje?**

Agenti jsou automaticky upgradováni společností Microsoft. Pro IT tým to snižuje zatížení, které musí testovat a ověřovat nové verze agentů. 

**Otázka: mohu zakázat automatický upgrade?**

Neexistuje žádný podporovaný způsob, jak automatický upgrade zakázat.

**Otázka: mohu změnit zdrojovou kotvu pro zřizování cloudu?**

Ve výchozím nastavení používá cloudové zřizování službu MS-DS-konzistence-GUID s přechodem k identifikátoru ObjectGUID jako zdrojový kotvu. Neexistuje žádný podporovaný způsob, jak změnit zdrojové ukotvení.

**Otázka: při použití zřizování cloudu se zobrazují nové instanční objekty s názvy domén služby AD. Očekává se?**

Ano, zřizování cloudu vytvoří instanční objekt pro konfiguraci zřizování s názvem domény jako hlavní název služby. Neprovádějte žádné změny v konfiguraci instančního objektu.

**Otázka: co se stane, když se synchronizovaný uživatel při příštím přihlášení vyžaduje, aby změnil heslo?**

Pokud je v zřizování cloudu zapnutá synchronizace hodnot hash hesel a synchronizovaný uživatel musí změnit heslo při příštím přihlášení v místní službě AD, zřizování cloudu nezřizuje hodnotu hash hesla "Změna" pro heslo pro Azure AD. Jakmile uživatel změní heslo, bude hodnota hash hesla uživatele zřízená ze služby AD do služby Azure AD.

**Otázka: podporuje zřízení cloudu zpětný zápis do MS-DS-consistencyGUID pro libovolný objekt?**

Ne, zřizování cloudu nepodporuje zpětný zápis do MS-DS-consistencyGUID pro žádný objekt (včetně uživatelských objektů). 

**Otázka: zřizování uživatelů pomocí zřizování cloudu. Odstranil (a) jsem konfiguraci. Proč se stále zobrazují staré synchronizované objekty ve službě Azure AD?** 

Když konfiguraci odstraníte, zřizování cloudu neodstraní automaticky synchronizované objekty ve službě Azure AD. Aby se zajistilo, že nemáte staré objekty, změňte obor konfigurace na prázdnou skupinu nebo organizační jednotky. Jakmile zřizování spustí a vyčistí objekty, zakažte a odstraňte konfiguraci. 

**Otázka: co znamená, že systém Exchange Hybrid není podporován?**

Funkce hybridního nasazení serveru Exchange umožňuje souběžnou existenci poštovních schránek Exchange místních i v Microsoft 365. Azure AD Connect synchronizuje konkrétní sadu atributů z Azure AD zpátky do místního adresáře.  Agent zřizování cloudu v současné době nesynchronizuje tyto atributy zpátky do místního adresáře, takže není podporován jako náhrada za Azure AD Connect.

**Otázka: mohu nainstalovat agenta zřizování cloudu na Windows serveru Core?**

Ne, instalace agenta na Server Core není podporovaná.

**Otázka: mohu použít pracovní server s agentem zřizování cloudu?**

Ne, pracovní servery se nepodporují.

**Otázka: mohu synchronizovat uživatelské účty typu Host?**

Ne, synchronizace uživatelských účtů typu Host není podporována.

**Otázka: když přesunete uživatele z organizační jednotky s vymezeným zřizováním cloudu na organizační jednotku, která je vymezená pro Azure AD Connect, co se stane?**

Uživatel se odstraní a znovu vytvoří.  Přesunutí uživatele z organizační jednotky, která je vymezená pro zřizování cloudu, se zobrazí jako operace odstranění.  Pokud se uživatel přesune do organizační jednotky, která je spravovaná pomocí Azure AD Connect, bude se znovu zřídit do Azure AD a vytvoří se nový vytvořený uživatel.

**Otázka: Pokud přejmenujete nebo přesunete organizační jednotku, která je v oboru pro filtr zřizování cloudu, co se stane s uživatelem vytvořeným ve službě Azure AD?**

Ničeho.  Pokud je organizační jednotka přejmenována nebo přesunuta, uživatelé nebudou smazáni.

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
