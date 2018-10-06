---
title: Azure SQL Database Managed Instance auditování | Dokumentace Microsoftu
description: Zjistěte, jak začít pracovat s auditování Azure SQL Database Managed Instance pomocí jazyka T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 1ff0df958e541d8dd4b70827ea8e064cd3100e50
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815082"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Začínáme s Azure auditem SQL Database Managed Instance

[Azure SQL Database Managed Instance](sql-database-managed-instance.md) auditování sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure. Také auditování:
- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Povolí a umožňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Nastavení auditování serveru

Následující část popisuje konfiguraci auditování pro Managed Instance.
1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Následující kroky slouží k vytvoření služby Azure Storage **kontejneru** kde jsou uloženy protokoly auditu.

   - Přejděte do služby Azure Storage, ve kterém chcete ukládat vaše protokoly auditu.

     > [!IMPORTANT]
     > Použijte účet úložiště ve stejné oblasti jako server pro Managed Instance, aby křížová čtení/zápisu.

   - V účtu úložiště, přejděte na **přehled** a klikněte na tlačítko **objekty BLOB**.

     ![Navigační podokno][1]

   - V horní nabídce klikněte na tlačítko **+ kontejner** vytvořit nový kontejner.

     ![Navigační podokno][2]

   - Zadejte kontejner **název**, nastavte úroveň pro veřejný přístup **privátní**a potom klikněte na tlačítko **OK**.

     ![Navigační podokno][3]

   - V seznamu kontejnerů klikněte na nově vytvořený kontejner a pak klikněte na tlačítko **vlastnosti kontejneru**.

     ![Navigační podokno][4]

   - Kliknutím na ikonu kopírování zkopírujte adresu URL kontejneru a uložit adresu URL (například v poznámkovém bloku) pro budoucí použití. Formát adresy URL kontejneru by měla být `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigační podokno][5]

3. Azure Storage můžete vytvořit následující postupu **tokenů SAS** používá k udělení Managed Instance auditování přístupová práva k účtu úložiště.

   - Přejděte do účtu Azure Storage, ve které jste vytvořili kontejner v předchozím kroku.

   - Klikněte na **sdílený přístupový podpis** v nabídce Nastavení úložiště.

     ![Navigační podokno][6]

   - Konfigurace sdíleného přístupového podpisu následujícím způsobem:
     - **Povolené služby**: objekt Blob
     - **Počáteční datum**: abyste zabránili problémům souvisejícím s časové pásmo, doporučuje se použít včerejší datum.
     - **Koncové datum**: Zvolte datum, které tento Token SAS vyprší platnost. 

       > [!NOTE]
       > Obnovení tokenu při vypršení platnosti, aby se zabránilo chybám auditu.

     - Klikněte na **Vygenerovat SAS**.

       ![Navigační podokno][7]

   - Po kliknutí na Generovat SAS SAS Token se zobrazí v dolní části. Kliknutím na ikonu kopírování zkopírujte token a uložte ho (například v poznámkovém bloku) pro budoucí použití.

     > [!IMPORTANT]
     > Odebrat otazník ("?") znaků od začátku tokenu.

     ![Navigační podokno][8]

4. Připojte se k vaší spravované Instance pomocí SQL Server Management Studio (SSMS).

5. Spuštěním následujícího příkazu T-SQL k **vytvoření nových přihlašovacích údajů** pomocí adresy URL kontejneru a tokenu SAS, který jste vytvořili v předchozích krocích:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Spusťte následující příkaz jazyka T-SQL, chcete-li vytvořit nový Server Audit (zvolte svůj vlastní název auditu, použijte adresu URL kontejneru, který jste vytvořili v předchozích krocích):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Pokud není zadán, `RETENTION_DAYS` výchozí hodnota je 0 (neomezenou dobu).

    Další informace:
    - [Auditování rozdíly mezi Managed Instance, Azure SQL DB a systému SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Vytvoření specifikace auditu serveru nebo specifikací auditu databáze, stejně jako pro SQL Server:
    - [Vytvořit průvodce pro specifikace jazyka T-SQL Server audit](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Vytvořit průvodce T-SQL specifikace auditu databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Povolte auditu serveru, který jste vytvořili v kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analýza protokolů auditu
Existuje několik metod, které lze použít k zobrazení protokolů auditování objektů blob.

- Použít funkci systému `sys.fn_get_audit_file` (T-SQL) vrátit data protokolu auditování v tabulkovém formátu. Další informace o použití této funkce najdete v článku [sys.fn_get_audit_file dokumentaci](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Úplný seznam metod spotřeby protokolu auditu, najdete [Začínáme s auditem SQL database](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoda pro zobrazení záznamů auditu na webu Azure Portal (záznamy auditu podokno) je nyní k dispozici pro Managed Instance.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Auditování rozdíly mezi Managed Instance, Azure SQL Database a SQL Server

Hlavní rozdíly mezi auditování SQL v Managed Instance, Azure SQL Database a SQL Server v místním jsou:

- Ve spravované instanci SQL Audit funguje na úrovni serveru nebo úložišti `.xel` soubory protokolu na účet úložiště objektů blob v Azure.
- Ve službě Azure SQL Database SQL Audit funguje na úrovni databáze.
- V místním SQL serverem / virtuální počítače, funguje auditování SQL serveru na úrovni, ale události úložiště v systému souborů protokolů událostí.

Relace XEvent auditování ve spravované instanci podporuje cíle úložiště objektů blob v Azure. Souborová služba a windows protokoly jsou **nepodporuje**.

Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování do Azure blob storage jsou:
- Novou syntaxi `TO URL` je k dispozici a umožní vám zadat adresu URL kontejneru objektů blob v Azure Storage, kde `.xel` soubory jsou umístěny.
- Syntaxe `TO FILE` je **nepodporuje** protože Managed Instance nemá přístup ke sdílené složky Windows.
- Možnost vypnutí je **nepodporuje**.
- `queue_delay` 0 je **nepodporuje**.


## <a name="next-steps"></a>Další postup

- Úplný seznam metod spotřeby protokolu auditu, najdete [Začínáme s auditem SQL database](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
