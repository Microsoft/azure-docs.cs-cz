---
title: Konfigurace vícefaktorového ověřování
description: Naučte se používat ověřování Multi-Factor Authentication s SSMS pro SQL Database a Azure synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: has-adal-ref
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: e551ac54414cb3114a7d0905b1b2e59b0d16580a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198553"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurace služby Multi-Factor Authentication pro SQL Server Management Studio a Azure AD

V tomto tématu se dozvíte, jak používat Azure Active Directory Multi-Factor Authentication (MFA) s SQL Server Management Studio. Azure AD MFA se dá použít při připojování SSMS nebo SqlPackage. exe k Azure [SQL Database](sql-database-technical-overview.md) a [Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Přehled Azure SQL Database Multi-Factor Authentication najdete v tématu [univerzální ověřování pomocí SQL Database a Azure synapse (podpora pro MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Toto téma se vztahuje na Azure SQL Server a na SQL Database i databáze Azure synapse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá při odkazování na SQL Database a Azure synapse.

## <a name="configuration-steps"></a>Postup konfigurace

1. **Konfigurace Azure Active Directory** – Další informace najdete v tématu [Správa adresáře služby Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integrace místních identit s Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Přidání vlastního názvu domény do Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure teď podporuje federaci s Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)a [Správa Azure AD pomocí Windows PowerShellu](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurace MFA** – podrobné pokyny najdete v tématu [co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [podmíněný přístup (MFA) s Azure SQL Database a Azure synapse](sql-database-conditional-access.md). (Úplný podmíněný přístup vyžaduje prémiovou Azure Active Directory (Azure AD). Omezená MFA je k dispozici ve standardní službě Azure AD.)
3. **Konfigurace SQL Database nebo Azure synapse pro ověřování Azure AD** – podrobné pokyny najdete v tématu [připojení k SQL Database nebo azure synapse pomocí Azure Active Directory ověřování](sql-database-aad-authentication.md).
4. **Stáhněte si SSMS** – v klientském počítači stáhněte nejnovější verzi SSMS, ze [Stažení SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pro všechny funkce v tomto tématu použijte minimálně červenec 2017, verze 17,2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Připojení pomocí univerzálního ověřování pomocí SSMS

Následující kroky ukazují, jak se připojit k SQL Database nebo SAzure synapse pomocí nejnovějšího SSMS.

1. Pokud se chcete připojit pomocí univerzálního ověřování, vyberte v dialogovém okně **připojit k serveru** možnost **Active Directory – univerzální s podporou vícefaktorového**ověřování. (Pokud se zobrazí **univerzální ověřování služby Active Directory** , nejste na nejnovější verzi SSMS.)  
   ![1mfa – Universal-Connect][1]  
2. Do pole **uživatelské jméno** zadejte přihlašovací údaje Azure Active Directory ve formátu `user_name@domain.com` .  
   ![1mfa – Universal-Connect – uživatel](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Pokud se připojujete jako uživatel typu Host, už nemusíte pro uživatele typu Host doplňovat pole název domény služby AD nebo ID tenanta, protože SSMS 18. x nebo novější ho automaticky rozpoznává. Další informace najdete v tématu [univerzální ověřování pomocí SQL Database a Azure synapse (podpora SSMS pro MFA)](sql-database-ssms-mfa-authentication.md).
   ![MFA – No – tenant – SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Pokud se však připojujete jako uživatel typu Host pomocí SSMS 17. x nebo staršího, musíte kliknout na možnost **Možnosti**a v dialogovém okně **Vlastnosti připojení** vyplnit pole **název domény služby AD nebo ID tenanta** .
   ![MFA – tenant – SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Obvyklým způsobem pro SQL Database a Azure synapse musíte kliknout na **Možnosti možnosti** a zadat databázi v dialogovém okně **Možnosti** . (Pokud je připojený uživatel uživatel typu Host (tj. joe@outlook.com ), musíte zaškrtnout políčko a jako součást možností přidat aktuální název domény služby AD nebo ID tenanta. Podívejte [se na téma Univerzální ověřování pomocí SQL Database a Azure synapse (podpora SSMS pro MFA)](sql-database-ssms-mfa-authentication.md). Pak klikněte na **Connect** (Připojit).  
5. Po zobrazení dialogového okna **Přihlásit se k účtu** zadejte účet a heslo vaší Azure Active Directory identity. Pokud je uživatel součástí domény federované se službou Azure AD, není nutné žádné heslo.  
   ![2mfa – přihlášení][2]  

   > [!NOTE]
   > Pro univerzální ověřování pomocí účtu, který nepotřebuje MFA, se v tomto okamžiku připojíte. Pro uživatele, kteří vyžadují MFA, pokračujte následujícími kroky:
   >  
   
6. Můžou se zobrazit dvě dialogová okna pro nastavení vícefaktorového ověřování. Tato jednorázová operace závisí na nastavení správce MFA, a proto může být volitelná. V případě domény s povolenou MFA je tento krok někdy předem definovaný (například doména vyžaduje, aby uživatelé používali čipovou kartu a PIN kód).  
   ![3mfa – instalace][3]  
7. Druhý možný jednorázový dialog umožňuje vybrat podrobnosti metody ověřování. Možné možnosti jsou nakonfigurovány vaším správcem.  
   ![4mfa-ověřit-1][4]  
8. Azure Active Directory odesílá potvrzující informace. Když obdržíte ověřovací kód, zadejte ho do pole **zadat ověřovací kód** a klikněte na **Přihlásit**se.  
   ![5mfa-ověřit – 2][5]  

Po dokončení ověření se SSMS připojuje obvykle k platným přihlašovacím údajům a přístup k bráně firewall.

## <a name="next-steps"></a>Další kroky

- Přehled Azure SQL Database Multi-Factor Authentication najdete v tématu univerzální ověřování pomocí [SQL Database a Azure synapse (podpora pro MFA)](sql-database-ssms-mfa-authentication.md).  
- Udělit ostatním přístup k vaší databázi: [SQL Database ověřování a autorizace: udělení přístupu](sql-database-manage-logins.md)  
- Zajistěte, aby se uživatelé mohli připojit přes bránu firewall: [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí Azure Portal](sql-database-configure-firewall-settings.md)  
- Při používání **služby Active Directory – univerzální s** ověřováním MFA je k dispozici trasování ADAL od [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ve výchozím nastavení můžete trasování ADAL zapnout pomocí nabídky **nástroje**, **Možnosti** , v části **služby Azure**, **cloud Azure**, **ADAL okno výstup úroveň trasování**a následným povolením **výstupu** v nabídce **zobrazení** . Trasování jsou k dispozici v okně výstup při výběru **možnosti Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

