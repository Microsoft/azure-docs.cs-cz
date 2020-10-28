---
title: Konfigurace vícefaktorového ověřování
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Naučte se používat ověřování Multi-Factor Authentication s SSMS pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 2de48946088316e9070e13396f124148a4ff6099
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675014"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurace služby Multi-Factor Authentication pro SQL Server Management Studio a Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto článku se dozvíte, jak používat službu Multi-Factor Authentication služby Azure Active Directory (Azure AD) s SQL Server Management Studio (SSMS). Azure AD MFA se dá použít při připojení SSMS nebo SqlPackage.exe k [Azure SQL Database](sql-database-paas-overview.md), [spravované instanci Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) a [azure synapse Analytics (dřív SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled služby Multi-Factor Authentication najdete v tématech [univerzální ověřování pomocí SQL Database, spravovaná instance SQL a Azure synapse (podpora SSMS pro MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Databáze v Azure SQL Database, Azure SQL Managed instance a Azure synapse (dříve SQL Data Warehouse) se ve zbývající části tohoto článku společně označují jako databáze a server odkazuje na [Server](logical-servers.md) , který je hostitelem databází pro Azure SQL Database a Azure synapse.

## <a name="configuration-steps"></a>Postup konfigurace

1. **Konfigurace Azure Active Directory** – Další informace najdete v tématu [Správa adresáře služby Azure AD](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [Integrace místních identit s Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure teď podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)a [Správa Azure AD pomocí Windows PowerShellu](/previous-versions/azure/jj151815(v=azure.100)).
2. **Konfigurace MFA** – podrobné pokyny najdete v tématu [co je Azure Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md), [podmíněný přístup (MFA) s Azure SQL Database a datovým skladem](conditional-access-configure.md). (Úplný podmíněný přístup vyžaduje prémiové Azure Active Directory. Omezená MFA je k dispozici ve standardní službě Azure AD.)
3. **Konfigurace ověřování Azure AD** – podrobné pokyny najdete v tématu [připojení k SQL Database, Managed instance SQL nebo Azure Synapse pomocí ověřování Azure Active Directory](authentication-aad-overview.md).
4. **Stáhněte si SSMS** – v klientském počítači stáhněte nejnovější verzi SSMS, ze [Stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Připojení pomocí univerzálního ověřování pomocí SSMS

Následující kroky ukazují, jak se připojit pomocí nejnovější SSMS.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Pokud se chcete připojit pomocí univerzálního ověřování, vyberte v dialogovém okně **připojit k serveru** v SQL Server Management Studio (SSMS) možnost **Active Directory – univerzální s podporou vícefaktorového** ověřování. (Pokud se zobrazí **univerzální ověřování služby Active Directory** , nejste na nejnovější verzi SSMS.)

   ![Snímek obrazovky karty Vlastnosti připojení v dialogovém okně připojit k serveru v S s M s. v rozevíracím seznamu připojit k databázi je vybraná možnost MyDatabase.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Do pole **uživatelské jméno** zadejte přihlašovací údaje Azure Active Directory ve formátu `user_name@domain.com` .

   ![Snímek obrazovky nastavení dialogového okna připojit k serveru pro typ serveru, název serveru, ověřování a uživatelské jméno.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Pokud se připojujete jako uživatel typu Host, už nemusíte pro uživatele typu Host doplňovat pole název domény služby AD nebo ID tenanta, protože SSMS 18. x nebo novější ho automaticky rozpoznává. Další informace najdete v tématu [univerzální ověřování pomocí SQL Database, spravované instance SQL a Azure synapse (podpora SSMS pro MFA)](../database/authentication-mfa-ssms-overview.md).

   ![Snímek obrazovky karty Vlastnosti připojení v dialogovém okně připojit k serveru v S s M s. v rozevíracím seznamu připojit k databázi je vybraná možnost MyDatabase.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Pokud se však připojujete jako uživatel typu Host pomocí SSMS 17. x nebo staršího, musíte kliknout na možnost **Možnosti** a v dialogovém okně **Vlastnosti připojení** vyplnit pole **název domény služby AD nebo ID tenanta** .

   ![Snímek obrazovky karty vlastností připojení v dialogovém okně připojit k serveru v S s M. je vyplněna možnost název domény služby AD nebo ID tenanta.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Vyberte **Možnosti** a zadejte databázi v dialogovém okně **Možnosti** . (Pokud je připojený uživatel uživatel typu Host (tj. joe@outlook.com ), musíte zaškrtnout políčko a jako součást možností přidat aktuální název domény služby AD nebo ID tenanta. Podívejte [se na téma Univerzální ověřování pomocí SQL Database a Azure synapse Analytics (podpora SSMS pro MFA)](../database/authentication-mfa-ssms-overview.md). Pak klikněte na **Connect** (Připojit).  
5. Po zobrazení dialogového okna **Přihlásit se k účtu** zadejte účet a heslo vaší Azure Active Directory identity. Pokud je uživatel součástí domény federované se službou Azure AD, není nutné žádné heslo.

   ![Snímek obrazovky s přihlášením k vašemu účtu pro Azure SQL Database a datový sklad Účet a heslo jsou vyplněny.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Pro univerzální ověřování pomocí účtu, který nepotřebuje MFA, se v tomto okamžiku připojíte. Pro uživatele, kteří vyžadují MFA, pokračujte následujícími kroky:
   >  

6. Můžou se zobrazit dvě dialogová okna pro nastavení vícefaktorového ověřování. Tato jednorázová operace závisí na nastavení správce MFA, a proto může být volitelná. V případě domény s povolenou MFA je tento krok někdy předem definovaný (například doména vyžaduje, aby uživatelé používali čipovou kartu a PIN kód).

   ![Snímek obrazovky s přihlášením k vašemu účtu pro Azure SQL Database a datový sklad s výzvou k nastavení dalšího ověření zabezpečení](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. Druhý možný jednorázový dialog umožňuje vybrat podrobnosti metody ověřování. Možné možnosti jsou nakonfigurovány vaším správcem.

   ![Snímek obrazovky dialogového okna další ověření zabezpečení s možnostmi pro výběr a konfiguraci metody ověřování.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory odesílá potvrzující informace. Když obdržíte ověřovací kód, zadejte ho do pole **zadat ověřovací kód** a klikněte na **Přihlásit** se.

   ![Snímek obrazovky s přihlášením k vašemu účtu v dialogovém okně pro Azure SQL Database a datový sklad s výzvou k zadání ověřovacího kódu.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Po dokončení ověření se SSMS připojuje obvykle k platným přihlašovacím údajům a přístup k bráně firewall.

## <a name="next-steps"></a>Další kroky

- Přehled služby Multi-Factor Authentication najdete v tématech [univerzální ověřování pomocí SQL Database, spravovaná instance SQL a Azure synapse (podpora SSMS pro MFA)](../database/authentication-mfa-ssms-overview.md).  
- Udělit ostatním přístup k vaší databázi: [SQL Database ověřování a autorizace: udělení přístupu](logins-create-manage.md)  
- Zajistěte, aby se uživatelé mohli připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru pomocí Azure Portal](./firewall-configure.md)  
- Při používání **služby Active Directory – univerzální s** ověřováním MFA je k dispozici trasování ADAL od [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete trasování ADAL zapnout pomocí nabídky **nástroje** , **Možnosti** , v části **služby Azure** , **cloud Azure** , **ADAL okno výstup úroveň trasování** a následným povolením **výstupu**  v nabídce **zobrazení** . Trasování jsou k dispozici v okně výstup při výběru **možnosti Azure Active Directory** .