---
title: Konfigurace ověřování službou Multi-Factor Authentication – Azure SQL | Dokumentace Microsoftu
description: Další informace o použití Multi-Factored ověřování pomocí aplikace SSMS pro SQL Database a SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 98d48659225d89f476ca1e3cc437245bc9e9f753
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564812"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurace vícefaktorového ověřování pro SQL Server Management Studio a Azure AD

V tomto tématu se dozvíte, jak pomocí Azure Active Directory vícefaktorové ověřování (MFA) pomocí aplikace SQL Server Management Studio. Azure AD MFA se dá použít při připojení k Azure, aplikace SSMS nebo SqlPackage.exe [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled služby Azure SQL Database Multi-Factor authentication, naleznete v tématu [univerzální ověřování pomocí SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

## <a name="configuration-steps"></a>Postup konfigurace

1. **Konfigurovat Azure Active Directory** – Další informace najdete v tématu [správě adresáře Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrace místních identit s Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [ Přidání vlastního názvu domény do Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure teď podporuje federace se službou Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), a [spravovat Azure AD pomocí prostředí Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurace vícefaktorového ověřování** – podrobné pokyny najdete v tématu [co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [podmíněný přístup (MFA) s Azure SQL Database a datovým skladem vykazovaných](sql-database-conditional-access.md). (Vyžaduje úplné podmíněného přístupu Premium služby Azure Active Directory (Azure AD). Omezené MFA je k dispozici s standard služby Azure AD.)
3. **Konfigurace SQL Database nebo SQL Data Warehouse pro Azure AD Authentication** – podrobné pokyny najdete v tématu [připojení k SQL Database nebo SQL Data Warehouse pomocí Active Directory ověřování služby Azure](sql-database-aad-authentication.md).
4. **Stáhnout aplikaci SSMS** – v klientském počítači, stáhněte si nejnovější verze aplikace SSMS z [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto tématu použijte alespoň verzi 17.2. července 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Připojení pomocí univerzálního ověřování pomocí aplikace SSMS

Následující kroky ukazují, jak se připojit k SQL Database nebo SQL Data Warehouse pomocí nejnovější verze aplikace SSMS.

1. Připojení pomocí univerzálního ověřování na **připojit k serveru** dialogu **univerzální podporující vícefaktorové ověřování služby Active Directory –**. (Pokud se zobrazí **univerzálního ověřování Active Directory** nejsou na nejnovější verzi SSMS.)  
   ![1mfa-universal-connect][1]  
2. Dokončení **uživatelské jméno** pole pomocí přihlašovacích údajů Azure Active Directory, ve formátu `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Pokud se chcete připojit jako uživatele typu Host, musíte kliknout na **možnosti**a na **vlastnost připojení** dialogové okno, dokončení **ID tenanta nebo název domény AD** pole. Další informace najdete v tématu [univerzální ověřování pomocí SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Obvyklým pro SQL Database a SQL Data Warehouse, musíte kliknout na **možnosti** a zadejte databázi na **možnosti** dialogové okno. (Pokud je uživatel typu Host připojeného uživatele (to znamená joe@outlook.com), musíte zaškrtnout políčko a přidat aktuální název domény AD nebo ID tenanta jako součást možnosti. Zobrazit [univerzální ověřování pomocí SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md). Pak klikněte na **Connect** (Připojit).  
5. Když **přihlásit ke svému účtu** se zobrazí dialogové okno, zadejte účet a heslo vaší identity Azure Active Directory. Pokud je uživatel součástí domény sdružených se službou Azure AD není vyžadováno heslo.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Univerzální ověřování pomocí účtu, který nevyžaduje vícefaktorové ověřování připojíte se v tuto chvíli. Pro uživatele, kteří vyžadují vícefaktorové ověřování pokračujte následujícími kroky:
   >  
   
6. Dvě MFA dialogových oken nastavení se může zobrazit. Tentokrát jeden operace závisí na MFA správce nastavení a proto může být volitelný. Pro doménu povolená služba MFA tento krok je někdy předem definovaných (například domény vyžaduje, aby uživatelé používat čipové karty a kód pin).  
   ![3mfa-setup][3]  
7. Druhý možné jednou, dialogové okno umožňuje vybrat podrobnosti, které metody ověřování. Možnosti jsou nakonfigurované vaším správcem.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory odešle potvrzující informace o vás. Když dostanete ověřovací kód, zadejte ho do **zadejte ověřovací kód** pole a klikněte na tlačítko **přihlášení**.  
   ![5mfa-verify-2][5]  

Po dokončení ověření SSMS připojí, obvykle za předpokladu platné přihlašovací údaje a přístup přes bránu firewall.

## <a name="next-steps"></a>Další postup

- Přehled služby Azure SQL Database Multi-Factor authentication, naleznete v tématu univerzální ověřování s [SQL Database a SQL Data Warehouse (podpora SSMS pro vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md).  
- Udělení přístupu k vaší databázi ostatní: [SQL Database ověřování a autorizace: Udělení přístupu](sql-database-manage-logins.md)  
- Ujistěte se, že se že ostatní připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí webu Azure portal](sql-database-configure-firewall-settings.md)  
- Při použití **Active Directory – univerzální vícefaktorovým Ověřováním** ověřování ADAL trasování je k dispozici od [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Vypnuto ve výchozím nastavení, můžete zapnout trasování ADAL pomocí **nástroje**, **možnosti** nabídky v části **Azure Services**, **Azure Cloud**,  **ADAL úroveň trasování okna výstup**následovaný povolení **výstup** v **zobrazení** nabídky. Trasování jsou k dispozici v okně výstupu při výběru **Azure Active Directory možnost**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

