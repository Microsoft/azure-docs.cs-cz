---
title: Obnovení zálohy do Azure SQL Database Managed Instance | Microsoft Docs
description: Zjistěte, jak obnovit zálohu databáze do Azure SQL Database Managed Instance pomocí SSMS.
keywords: kurz k databázi sql, sql database managed instance, obnovení zálohy
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 5dd8b1f662f1ae6d6502743c6d976db4b58e962f
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920020"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Obnovení zálohy databáze do Azure SQL Database Managed Instance

V tomto kurzu se dozvíte, jak obnovit zálohu databáze uloženou v Azure Blob Storage do spravované instance pomocí zálohy Wide World Importers – Standard. Tato metoda s sebou nese nutný výpadek. Kurz migrace pomocí Azure Database Migration Service (DMS) najdete v tématu věnovaném [migraci Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md). Diskuzi o různých metodách migrace najdete v tématu [Migrace instance SQL Serveru do Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Stažení zálohy Wide World Importers – Standard
> * Vytvoření účtu úložiště Azure a nahrání zálohy
> * Obnovení databáze Wide World Importers ze zálohy

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se jako výchozí bod používají prostředky vytvořené zde: [Vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Stažení zálohy Wide World Importers – Standard

Pomocí následujících kroků stáhněte zálohu Wide World Importers – Standard.

V aplikaci Internet Explorer do pole Adresa URL zadejte https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak a po zobrazení výzvy kliknutím na **Uložit** uložte tento soubor do složky **Stažené soubory**.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Vytvoření účtu úložiště Azure a nahrání zálohy

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte **Úložiště** a pak kliknutím na **Účet úložiště** otevřete formulář účtu úložiště.

   ![účet úložiště](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Ve formuláři účtu úložiště vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Model nasazení**|Model prostředků||
   |**Druh účtu**|Blob Storage||
   |**Výkon**|Standard nebo Premium|Magnetické jednotky nebo disky SSD|
   |**Replikace**|(Locally redundant storage) Místně redundantní úložiště||
   |** Úroveň přístupu (výchozí)|Studená nebo horká||
   |**Vyžádání bezpečného přenosu**|Zakázáno||
   |**Předplatné**|Vaše předplatné|Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions).|
   |**Skupina prostředků**|Skupina prostředků, kterou jste vytvořili dříve|| 
   |**Umístění**|Umístění, které jste vybrali dříve||
   |**Virtuální sítě**|Zakázáno||

4. Klikněte na možnost **Vytvořit**.

   ![podrobnosti o účtu úložiště](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Po úspěšném nasazení účtu úložiště otevřete svůj nový účet úložiště.
6. V části **Nastavení** kliknutím na **Sdílený přístupový podpis** otevřete formulář sdíleného přístupového podpisu (SAS).

   ![formulář SAS](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Ve formuláři SAS podle potřeby upravte výchozí hodnoty. Všimněte si, že datum/čas vypršení platnosti je ve výchozím nastavení pouze 8 hodin.
8. Klikněte na **Vygenerovat SAS**.

   ![dokončený formulář SAS](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Zkopírujte a uložte hodnoty **Token SAS** a **Adresa URL SAS serveru Blob**.
10. V části **Nastavení** klikněte na **Kontejnery**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Kliknutím na **+ Kontejner** vytvořte kontejner pro uložení vaší zálohy.
12. Ve formuláři kontejneru vyplňte požadované informace s použitím informací v následující tabulce:

    | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   |**Název**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Úroveň veřejného přístupu**|Privátní (bez anonymního přístupu)||

    ![podrobnosti o kontejneru](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Klikněte na **OK**.
14. Po vytvoření kontejneru ho otevřete.

    ![kontejner](./media/sql-database-managed-instance-tutorial/container.png)

15. Klikněte na **Vlastnosti kontejneru** a pak zkopírujte adresu URL ke kontejneru.

    ![adresa URL kontejneru](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kliknutím na **Nahrát** otevřete formulář **Nahrát objekt blob**.

    ![nahrání](./media/sql-database-managed-instance-tutorial/upload.png)

17. Přejděte do své složky pro stahování a vyberte soubor **WideWorldIimporters-Standard.bak**.

    ![nahrání](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Klikněte na **Odeslat**.
19. Nepokračujte, dokud se nahrávání nedokončí.

    ![dokončené nahrávání](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Obnovení databáze Wide World Importers ze zálohy

Pomocí aplikace SSMS podle následujících kroků obnovte ze zálohy databázi Wide World Importers do své Managed Instance.

1. V aplikaci SSMS otevřete nové okno dotazu.
2. Pomocí následujícího skriptu vytvořte pověření SAS – zadejte adresu URL kontejneru účtu úložiště a klíč SAS, jak je uvedeno níže.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![pověření](./media/sql-database-managed-instance-tutorial/credential.png)

3. Pomocí následující skriptu zkontrolujte platnost pověření SAS a zálohy – zadejte adresu URL kontejneru se zálohou:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Pomocí následujícího skriptu obnovte ze zálohy databázi Wide World Importers – zadejte adresu URL kontejneru se zálohou:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![provádění obnovení](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Pokud chcete sledovat stav obnovení, spusťte v nové relaci dotazu následující dotaz:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![průběh obnovení v procentech](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Po dokončení obnovení zobrazte databázi v Průzkumníku objektů. 

    ![dokončené obnovení](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak obnovit zálohu databáze uloženou v Azure Blob Storage do spravované instance pomocí zálohy Wide World Importers – Standard. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Stažení zálohy Wide World Importers – Standard
> * Vytvoření účtu úložiště Azure a nahrání zálohy
> * Obnovení databáze Wide World Importers ze zálohy

Přejděte na další kurz, ve kterém se naučíte migrovat SQL Server do Azure SQL Database Managed Instance pomocí DMS.

> [!div class="nextstepaction"]
>[Migrace SQL Serveru do Azure SQL Database Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md)
