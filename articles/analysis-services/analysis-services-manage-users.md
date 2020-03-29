---
title: Ověřování a uživatelská oprávnění služby Azure Analysis Services| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak Azure Analysis Services používá Azure Active Directory (Azure AD) pro správu identit a ověřování uživatelů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572753"
---
# <a name="authentication-and-user-permissions"></a>Ověřování a uživatelská oprávnění

Azure Analysis Services používá Azure Active Directory (Azure AD) pro správu identit a ověřování uživatelů. Každý uživatel, který vytváří, spravuje nebo připojuje server Služby Azure Analysis Services, musí mít platnou identitu uživatele v [tenantovi Azure AD](../active-directory/fundamentals/active-directory-administer.md) ve stejném předplatném.

Azure Analysis Services podporuje [spolupráci Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). S B2B, uživatelé mimo organizaci mohou být pozváni jako uživatelé typu Host v adresáři Azure AD. Hosté mohou být z jiného adresáře klienta Azure AD nebo z libovolné platné e-mailové adresy. Jakmile je uživatel pozván a uživatel přijme pozvánku odeslanou e-mailem z Azure, identita uživatele se přidá do adresáře klienta. Tyto identity lze přidat do skupin zabezpečení nebo jako členy role správce serveru nebo databáze.

![Architektura ověřování služby Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Ověřování

Všechny klientské aplikace a nástroje používají pro připojení k serveru jednu nebo více [klientských knihoven](analysis-services-data-providers.md) Analysis Services (AMO, MSOLAP, ADOMD). 

Všechny tři klientské knihovny podporují interaktivní tok Azure AD i neinteraktivní metody ověřování. Dvě neinteraktivní metody, služby Active Directory Password a metody integrovaného ověřování služby Active Directory, lze použít v aplikacích využívajících amomd a msolap. Tyto dvě metody nikdy nevedou k automaticky otevíraná dialogová okna.

Klientské aplikace, jako je Excel a Power BI Desktop, a nástroje jako Rozšíření projektů SSMS a Analysis Services pro Visual Studio nainstalují nejnovější verze knihoven při aktualizaci na nejnovější verzi. Rozšíření projektů Power BI Desktop, SSMS a Analysis Services se aktualizuje měsíčně. Excel se [aktualizuje pomocí Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizace Office 365 jsou méně časté a některé organizace používají odložený kanál, což znamená, že aktualizace jsou odloženy až na tři měsíce.

V závislosti na klientské aplikaci nebo nástroji, který používáte, se může typ ověřování a způsob přihlášení lišit. Každá aplikace může podporovat různé funkce pro připojení ke cloudovým službám, jako je Azure Analysis Services.

Power BI Desktop, Visual Studio a SSMS podporují univerzální ověřování služby Active Directory, což je interaktivní metoda, která také podporuje vícefaktorové ověřování Azure (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím a zároveň poskytuje jednoduchý proces přihlášení. Poskytuje silné ověřování s několika možnostmi ověření (telefonní hovor, textová zpráva, čipové karty s pinem nebo oznámení mobilní aplikace). Interaktivní vícefaktorové ověřování s Azure AD může mít za následek rozbalovací dialogové okno pro ověření. **Univerzální ověřování se doporučuje**.

Pokud se k Azure přihlašujete pomocí účtu Windows a univerzální ověřování není vybrané nebo dostupné (Excel), je vyžadována [služba AD FS (AD FS).](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) Díky federaci se uživatelé Azure AD a Office 365 ověřují pomocí místních přihlašovacích údajů a mají přístup k prostředkům Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Servery Služby Azure Analysis Services podporují připojení ze [služby SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a vyšší pomocí ověřování systému Windows, ověřování hesla služby Active Directory a univerzálního ověřování služby Active Directory. Obecně se doporučuje používat univerzální ověřování služby Active Directory, protože:

*  Podporuje interaktivní a neinteraktivní metody ověřování.

*  Podporuje uživatele typu Host Azure B2B pozvané do tenanta Azure AS. Při připojování k serveru musí uživatelé typu Host při připojování k serveru vybrat univerzální ověřování služby Active Directory.

*  Podporuje vícefaktorové ověřování (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím s řadou možností ověření: telefonní hovor, textové zprávy, čipové karty s pinem nebo oznámení mobilní aplikace. Interaktivní vícefaktorové ověřování s Azure AD může mít za následek rozbalovací dialogové okno pro ověření.

### <a name="visual-studio"></a>Visual Studio

Visual Studio se připojuje ke službě Azure Analysis Services pomocí univerzálního ověřování služby Active Directory s podporou vícefaktorové ověřování. Uživatelé se při prvním nasazení přihlásí k Azure. Uživatelé se musí přihlásit k Azure pomocí účtu s oprávněními správce serveru na serveru, na který nasazují. Při prvním přihlášení do Azure se přiřazuje token. Token je uložen do mezipaměti v paměti pro budoucí opětovné připojení.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop se připojuje ke službě Azure Analysis Services pomocí univerzálního ověřování služby Active Directory s podporou vícefaktorové ověřování. Uživatelé se při prvním připojení přihlašují k přihlášení do Azure. Uživatelé se musí přihlásit k Azure pomocí účtu, který je součástí role správce serveru nebo databáze.

### <a name="excel"></a>Excel

Uživatelé aplikace Excel se mohou k serveru připojit pomocí účtu systému Windows, ID organizace (e-mailová adresa) nebo externí e-mailové adresy. Externí e-mailové identity musí existovat ve službě Azure AD jako uživatel typu Host.

## <a name="user-permissions"></a>Uživatelská oprávnění

**Správci serveru** jsou specifické pro instanci serveru Azure Analysis Services. Propojují se s nástroji, jako je Azure Portal, SSMS a Visual Studio, a provádějí úlohy, jako je přidávání databází a správa uživatelských rolí. Ve výchozím nastavení je uživatel, který vytvoří server, automaticky přidán jako správce serveru služby Analysis Services. Další správci můžete přidat pomocí portálu Azure nebo SSMS. Správci serveru musí mít účet v tenantovi Azure AD ve stejném předplatném. Další informace naleznete v [tématu Správa správců serveru](analysis-services-server-admins.md). 

**Uživatelé databází** se připojují k databázím modelů pomocí klientských aplikací, jako je Excel nebo Power BI. Uživatelé musí být přidáni do databázových rolí. Role databáze definují oprávnění správce, procesu nebo čtení databáze. Je důležité pochopit, že uživatelé databáze v roli s oprávněními správce se liší od správců serveru. Ve výchozím nastavení jsou však správci serveru také správci databáze. Další informace najdete v [tématu Správa databázových rolí a uživatelů](analysis-services-database-users.md).

**Vlastníci prostředků Azure**. Vlastníci prostředků spravují prostředky pro předplatné Azure. Vlastníci prostředků můžou přidávat identity uživatelů Azure AD do rolí vlastníka nebo přispěvatele v rámci předplatného pomocí **řízení přístupu** na webu Azure Portal nebo pomocí šablon Azure Resource Manager. 

![Řízení přístupu na webu Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na této úrovni platí pro uživatele nebo účty, které je potřeba provádět úkoly, které lze dokončit na portálu nebo pomocí šablon Azure Resource Manager. Další informace naleznete [v tématu Řízení přístupu na základě rolí](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Databázové role

 Role definované pro tabulkový model jsou databázové role. To znamená, že role obsahují členy skládající se z uživatelů Azure AD a skupin zabezpečení, které mají specifická oprávnění, která definují akci, kterou mohou tito členové provést v databázi modelu. Databázová role se vytvoří jako samostatný objekt v databázi a použije se pouze na databázi, ve které byla vytvořena.   
  
 Ve výchozím nastavení při vytváření nového projektu tabulkového modelu projekt modelu nemá žádné role. Role lze definovat pomocí dialogového okna Správce rolí v sadě Visual Studio. Pokud jsou role definovány během návrhu projektu modelu, jsou použity pouze pro databázi pracovního prostoru modelu. Při nasazení modelu se používají stejné role pro nasazený model. Po nasazení modelu mohou správci serveru a databáze spravovat role a členy pomocí služby SSMS. Další informace najdete v [tématu Správa databázových rolí a uživatelů](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Další kroky

[Správa přístupu k prostředkům pomocí skupin Služby Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Správa databázových rolí a uživatelů](analysis-services-database-users.md)  
[Správa správců serveru](analysis-services-server-admins.md)  
[Řízení přístupu na základě rolí](../role-based-access-control/overview.md)  