---
title: 'Kurz: Návrh první relační databáze ve službě Azure SQL Database pomocí SSMS | Dokumentace Microsoftu'
description: Zjistěte, jak navrhnout první relační databáze v jedné databázi ve službě Azure SQL Database pomocí aplikace SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 3ca17ae905fff0911b58a0d336e0899ff385085c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990475"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Kurz: Návrh relační databáze v jedné databázi v rámci Azure SQL Database pomocí SSMS

Azure SQL database je relační databáze as-a-service (DBaaS) v Microsoft cloudu (Azure). V tomto kurzu se naučíte používat web Azure Portal a aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS), když chcete:

> [!div class="checklist"]
> - Vytvoření izolované databáze pomocí Azure portal *
> - Nastavit pravidlo firewallu protokolu IP úrovni serveru pomocí webu Azure portal
> - Připojit se k databázi pomocí SSMS
> - Vytvářet tabulky pomocí SSMS
> - Hromadně načítat data pomocí BCP
> - Dotazování dat pomocí aplikace SSMS

* Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.

> [!NOTE]
> Pro účely tohoto kurzu používáme izolované databáze. Můžete také použít ve fondu databáze v elastickém fondu nebo instanci databáze do spravované instance. Připojení k managed instance najdete v těchto rychlých startů pro spravovanou instanci: [Rychlé zprovoznění: Konfigurace virtuálního počítače Azure pro připojení k Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) a [rychlý start: Konfigurace připojení typu point-to-site k Azure SQL Database Managed Instance z místní](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že jste nainstalovali:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (nejnovější verze)
- [BCP a SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (nejnovější verze)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Vytvoření prázdné jedné databáze

Izolované databáze ve službě Azure SQL Database se vytvoří s definovanou sadou výpočetních a úložných prostředků. Vytvoření databáze v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) a spravuje se pomocí [databázový server](sql-database-servers.md).

Postupujte podle těchto kroků můžete vytvořit prázdné jedné databáze.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** v části Azure Marketplace vyberte **Databases** a potom klikněte v části **Doporučené** na **SQL Database**.

   ![Vytvoření prázdné databáze](./media/sql-database-design-first-database/create-empty-database.png)

3. Vyplňte **SQL Database** formuláře následující informace, jak je znázorněno na předchozím obrázku:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název databáze** | *yourDatabase* | Platné názvy databází najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
    | **Předplatné** | *yourSubscription*  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *yourResourceGroup* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
    | **Výběr zdroje** | Prázdná databáze | Určuje, že se má vytvořit prázdná databáze. |

4. Klikněte na tlačítko **Server** použít existující databázový server nebo vytvořte a nakonfigurujte nový server databáze. Vyberte existující server, nebo klikněte na tlačítko **vytvořit nový server** a vyplňte **nový server** formuláře následující informace:

    | Nastavení       | Navrhovaná hodnota | Popis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions). |
    | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [databáze identifikátory](/sql/relational-databases/databases/database-identifiers). |
    | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň osm znaků a musí používat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
    | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klikněte na **Vybrat**.
6. Klikněte na **Cenová úroveň** a zadejte úroveň služby, počet DTU nebo virtuálních jader a velikost úložiště. Může prozkoumejte možnosti počtu Dtu nebo virtuálních jader a úložiště, které je k dispozici na jednotlivých úrovních služby.

    Po výběru úrovně služby, počet Dtu nebo virtuálních jader a velikost úložiště, klikněte na tlačítko **použít**.

7. Zadejte **kolace** pro prázdnou databázi (pro účely tohoto kurzu použijte výchozí hodnotu). Další informace o kolacích najdete v tématu [Kolace](/sql/t-sql/statements/collations).

8. Teď, když jste dokončili **SQL Database** formuláře, klikněte na tlačítko **vytvořit** ke zřízení izolované databáze. Tento krok může trvat několik minut.

9. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

   ![oznámení](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Vytvoření pravidla brány firewall úrovni serveru IP

Služba SQL Database vytvoří bránu firewall IP na úrovni serveru. Tato brána firewall brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru, pokud pravidlo brány firewall umožňuje jejich IP přes bránu firewall. Pokud chcete povolit externí připojení k izolované databáze, musíte nejprve přidat pravidlo IP brány firewall pro IP adresu (nebo rozsah IP adres). Postupujte podle těchto kroků můžete vytvořit [pravidlo firewallu protokolu IP serveru SQL Database](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Služba SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit k této službě z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k izolované databáze, dokud správce otevře port 1433.

1. Jakmile se nasazení dokončí, klikněte na tlačítko **databází SQL** z nabídky na levé straně a pak klikněte na tlačítko *yourDatabase* na **databází SQL** stránky. Otevře se stránka s přehledem pro vaši databázi, zobrazí plně kvalifikovaný **název serveru** (například *yourserver.database.windows.net*) a poskytne vám možnosti další konfigurace.

2. Zkopírujte tento plně kvalifikovaný název serveru použít pro připojení k vašemu serveru a databáze z SQL Server Management Studio.

   ![název serveru](./media/sql-database-design-first-database/server-name.png)

3. Na panelu nástrojů klikněte na **Nastavit bránu firewall serveru**. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall na úrovni serveru IP](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall protokolu IP. Pravidla brány firewall protokolu IP můžete otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

5. Klikněte na **Uložit**. Pravidlo firewallu protokolu IP úrovni serveru se vytvoří pro vaši aktuální IP adresu, které otevře port 1433 na serveru SQL Database.

6. Klikněte na **OK** a pak zavřete stránku **Nastavení brány firewall**.

Vaši IP adresu můžete nyní procházet brány firewall protokolu IP. Teď můžete připojit k vaší izolované databáze pomocí SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Nezapomeňte použít účet správce serveru, který jste vytvořili dříve.

> [!IMPORTANT]
> Ve výchozím nastavení je přístup přes bránu firewall služby SQL Database IP povolené pro všechny služby Azure. Kliknutím na **OFF** na této stránce provedete zákaz pro všechny služby Azure.

## <a name="connect-to-the-database"></a>Připojení k databázi

Použití [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) k navázání připojení k izolované databáze.

1. Otevřete SQL Server Management Studio.
2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Typ serveru** | Databázový stroj | Tato hodnota se vyžaduje. |
   | **Název serveru** | Plně kvalifikovaný název serveru | Například *yourserver.database.windows.net*. |
   | **Ověřování** | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který jsme nakonfigurovali funkci v tomto kurzu. |
   | **Přihlášení** | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | **Heslo** | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |

   ![Připojení k serveru](./media/sql-database-design-first-database/connect.png)

3. Klikněte na **Možnosti** v dialogovém okně **Připojit k serveru**. V **připojit k databázi** části, zadejte *yourDatabase* pro připojení k této databázi.

    ![připojení k databázi na serveru](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Klikněte na **Připojit**. **Průzkumník objektů systému** v aplikaci SSMS se otevře okno.

5. V **Průzkumník objektů**, rozbalte **databází** a potom rozbalte *yourDatabase* a zobrazte objekty v ukázkové databázi.

   ![databázové objekty](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Vytvoření tabulek v databázi

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

3. Rozbalte **tabulky** pod uzlem *yourDatabase* v **Průzkumník objektů** zobrazíte tabulky, které jste vytvořili.

   ![Vytvořené tabulky aplikace SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

1. Vytvořte složku s názvem *sampleData* ve složce stažené soubory ukládat ukázková data pro vaši databázi.

2. Klikněte pravým tlačítkem na následující odkazy a uložte je do *sampleData* složky.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otevřete okno příkazového řádku a přejděte *sampleData* složky.

4. Spuštěním následujících příkazů vložte ukázková data do tabulek a zároveň nahraďte hodnoty *server*, *databáze*, *uživatele*, a *heslo* hodnotami pro vaše prostředí.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, mnoho běžných úloh. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření izolované databáze
> - Nastavit pravidlo brány firewall na úrovni serveru IP
> - Připojení k databázi pomocí aplikace [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Vytvoření tabulek
> - Hromadné načtení dat
> - Dotazování těchto dat

V následujícím kurzu se dozvíte, jak navrhnout databázi pomocí sady Visual Studio a jazyka C#.

> [!div class="nextstepaction"]
> [Návrh relační databáze v jedné databázi v rámci Azure SQL Database C# a ADO.NET](sql-database-design-first-database-csharp.md)
