---
title: Databáze Azure SQL spravované Instance auditování | Microsoft Docs
description: Zjistěte, jak začít pracovat s audity Azure SQL Database spravované Instance pomocí T-SQL
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 71929be456de4b798da48bb202969deb71e1c371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648849"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Začínáme s Azure SQL Database spravované Instance auditování

[Azure SQL Database spravované Instance](sql-database-managed-instance.md) auditování sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure. Auditování také:
- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které by mohly být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení.
- Umožňuje a zjednodušuje dodržování standardů dodržování předpisů, i když není zárukou toho, dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v části [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Nastavení auditování pro server

Následující část popisuje konfigurace auditování pro vaše spravované Instance.
1. Přejděte na [portál Azure](https://portal.azure.com).
2. Následující kroky slouží k vytvoření Azure Storage **kontejneru** kde jsou uloženy protokoly auditu.

   - Přejděte do služby Azure Storage, kam chcete uložit vaše protokoly auditu.

     > [!IMPORTANT]
     > Aby se zabránilo mezi oblastmi čtení/zápisu použijte účet úložiště ve stejné oblasti jako spravované Instance serveru.

   - Účet úložiště, přejděte na **přehled** a klikněte na tlačítko **objekty BLOB**.

     ![Navigační podokno][1]

   - V horní nabídce klikněte na tlačítko **+ kontejner** vytvořit nový kontejner.

     ![Navigační podokno][2]

   - Zadejte kontejner **název**, nastavte úroveň na veřejný přístup **privátní**a potom klikněte na **OK**.

     ![Navigační podokno][3]

   - V seznamu kontejnery, klikněte na nově vytvořený kontejner a potom klikněte na **vlastnosti kontejneru**.

     ![Navigační podokno][4]

   - Zkopírujte adresu URL kontejneru kliknutím na ikonu kopírování a uložte na adresu URL (například v programu Poznámkový blok) pro budoucí použití. Musí být ve formátu adresy URL kontejneru `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigační podokno][5]

3. Následující postup generování Azure Storage **tokenu SAS** použít pro přidělení spravované Instance auditování přístupová práva k účtu úložiště.

   - Přejděte na účet úložiště Azure, kde jste vytvořili kontejner v předchozím kroku.

   - Klikněte na **sdílený přístupový podpis** v nabídce Nastavení úložiště.

     ![Navigační podokno][6]

   - SAS nakonfigurujte následujícím způsobem:
     - **Povolené služby**: objektů Blob
     - **Počáteční datum**: aby se zabránilo problémy související s časové pásmo, se doporučuje použít datum předchozího dne.
     - **Koncové datum**: Zvolte datum, které tento Token SAS vyprší platnost. 

       > [!NOTE]
       > Obnovte token při vypršení platnosti, aby se zabránilo selhání auditu.

     - Klikněte na **Vygenerovat SAS**.

       ![Navigační podokno][7]

   - Po kliknutí na generování SAS, SAS Token se zobrazí v dolní části. Zkopírujte token kliknutím na ikonu kopírování a uložte ho (například Poznámkový blok) pro budoucí použití.

     > [!IMPORTANT]
     > Odebrat znaky otazníku ("?") znak od začátku tokenu.

     ![Navigační podokno][8]

4. Připojte k vaší instanci spravované přes SQL Server Management Studio (SSMS).

5. Spusťte následující příkaz T-SQL na **vytvoření nového pověření** pomocí adresy URL kontejneru a SAS Token, který jste vytvořili v předchozích krocích:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Spusťte následující příkaz T-SQL, chcete-li vytvořit nový Server Audit (zvolte svůj vlastní název auditu, použijte adresu URL kontejneru, který jste vytvořili v předchozích krocích):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Pokud není zadaný, `RETENTION_DAYS` výchozí hodnota je 0 (neomezená uchovávání).

    Další informace:
    - [Auditování rozdíly mezi spravované Instance, databázi SQL Azure a SQL Server](#subheading-3)
    - [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Vytvořte Server Audit Specification nebo specifikací auditu databáze, jako byste pro SQL Server:
    - [Vytvořit Server audit specification T-SQL Průvodce](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Vytvořit databázi audit specification T-SQL Průvodce](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Povolte auditování serveru, který jste vytvořili v kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analýza protokolů auditu
Existuje několik metod, které můžete použít k zobrazení protokolů auditování objektů blob.

- Pomocí funkce systému `sys.fn_get_audit_file` (T-SQL) se vrátit daty protokolu auditování v tabulkovém formátu. Další informace o použití této funkce najdete v tématu [sys.fn_get_audit_file dokumentaci](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Úplný seznam metod spotřeby protokolu auditu, najdete v části [začít pracovat s auditování databáze SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoda pro zobrazení záznamů auditu na portálu Azure (podokno 'Audit záznamy') není aktuálně dostupná pro spravované Instance.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Auditování rozdíly mezi spravované Instance, Azure SQL Database a SQL Server

Hlavní rozdíly mezi SQL auditu v spravované Instance, Azure SQL Database a SQL Server v místním jsou:

- Ve spravované Instance SQL Audit pracuje na úrovni serveru a úložišť `.xel` soubory protokolu na účtu úložiště Azure blob.
- V databázi SQL Azure SQL Audit funguje na úrovni databáze.
- V systému SQL Server na místním / virtuální počítače, SQL Audit funguje na serveru na úrovni, ale událostí úložiště pro soubory systémové protokoly událostí.

XEvent auditování ve spravované Instance podporuje cíle úložiště objektů blob v Azure. Protokoly souboru a windows jsou **nepodporuje**.

Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování do Azure blob storage jsou:
- Nové syntaxe `TO URL` je k dispozici a umožňuje zadat adresu URL kontejneru objektů blob v Azure Storage, kde `.xel` soubory jsou umístěny.
- Syntaxe `TO FILE` je **nepodporuje** vzhledem spravované Instance nelze získat přístup ke sdílené složky systému Windows.
- Možnost vypnutí je **nepodporuje**.
- `queue_delay` 0 je **nepodporuje**.


## <a name="next-steps"></a>Další postup

- Úplný seznam metod spotřeby protokolu auditu, najdete v části [začít pracovat s auditování databáze SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Další informace o Azure programy dodržování standardů tuto podporu, najdete v části [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).


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
