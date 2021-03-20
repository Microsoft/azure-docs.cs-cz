---
title: Azure Analysis Services ověřování a oprávnění uživatele | Microsoft Docs
description: Tento článek popisuje, jak Azure Analysis Services používá Azure Active Directory (Azure AD) pro správu identit a ověřování uživatelů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487435"
---
# <a name="authentication-and-user-permissions"></a>Ověřování a uživatelská oprávnění

Azure Analysis Services používá ke správě identit a ověřování uživatelů Azure Active Directory (Azure AD). Každý uživatel, který vytváří, spravuje nebo připojuje k serveru Azure Analysis Services, musí mít platnou identitu uživatele v [Tenantovi Azure AD](../active-directory/fundamentals/active-directory-whatis.md) ve stejném předplatném.

Azure Analysis Services podporuje [spolupráci Azure AD B2B](../active-directory/external-identities/what-is-b2b.md). Pomocí B2B můžou být uživatelé mimo organizaci pozváni jako uživatelé typu Host v adresáři Azure AD. Hosté můžou být z jiného adresáře tenanta služby Azure AD nebo jakékoli platné e-mailové adresy. Po pozvánce a uživatel přijme pozvánku odeslanou e-mailem z Azure, přidá se do adresáře tenanta identita uživatele. Tyto identity je možné přidat do skupin zabezpečení nebo jako členy role správce serveru nebo databáze.

![Azure Analysis Services Architektura ověřování](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

Všechny klientské aplikace a nástroje používají k připojení k serveru jednu nebo více Analysis Services [klientských knihoven](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (AMO, MSOLAP, ADOMD). 

Všechny tři klientské knihovny podporují jak interaktivní tok Azure AD, tak i neinteraktivní metody ověřování. Dvě neinteraktivní metody, heslo služby Active Directory a integrované metody ověřování služby Active Directory, se dají použít v aplikacích, které využívají AMOMD a MSOLAP. Tyto dvě metody nikdy nevedou k místním dialogovým oknům.

Klientské aplikace, jako je Excel a Power BI Desktop, a nástroje, jako je například SSMS a Analysis Services Project Extensions pro sadu Visual Studio, nainstalují nejnovější verze knihoven až po aktualizaci na nejnovější verzi. Rozšíření projektů Power BI Desktop, SSMS a Analysis Services se aktualizují měsíčně. Aplikace Excel je [aktualizována pomocí Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizace Microsoft 365 jsou méně časté a některé organizace používají odložený kanál, což znamená, že aktualizace se odúčtují až po třech měsících.

V závislosti na klientské aplikaci nebo nástroji, které používáte, se typ ověřování a způsob, jakým se přihlašujete, může lišit. Každá aplikace může podporovat různé funkce pro připojení ke cloudovým službám, jako je Azure Analysis Services.

Power BI Desktop, Visual Studio a SSMS podporují univerzální ověřování služby Active Directory, což je interaktivní metoda, která podporuje taky Azure AD Multi-Factor Authentication (MFA). Azure AD MFA pomáhá chránit přístup k datům a aplikacím a současně poskytuje jednoduchý proces přihlašování. Zajišťuje silné ověřování s několika možnostmi ověřování (telefonní hovor, textová zpráva, čipové karty s kódem PIN nebo oznámením v mobilní aplikaci). Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření. **Doporučuje se univerzální ověřování**.

Pokud se přihlašujete k Azure pomocí účtu systému Windows a univerzální ověřování není vybráno nebo není k dispozici (Excel), je požadováno [Active Directory Federation Services (AD FS) (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) . Pomocí federace se uživatelé Azure AD a Microsoft 365 ověřují pomocí místních přihlašovacích údajů a můžou získat přístup k prostředkům Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Servery Azure Analysis Services podporují připojení ze [SSMS v 17.1](/sql/ssms/download-sql-server-management-studio-ssms) a vyšší pomocí ověřování systému Windows, ověřování pomocí hesla služby Active Directory a univerzálního ověřování služby Active Directory. Obecně se doporučuje použít univerzální ověřování služby Active Directory z těchto důvodů:

*  Podporuje interaktivní a neinteraktivní metody ověřování.

*  Podporuje uživatele hosta Azure B2B pozvaní do Azure jako tenant. Při připojování k serveru musí uživatelé typu Host při připojování k serveru vybrat univerzální ověřování služby Active Directory.

*  Podporuje Multi-Factor Authentication (MFA). Azure AD MFA pomáhá chránit přístup k datům a aplikacím pomocí škály možností ověřování: telefonní hovor, textová zpráva, čipové karty a PIN kód nebo mobilní aplikace oznámení. Interaktivní vícefaktorové ověřování pomocí Azure AD může mít za následek automaticky otevírané okno k ověření.

### <a name="visual-studio"></a>Visual Studio

Visual Studio se připojuje k Azure Analysis Services pomocí univerzálního ověřování služby Active Directory s podporou VÍCEFAKTOROVÉHO ověřování. Uživatelům se při prvním nasazení zobrazí výzva, abyste se přihlásili k Azure. Uživatelé se musí přihlásit k Azure pomocí účtu s oprávněními správce serveru na serveru, na kterém nasazují. Při prvním přihlášení k Azure se přiřadí token. Token se v paměti ukládá do mezipaměti pro budoucí opětovné připojení.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop se připojuje k Azure Analysis Services pomocí univerzálního ověřování služby Active Directory s podporou VÍCEFAKTOROVÉHO ověřování. Uživatelům se zobrazí výzva, abyste se k Azure přihlásili při prvním připojení. Uživatelé se musí přihlásit k Azure pomocí účtu, který je součástí Správce serveru nebo databázové role.

### <a name="excel"></a>Excel

Uživatelé Excelu se můžou připojit k serveru pomocí účtu systému Windows, ID organizace (e-mailové adresy) nebo externí e-mailové adresy. Externí e-mailové identity musí existovat ve službě Azure AD jako uživatel typu Host.

## <a name="user-permissions"></a>Uživatelská oprávnění

**Správci serveru** jsou specifické pro instanci serveru Azure Analysis Services. Připojují se pomocí nástrojů, jako jsou Azure Portal, SSMS a Visual Studio, k provádění úloh, jako je přidávání databází a Správa rolí uživatelů. Ve výchozím nastavení se uživatel, který vytváří Server, automaticky přidá jako správce serveru Analysis Services. Ostatní správce je možné přidat pomocí Azure Portal nebo SSMS. Správci serveru musí mít v tenantovi Azure AD ve stejném předplatném účet. Další informace najdete v tématu [Správa správců serverů](analysis-services-server-admins.md). 

**Uživatelé databáze** se připojují k modelovým databázím pomocí klientských aplikací, jako je Excel nebo Power BI. Uživatelé musí být přidáni do databázových rolí. Databázové role definují oprávnění správce, procesu nebo čtení pro databázi. Je důležité pochopit uživatele databáze v roli s oprávněními správce, které se liší od správců serverů. Ve výchozím nastavení jsou však správci serveru také správci databáze. Další informace najdete v tématu [Správa databázových rolí a uživatelů](analysis-services-database-users.md).

**Vlastníci prostředků Azure**. Vlastníci prostředků spravují prostředky pro předplatné Azure. Vlastníci prostředků můžou přidávat identity uživatelů Azure AD do rolí vlastníka nebo přispěvatele v rámci předplatného pomocí **řízení přístupu** v Azure Portal nebo pomocí šablon Azure Resource Manager. 

![Řízení přístupu v Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na této úrovni se vztahují na uživatele nebo účty, které potřebují provádět úkoly, které je možné dokončit na portálu nebo pomocí Azure Resource Manager šablon. Další informace najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Databázové role

 Role definované pro tabelární model jsou databázové role. To znamená, že role obsahují členy sestávající z uživatelů Azure AD a skupin zabezpečení, které mají specifická oprávnění definující akci, kterou mohou členové provádět v databázi modelu. Databázová role se vytvoří jako samostatný objekt v databázi a použije se pouze na databázi, ve které byla vytvořena.   
  
 Ve výchozím nastavení platí, že při vytváření nového projektu s tabelárním modelem nemá projekt modelu žádné role. Role lze definovat pomocí dialogového okna Správce rolí v aplikaci Visual Studio. Pokud jsou role definovány během návrhu projektu modelu, jsou aplikovány pouze na databázi pracovního prostoru modelu. Při nasazení modelu se stejné role aplikují na nasazený model. Po nasazení modelu mohou správci serveru a databáze spravovat role a členy pomocí SSMS. Další informace najdete v tématu [Správa databázových rolí a uživatelů](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Další kroky

[Správa přístupu k prostředkům pomocí skupin Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Správa databázových rolí a uživatelů](analysis-services-database-users.md)  
[Správa správců serveru](analysis-services-server-admins.md)  
[Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)