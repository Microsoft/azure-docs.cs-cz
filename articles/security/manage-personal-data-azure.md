---
title: "Správa osobních údajů v Microsoft Azure | Microsoft Docs"
description: "Opravte, aktualizaci, odstranění a exportovat osobní údaje, můžete v souladu s obecné Data Protection nařízení (GDPR) v Azure Active Directory a Azure SQL Database"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: 41c0cc4eb3697aa79abeabddc98a84598ce4ea50
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Správa osobních údajů v Microsoft Azure

Tento článek obsahuje pokyny o tom, jak opravit, aktualizovat, odstranit a exportujte osobních údajů pro dosažení souladu s obecné Data Protection nařízení (GDPR) v Azure Active Directory a Azure SQL Database.

## <a name="scenario"></a>Scénář

Na základě Dublin společnost poskytuje centrální místo pro výkonných cílové svatby v Irsku a po celém světě pro obě místní i mezinárodní zákazníka základní. Mají pobočky, zákazníky, zaměstnanci a dodavatelé umístěné po celém světě plně služby místa, které nabízejí.

Mezi mnoha dalším položkám uchovává informace společnosti o RSVPs, které zahrnují alergií a podávání předvolby. Hosté svatební můžete zaregistrovat pro různé aktivity, například na koni JÍZDA, procházení webu, lodních lyžovačku atd. a dokonce vzájemné interakce na webové stránce Centrální během měsíců vedoucích k události. Společnost shromažďuje ze zaměstnanců, dodavatelů, zákazníků a svatební hosté osobní údaje. Z důvodu mezinárodní povahu podnikání společnosti musí být v souladu s více úrovněmi nařízení.

## <a name="problem-statement"></a>Popis problému

- Data správců musí být schopen správné nesprávné informace a aktualizace neúplný nebo změna osobní informace.

- Data správců musí být schopen odstranit osobní informace na žádost subjektu data.

- Data správců je nutné exportovat data a zadejte předmět data ve formátu běžné, strukturovaných při jeho žádost.

## <a name="company-goals"></a>Cíle společnosti

- Nesprávné nebo neúplné zákazníka, svatební hosta, zaměstnance a dodavatele osobní údaje, musí být opraveny nebo aktualizovány v Azure Active Directory a Azure SQL Database.

- Osobní údaje třeba jej odstranit v Azure Active Directory a Azure SQL Database na žádost subjektu data.

- Osobní data musí být exportován ve formátu běžné, strukturovaných při žádosti o subjektu data.

## <a name="solutions"></a>Řešení

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: Tato napravila nebo opravte nesprávné nebo neúplné osobní údaje a vymazání a odstranění dat nebo uživatelských profilů

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft založená na cloudu, víceklientské adresáře a identity management service.
Opravte, aktualizovat nebo odstranit zákazníků a profily uživatelů a pracovní informace o uživateli, které obsahují osobní údaje, jako je například název, název pracovní, adresa nebo telefonní číslo uživatele v vaše [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) prostředí pomocí [portál Azure](https://portal.azure.com/).

Přihlaste se pod účtem, který je globální správce adresáře.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Jak opravit nebo aktualizovat uživatelský profil a pracovní informace v Azure Active Directory?

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.

2. Vyberte **všechny služby**, zadejte **uživatelů a skupin** v textovém poli a potom vyberte **Enter**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. Na **uživatelů a skupin** vyberte **uživatelé**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. Na **uživatelé a skupiny - Uživatelé** okně vyberte uživatele ze seznamu a poté v okně pro vybraného uživatele, vyberte možnost **profil** zobrazíte informace o profilu uživatele, které musí být opraveny nebo aktualizovat.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Opravte nebo aktualizujte informace a pak na panelu příkazů vyberte **uložit.**

6.  V okně pro vybraného uživatele, vyberte **informace o pracovních** zobrazíte informace o práci uživatele vyžadující opravu či aktualizovat.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Opravte nebo aktualizovat informace o práci uživatele a pak na panelu příkazů vyberte **uložit.**

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Jak lze odstranit profil uživatele v Azure Active Directory?

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.

2. Vyberte **všechny služby**, zadejte **uživatelů a skupin** v textovém poli a potom vyberte **Enter**.

    ![](media/manage-personal-data-azure/image001.png)

3. Na **uživatelů a skupin** vyberte **uživatelé**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. V okně **Uživatelé a skupiny – Uživatelé** vyberte uživatele ze seznamu.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. V okně pro vybraného uživatele, vyberte **přehled**a potom na panelu příkazů vyberte **odstranit**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database: nápravě nebo opravte nesprávné nebo neúplné osobní údaje; smazání nebo odstranění osobních údajů; Exportovat osobní údaje 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) je databáze cloudu, která pomáhá vývojářům vytvářet a udržovat aplikace.

Osobní data můžete aktualizovat v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) pomocí standardní dotazy SQL a můžete také odstranit. Kromě toho je možné exportovat osobní data z databáze SQL pomocí různých metod, včetně serveru SQL Azure import a export průvodce a v různých formátech, včetně souboru BACPAC souboru.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Jak opravit, aktualizovat a vymazat osobní data v databázi SQL?

Zjistěte, jak odstranit nebo aktualizovat osobní data v databázi SQL, najdete [aktualizace (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [aktualizovat Text](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [aktualizace pomocí výrazu běžné tabulky](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql), nebo [Aktualizovat zápisu Text](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) dokumentaci.

Zjistěte, jak odstranit osobní data v databázi SQL, najdete [odstranit (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) dokumentaci.

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Jak exportovat osobní data do souboru BACPAC souboru v databázi SQL?

Soubor souboru BACPAC obsahuje data databáze SQL a metadata a je soubor zip s příponou souboru BACPAC. To lze provést pomocí [portál Azure](https://portal.azure.com/), SQLPackage nástroj příkazového řádku, SQL Server Management Studio (SSMS) nebo prostředí PowerShell.

Zjistěte, jak exportovat data do souboru BACPAC souboru, najdete [exportovat do souboru BACPAC souboru Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-export) stránku, která obsahuje podrobné pokyny pro jednotlivé metody uvedené výše.

Jak exportovat osobní data z SQL Database přes SQL Server importovat a exportovat Průvodce?

Tento průvodce umožňuje kopírování dat ze zdrojového do cílového umístění. Úvod do průvodce, včetně toho, jak se dá stáhnout, informace o oprávnění a jak získat nápovědu k nástroji, přejděte [Import a Export dat s Microsoft SQL Server importovat a exportovat průvodce](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) webové stránky.

Přehled kroků průvodce, přejděte [kroky v Průvodci Export a Import serveru SQL](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) webové stránky.

## <a name="next-steps"></a>Další kroky:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

