---
title: Ověřování a uživatelská oprávnění ve službě Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o ověřování a uživatelská oprávnění ve službě Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 846b76bddbc771a924a1d710cd333dfa4623f5ec
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449768"
---
# <a name="authentication-and-user-permissions"></a>Ověřování a uživatelská oprávnění
Služba Azure Analysis Services použije Azure Active Directory (Azure AD) pro ověření identity management a uživatele. Každý uživatel vytváření, Správa nebo připojení k Azure Analysis Services server musí mít platné uživatelské identity [tenanta Azure AD](../active-directory/fundamentals/active-directory-administer.md) ve stejném předplatném.

Služba Azure Analysis Services podporuje [spolupráce B2B ve službě Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). S B2B uživatele mimo organizaci pracovního prostoru může pozvat jako hosty do adresáře služby Azure AD. Hosté může být z jiného tenanta adresáře Azure AD nebo libovolnou platnou e-mailovou adresu. Jednou pozvat a uživatel přijme podmínky e-mailové pozvánce odeslat e-mailem z Azure, identitu uživatele se přidá do tenanta adresáře. Tyto identity se dají přidat do skupiny zabezpečení nebo jako členy role serveru správce nebo databáze.

![Architektura ověřování služby Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication
Všechny klientské aplikace a nástroje použít jeden nebo více služby Analysis Services [klientské knihovny](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) pro připojení k serveru. 

Všechny tři klientské knihovny pro podporu interaktivní tok Azure AD a metod jako neinteraktivní ověřování. Dvě metody jako neinteraktivní, Active Directory heslo a metody Integrované ověřování Active Directory můžete použít v aplikacích využívající AMOMD a MSOLAP. Tyto dvě metody nikdy způsobit automaticky otevíraná okna.

Klientské aplikace, jako jsou aplikace Excel a Power BI Desktopu a nástrojů, jako je SSMS a SSDT nainstalujte nejnovější verze knihovny, když se aktualizuje na nejnovější verzi. Power BI Desktopu, aplikace SSMS a SSDT se aktualizuje každý měsíc. Excel je [aktualizován s Office 365](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizace Office 365 jsou méně časté a v některých organizacích pomocí kanálu deferred channel, odkládat aktualizace význam až na tři měsíce.

Podle klientská aplikace nebo nástroj, který používáte může být jiný typ ověřování a jak se přihlásit. Každá aplikace může podporovat různé funkce pro připojení ke cloudovým službám, jako jsou služby Azure Analysis Services.

Power BI Desktopu, SSDT a SSMS podporují univerzálního ověřování Active Directory, je interaktivní metoda také podporuje Azure Multi-Factor Authentication (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím poskytuje jednoduchý proces přihlašování. Nabízí silné ověřování s několika možnostmi ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci). Interaktivní MFA s Azure AD může vést k místní dialogové okno pro ověření. **Univerzální ověřování se doporučuje**.

Pokud se přihlášení do Azure pomocí účtu Windows a univerzální ověřování není vybraná nebo k dispozici (Excel), [Active Directory Federation Services (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) je povinný. S federací Azure AD a ověření pomocí místních přihlašovacích údajů uživatele služeb Office 365 a mít přístup k prostředkům Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Servery služby Azure Analysis Services podporují připojení od [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a vyšší s použitím ověřování Windows, ověřování hesla Active Directory a univerzálního ověřování Active Directory. Obecně se doporučuje, že protože se pomocí univerzálního ověřování Active Directory:

*  Podporuje metody interaktivní i neinteraktivní ověřování.

*  Podporuje pozvat do tenanta Azure jako uživatelé typu Host B2B ve službě Azure. Při připojování k serveru, uživatele typu Host musíte vybrat univerzálního ověřování Active Directory při připojování k serveru.

*  Podporuje Vícefaktorové ověřování (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím s celou řadou možností ověření: telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci. Interaktivní MFA s Azure AD může vést k místní dialogové okno pro ověření.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
Rozšíření SSDT připojuje ke službě Azure Analysis Services pomocí univerzálního ověřování Active Directory podporující vícefaktorové ověřování. Uživatelům se výzva k přihlášení do Azure při prvním nasazení. Uživatelé musí přihlásit do Azure pomocí účtu s oprávněními správce serveru na serveru jsou nasazení. Při přihlašování do Azure poprvé, se přiřadí token. Rozšíření SSDT ukládá do mezipaměti token v paměti pro budoucí připojování.

### <a name="power-bi-desktop"></a>Power BI Desktopu
Power BI Desktop se připojí ke službě Azure Analysis Services pomocí univerzálního ověřování Active Directory podporující vícefaktorové ověřování. Uživatelům se výzva k přihlášení k Azure pro první připojení. Uživatelé se musíte přihlásit do Azure pomocí účtu, který je součástí Správce serveru nebo databázové role.

### <a name="excel"></a>Excel
Uživatelé aplikace Excel můžete připojit k serveru pomocí účtu Windows, organizace ID (e-mailovou adresu) nebo externí e-mailovou adresu. Identity externího e-mailu musí existovat ve službě Azure AD jako uživatele typu Host.

## <a name="user-permissions"></a>Uživatelská oprávnění

**Správci serverů** jsou specifické pro instanci serveru Azure Analysis Services. Se připojují pomocí nástrojů, jako je Azure portal, SSMS a SSDT provádět úkoly, jako je přidání databází a Správa rolí uživatelů. Ve výchozím nastavení se automaticky přidá uživatele, který vytvoří na serveru jako správce serveru Analysis Services. Další správci se dají přidat pomocí webu Azure portal nebo pomocí aplikace SSMS. Správci serverů musí mít účet v tenantovi Azure AD v rámci stejného předplatného. Další informace najdete v tématu [Správa správců serveru](analysis-services-server-admins.md). 

**Databáze uživatelů** připojení k databázím modelu pomocí klientské aplikace, jako je Excel nebo Power BI. Uživatelé musí být přidáni do databázových rolí. Databázové role definují správce, procesu nebo oprávnění ke čtení pro databázi. Je důležité informace o tom uživatele databáze do role s oprávněním správce se liší od správce serveru. Ve výchozím nastavení, správci serveru jsou však také správci databází. Další informace najdete v tématu [spravovat databázové role a uživatele](analysis-services-database-users.md).

**Vlastníci prostředků Azure**. Vlastníci prostředků spravovat prostředky pro předplatné Azure. Vlastníci prostředků můžete přidat identit uživatelů Azure AD na roli vlastníka nebo přispěvatele rolí v rámci předplatného pomocí **řízení přístupu** na webu Azure portal nebo pomocí šablon Azure Resource Manageru. 

![Řízení přístupu na webu Azure portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na této úrovni se vztahují na uživatele nebo účty, které potřebují k provádění úloh, které je možné dokončit na portálu nebo pomocí šablon Azure Resource Manageru. Další informace najdete v tématu [řízení přístupu na základě Role](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Databázové role

 Role definované pro tabulkový model jsou databázové role. To znamená role obsahují členy skládající se z Azure AD, uživatelé a skupiny zabezpečení, které mají konkrétní oprávnění, které definují akce tyto členy můžete provést modelové databáze. Databázová role se vytvoří jako samostatný objekt v databázi a použije se pouze na databázi, ve které byla vytvořena.   
  
 Ve výchozím nastavení při vytváření nového projektu tabulkového modelu, model projektu nemá žádné role. Role je možné definovat pomocí Správce rolí dialogových oken v sadě SSDT. Pokud role jsou definované při návrhu modelu projektu, budou použity pouze do databáze pracovního prostoru modelu. Pokud je model nasazený, stejné role se použijí na nasazeného modelu. Po nasazení model serveru a databáze správci můžou spravovat role a členy s použitím aplikace SSMS. Další informace najdete v tématu [spravovat databázové role a uživatele](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Další postup

[Správa přístupu k prostředkům pomocí skupin Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Spravovat databázové role a uživatele](analysis-services-database-users.md)  
[Správa správců serveru](analysis-services-server-admins.md)  
[Řízení přístupu na základě role](../role-based-access-control/overview.md)  