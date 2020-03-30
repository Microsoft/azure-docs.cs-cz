---
title: Nejčastější dotazy ke zřizování cloudu Azure AD Connect
description: Tento dokument popisuje nejčastější dotazy pro zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916570"
---
# <a name="azure-active-directory-connect-faq"></a>Nejčastější dotazy k připojení služby Azure Active Directory

Přečtěte si o nejčastějších dotazech pro Azure Active Directory (Azure AD) Connect zřizování cloudu.

## <a name="general-installation"></a>Obecná instalace

**Otázka: Jak často běží zřizování cloudu?**

Zřizování cloudu je naplánováno na každé 2 min. Každé 2 min, všechny změny hash uživatele, skupiny a hesla se zřídí do Azure AD.

**Otázka: Zobrazení selhání synchronizace hash hesla při prvním spuštění. Proč?**

To se očekává. Chyby jsou způsobeny objektuživatele není k dispozici ve službě Azure AD. Jakmile se uživatel zřídí do Azure AD, by měly zřídit hashe hesla v následném spuštění. Počkejte na několik spuštění a potvrďte, že synchronizace hash hesla již nemá chyby.

**Otázka: Co se stane, pokud instance služby Active Directory obsahuje atributy, které nejsou podporovány cloudovou provisonicí (například rozšíření adresářů)?**

Zřizování cloudu se spustí a zřídí podporované atributy. Nepodporované atributy se nezřídí do služby Azure AD. Zkontrolujte rozšíření adresáře ve službě Active Directory a ujistěte se, že nepotřebujete tyto atributy toku do Azure AD. Pokud jsou požadovány jeden nebo více atributů, zvažte použití synchronizace Azure AD Connect nebo přesunutí požadovaných informací do jednoho z podporovaných atributů (například atributy rozšíření 1-15).

**Otázka: Jaký je rozdíl mezi synchronizací Azure AD Connect a zřizováním cloudu?**

Díky synchronizaci Azure AD Connect se zřizování spouští na místním synchronizačním serveru. Konfigurace je uložena na místním synchronizačním serveru. Díky zřizování cloudu Azure AD Connect se konfigurace zřizování uložených v cloudu a běží v cloudu jako součást zřizovací služby Azure AD. 

**Otázka: Můžu použít zřizování cloudu k synchronizaci z více doménových struktur služby Active Directory?**

Ano. Zřizování cloudu lze použít k synchronizaci z více doménových struktur služby Active Directory. V prostředí s více doménovými strukturami musí být všechny odkazy (například správce) v doméně.  

**Otázka: Jak je agent aktualizován?**

Agenti jsou automaticky inovován společností Microsoft. Pro it tým to snižuje zátěž nutnosti testovat a ověřovat nové verze agenta. 

**Otázka: Mohu zakázat automatický upgrade?**

Neexistuje žádný podporovaný způsob, jak zakázat automatický upgrade.

**Otázka: Můžu změnit zdrojovou kotvu pro zřizování cloudu?**

Ve výchozím nastavení cloud zřizování používá ms-ds-consistency-GUID s záložní objekt UBodA jako zdrojové kotvy. Neexistuje žádný podporovaný způsob, jak změnit zdrojovou kotvu.

**Otázka: Při použití zřizování cloudu se zobrazují nové objekty zabezpečení služeb s názvy domén služby AD. Očekává se to?**

Ano, zřizování cloudu vytvoří instanční objekt pro konfiguraci zřizování s názvem domény jako hlavním názvem služby. Neprováděte žádné změny konfigurace instančního objektu.

**Otázka: Co se stane, když je synchronizovaný uživatel povinen změnit heslo při příštím přihlášení?**

Pokud je synchronizace hash hesla povolena při zřizování cloudu a synchronizovaný uživatel je povinen změnit heslo při příštím přihlášení v místním ad, zřizování cloudu nezřídí, aby se změnilo hash hesla do Azure AD. Jakmile uživatel změní heslo, hash uživatelského hesla se zřídí ze služby AD do služby Azure AD.

**Otázka: Podporuje zřizování cloudu zpětný zápis ms-ds-consistencyGUID pro libovolný objekt?**

Ne, zřizování cloudu nepodporuje zpětný zápis ms-ds-consistencyGUID pro libovolný objekt (včetně objektů uživatelů). 

**Otázka: Zřizuji uživatele pomocí zřizování cloudu. Vymazal jsem konfiguraci. Proč se stále zobrazují staré synchronizované objekty ve službě Azure AD?** 

Když odstraníte konfiguraci, zřizování cloudu nečistí synchronizované objekty ve službě Azure AD. Chcete-li zajistit, že nemáte staré objekty, změňte rozsah konfigurace na prázdnou skupinu nebo organizační jednotky. Jakmile zřizování spustí a vyčistí objekty, zakázat a odstranit konfiguraci. 

**Otázka: Co to znamená, že exchange hybrid není podporován?**

Funkce Hybridní nasazení systému Exchange umožňuje souběžnou existenci poštovních schránek serveru Exchange jak místně, tak v Office 365. Azure AD Connect synchronizuje konkrétní sadu atributů z Azure AD zpátky do místního adresáře.  Agent cloudu zřizování aktuálně nesynchronizuje tyto atributy zpět do místního adresáře a proto není podporován jako náhrada za Azure AD Connect.

**Otázka: Můžu nainstalovat agenta cloudu pro zřizování do windows servercore?**

Ne, instalace agenta na jádro serveru není podporována.

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
