---
title: "Konfigurace služby Multi-Factor authentication - Azure SQL | Microsoft Docs"
description: "Další informace o použití Multi-Factored ověřování pomocí SSMS pro SQL Database a SQL Data Warehouse."
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/27/2017
ms.author: mireks
ms.openlocfilehash: 7b74cd6b62686fd03d9f42316701f44daf99eed8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurace vícefaktorového ověřování pro SQL Server Management Studio a Azure AD

Toto téma ukazuje, jak používat Azure Active Directory vícefaktorové ověřování (MFA) se SQL Server Management Studio. Azure AD MFA lze použít při připojení k databázi SQL Azure a Azure SQL Data Warehouse SSMS nebo SqlPackage.exe.

Přehled služby databáze SQL Azure Multi-Factor authentication, naleznete v části [Universal ověřování se službou SQL Database a SQL Data Warehouse (SSMS podporu vícefaktorového ověřování)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Kroky konfigurace

1. **Konfigurace Azure Active Directory** – Další informace najdete v tématu [Správa adresáře služby Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrace místních identit s Azure Active Directory](../active-directory/active-directory-aadconnect.md), [ Přidání vlastního názvu domény do Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure teď podporuje federační službou Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), a [Správa Azure AD pomocí prostředí Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurace vícefaktorového ověřování** – podrobné pokyny najdete v tématu [co je Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md), [podmíněný přístup (MFA) s Azure SQL Database a datový sklad](sql-database-conditional-access.md). (Vyžaduje úplné podmíněného přístupu Premium Azure Active Directory (Azure AD). Omezené MFA je k dispozici s standardní Azure AD.)
3. **Konfigurace SQL Database nebo SQL Data Warehouse pro Azure AD Authentication** – podrobné pokyny najdete v tématu [připojení k SQL Database nebo SQL Data Warehouse pomocí pomocí Azure ověřování služby Active Directory](sql-database-aad-authentication.md).
4. **Stažení aplikace SSMS** – v klientském počítači, stáhněte si nejnovější SSMS z [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto tématu použijte alespoň července 2017 verze 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Připojení pomocí universal ověřování pomocí SSMS

Následující kroky ukazují, jak se připojit k SQL Database nebo SQL Data Warehouse pomocí nejnovější aplikace SSMS.

1. Připojení pomocí Universal ověřování na **připojit k serveru** dialogové okno, vyberte **Universal s podpora vícefaktorového ověřování služby Active Directory -**. (Pokud se zobrazí **Universal ověřování služby Active Directory** nejsou na nejnovější verzi aplikace SSMS.)  
   ![1mfa-universal-connect][1]  
2. Dokončení **uživatelské jméno** pole s Azure Active Directory přihlašovací údaje ve formátu `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Pokud se připojujete jako uživatel guest, musíte kliknout na **možnosti**a na **vlastnost připojení** dialogové okno, dokončení **ID názvu nebo klienta domény AD** pole. Další informace najdete v tématu [Universal ověřování se službou SQL Database a SQL Data Warehouse (SSMS podporu vícefaktorového ověřování)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Obvyklým pro SQL Database a SQL Data Warehouse, musíte kliknout na **možnosti** a zadejte databázi na **možnosti** dialogové okno. (Pokud je uživatel připojený uživatel typu Host (tj. joe@outlook.com), musíte zaškrtnout políčko a přidat aktuální název domény AD nebo ID klienta jako součást možností. V tématu [Universal ověřování se službou SQL Database a SQL Data Warehouse (SSMS podporu vícefaktorového ověřování)]()(sql-database-ssms-mfa-authentication.md. Pak klikněte na tlačítko **Connect**.  
5. Když **přihlásit ke svému účtu** se zobrazí dialogové okno, zadejte účet a heslo vaší identity Azure Active Directory. Pokud je uživatel součástí domény sdružených se službou Azure AD není vyžadováno heslo.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Pro univerzální ověřování pomocí účtu, který nevyžaduje MFA připojíte se v tomto okamžiku. Vyžadování vícefaktorového ověřování uživatelů pokračujte následujícími kroky:
   >  
   
6. Může se objevit dvě dialogových oken nastavení vícefaktorového ověřování. Tato jednou operace závisí na MFA správce nastavení a proto mohou být nepovinné. Pro doménu povolené ověřování MFA tento krok je někdy předem definované (například domény vyžaduje uživatelům používat čipové karty a kód pin).  
   ![3mfa-setup][3]  
7. Druhý možné jednou, dialogové okno umožňuje vybrat podrobnosti o metodu ověřování. Dostupné možnosti jsou nakonfigurované správcem.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory odešle potvrzující informace pro vás. Až dostanete ověřovací kód, zadejte ji do **zadejte ověřovací kód** pole a klikněte na tlačítko **přihlášení**.  
   ![5mfa-verify-2][5]  

Po dokončení ověření SSMS připojí, obvykle za předpokladu platné přihlašovací údaje a přístup přes bránu firewall.

## <a name="next-steps"></a>Další kroky

- Přehled služby databáze SQL Azure Multi-Factor authentication, naleznete v části Universal ověřování s [SQL Database a SQL Data Warehouse (SSMS podporu vícefaktorového ověřování)](sql-database-ssms-mfa-authentication.md).  
- Ostatní udělit přístup k vaší databázi: [SQL databáze ověřování a autorizace: udělení přístupu](sql-database-manage-logins.md)  
- Zajistěte, aby ostatní můžete připojit přes bránu firewall: [konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí portálu Azure](sql-database-configure-firewall-settings.md)  
- Při použití **Active Directory - Universal s MFA** ověřování ADAL trasování je k dispozici počínaje [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Vypnout ve výchozím nastavení, můžete zapnout ADAL trasování pomocí **nástroje**, **možnosti** nabídky v části **služeb Azure**, **cloudu Azure**,  **ADAL úroveň trasování okno výstup**, za nímž povolením **výstup** v **zobrazení** nabídky. Trasování jsou k dispozici v okně výstupu. při výběru **možnost Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

