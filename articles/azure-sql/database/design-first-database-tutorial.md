---
title: 'Kurz: návrh první relační databáze pomocí SSMS'
description: Naučte se navrhovat první relační databázi v Azure SQL Database pomocí SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: 1e0ab1d6c1266b37dfcba461fbbdc373fc526783
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362159"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Kurz: návrh relační databáze v Azure SQL Database pomocí SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database je relační databáze jako služba (DBaaS) v Microsoft Cloud (Azure). V tomto kurzu se naučíte používat web Azure Portal a aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS), když chcete:

> [!div class="checklist"]
>
> - Vytvoření databáze pomocí Azure Portal *
> - Nastavení pravidla brány firewall protokolu IP na úrovni serveru pomocí Azure Portal
> - Připojit se k databázi pomocí SSMS
> - Vytvářet tabulky pomocí SSMS
> - Hromadně načítat data pomocí BCP
> - Dotazování dat pomocí SSMS

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!TIP]
> Následující Microsoft Learn modul vám pomůže naučit se zdarma, jak [vyvíjet a konfigurovat aplikaci ASP.NET, která se dotazuje na Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), včetně vytvoření jednoduché databáze.
> [!NOTE]
> Pro účely tohoto kurzu používáme Azure SQL Database. Můžete použít také databázi ve fondu v elastickém fondu nebo v spravované instanci SQL. Informace o připojení ke spravované instanci SQL najdete v těchto rychlých startech spravované instance SQL: [rychlý Start: konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL](../managed-instance/connect-vm-instance-configure.md) a [rychlé zprovoznění: Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního](../managed-instance/point-to-site-p2s-configure.md)prostředí.

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu se ujistěte, že jste nainstalovali:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (nejnovější verze)
- [BCP a Sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (nejnovější verze)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Vytvoření prázdné databáze v Azure SQL Database

Databáze v Azure SQL Database se vytvoří s definovanou sadou výpočetních prostředků a prostředků úložiště. Databáze se vytvoří v rámci [skupiny prostředků Azure](../../active-directory-b2c/overview.md) a spravuje se pomocí [logického SQL serveru](logical-servers.md).

Pomocí těchto kroků vytvořte prázdnou databázi.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Create a resource** (Vytvořit prostředek).
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/design-first-database-tutorial/create-empty-database.png)

3. Do formuláře **SQL Database** zadejte následující informace, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Zvolit zdroj** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Pokud chcete použít existující server nebo vytvořit a nakonfigurovat nový server, klikněte na **Server** . Buď vyberte existující server, nebo klikněte na **vytvořit nový server** a vyplňte formulář **nového serveru** pomocí následujících informací:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/design-first-database-tutorial/create-database-server.png)

5. Klikněte na **Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Můžete prozkoumat možnosti pro počet DTU/virtuální jádra a úložiště, které máte k dispozici pro jednotlivé úrovně služeb.

    Po výběru úrovně služby, počtu DTU nebo virtuální jádra a velikosti úložiště klikněte na **použít**.

7. Zadejte **kolaci** pro prázdnou databázi (pro tento kurz použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste dokončili formulář **SQL Database** , klikněte na **vytvořit** a zřiďte databázi. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![Snímek obrazovky s probíhajícím nasazením zobrazuje nabídku oznámení.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall protokolu IP na úrovni serveru

Azure SQL Database vytvoří bránu firewall protokolu IP na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru a libovolné databázi na serveru, pokud pravidlo brány firewall nepovoluje jejich IP adresu přes bránu firewall. Pokud chcete povolit externí připojení k vaší databázi, musíte nejdřív přidat pravidlo brány firewall protokolu IP pro vaši IP adresu (nebo rozsah IP adres). Pomocí těchto kroků vytvořte [pravidlo brány firewall protokolu IP na úrovni serveru](firewall-configure.md).

> [!IMPORTANT]
> Azure SQL Database používá ke komunikaci port 1433. Pokud se pokoušíte připojit k této službě v rámci podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k databázi, pokud správce neotevře port 1433.

1. Po dokončení nasazení vyberte **databáze SQL** z nabídky Azure Portal nebo vyhledejte a vyberte *databáze SQL* z libovolné stránky.  

1. Na stránce **databáze SQL** vyberte *yourDatabase* . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný **název serveru** (například), který `contosodatabaseserver01.database.windows.net` poskytuje možnosti pro další konfiguraci.

   ![název serveru](./media/design-first-database-tutorial/server-name.png)

1. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a databázím z SQL Server Management Studio.

1. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **nastavení brány firewall** pro server.

   ![pravidlo brány firewall protokolu IP na úrovni serveru](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Kliknutím na **Přidat IP adresu klienta** na panelu nástrojů přidejte svoji aktuální IP adresu do nového pravidla brány firewall protokolu IP. Pravidlo brány firewall protokolu IP může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

1. Klikněte na **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru.

1. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaše IP adresa teď může projít bránou firewall protokolu IP. Nyní se můžete připojit k databázi pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Ujistěte se, že používáte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup prostřednictvím brány firewall SQL Database IP adres povolený pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

## <a name="connect-to-the-database"></a>Připojte se k databázi.

K navázání připojení k databázi použijte [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) .

1. Otevřete sadu SQL Server Management Studio.
2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Typ serveru** | Databázový stroj | Tato hodnota se vyžaduje. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Například *yourserver.Database.Windows.NET*. |
   | **Authentication** | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme nakonfigurovali v tomto kurzu. |
   | **Přihlásit** | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | **Heslo** | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |

   ![Připojení k serveru](./media/design-first-database-tutorial/connect.png)

3. Klikněte na **Možnosti** v dialogovém okně **Připojit k serveru**. V části **připojit k databázi** zadejte *yourDatabase* pro připojení k této databázi.

    ![připojení k databázi na serveru](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Klikněte na **Připojit**. Okno **Průzkumník objektů** se otevře v SSMS.

5. V **Průzkumník objektů**rozbalte **databáze** a potom rozbalte *yourDatabase* a zobrazte objekty v ukázkové databázi.

   ![databázové objekty](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Vytvoření tabulek v databázi

Vytvořte schéma databáze se čtyřmi tabulkami, které modelují systém správy studentů univerzity, pomocí [Transact-SQL](/sql/t-sql/language-reference):

- Osoba
- Kurz
- Student
- Kredit

Následující diagram znázorňuje, jak spolu tyto tabulky vzájemně souvisejí. Některé z těchto tabulek odkazují na sloupce v jiných tabulkách. Například tabulka *student* odkazuje na sloupec *PersonId* v tabulce *Person (osoba* ). Prohlédněte si diagram, abyste pochopili, jak spolu tabulky v tomto kurzu souvisejí. Podrobný rozbor toho, jak vytvářet efektivní databázové tabulky, najdete v tématu [Vytváření efektivních databázových tabulek](https://msdn.microsoft.com/library/cc505842.aspx). Informace o výběru datových typů najdete v tématu [Datové typy](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tabulky můžete také vytvářet a navrhovat pomocí [Návrháře tabulky v aplikaci SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Vztahy mezi tabulkami](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. V **Průzkumník objektů**klikněte pravým tlačítkem na *YourDatabase* a vyberte **Nový dotaz**. Otevře se prázdné okno dotazu připojené k vaší databázi.

2. V okně dotazu vytvořte v databázi čtyři tabulky spuštěním následujícího dotazu:

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

   ![Vytváření tabulek](./media/design-first-database-tutorial/create-tables.png)

3. Rozbalením uzlu **Tables** ( *yourDatabase* ) v **Průzkumník objektů** zobrazíte tabulky, které jste vytvořili.

   ![Vytvořené tabulky aplikace SSMS](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

1. Ve složce stažené soubory vytvořte složku s názvem *sampleData* , do které se budou ukládat ukázková data pro vaši databázi.

2. Klikněte pravým tlačítkem na následující odkazy a uložte je do složky *sampleData* .

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otevřete okno příkazového řádku a přejděte do složky *sampleData* .

4. Spuštěním následujících příkazů vložte ukázková data do tabulek, které nahradí hodnoty pro *Server*, *databázi*, *uživatele*a *heslo* hodnotami pro vaše prostředí.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Teď jste načetli ukázková data do tabulek, které jste předtím vytvořili.

## <a name="query-data"></a>Dotazování dat

Informace z databázových tabulek můžete načíst spuštěním následujících dotazů. Další informace o psaní dotazů SQL najdete v tématu popisujícím [zápis dotazů SQL](https://technet.microsoft.com/library/bb264565.aspx) . První dotaz spojí všechny čtyři tabulky a vyhledá studenty, které jsou na Dominick Pope, kteří mají třídu vyšší než 75%. Druhý dotaz spojí všechny čtyři tabulky a vyhledá kurzy, ve kterých se někdy zaregistrovala možnost "Noe Coleman".

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

2. V okně dotazu spusťte následující dotaz:

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili mnoho základních databázových úloh. Naučili jste se:

> [!div class="checklist"]
>
> - Vytvoření databáze pomocí Azure Portal *
> - Nastavení pravidla brány firewall protokolu IP na úrovni serveru pomocí Azure Portal
> - Připojit se k databázi pomocí SSMS
> - Vytvářet tabulky pomocí SSMS
> - Hromadně načítat data pomocí BCP
> - Dotazování dat pomocí SSMS

V následujícím kurzu se dozvíte, jak navrhnout databázi pomocí sady Visual Studio a jazyka C#.

> [!div class="nextstepaction"]
> [Návrh relační databáze v Azure SQL Database C# a ADO.NET](design-first-database-csharp-tutorial.md)
