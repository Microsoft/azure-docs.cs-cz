---
title: 'Kurz: Návrh první databáze Azure SQL Database pomocí SSMS | Microsoft Docs'
description: Zjistěte, jak navrhnout první databázi SQL Azure pomocí aplikace SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956053"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Kurz: Návrh první databáze Azure SQL Database pomocí SSMS

Azure SQL database je relační databáze as-a-service (DBaaS) v Microsoft cloudu (Azure). V tomto kurzu se naučíte používat web Azure Portal a aplikaci [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS), když chcete:

> [!div class="checklist"]
> * Vytvořit databázi na portálu Azure Portal*
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojit se k databázi pomocí SSMS
> * Vytvářet tabulky pomocí SSMS
> * Hromadně načítat data pomocí BCP
> * Dotazování dat pomocí aplikace SSMS

* Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.

> [!NOTE]
> Pro účely tohoto kurzu používáme [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), ale můžete si zvolit i [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že jste nainstalovali:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (nejnovější verze)
- [BCP a SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (nejnovější verze)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze

Databáze SQL Azure se vytvoří s definovanou sadou [výpočetních prostředků a prostředků úložiště](sql-database-service-tiers-dtu.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a [logického serveru Azure SQL database](sql-database-features.md).

Pomocí následujících kroků vytvořte prázdnou databázi SQL.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

1. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

   1. Vyplňte **SQL Database** formuláře následující informace, jak je znázorněno na předchozím obrázku:

      | Nastavení       | Navrhovaná hodnota | Popis |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
      | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
      | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
      | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

   1. Klikněte na tlačítko **Server** chcete použít existující server nebo vytvořte a nakonfigurujte nový server pro vaši databázi. Vyberte server, nebo klikněte na tlačítko **vytvořit nový server** a vyplňte **nový server** formuláře následující informace:

      | Nastavení       | Navrhovaná hodnota | Popis |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
      | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
      | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
      | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

      ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

      Klikněte na **Vybrat**.

   1. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Může prozkoumejte možnosti počtu Dtu nebo virtuálních jader a úložiště, které je k dispozici na jednotlivých úrovních služby. Ve výchozím nastavení **standardní** [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) je vybrána, ale je nutné zvolit [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

      > [!IMPORTANT]
      > Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech kromě následujících: Velká Británie – sever, střed USA – Západ, Velká Británie South2, Čína – východ, USDoDCentral, Německo – střed, USDoDEast, USA (gov) – jihozápad, USA (gov) – jih – střed, Německo – severovýchod, Čína – Sever, USA (gov) – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

      Po výběru úrovně služby, počet Dtu a velikost úložiště, klikněte na **použít**.

   1. Zadejte **kolace** pro prázdnou databázi (pro účely tohoto kurzu použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

1. Teď, když jste dokončili **SQL Database** formuláře, klikněte na tlačítko **vytvořit** a databázi zřiďte. Tento krok může trvat několik minut.

1. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

     ![oznámení](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall

Službu SQL database vytvoří bránu firewall na úrovni serveru. Brána firewall brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru. Pokud chcete povolit externí připojení k databázi, musí nejprve přidat pravidlo pro vaši IP adresu do brány firewall. Postupujte podle těchto kroků můžete vytvořit [pravidlo brány firewall na úrovni serveru SQL database](sql-database-firewall-configure.md).

> [!NOTE]
> SQL database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k serveru Azure SQL Database, dokud správce otevře port 1433.

1. Jakmile se nasazení dokončí, klikněte na tlačítko **databází SQL** z nabídky na levé straně a pak klikněte na tlačítko *yourDatabase* na **databází SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, zobrazí plně kvalifikovaný **název serveru** (například *yourserver.database.windows.net*) a poskytne vám možnosti další konfigurace.

1. Zkopírujte tento plně kvalifikovaný název serveru použít pro připojení k vašemu serveru a databáze z SQL Server Management Studio.

   ![název serveru](./media/sql-database-design-first-database/server-name.png)

1. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. **Nastavení brány Firewall** otevře se stránka pro server služby SQL database.

   ![pravidlo brány firewall serveru](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. Klikněte na **Přidat IP adresu klienta** na panelu nástrojů a přidejte svoji aktuální IP adresu do nového pravidla brány firewall. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

   1. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

   1. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

IP adresu můžete nyní průchod branou firewall. Teď můžete připojit k serveru SQL database a jeho databázím pomocí aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Nezapomeňte použít účet správce serveru, který jste vytvořili dříve.

> [!IMPORTANT]
> Přístup přes bránu firewall služby SQL database je ve výchozím nastavení povolené pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

## <a name="connect-to-the-database"></a>Připojení k databázi

Použití [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) k navázání připojení k vašemu serveru Azure SQL database.

1. Otevřete SQL Server Management Studio.

1. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Typ serveru** | Databázový stroj | Tato hodnota se vyžaduje. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Například *yourserver.database.windows.net*. |
   | **Ověřování** | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme nakonfigurovali funkci v tomto kurzu. |
   | **Přihlášení** | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | **Heslo** | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |

   ![Připojení k serveru](./media/sql-database-design-first-database/connect.png)

   1. Klikněte na **Možnosti** v dialogovém okně **Připojit k serveru**. V **připojit k databázi** části, zadejte *yourDatabase* pro připojení k této databázi.

      ![připojení k databázi na serveru](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. Klikněte na **Připojit**. **Průzkumník objektů systému** v aplikaci SSMS se otevře okno.

1. V **Průzkumník objektů**, rozbalte **databází** a potom rozbalte *yourDatabase* a zobrazte objekty v ukázkové databázi.

   ![databázové objekty](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi

Vytvořte schéma databáze se čtyřmi tabulkami, které modelují systém správy studentů univerzity, pomocí [Transact-SQL](/sql/t-sql/language-reference):

- Person (Osoba)
- Course (Kurz)
- Student
- Kredit

Následující diagram znázorňuje, jak spolu tyto tabulky vzájemně souvisejí. Některé z těchto tabulek odkazují na sloupce v jiných tabulkách. Například *Student* tabulky odkazů *PersonId* sloupec *osoba* tabulky. Prohlédněte si diagram, abyste pochopili, jak spolu tabulky v tomto kurzu souvisejí. Podrobný rozbor toho, jak vytvářet efektivní databázové tabulky, najdete v tématu [Vytváření efektivních databázových tabulek](https://msdn.microsoft.com/library/cc505842.aspx). Informace o výběru datových typů najdete v tématu [Datové typy](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tabulky můžete také vytvářet a navrhovat pomocí [Návrháře tabulky v aplikaci SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Vztahy mezi tabulkami](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na *yourDatabase* a vyberte **nový dotaz**. Otevře se prázdné okno dotazu připojené k vaší databázi.

1. V okně dotazu vytvořte v databázi čtyři tabulky spuštěním následujícího dotazu:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Vytvoření tabulek](./media/sql-database-design-first-database/create-tables.png)

1. Rozbalte **tabulky** pod uzlem *yourDatabase* v **Průzkumník objektů** zobrazíte tabulky, které jste vytvořili.

   ![Vytvořené tabulky aplikace SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

1. Vytvořte složku s názvem *sampleData* ve složce stažené soubory ukládat ukázková data pro vaši databázi.

1. Klikněte pravým tlačítkem na následující odkazy a uložte je do *sampleData* složky.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. Otevřete okno příkazového řádku a přejděte *sampleData* složky.

1. Spuštěním následujících příkazů vložte ukázková data do tabulek a zároveň nahraďte hodnoty *server*, *databáze*, *uživatele*, a *heslo* hodnotami pro vaše prostředí.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Teď jste načetli ukázková data do tabulek, které jste předtím vytvořili.

## <a name="query-data"></a>Dotazování dat

Informace z databázových tabulek můžete načíst spuštěním následujících dotazů. Zobrazit [dotazy SQL zapisovat](https://technet.microsoft.com/library/bb264565.aspx) Další informace o psaní dotazů SQL. První dotaz spojí všechny čtyři tabulky a vyhledá studenty "Dominick Pope", kteří mají známku vyšší než 75 %. Druhý dotaz spojí všechny čtyři tabulky a vyhledá kurzů, ve kterých je "Noe Colemane" nikdy zaregistrované.

1. V okně dotazu aplikace SQL Server Management Studio spusťte následující dotaz:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. V okně dotazu spusťte následující dotaz:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, mnoho běžných úloh. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí aplikace [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Vytvoření tabulek
> * Hromadné načtení dat
> * Dotazování těchto dat

V následujícím kurzu se dozvíte, jak navrhnout databázi pomocí sady Visual Studio a jazyka C#.

> [!div class="nextstepaction"]
> [Návrh databáze SQL Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)
