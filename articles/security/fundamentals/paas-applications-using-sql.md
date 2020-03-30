---
title: Zabezpečení databází PaaS v Azure | Dokumenty společnosti Microsoft
description: 'Seznamte se s doporučenými postupy zabezpečení Azure SQL Database a SQL Data Warehouse pro zabezpečení webových a mobilních aplikací PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124996"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Doporučené postupy pro zabezpečení databází PaaS v Azure

V tomto článku se zabýváme kolekcí doporučených postupů zabezpečení [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) a SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pro zabezpečení webových a mobilních aplikací platformy jako služby (PaaS). Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Azure a zkušeností zákazníků, jako jste vy.

Azure SQL Database a SQL Data Warehouse poskytují relační databázovou službu pro vaše internetové aplikace. Podívejme se na služby, které pomáhají chránit vaše aplikace a data při používání Azure SQL Database a SQL Data Warehouse v nasazení PaaS:

- Ověřování azure služby Active Directory (místo ověřování serveru SQL Server)
- Brána firewall Azure SQL
- Transparentní šifrování dat (TDE)

## <a name="use-a-centralized-identity-repository"></a>Použití centralizovaného úložiště identit
Databáze Azure SQL lze nakonfigurovat tak, aby používaly jeden ze dvou typů ověřování:

- **Ověřování SQL** používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto pověření, můžete ověřit do libovolné databáze na tomto serveru jako vlastník databáze.

- **Ověřování Azure Active Directory** používá identity spravované službou Azure Active Directory a je podporované pro spravované a integrované domény. Pokud chcete používat Azure Active Directory Authentication, musíte vytvořit jiného správce serveru s názvem "Správce Azure AD", který má povolenou správu uživatelů a skupin Azure AD. Tento správce také smí provádět všechny operace jako běžný správce serveru.

[Azure Active Directory ověřování](../../active-directory/develop/authentication-scenarios.md) je mechanismus připojení k Azure SQL Database a SQL Data Warehouse pomocí identit ve službě Azure Active Directory (AD). Azure AD poskytuje alternativu k ověřování serveru SQL Server, takže můžete zastavit šíření identit uživatelů napříč databázovými servery. Ověřování Azure AD umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Výhody používání Azure AD namísto ověřování SQL
- Umožňuje střídání hesel na jednom místě.
- Spravuje oprávnění databáze pomocí externích skupin Azure AD.
- Eliminuje ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování podporovaných službou Azure AD.
- Používá obsažené uživatele databáze k ověřování identit na úrovni databáze.
- Podporuje ověřování založené na tokenech pro aplikace, které se připojují k databázi SQL.
- Podporuje federaci domény se službou ADFS (ADFS) nebo nativním ověřováním uživatelů/hesel pro místní službu Azure AD bez synchronizace domény.
- Podporuje připojení ze služby SQL Server Management Studio, která používají univerzální ověřování služby Active Directory, které zahrnuje [vícefaktorové ověřování (MFA).](/azure/active-directory/authentication/multi-factor-authentication) MFA zahrnuje silné ověřování s využitím celé řady možností ověření – telefonických hovorů, textových zpráv, čipových karet s kódem PIN nebo oznámení mobilní aplikace. Další informace naleznete [v tématu Univerzální ověřování pomocí databáze SQL a datového skladu SQL](../../sql-database/sql-database-ssms-mfa-authentication.md).

Další informace o ověřování Azure AD najdete v tématu:

- [Použití ověřování azure služby Active Directory pro ověřování pomocí databáze SQL, spravované instance nebo datového skladu SQL](../../sql-database/sql-database-aad-authentication.md)
- [Ověřování do Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Podpora ověřování založená na tokenech pro Azure SQL DB pomocí ověřování Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Pokud chcete zajistit, aby služba Azure Active Directory vyhovovala vašemu prostředí, přečtěte si informace [o funkcích a omezeních služby Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Omezení přístupu na základě IP adresy
Můžete vytvořit pravidla brány firewall, která určují rozsahy přijatelných adres IP. Tato pravidla mohou být zaměřena na server i databázi. Doporučujeme používat pravidla brány firewall na úrovni databáze, kdykoli je to možné, aby se zvýšila bezpečnost a aby byla databáze přenosnější. Pravidla brány firewall na úrovni serveru se nejlépe používají pro správce a pokud máte mnoho databází, které mají stejné požadavky na přístup, ale nechcete trávit čas konfigurací jednotlivých databází jednotlivě.

Výchozí omezení zdrojové IP adresy SQL Database umožňují přístup z libovolné adresy Azure, včetně jiných předplatných a klientů. Můžete to omezit tak, aby přístup k instanci umožňoval pouze vašim IP adresám. I s omezením brány firewall SQL a IP adres je stále zapotřebí silné houštění. Podívejte se na doporučení uvedená dříve v tomto článku.

Další informace o azure sql firewalla a omezení IP adres, najdete v tématu:

- [Řízení přístupu k Azure SQL Database a SQL Data Warehouse](../../sql-database/sql-database-manage-logins.md)
- [Pravidla brány firewall služeb Azure SQL Database a SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Šifrování neaktivních uložených dat
[Transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) je ve výchozím nastavení povoleno. TDE transparentně šifruje SQL Server, Azure SQL Database a Azure SQL Data Warehouse data a soubory protokolu. TDE chrání před ohrožením přímého přístupu k souborům nebo jejich zálohování. To umožňuje šifrovat data v klidovém stavu bez změny stávajících aplikací. TDE by měla zůstat vždy povolena; To však nezastaví útočníka pomocí normální přístupové cesty. TDE poskytuje schopnost dodržovat mnoho zákonů, předpisů a pokynů stanovených v různých průmyslových odvětvích.

Azure SQL spravuje klíčové související problémy pro TDE. Stejně jako u TDE, místní zvláštní péče musí být věnována zajištění obnovitelnosti a při přesouvání databází. V složitějších scénářích lze klíče explicitně spravovat v azure key vault prostřednictvím rozšiřitelné správy klíčů. Viz [Povolení TDE na serveru SQL Server pomocí EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). To také umožňuje bring your own key (BYOK) prostřednictvím azure key trezory BYOK možnost.

Azure SQL poskytuje šifrování pro sloupce prostřednictvím [vždy šifrované](/sql/relational-databases/security/encryption/always-encrypted-database-engine). To umožňuje přístup pouze oprávněným aplikacím k citlivým sloupcům. Použití tohoto druhu šifrování omezuje dotazy SQL pro šifrované sloupce na hodnoty založené na rovnosti.

Šifrování na úrovni aplikace by mělo být použito také pro selektivní data. Obavy o suverenitu dat lze někdy zmírnit šifrováním dat pomocí klíče, který je uložen ve správné zemi nebo oblasti. Tím se zabrání tomu, aby i náhodný přenos dat způsobil problém, protože není možné dešifrovat data bez klíče, za předpokladu, že je použit silný algoritmus (například AES 256).

Další opatření můžete použít k zabezpečení databáze, jako je například návrh zabezpečeného systému, šifrování důvěrných datových zdrojů a vytvoření brány firewall kolem databázových serverů.

## <a name="next-steps"></a>Další kroky
Tento článek vás seznámil s kolekcí doporučených postupů zabezpečení SQL Database a SQL Data Warehouse pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení webových a mobilních aplikací PaaS pomocí služby Azure App Services](paas-applications-using-app-services.md)
