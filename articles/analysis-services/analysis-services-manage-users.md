---
title: Oprávnění k ověřování a uživatel ve službě Azure Analysis Services | Microsoft Docs
description: Další informace o ověřování a uživatel oprávnění ve službě Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 47846af015bf02940da7a7b8a4154113aab57b08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597626"
---
# <a name="authentication-and-user-permissions"></a>Ověřování a uživatel oprávnění
Azure Analysis Services použije Azure Active Directory (Azure AD) pro ověřování identity management a uživatelů. Jakýkoli uživatel, vytváření, Správa nebo připojení k serveru Azure Analysis Services server musí mít platné uživatelské identity [klienta Azure AD](../active-directory/active-directory-administer.md) ve stejném předplatném.

Podporuje Azure Analysis Services [spolupráce Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). S B2B můžete pozvat uživatele mimo organizaci jako uživatele typu Host v adresáři služby Azure AD. Hosté může být z jiného adresáře služby Azure AD klienta nebo žádné platné e-mailovou adresu. Jednou pozvali a uživatel přijme podmínky pozvánku odeslat e-mailem z Azure, identity uživatele je přidán do adresáře klienta. Tyto identity je možné přidat do skupin zabezpečení nebo jako členové role správce nebo databázi serveru.

![Architektura ověřování Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication
Všechny klientské aplikace a nástroje pomocí jednoho nebo více služby Analysis Services [klientské knihovny](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) pro připojení k serveru. 

Všechny tři klientské knihovny podporují interaktivní toku Azure AD a metody neinteraktivní ověřování. V aplikacích využitím AMOMD a MSOLAP můžete využít dvě metody neinteraktivní, hesla služby Active Directory a metody Integrované ověřování Active Directory. Tyto dvě metody nikdy výsledkem automaticky otevíraná okna.

Klientské aplikace, jako třeba aplikace Excel a Power BI Desktop a nástroje, například aplikace SSMS a rozšíření SSDT nainstalujte nejnovější verze knihoven při aktualizaci na nejnovější verzi. Power BI Desktop, aplikace SSMS a rozšíření SSDT se aktualizují každý měsíc. Aplikace Excel je [aktualizován s Office 365](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizace Office 365 jsou méně časté a některé organizace používat odložené kanál, význam aktualizace odložit až na tři měsíce.

V závislosti na klientská aplikace nebo nástroje, které používáte může být jiný typ ověřování a jak se přihlásit. Každá aplikace může podporovat různé funkce pro připojení ke cloudovým službám, jako je Azure Analysis Services.

Power BI Desktop a rozšíření SSDT, aplikace SSMS podporovat Universal ověřování služby Active Directory, interaktivní metoda, která také podporuje Azure Multi-Factor Authentication (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím při současném poskytování jednoduchý proces přihlášení. Zajišťuje silné ověřování s několik možností ověřování (telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace). Interaktivní vícefaktorového ověřování s Azure AD může způsobit v místním dialogovém okně pro ověření. **Doporučuje se univerzální ověřování**.

Pokud přihlášení do Azure pomocí účtu systému Windows a univerzální ověřování není vybrána, nebo k dispozici (aplikace Excel), [Active Directory Federation Services (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) je vyžadován. S federací, Azure AD a Office 365 uživatelé se ověřují pomocí místních přihlašovacích údajů a mají přístup k prostředkům Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Azure servery služby Analysis Services podporují připojení od [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a vyšší pomocí ověřování systému Windows, ověřování hesla Active Directory a univerzální ověřování služby Active Directory. Obecně se doporučuje, že abyste použili Universal ověřování služby Active Directory, protože:

*  Podporuje metody interaktivní i neinteraktivní ověřování.

*  Podporuje uživatele typu Host Azure B2B pozvat do Azure AS klienta. Při připojování k serveru, musíte uživatele typu Host vybrat Universal ověřování služby Active Directory při připojování k serveru.

*  Podporuje Vícefaktorové ověřování (MFA). Azure MFA pomáhá chránit přístup k datům a aplikacím s celou řadu možností ověřování: telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace. Interaktivní vícefaktorového ověřování s Azure AD může způsobit v místním dialogovém okně pro ověření.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
Rozšíření SSDT připojení k Azure Analysis Services pomocí ověřování služby Active Directory Universal podpora vícefaktorového ověřování. Uživatelé vyzváni k přihlášení k Azure při prvním nasazení. Uživatelé musíte se přihlásit pomocí účtu s oprávněními správce serveru na serveru jsou nasazení do Azure. Při přihlášení se k Azure poprvé, je přiřazen token. Rozšíření SSDT ukládá do mezipaměti token v paměti pro budoucí připojování.

### <a name="power-bi-desktop"></a>Power BI Desktop
Power BI Desktop připojí ke službě Azure Analysis Services pomocí ověřování služby Active Directory Universal podpora vícefaktorového ověřování. Uživatelé vyzváni k přihlášení k Azure na prvním připojení. Uživatelé musí přihlaste k Azure pomocí účtu, který je součástí Správce serveru nebo role databáze.

### <a name="excel"></a>Excel
Uživatelé aplikace Excel můžete připojit k serveru pomocí účet systému Windows, ID organizace (e-mailovou adresu) nebo externí e-mailovou adresu. Externí e-mailové identity musí existovat ve službě Azure AD jako uživatel guest.

## <a name="user-permissions"></a>Uživatelská oprávnění

**Správci serveru** jsou specifické pro instanci serveru Azure Analysis Services. Připojit se pomocí nástrojů, například portál Azure, aplikace SSMS a rozšíření SSDT k úlohám jako přidání databází a Správa rolí uživatelů. Ve výchozím nastavení se automaticky přidá uživatele, který vytvoří serveru jako správce serveru služby Analysis Services. Další správci můžete přidat pomocí portálu Azure nebo SSMS. Správci serveru musí mít účet v klientovi Azure AD ve stejném předplatném. Další informace najdete v tématu [spravovat správce serveru](analysis-services-server-admins.md). 

**Databáze uživatelů** připojení k databázím modelu pomocí klientské aplikace jako aplikace Excel nebo produktu Power BI. Uživatelé musí být přidaný do role databáze. Role databáze definují, správce procesu nebo oprávnění ke čtení pro databázi. Je důležité si uvědomit, databáze uživatelů v roli s oprávněními správce se liší od správce serveru. Ve výchozím nastavení, správci serveru jsou však také správci databází. Další informace najdete v tématu [spravovat role databáze a uživatele](analysis-services-database-users.md).

**Vlastníci prostředků Azure**. Vlastníci prostředků spravovat prostředky pro předplatné Azure. Vlastníci prostředků můžete přidat identit uživatelů Azure AD na vlastníka nebo přispěvatele rolí v rámci předplatného pomocí **řízení přístupu** na portálu Azure nebo pomocí šablony Azure Resource Manager. 

![Řízení přístupu na portálu Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na této úrovni aplikovat na uživatele nebo účty, které je třeba provést úlohy, které můžete provést na portálu nebo pomocí šablony Azure Resource Manager. Další informace najdete v tématu [řízení přístupu na základě Role](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Databázové role

 Role pro tabulkový model definován jsou databázové role. To znamená role obsahovat členy skládající se z Azure AD Uživatelé a skupiny zabezpečení, které jste těmto členům konkrétní oprávnění, která definují akce může trvat na modelové databáze. Databázové role je vytvořen jako samostatný objekt v databázi a platí jenom pro databázi, ve kterém se vytvoří dané roli.   
  
 Ve výchozím nastavení když vytvoříte nový projekt tabulkový model, projekt modelu nemá žádné role. Pomocí dialogového okna Role správce v sadě SSDT lze definovat role. Role jsou definovány při návrhu modelu projektu, se uplatňují pouze na databázi pracovního prostoru modelu. Po nasazení modelu se stejnou rolí platí pro nasazené modelu. Po nasazení model serveru a správci databází můžete spravovat role a členy pomocí aplikace SSMS. Další informace najdete v tématu [spravovat role databáze a uživatele](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Další postup

[Spravovat přístup k prostředkům pomocí skupin Azure Active Directory](../active-directory/active-directory-manage-groups.md)   
[Spravovat role databáze a uživatele](analysis-services-database-users.md)  
[Správa správců serveru](analysis-services-server-admins.md)  
[Řízení přístupu na základě role](../role-based-access-control/overview.md)  