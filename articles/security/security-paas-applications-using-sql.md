---
title: Zabezpečení databáze PaaS v Azure | Microsoft Docs
description: " Další informace o zabezpečení Azure SQL Database a SQL Data Warehouse osvědčené postupy pro zabezpečení vašich PaaS webové a mobilní aplikace. "
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
ms.openlocfilehash: 3e7dc4dfba001228a4d11e2b21cdeed8e7af45ac
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794867"
---
# <a name="securing-paas-databases-in-azure"></a>Zabezpečení databáze PaaS v Azure

V tomto článku probereme kolekce [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Tyto doporučené postupy jsou odvozeny od našich zkušeností s Azure a prostředí zákazníků jako sami.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database a SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) zadejte služba relační databáze pro aplikace založené na Internetu. Podívejme se na služby, které pomáhají chránit vaše aplikace a data při použití Azure SQL Database a SQL Data Warehouse v nasazení PaaS:

- Ověřování Azure Active Directory (namísto ověřování serveru SQL Server)
- Brány firewall pro Azure SQL
- Transparentní šifrování dat (šifrování TDE)

## <a name="best-practices"></a>Osvědčené postupy

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Používání úložiště v centrální identity pro ověřování a autorizaci

Databáze Azure SQL jde použít jednu z dva typy ověřování:

- **Ověřování SQL** používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů, můžete ověřovat pro všechny databáze na tomto serveru jako vlastník databáze.

- **Ověřování služby Azure Active Directory** používá identity spravované službou Azure Active Directory a je podporována pro spravované a integrované domén. Pokud chcete používat ověřování Azure Active Directory, musíte vytvořit jiný správce serveru se nazývá "admin Azure AD," což je povoleno ke správě Azure AD Uživatelé a skupiny. Tento správce také smí provádět všechny operace jako běžný správce serveru.

[Ověřování Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) mechanismus, připojení k Azure SQL Database a SQL Data Warehouse pomocí identit v Azure Active Directory (AD). Azure AD poskytuje alternativu k ověřování serveru SQL Server, můžete zastavit šíření identit uživatelů mezi servery databáze. Ověřování služby Azure AD umožňuje centrálně spravovat identity uživatelů a další služby Microsoftu v jednom centrálním místě. Centrální správa ID poskytuje jednotné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.  

Výhody používání ověřování Azure AD místo ověřování SQL patří:

- Umožňuje otočení heslo na jednom místě.
- Spravuje oprávnění databáze pomocí externí Azure skupiny AD.
- Eliminuje ukládání hesel povolením integrované ověřování systému Windows a jiných forem ověřování podporovaných službou Azure AD.
- Uživatelé databáze obsažené používá k ověření identity na úrovni databáze.
- Podporuje ověřování na základě tokenu pro aplikace, připojení k databázi SQL.
- Podporuje služby AD FS (federation domény) nebo ověřování nativní uživatele a heslo pro místní služby Azure AD bez synchronizace domény.
- Podporuje připojení z SQL Server Management Studio, které používají Universal ověřování služby Active Directory, která zahrnuje [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). Vícefaktorové ověřování zahrnuje silné ověřování s celou řadu možností snadno ověření – telefonní hovor, textová zpráva, čipové karty s PIN kód nebo oznámení mobilní aplikace. Další informace najdete v tématu [SSMS podpora pro Azure AD MFA s SQL Database a SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Další informace o ověřování Azure AD najdete v tématu:

- [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Ověřování do Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Podpora ověřování na základě tokenu pro databázi Azure SQL pomocí ověřování Azure AD](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Pro zajištění, že Azure Active Directory je vhodné pro vaše prostředí, naleznete v [funkce Azure AD a omezení](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), konkrétně další aspekty.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Omezení přístupu na základě IP adresy
Můžete vytvořit pravidla brány firewall, které zadejte rozsahy IP adres, přijatelný. Tato pravidla je možné cílit na úrovních serveru a databáze. Doporučujeme používat pravidla brány firewall na úrovni databáze vždy, když je možné zvýšit zabezpečení a vytvořte více přenositelných vaši databázi. Pravidla brány firewall na úrovni serveru jsou nejvhodnější pro správce a pokud máte mnoho databází, které mají stejné požadavky na přístup, ale nechcete času konfigurace jednotlivě každou databázi.

Omezení podle IP adresy SQL Database výchozí zdroj povolit přístup z libovolné Azure adresy (včetně jiných předplatných a klienti). Můžete omezit tím možnost Povolit jenom IP adresy pro přístup k instanci. I přes brány firewall pro SQL a omezení podle IP adresy je stále nutné silné ověřování. Najdete doporučení, která se dříve v tomto článku.

Další informace o omezení brány Firewall SQL Azure a IP adresy najdete v tématu:

- [Řízení přístupu Azure SQL Database](../sql-database/sql-database-control-access.md)
- [Konfigurace pravidel brány firewall databáze Azure SQL Database – přehled](../sql-database/sql-database-firewall-configure.md)
- [Konfigurace pravidla brány firewall na úrovni serveru Azure SQL Database pomocí portálu Azure](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat
[Transparentní šifrování šifrování dat (TDE)](https://msdn.microsoft.com/library/azure/bb934049) je ve výchozím nastavení povolené. Šifrování TDE transparentně šifruje SQL Server, databáze SQL Azure a Azure SQL Data Warehouse data a soubory protokolu. Šifrování TDE chrání před ohrožení přímý přístup k souborům a jejich zálohování. To umožňuje šifrovat data umístěná beze změny stávajících aplikací. Šifrování TDE by měla zůstat vždy povoleno; To však neukončí útočník pomocí cesty normální přístup. Šifrování TDE poskytuje možnost pro dosažení souladu s mnoha právní a pokyny uvedenými v různých odvětví.

Azure SQL spravuje o problémech souvisejících s klíče pro šifrování TDE. Jak se šifrování TDE, místní musí dát zvláštní pozor zajistit obnovitelnost a při přesunu databází. Ve složitějších scénářích klíče se dají explicitně spravovat Azure Key Vault prostřednictvím ekm (viz [povolit šifrování TDE na SQL serveru pomocí EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). To také umožňuje prostřednictvím funkce Azure klíč trezory BYOK pro přineste si vlastní klíč (BYOK).

Azure SQL zajišťuje šifrování pro sloupce prostřednictvím [vždy šifrována](/sql/relational-databases/security/encryption/always-encrypted-database-engine). To umožňuje pouze autorizovaných aplikací přístup k citlivým sloupce. Tento druh šifrování pomocí omezuje dotazy SQL pro šifrované sloupce na základě rovnosti hodnot.

Šifrování na úrovni aplikace by měla být používána pro vybrané údaje. Otázky suverenity dat. může být někdy zmírnit šifrování dat s klíčem, který je uložen v správnou zemi. Přenos dat i náhodných zabrání způsobující problém, protože není možné dešifrovat data bez klíče, za předpokladu, že se používá silný algoritmus (jako je AES 256).

Abyste pomohli zabezpečit databázi například návrhu zabezpečení systému, šifrování důvěrné prostředky a vytváření brány firewall kolem databázové servery, můžete použít další bezpečnostní opatření.

## <a name="next-steps"></a>Další postup
Tento článek seznámili kolekce SQL Database a SQL Data Warehouse osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Další informace o zabezpečení vašich PaaS nasazení najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení PaaS webové a mobilní aplikace pomocí Azure App Services](security-paas-applications-using-app-services.md)
