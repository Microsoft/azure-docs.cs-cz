---
title: "Zjistit, identifikovat a klasifikovat osobních údajů v Microsoft Azure | Microsoft Docs"
description: "vyhledávání, klasifikace, zjišťování a identifikaci dat v Azure mohou pomoci v souladu s obecné Data Protection nařízení (GDPR)"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 21308faf5d3efd3908eccab4f37acb0cfccdcb8e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Zjistit, identifikovat a klasifikovat osobních údajů v Microsoft Azure

Tento článek obsahuje pokyny o tom, jak zjistit, identifikovat a klasifikovat osobních údajů v několik nástrojů Azure a službami, včetně pomocí Azure Data Catalog, Azure Active Directory, SQL Database, Power Query pro clustery systému Hadoop v Azure HDInsight, Azure Information Protection, Azure Search a SQL dotazy pro Azure Cosmos DB. Tyto procesy a nástroje lze použít k zajištění souladu s obecné Data Protection nařízení (GDPR)

## <a name="scenario-problem-statement-and-goal"></a>Scénář, formulace problému a cíle

Na základě USA sportu společnosti shromažďuje řadu osobní a další data. To zahrnuje zákazníků a údaje o zaměstnancích. Společnost udržuje ho v několika databází a ukládá je v několika různých místech v jejich prostředí Azure. Kromě prodejní zařízení sportu, také hostitele a spravovat registraci pro Elitní sportovní události po celém světě, včetně EU, a v některých případech shromážděná data zákazníků zahrnuje lékařské informace.

Vzhledem k tomu, že společnost hostuje mnoho mezinárodní bicycling prohlídka každý rok a má externí pracovníky v umístěních po celém světě, je poměrně velké několik datových sad. Společnost má také vytvořené vývojáře aplikací, které jsou používány zákazníky a zaměstnanci.

Společnost chce vyřešit tyto problémy:

- Osobní data zákazníků a musí být klasifikované nebo rozlišující z jiných dat společnosti shromažďuje, aby se zajistilo správné přístupu a zabezpečení.
- Správce dat musí snadno zjistit umístění zákazníka osobních údajů v různých oblastech prostředí Azure.
- Zákazníků a osobní data, která se zobrazí v sdílené dokumenty a e-mailovou komunikaci, musí být označeny k zajištění, aby se zachovala zabezpečené.
- Vývojáři aplikací společnosti potřebují způsob, jak snadno vyhledejte osobní data zákazníků a v jejich webových a mobilních aplikací.
- Vývojáři taky potřebujete k dotazování databáze jejich dokumentu pro osobní data.

### <a name="company-goals"></a>Cíle společnosti

- Všechny osobní data zákazníků a musí být označené/opatřeny poznámkami v Azure Data Catalog, proto je možné snadno najít. V ideálním případě osobní data zákazníků a jsou označené poznámkou samostatně.
- Osobních dat od zákazníků a profily uživatelů a pracovní informace, které se nacházejí v Azure Active Directory musí být snadno najít.
- Osobní údaje, které se nacházejí v několika databází SQL musí být snadno dotazována. 
- Některé společnosti velkých datových sad jsou spravované prostřednictvím Azure HDInsight a uložené v Hadoop. Budou se musí importovat do aplikace Excel, může být dotazován pro osobní data.
- Osobní údaje, které jsou sdíleny v dokumentů a e-mailovou komunikaci musí být klasifikované, s názvem bez přípony a zabezpečen s Azure Information Protection.
- Vývojáři aplikací společnosti musí být schopen zjistit osobní data zákazníků a v aplikacích, které jste vytvořili jejich, které můžete provést s Azure Search.
- Vývojáři musí být schopna nalézt osobní data v databázi jejich dokumentu.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: Data zjišťování

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft založená na cloudu, víceklientské adresáře a identity management service. Můžete vyhledat zákazníka a samotnými zaměstnanci uživatelských profilů a pracovní informace o uživateli, které obsahují osobní údaje v vaše [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) prostředí pomocí [portál Azure](https://portal.azure.com/).

To je zvláště užitečné, pokud chcete najít, nebo změňte osobních údajů pro konkrétního uživatele. Můžete také přidat nebo změnit uživatelský profil a informace o práci. Přihlaste se pod účtem, který je globální správce adresáře.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Jak najít nebo zobrazit uživatelský profil a informace o práci?

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.

2. Vyberte **všechny služby**, zadejte **uživatelů a skupin** v textovém poli a potom vyberte **Enter**.

   ![jak najít profil uživatele a informace o práci](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. Na **uživatelů a skupin** vyberte **uživatelé**.

      ![Otevírání uživatelů a skupin](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. Na **uživatelé a skupiny - Uživatelé** okně vyberte uživatele ze seznamu a poté v okně pro vybraného uživatele, vyberte možnost **profil** zobrazíte informace o profilu uživatele, který může obsahovat osobní data.

      ![Vyberte uživatele](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Pokud potřebujete přidat nebo změnit informace o profilu uživatele, můžete tak učinit a pak na panelu příkazů vyberte **uložit.**
6. V okně pro vybraného uživatele, vyberte **informace o pracovních** zobrazíte informace o práci uživatele, který může obsahovat osobní data.

     ![informace o zobrazení zaměstnání](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Pokud potřebujete přidat nebo změnit informace o práci uživatelů, můžete tak učinit a pak na panelu příkazů vyberte **uložit.**

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database: Data zjišťování

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) je databáze cloudu, která pomáhá vývojářům vytvářet a udržovat aplikace. Osobní údaje lze nalézt v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) pomocí standardní dotazů SQL. Azure elastické dotazu SQL (preview) umožňuje uživatelům provádět mezi databázové dotazy.

Podrobné [databáze SQL](../sql-database/sql-database-technical-overview.md) kurz vysvětluje mnoho aspektů použití databáze systému SQL, včetně jak sestavit jeden a jak spouštět dotazy na data. Zde je souhrn informací, které jsou k dispozici v tomto kurzu s odkazy na určité části.

### <a name="how-do-i-build-a-sql-database"></a>Jak lze vytvořit databázi SQL?

Existují tři způsoby, jak provést:

- Azure SQL database lze vytvořit v [portál Azure](https://portal.azure.com/). V tomto kurzu budete používat konkrétní sadu výpočetní a úložnou kapacitu v rámci skupiny prostředků a logického serveru. Ukázková data z fiktivní společnosti AdventureWorks budete používat. Pokud vytvoříte pravidla brány firewall na úrovni serveru. Zjistěte, jak to udělat, najdete [vytvoření Azure SQL database na portálu Azure](../sql-database/sql-database-get-started-portal.md) kurzu.

  ![Vytvoření databáze Azure SQL](media/how-to-discover-classify-personal-data-azure/create-database.png)
- Databáze SQL můžete také vytvořit v [prostředí cloudu Azure](https://azure.microsoft.com/features/cloud-shell/) rozhraní příkazového řádku, nástroj příkazového řádku založené na prohlížeči. Nástroj je k dispozici na webu Azure portal a můžete spustit přímo z ní. V tomto kurzu spusťte nástroj, definovat skriptu proměnné, vytvořte skupinu prostředků a logického serveru a nakonfigurujte pravidlo brány firewall serveru. Potom můžete vytvořit databázi s ukázkovými daty. Naučte se vytvořit databázi tímto způsobem, najdete [vytvářet izolované databáze Azure SQL pomocí rozhraní příkazového řádku Azure](../sql-database/sql-database-get-started-cli.md) kurzu.

  ![Kurz CLIT](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Rozhraní příkazového řádku Azure se často používá Linux správci a vývojáři. Někteří uživatelé ji najít jednodušší a intuitivnější než prostředí PowerShell, což je třetí možnost.

- Nakonec můžete vytvořit databázi SQL pomocí prostředí PowerShell, což je nástroj příkazového řádku nebo skriptu použít k vytváření a správě Azure a dalším prostředkům. V tomto kurzu spusťte nástroj, definovat skriptu proměnné, vytvořte skupinu prostředků a logického serveru a nakonfigurujte pravidlo brány firewall serveru. Pak vytvoříte databázi s ukázkovými daty.

Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 4.0 nebo novější. Spusťte Get-Module - ListAvailable AzureRM najít vaší verzí. Pokud potřebujete k instalaci nebo upgradu, najdete v části modulu nainstalovat Azure PowerShell.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Naučte se vytvořit databázi tímto způsobem, najdete [vytvářet izolované databáze Azure SQL pomocí prostředí Powershell](../sql-database/sql-database-get-started-powershell.md) kurzu.

>[!Note]
Windows správci mohou pomocí prostředí PowerShell, ale některé z nich raději rozhraní příkazového řádku Azure.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Jak najít pro osobní data v databázi SQL na portálu Azure?

Nástroj editor integrovaného dotazu v portálu Azure slouží k vyhledání osobní data. Budete přihlášení k nástroji vaše přihlašovací jméno správce serveru SQL a heslo a potom zadejte dotaz.

  ![vyhledávání sql pomocí portálu](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

Krok 5 tohoto kurzu ukazuje příklad dotazu v podokně editor dotazů, ale není soustředit na osobních nebo citlivých informací (také zkombinuje data ze dvou tabulek a vytvoří aliasy pro zdrojový sloupec v datové sadě nebyl vrácen). Následující snímek obrazovky ukazuje dotaz z kroku 5 a také v podokně výsledků, která je vrácena:

  ![editor dotazů](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Pokud vaše databáze byla volána MyTable, ukázkový dotaz osobní informace může obsahovat název, číslo sociálního pojištění a identifikační číslo a bude vypadat takto:

"Vyberte název, číslo sociálního pojištění, ID číslo z MyTable"

By spuštění dotazu a pak zobrazte výsledky v **výsledky** podokně.

Další informace o tom, jak dotazu na SQL databázi na portálu Azure, najdete [dotazu SQL databázi](../sql-database/sql-database-get-started-portal.md) části kurzu.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Jak vyhledávat data napříč více databází?

Elastické dotazu SQL (preview) umožňuje provádět mezidatabázové a více dotazů databáze a vrátí jeden výsledek. [Přehled kurzu](../sql-database/sql-database-elastic-query-overview.md) obsahuje podrobný popis scénáře a najdete vysvětlení rozdílu mezi databáze svislého a vodorovného rozdělení do oddílů. Vodorovné rozdělení do oddílů se označuje jako "horizontálního dělení."

  ![Vertikální dělení](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![vodorovné rozdělení do oddílů](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Chcete-li začít, navštivte [přehled elastické dotazu Azure SQL Database (preview)](../sql-database/sql-database-elastic-query-overview.md) stránky.

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (pro import clusterů systému Azure HDInsight Hadoop): data zjišťování pro velké sady dat

Hadoop je typu open source Apache úložiště a zpracování služby pro velké sady dat, které jsou analyzovány a uložené v clusterů systému Hadoop. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) umožňuje uživatelům pracovat s clustery systému Hadoop v Azure. Power Query je doplněk aplikace Excel, mimo jiné, pomáhají uživatelům zjistit data z různých zdrojů.

Osobní data související s clustery systému Hadoop v prostředí Azure HDInsight lze importovat do aplikace Excel pomocí doplňku Power Query. Jakmile jsou data v aplikaci Excel můžete k identifikaci dotazu.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Použití doplňku Power Query pro Excel k importu clusterů systému Hadoop v prostředí Azure HDInsight do aplikace Excel

HDInsight kurz vás provede tento celý proces. Popisuje požadavky a zahrnuje odkaz [Začínáme s Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md) kurzu. Pokyny zahrnují Excel 2016 a také 2013 a 2010 (postup je mírně odlišný pro starší verze Excelu). Pokud nemáte doplňku Power Query aplikace Excel, tento kurz ukazuje, jak ji připravit. Kurz budete spustíte v aplikaci Excel a bude třeba mít účet úložiště objektů Blob Azure přidruženého k vašemu clusteru.

  ![Dotaz v aplikaci Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Zjistěte, jak to udělat, najdete [připojit Excel k systému Hadoop pomocí Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md) kurzu.

Zdroj: [připojit Excel k systému Hadoop pomocí Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: osobní data klasifikace pro dokumenty a e-mailu

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) můžete použít nápovědy Azure zákazníků popisky klasifikovat a chránit interně nebo externě sdílené dokumenty a e-mailu komunikace. Některé z těchto položek může obsahovat osobní údaje zákazníků nebo zaměstnanců. Pravidla a podmínky je možné definovat automaticky nebo ručně, správci nebo uživatelé. Například pokud uživatel je ukládání dokumentu, který obsahuje informace o kreditní kartě, potvrdí by zobrazí popisek doporučení, která byla nakonfigurována správcem.

### <a name="how-do-i-try-it"></a>Jak zkuste ho?

Pokud chcete umožnit Azure Information Protection zkuste zobrazíte-li to může být přizpůsobit pro vaši organizaci, navštivte [rychlý úvodní kurz](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). Provede vás prostřednictvím 5 základních kroků – z instalace ke konfiguraci zásady, které zobrazuje klasifikace, označování a sdílení v akci – a provést méně než půl hodiny.

### <a name="how-do-i-deploy-it"></a>Jak můžu tuto službu nasadit?

Pokud chcete nasadit Azure Information Protection ve vaší organizaci, navštivte [plán nasazení pro klasifikaci, označování a ochranu](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Další informace je třeba vědět?

Doplňující informace, které vám pomohou pečlivě promyslete jak ji nastavit, najdete v článku [připravené, nastavit, ochrana!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
příspěvku na blogu. A zkontrolovat, přečtěte si další odkazy níže uvedené další informace o Azure Information Protection.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Vyhledávání systému Azure: zjišťování dat pro vývojáře aplikací

[Služba Azure Search](https://azure.microsoft.com/services/search/) je cloudové řešení vyhledávání pro vývojáře a nabízí bohaté data vyhledávání prostředí pro vaše aplikace. Služba Azure Search umožňuje najít data mezi uživatelem definované indexy, která byla vytvořena z Azure kosmetický salón DB, databáze SQL Azure, Azure Blob Storage, Azure Table storage nebo zákazník vlastní JSON data. Můžete také struktury dotazů Lucene pomocí REST API služby Azure Search k vyhledání typů osobní údaje nebo osobní data konkrétní osoby. Funkce zahrnují fulltextové vyhledávání, jednoduchá syntaxe dotazů a syntaxe dotazů Lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>Použití SQL k dotazování na data

Začátku se základy, přejděte [CosmosD databázi Azure: jak dotazovat pomocí SQL](../cosmos-db/tutorial-query-documentdb.md) kurzu. Tento kurz obsahuje ukázkové dokumentu a dva ukázkové dotazy SQL a výsledky.

Další podrobné pokyny k vytváření dotazů SQL, najdete v článku [dotazy SQL pro rozhraní API služby Azure Cosmos DB dokumentu DB.](../cosmos-db/sql-api-sql-query.md)

Pokud jste pro Azure Cosmos DB nové a chcete se dozvíte, jak k vytvoření databáze, přidejte do kolekce a přidat data, navštivte [Cosmos databázi Azure: sestavení webové aplikace SQL API](../cosmos-db/create-sql-api-dotnet.md) rychlý úvodní kurz. Pokud chcete to udělat v jiném jazyce než v rozhraní .NET, například Java nebo Python, právě vyberte upřednostňovaný jazyk po získání do lokality.

## <a name="next-steps"></a>Další postup

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[Co je SQL Database?](../sql-database/sql-database-technical-overview.md)

[SQL databáze Editor dotazů na portálu Azure k dispozici] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Co je Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Co je Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: Připraveni, nastavit, ochrana!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
