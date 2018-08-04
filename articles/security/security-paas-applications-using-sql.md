---
title: Zabezpečení databáze PaaS v Azure | Dokumentace Microsoftu
description: " Další informace o zabezpečení Azure SQL Database a SQL Data Warehouse osvědčené postupy pro zabezpečení vašich webových a mobilních aplikací PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: ee606540bef47b11ad8fd9e820af2f5b51d47b0b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493017"
---
# <a name="securing-paas-databases-in-azure"></a>Zabezpečení databáze PaaS v Azure

V tomto článku se podíváme na kolekci [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) osvědčené postupy zabezpečení pro zabezpečení vašich webových a mobilních aplikací PaaS. Tyto osvědčené postupy jsou odvozeny z našich zkušenostech s Azure a prostředí zákazníků, jako sami.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database a SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) poskytuje relační databázovou službu pro aplikace založené na Internetu. Podívejme se na služby, které pomáhají chránit vaše aplikace a data při použití Azure SQL Database a SQL Data Warehouse v PaaS nasazení:

- Ověřování pomocí Azure Active Directory (místo ověřování systému SQL Server)
- Brána firewall služby Azure SQL
- Transparentní šifrování dat (TDE)

## <a name="best-practices"></a>Osvědčené postupy

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Použití úložiště centralizovaných identit pro ověřování a autorizace

Azure SQL Database můžete nakonfigurovat použít jeden ze dvou typů ověřování:

- **Ověřování SQL** používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů, můžete ověřovat k jakékoli databázi na daném serveru jako vlastník databáze.

- **Ověřování pomocí Azure Active Directory** používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. Pokud chcete používat ověřování služby Azure Active Directory, musíte vytvořit jiného správce serveru, volá se "Azure AD admin" a který smí spravovat uživatele Azure AD a skupiny. Tento správce také smí provádět všechny operace jako běžný správce serveru.

[Ověřování pomocí Azure Active Directory](../active-directory/develop/authentication-scenarios.md) virtuálních sítí je mechanismus připojení k Azure SQL Database a SQL Data Warehouse s využitím identit v Azure Active Directory (AD). Azure AD poskytuje alternativu k ověřování serveru SQL Server, můžete zastavit růst počtu identit uživatelů více databázových serverů. Ověřování Azure AD umožňuje centrálně spravovat identity uživatelů databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID zajišťuje centrální místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.  

Výhody používání ověřování Azure AD namísto ověřování SQL patří:

- Umožňuje rotace hesla na jednom místě.
- Spravuje oprávnění k databázi pomocí Azure externí skupiny AD.
- Eliminuje ukládat tím, že integrované ověřování Windows a další formy ověřování podporovaných službou Azure AD.
- Uživatelé databáze k ověření identity na úrovni databáze s omezením použití.
- Podporuje ověřování založené na tokenech pro aplikace, připojení k SQL Database.
- Podporuje místní služby Azure AD bez synchronizace domény služby AD FS (federation domény) nebo ověřování nativní uživatele a hesla.
- Podporuje připojení z SQL Server Management Studio, které pomocí univerzálního ověřování Active Directory, která zahrnuje [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). Vícefaktorové ověřování zahrnuje silné ověřování s řadou jednoduchých možností – telefonní hovor, textová zpráva, čipové karty s PIN kódu nebo oznámení přes mobilní aplikaci. Další informace najdete v tématu [podpora nástroje SSMS pro ověřování Azure AD MFA s SQL Database a SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Další informace o ověřování Azure AD, najdete v tématech:

- [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Ověřování do Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Podpora ověřování na základě token pro službu Azure SQL DB pomocí ověřování Azure AD](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Ujistěte se, že Azure Active Directory je vhodné pro vaše prostředí, najdete v článku [funkcí Azure AD a omezení](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), konkrétně další aspekty.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Omezení přístupu na základě IP adresy
Můžete vytvořit pravidla brány firewall určující rozsahy přípustných IP adres. Tato pravidla lze zacílit na úrovni serveru a databáze. Doporučujeme používat pravidla brány firewall na úrovni databáze kdykoli je to možné, pro zvýšení zabezpečení a přenositelnosti databáze. Pravidla brány firewall na úrovni serveru jsou nejvhodnější pro správce a pokud máte mnoho databází se stejnými požadavky na přístup, ale nechcete ztrácet čas konfigurací jednotlivých databází.

Omezení IP adres v databázi SQL výchozí zdroj povolit přístup z jakékoli Azure adresy (včetně jiných předplatných a tenantů). Můžete omezit tím povolíte jenom IP adres pro přístup k instanci. I přes brány SQL firewall a omezení podle IP adresy je stále potřeba silné ověřování. Zobrazte doporučení výše v tomto článku.

Další informace o omezení brány Firewall SQL Azure a IP, naleznete v tématu:

- [Řízení přístupu služby Azure SQL Database](../sql-database/sql-database-control-access.md)
- [Konfigurace pravidel brány firewall Azure SQL Database – přehled](../sql-database/sql-database-firewall-configure.md)
- [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí webu Azure portal](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat
[Transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/azure/bb934049) je ve výchozím nastavení povolené. Transparentní šifrování dat transparentně šifruje systému SQL Server, Azure SQL Database a Azure SQL Data Warehouse dat a souborů protokolu. Transparentní šifrování dat chrání proti ohrožení zabezpečení přímý přístup k souborům a jejich zálohování. To umožňuje šifrování neaktivních uložených dat bez změny konfigurace existujících aplikací. Transparentní šifrování dat by mělo zůstat vždy povolena, to ale nezpůsobí ukončení útočník pomocí cesty normální přístup. Transparentní šifrování dat umožňuje v souladu s mnoha zákonům, předpisům a pokyny uvedenými v různých oborech.

Azure SQL pro transparentní šifrování dat spravuje klíče související problémy. Jak se TDE, místní speciální musí být dbát na možnosti obnovení a při přesunu databází. Ve složitějších scénářích klíče můžete explicitně spravovat ve službě Azure Key Vault pomocí rozšiřitelná Správa klíčů (viz [povolení šifrování TDE u SQL serveru pomocí EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Také díky tomu pro používání Your Own Key (BYOK) přes funkci BYOK služby Azure Key trezory.

Azure SQL, zajišťuje šifrování pro sloupce prostřednictvím [s funkcí Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). To umožňuje jenom autorizované aplikace přístup k citlivým sloupce. Pomocí tohoto typu šifrování omezuje dotazy SQL pro šifrované sloupce na základě rovnosti hodnot.

Šifrování na úrovni aplikace by měla sloužit i pro vybrané údaje. Aspekty suverenita dat někdy dají zmírnit tím, že šifruje data pomocí klíče, který je uložen ve správné zemi. Přenos dat i náhodnému zabrání způsobí chybu, protože jde o neuskutečnitelnou k dešifrování dat bez klíče, za předpokladu, že se používá silný algoritmus (například AES 256).

Další bezpečnostní opatření slouží k zabezpečení databáze, jako jsou návrh zabezpečení systému, šifrovat důvěrné prostředky a vytváření brány firewall kolem databázové servery.

## <a name="next-steps"></a>Další postup
V tomto článku jste se seznámili s kolekce SQL Database a SQL Data Warehouse osvědčené postupy zabezpečení pro zabezpečení vašich webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení PaaS webových a mobilních aplikací pomocí služby Azure App Services](security-paas-applications-using-app-services.md)
