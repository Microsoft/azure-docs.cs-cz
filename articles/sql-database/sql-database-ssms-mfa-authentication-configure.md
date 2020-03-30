---
title: Konfigurace vícefaktorového ověřování
description: Přečtěte si, jak používat vícefaktorové ověřování s SSMS pro DATABÁZI SQL a datový sklad SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 5d4d410f6fca566dab14e601972952b5996c331a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124884"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurace vícefaktorového ověřování pro SQL Server Management Studio a Azure AD

Toto téma ukazuje, jak používat vícefaktorové ověřování Azure Active Directory (MFA) se službou SQL Server Management Studio. Azure AD MFA lze použít při připojení SSMS nebo SqlPackage.exe k Azure [SQL Database](sql-database-technical-overview.md) a SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled vícefaktorového ověřování Azure SQL Database najdete v tématu [univerzální ověřování s databází SQL a sql datový sklad (podpora SSMS pro MFA).](sql-database-ssms-mfa-authentication.md)

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

## <a name="configuration-steps"></a>Postup konfigurace

1. **Konfigurace služby Azure Active Directory** – další informace najdete [v tématu Správa adresáře Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integrace místních identit pomocí služby Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), Přidání vlastního názvu domény do Azure [AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure teď podporuje federaci se službou Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)a Správa Azure [AD pomocí Prostředí Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurace mfa** – podrobné pokyny najdete v tématu [Co je Azure multi-factor authentication?](../active-directory/authentication/multi-factor-authentication.md), [Podmíněný přístup (MFA) s Azure SQL Database a datového skladu](sql-database-conditional-access.md). (Úplný podmíněný přístup vyžaduje premium Azure Active Directory (Azure AD). Omezené vícefaktorové informace jsou k dispozici se standardním Azure AD.)
3. **Konfigurace databáze SQL database nebo datového skladu SQL pro ověřování azure ad** – podrobné pokyny najdete v [tématu Připojení k databázi SQL nebo datového skladu SQL pomocí ověřování Azure Active Directory](sql-database-aad-authentication.md).
4. **Ke stažení SSMS** - Na klientském počítači, stáhněte si nejnovější SSMS, z [download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto tématu použijte alespoň červenec 2017 verze 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Připojení pomocí univerzálního ověřování pomocí SSMS

Následující kroky ukazují, jak se připojit k databázi SQL nebo datovému skladu SQL pomocí nejnovější ssms.

1. Chcete-li se připojit pomocí univerzálního ověřování, vyberte v dialogovém okně **Připojit k serveru** **položku Active Directory - Universal s podporou vícefaktorové ověřování**. (Pokud vidíte **univerzální ověřování služby Active Directory,** nejste na nejnovější verzi služby SSMS.)  
   ![1mfa-univerzální připojení][1]  
2. Dokončete pole **Uživatelské jméno** s přihlašovacími `user_name@domain.com`údaji služby Azure Active Directory ve formátu .  
   ![1mfa-univerzální-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Pokud se připojujete jako uživatel typu Host, nemusíte již vyplňovat pole Název domény služby AD nebo ID klienta pro uživatele typu Host, protože jej ssms 18.x nebo novější automaticky rozpozná. Další informace naleznete [v tématu univerzální ověřování s databází SQL a SQL Data Warehouse (Podpora SSMS pro vícefaktorové ověřování).](sql-database-ssms-mfa-authentication.md)
   ![mfa-no-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Pokud se však připojujete jako uživatel typu Host pomocí souboru SSMS 17.x nebo staršího, musíte klepnout na tlačítko **Možnosti**a v dialogovém okně **Vlastnost připojení** a vyplnit pole **Název domény služby AD nebo ID klienta.**
   ![mfa-nájemce-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Jako obvykle pro databázi SQL a datový sklad SQL musíte klepnout na **položku Možnosti** a zadat databázi v dialogovém okně **Možnosti.** (Pokud je připojený uživatel uživatelem typu joe@outlook.comHost ( tj. ), musíte zaškrtnout políčko a přidat aktuální název domény služby AD nebo ID klienta jako součást možností. Viz [Univerzální ověřování s databází SQL a datovým skladem SQL (podpora SSMS pro vícefaktorové ověřování).](sql-database-ssms-mfa-authentication.md) Pak klikněte na **Connect** (Připojit).  
5. Až se zobrazí dialogové okno **Přihlásit se k účtu,** zadejte účet a heslo identity služby Azure Active Directory. Žádné heslo je vyžadováno, pokud je uživatel součástí domény federované s Azure AD.  
   ![2mfa-přihlášení][2]  

   > [!NOTE]
   > Pro univerzální ověřování s účtem, který nevyžaduje vícefaktorové ověřování, se připojíte v tomto okamžiku. Pro uživatele, kteří vyžadují vícefaktorové informace, pokračujte následujícími kroky:
   >  
   
6. Mohou se zobrazit dvě dialogová okna nastavení vícefaktorové ho dispozice. Tato jednorázová operace závisí na nastavení správce vícefaktorové mfa, a proto může být volitelné. U domény s povolenou mfa je tento krok někdy předdefinovaný (například doména vyžaduje, aby uživatelé používali čipovou kartu a pin).  
   ![3mfa-nastavení][3]  
7. Druhé možné jednorázové dialogové okno umožňuje vybrat podrobnosti o metodě ověřování. Možné možnosti nakonfiguruje správce.  
   ![4mfa-ověřit-1][4]  
8. Služba Azure Active Directory vám odešle potvrzující informace. Když ověřovací kód obdržíte, zadejte ho do pole **Zadat ověřovací kód** a klikněte na **Přihlásit se**.  
   ![5mfa-ověřit-2][5]  

Po dokončení ověření se SSMS připojí obvykle s předpokladem platných přihlašovacích údajů a přístupu brány firewall.

## <a name="next-steps"></a>Další kroky

- Přehled vícefaktorového ověřování Azure SQL Database najdete v tématu univerzální ověřování s [databází SQL a sql datový sklad (podpora SSMS pro MFA).](sql-database-ssms-mfa-authentication.md)  
- Udělení přístupu k databázi ostatním uživatelům: [Ověřování a autorizace databáze SQL: Udělení přístupu](sql-database-manage-logins.md)  
- Ujistěte se, že se ostatní můžou připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí portálu Azure](sql-database-configure-firewall-settings.md)  
- Při použití **služby Active Directory- Universal s ověřováním Vícefaktorové** ověřování je k dispozici trasování ADAL začínající s [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete zapnout trasování ADAL pomocí nabídky **Nástroje**, **Možnosti** v části **Azure Services**, **Azure Cloud**, **ADAL Output Window Trace Level**, následované povolením **výstupu** v nabídce **Zobrazení.** Trasování jsou k dispozici ve výstupním okně při výběru **možnosti služby Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

