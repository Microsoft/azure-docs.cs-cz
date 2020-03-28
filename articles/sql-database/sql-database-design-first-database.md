---
title: 'Kurz: Navrhněte svou první relační databázi pomocí SSMS'
description: Naučte se navrhnout první relační databázi v jedné databázi v Azure SQL Database pomocí SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: 9764c4bc794eb8d133270b762fa2bca30a056fea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75459624"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Kurz: Návrh relační databáze v jedné databázi v rámci Azure SQL Database pomocí SSMS

Azure SQL database je relační databáze jako služba (DBaaS) v Microsoft Cloudu (Azure). V tomto kurzu se naučíte používat web Azure Portal a aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS), když chcete:

> [!div class="checklist"]
> - Vytvoření jedné databáze pomocí portálu Azure*
> - Nastavení pravidla brány firewall IP na úrovni serveru pomocí portálu Azure
> - Připojit se k databázi pomocí SSMS
> - Vytvářet tabulky pomocí SSMS
> - Hromadně načítat data pomocí BCP
> - Dotaz na data pomocí SSMS

*Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

> [!TIP]
> Následující modul Microsoft Learn vám pomůže zdarma se naučit [vyvíjet a konfigurovat ASP.NET aplikaci, která se dotazuje na Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), včetně vytvoření jednoduché databáze.
> [!NOTE]
> Pro účely tohoto kurzu používáme jednu databázi. Můžete také použít sdruženou databázi v elastickém fondu nebo databázi instancí ve spravované instanci. Připojení ke spravované instanci najdete v tématu tyto rychlé starty spravované instance: [Úvodní příručka: Konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL Database](sql-database-managed-instance-configure-vm.md) a úvodní [příručka: Konfigurace připojení z bodu na místo ke spravované instanci Azure SQL Database spravované instance z místního prostředí](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li tento kurz dokončit, zkontrolujte, zda jste nainstalovali:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (nejnovější verze)
- [BCP a SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (nejnovější verze)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Vytvoření prázdné jedné databáze

Jedna databáze v Azure SQL Database se vytvoří s definovanou sadou výpočetních prostředků a prostředků úložiště. Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) a spravuje se pomocí [databázového serveru](sql-database-servers.md).

Chcete-li vytvořit prázdnou jednu databázi, postupujte takto.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

3. Vyplňte formulář **databáze SQL** následujícími informacemi, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *vaše databáze* | Platné názvy databází naleznete v [tématu Identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *vaše předplatné*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *vašeskupina zdrojů* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Vybrat zdroj** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Chcete-li použít existující databázový server nebo vytvořit a nakonfigurovat nový databázový server, klepněte na **tlačítko Server.** Vyberte existující server nebo **klepněte** na tlačítko Vytvořit nový server a vyplňte formulář **Nový server** následujícími informacemi:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
    | **Přihlášení správce serveru** | Libovolné platné jméno | Platné přihlašovací názvy naleznete v [tématu Identifikátory databáze](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, čísla a nealfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klepněte na **tlačítko Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Můžete prozkoumat možnosti pro počet DTU/virtuálních jader a úložiště, které jsou k dispozici pro každou úroveň služby.

    Po výběru úrovně služby, počtu DTU nebo virtuálních jader a velikosti úložiště klikněte na **použít**.

7. Zadejte **kolaci** pro prázdnou databázi (pro tento kurz použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste vyplnili formulář **databáze SQL,** klikněte na **Vytvořit** a zřídit jednu databázi. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![oznámení](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall IP na úrovni serveru

Služba SQL Database vytvoří bránu firewall IP na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru a všem databázím na serveru, pokud pravidlo brány firewall neumožňuje jejich adresu IP přes bránu firewall. Chcete-li povolit externí připojení k jedné databázi, musíte nejprve přidat pravidlo brány firewall IP pro vaši IP adresu (nebo rozsah IP adres). Následujícím postupem vytvořte [pravidlo brány firewall na úrovni serveru SQL Database .](sql-database-firewall-configure.md)

> [!IMPORTANT]
> Služba SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit k této službě z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall sítě povolen. Pokud ano, nelze se připojit k jedné databázi, pokud správce neotevře port 1433.

1. Po dokončení nasazení vyberte **databáze SQL** z nabídky portálu Azure nebo vyhledejte a vyberte *databáze SQL* z libovolné stránky.  

1. Na stránce **databáze SQL vyberte databázi.** *yourDatabase* Otevře se stránka s přehledem databáze, která zobrazuje `contosodatabaseserver01.database.windows.net`plně kvalifikovaný název **serveru** (například) a poskytuje možnosti pro další konfiguraci.

   ![název serveru](./media/sql-database-design-first-database/server-name.png)

1. Zkopírujte tento plně kvalifikovaný název serveru pro připojení k serveru a databázím ze sady SQL Server Management Studio.

1. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![Pravidlo brány firewall IP na úrovni serveru](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Klepnutím na **tlačítko Přidat IP adresu klienta** na panelu nástrojů přidejte aktuální adresu IP do nového pravidla brány firewall IP. Pravidlo brány firewall IP může otevřít port 1433 pro jednu adresu IP nebo rozsah adres IP.

1. Klikněte na **Uložit**. Pro aktuální port pro otevření ip adresy 1433 na serveru SQL Database je vytvořeno pravidlo brány firewall IP na úrovni serveru.

1. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaše IP adresa může nyní projít ip firewallem. Nyní se můžete připojit k jedné databázi pomocí sql server management studio nebo jiný nástroj dle vašeho výběru. Nezapomeňte použít účet správce serveru, který jste vytvořili dříve.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup přes bránu firewall IP databáze SQL povolen pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

## <a name="connect-to-the-database"></a>Připojte se k databázi.

Pomocí [aplikace SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) vytvořte připojení k jedné databázi.

1. Otevřete SQL Server Management Studio.
2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Typ serveru** | Databázový stroj | Tato hodnota se vyžaduje. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Například *yourserver.database.windows.net*. |
   | **Ověřování** | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme nakonfigurovali v tomto kurzu. |
   | **Přihlášení** | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | **Heslo** | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |

   ![Připojení k serveru](./media/sql-database-design-first-database/connect.png)

3. Klikněte na **Možnosti** v dialogovém okně **Připojit k serveru**. V části **Připojit k databázi** zadejte *databázi* pro připojení k této databázi.

    ![připojení k databázi na serveru](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Klikněte na **Připojit**. Otevře se okno **Průzkumníka objektů** v ssms.

5. V **Průzkumníku objektů**rozbalte **položku Databáze** a potom *rozbalte databázi a* zobrazte objekty v ukázkové databázi.

   ![databázové objekty](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Vytváření tabulek v databázi

Vytvořte schéma databáze se čtyřmi tabulkami, které modelují systém správy studentů univerzity, pomocí [Transact-SQL](/sql/t-sql/language-reference):

- Person (Osoba)
- Course (Kurz)
- Student
- Kredit

Následující diagram znázorňuje, jak spolu tyto tabulky vzájemně souvisejí. Některé z těchto tabulek odkazují na sloupce v jiných tabulkách. Například tabulka *Student* odkazuje na sloupec *PersonId* tabulky *Osoba.* Prohlédněte si diagram, abyste pochopili, jak spolu tabulky v tomto kurzu souvisejí. Podrobný rozbor toho, jak vytvářet efektivní databázové tabulky, najdete v tématu [Vytváření efektivních databázových tabulek](https://msdn.microsoft.com/library/cc505842.aspx). Informace o výběru datových typů najdete v tématu [Datové typy](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tabulky můžete také vytvářet a navrhovat pomocí [Návrháře tabulky v aplikaci SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Vztahy mezi tabulkami](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na *databázi* a vyberte **nový dotaz**. Otevře se prázdné okno dotazu připojené k vaší databázi.

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

   ![Vytvoření tabulek](./media/sql-database-design-first-database/create-tables.png)

3. Rozbalte uzel **Tabulky** pod *databází* v **Průzkumníkovi objektů,** abyste viděli tabulky, které jste vytvořili.

   ![Vytvořené tabulky aplikace SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

1. Vytvořte složku nazvanou *sampleData* ve složce Stahování a uložte ukázková data pro databázi.

2. Klikněte pravým tlačítkem myši na následující odkazy a uložte je do složky *sampleData.*

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otevřete okno příkazového řádku a přejděte do složky *sampleData.*

4. Provedením následujících příkazů vložte ukázková data do tabulek a nahradíte hodnoty *pro server*, *databázi*, *uživatele*a *heslo* hodnotami pro vaše prostředí.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Teď jste načetli ukázková data do tabulek, které jste předtím vytvořili.

## <a name="query-data"></a>Dotazování dat

Informace z databázových tabulek můžete načíst spuštěním následujících dotazů. Další informace o psaní dotazů SQL najdete v [tématu Zápis dotazů SQL.](https://technet.microsoft.com/library/bb264565.aspx) První dotaz se připojí ke všem čtyřem stolům, aby našel studenty vyučované "Dominick Pope", kteří mají známku vyšší než 75%. Druhý dotaz se připojí všechny čtyři tabulky a najde kurzy, ve kterých 'Noe Coleman' někdy zapsán.

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

V tomto kurzu jste se naučili mnoho základních databázových úloh. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření jedné databáze
> - Nastavení pravidla brány firewall IP na úrovni serveru
> - Připojení k databázi pomocí aplikace [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Vytvoření tabulek
> - Hromadné načtení dat
> - Dotazování těchto dat

V následujícím kurzu se dozvíte, jak navrhnout databázi pomocí sady Visual Studio a jazyka C#.

> [!div class="nextstepaction"]
> [Návrh relační databáze v jedné databázi v rámci Azure SQL Database C# a ADO.NET](sql-database-design-first-database-csharp.md)
