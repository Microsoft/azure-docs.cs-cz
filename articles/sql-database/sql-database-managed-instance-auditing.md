---
title: Auditování spravované instance
description: Zjistěte, jak začít s auditováním spravovaných instancí Azure SQL Database pomocí T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387763"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Začínáme s auditováním spravované instance Azure SQL Database

[Auditování spravovaných instancí](sql-database-managed-instance.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu úložiště Azure. Auditování také:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Umožňuje a usnadňuje dodržování předpisů, i když soulad s těmito standardy nezaručuje. Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centru zabezpečení Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Nastavení auditování serveru do úložiště Azure

Následující část popisuje konfiguraci auditování spravované instance.

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Vytvořte **kontejner** úložiště Azure, kde jsou uloženy protokoly auditování.

   1. Přejděte do úložiště Azure, kde chcete ukládat protokoly auditu.

      > [!IMPORTANT]
      > Použijte účet úložiště ve stejné oblasti jako spravovaná instance, abyste se vyhnuli čtení/zápisům mezi oblastmi.

   1. V účtu úložiště přejděte na **Přehled** a klikněte na **Objekty blob .**

      ![Widget Objekt blob Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. V horní nabídce klikněte na **+ Kontejner** a vytvořte nový kontejner.

      ![Ikona kontejneru vytvoření objektu blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Zadejte **název**kontejneru , nastavte úroveň veřejného přístupu na **soukromé**a klepněte na tlačítko **OK**.

      ![Vytvoření konfigurace kontejneru objektů blob](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Zákazník, který chce nakonfigurovat neměnné úložiště protokolů pro události auditu na úrovni serveru nebo databáze, by se měl řídit [pokyny poskytnutými službou Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Ujistěte se, že jste při konfiguraci neměnného úložiště objektů blob vybrali možnost **Povolit další připojení)**
  
3. Po vytvoření kontejneru pro protokoly auditu existují dva způsoby, jak jej nakonfigurovat jako cíl pro protokoly auditu: [pomocí T-SQL](#blobtsql) nebo [pomocí SQL Server Management Studio (SSMS) UI](#blobssms):

   - <a id="blobtsql"></a>Konfigurace úložiště blogu pro protokoly auditování pomocí T-SQL:

     1. V seznamu kontejnerů klikněte na nově vytvořený kontejner a potom klikněte na **vlastnosti kontejneru**.

        ![Tlačítko Vlastnosti kontejneru objektů blob](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Zkopírujte adresu URL kontejneru kliknutím na ikonu kopírování a uložte adresu URL (například do poznámkového bloku) pro budoucí použití. Formát adresy URL kontejneru by měl být`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adresa URL pro kopírování kontejnerů objektů Blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Vygenerujte token Azure Storage **SAS pro** udělení spravovaných instancí auditování přístupových práv k účtu úložiště:

        - Přejděte na účet Azure Storage, kde jste vytvořili kontejner v předchozím kroku.

        - V nabídce Nastavení úložiště klikněte na **Sdílený podpis** přístupu.

          ![Ikona podpisu sdíleného přístupu v nabídce nastavení úložiště](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Nakonfigurujte SAS takto:

          - **Povolené služby**: Blob

          - **Počáteční datum**: chcete-li se vyhnout problémům souvisejícím s časovým pásmem, doporučujeme použít včerejší datum

          - **Koncové datum**: zvolte datum, kdy vyprší platnost tohoto tokenu SAS.

            > [!NOTE]
            > Obnovte token po vypršení platnosti, abyste se vyhnuli selhání auditu.

          - Klikněte na **Vygenerovat SAS**.
            
            ![Konfigurace SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknutí na generovat SAS se v dolní části zobrazí token SAS. Zkopírujte token kliknutím na ikonu kopie a uložte jej (například do poznámkového bloku) pro budoucí použití.

          ![Kopírovat token SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Odeberte znak otazníku ("?") od začátku tokenu.

     1. Připojte se ke spravované instanci prostřednictvím sql server management studio (SSMS) nebo jiného podporovaného nástroje.

     1. Spusťte následující příkaz T-SQL a **vytvořte nové pověření** pomocí adresy URL kontejneru a tokenu SAS, které jste vytvořili v předchozích krocích:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Spusťte následující příkaz T-SQL k vytvoření nového auditu serveru (zvolte vlastní název auditu, použijte adresu URL kontejneru, kterou jste vytvořili v předchozích krocích). Pokud není `RETENTION_DAYS` zadán, výchozí hodnota je 0 (neomezené uchovávání informací):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Pokračovat [vytvořením specifikace auditu serveru nebo specifikace auditu databáze](#createspec)

   - <a id="blobssms"></a>Konfigurace úložiště objektů blob pro protokoly auditování pomocí SQL Server Management Studio (SSMS) 18 (Preview):

     1. Připojte se ke spravované instanci pomocí ui SQL Server Management Studio (SSMS).

     1. Rozbalte kořenovou poznámku Průzkumníka objektů.

     1. Rozbalte uzel **Zabezpečení,** klikněte pravým tlačítkem myši na uzel **Audity** a klikněte na "Nový audit":

        ![Rozšíření uzlu zabezpečení a auditu](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Ujistěte se, že "URL" je vybrána v **cíli auditu** a klikněte na **Procházet**:

        ![Procházet úložiště Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Nepovinné) Přihlaste se ke svému účtu Azure:

        ![Přihlášení k Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. V rozevíracích souborech vyberte předplatné, účet úložiště a kontejner objektů Blob nebo si vytvořte vlastní kontejner kliknutím na **vytvořit**. Po dokončení klikněte na **OK**:

        ![Vyberte předplatné Azure, účet úložiště a kontejner objektů blob.](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. V dialogovém okně Vytvořit audit klepněte na **tlačítko OK.**

4. <a id="createspec"></a>Po konfiguraci kontejneru objektů Blob jako cíl pro protokoly auditu, vytvořit a povolit specifikace auditu serveru nebo specifikace auditu databáze, stejně jako byste pro SQL Server:

   - [Vytvořit průvodce specifikací auditu serveru T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvořit specifikaci auditu databáze T-SQL průvodce](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Povolte audit serveru, který jste vytvořili v kroku 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Další informace:

- [Auditování rozdílů mezi jednotlivými databázemi, elastické fondy a spravované instance v Azure SQL Database a databáze mi sql server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [VYTVOŘIT AUDIT SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ZMĚNIT AUDIT SERVERU](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Nastavení auditování serveru do protokolů Centra událostí nebo Azure Monitoru

Protokoly auditu ze spravované instance lze odeslat do protokolů Even Hubs nebo Azure Monitor. Tato část popisuje, jak tuto konfiguraci nakonfigurovat:

1. Přejděte na [webu Azure Portal](https://portal.azure.com/) do spravované instance.

2. Klikněte na **Nastavení diagnostiky**.

3. Klikněte **na Zapnout diagnostiku**. Pokud je diagnostika již povolena, zobrazí se místo toho *nastavení +Přidat diagnostiku.*

4. V seznamu protokolů vyberte **sqlsecurityauditevents.**

5. Vyberte cíl pro události auditu – Centrum událostí, protokoly Azure Monitoru nebo obojí. Nakonfigurujte pro každý cíl požadované parametry (např. pracovní prostor Log Analytics).

6. Klikněte na **Uložit**.

    ![Konfigurace nastavení diagnostiky](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Připojte se ke spravované instanci pomocí **sql server management studio (SSMS)** nebo jiného podporovaného klienta.

8. Chcete-li vytvořit audit serveru, proveďte následující příkaz T-SQL:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Vytvořte a povolte specifikaci auditu serveru nebo specifikaci auditu databáze stejně jako pro SQL Server:

   - [Vytvořit průvodce specifikací auditu serveru T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvořit specifikaci auditu databáze T-SQL průvodce](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Povolte audit serveru vytvořený v kroku 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Využití protokolů auditu

### <a name="consume-logs-stored-in-azure-storage"></a>Využití protokolů uložených ve službě Azure Storage

Existuje několik metod, které můžete použít k zobrazení protokolů auditování objektů blob.

- Pomocí systémové `sys.fn_get_audit_file` funkce (T-SQL) vrátíte data protokolu auditu v tabulkovém formátu. Další informace o použití této funkce naleznete v [dokumentaci sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Protokoly auditu můžete prozkoumat pomocí nástroje, jako je [Například Průzkumník a údržby zařízení Azure](https://azure.microsoft.com/features/storage-explorer/). V úložišti Azure protokoly auditování jsou uloženy jako kolekce souborů objektů blob v rámci kontejneru, který byl definován pro ukládání protokolů auditu. Další podrobnosti o hierarchii složky úložiště, konvencí pojmenování a formátu protokolu naleznete v [tématu Odkaz na formát protokolu auditování objektů blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Úplný seznam metod využití protokolu auditu naleznete v části [Začínáme s auditováním databáze SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Využití protokolů uložených v centru událostí

Chcete-li využívat data protokolů auditu z centra událostí, budete muset nastavit datový proud, aby spotřebovával události a zapisovat je do cíle. Další informace najdete v tématu Dokumentace centra událostí Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Využití a analýza protokolů uložených v protokolech Azure Monitoru

Pokud protokoly auditu jsou zapsány do protokolů Azure Monitor, jsou k dispozici v pracovním prostoru Log Analytics, kde můžete spustit rozšířené vyhledávání na data auditu. Jako výchozí bod přejděte do pracovního prostoru Log Analytics a v části *Obecné* `search "SQLSecurityAuditEvents"` klikněte na *protokoly* a zadejte jednoduchý dotaz, například: chcete-li zobrazit protokoly auditu.  

Protokoly Azure Monitoru poskytují provozní přehledy v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, které umožňují snadno analyzovat miliony záznamů ve všech vašich úlohách a serverech. Další užitečné informace o jazyce hledání protokolů protokolů Azure Monitor a příkazy, najdete v [tématu Azure Monitor protokoly hledání odkaz](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Auditování rozdílů mezi databázemi v Azure SQL Database a databázemi na SQL Serveru

Hlavní rozdíly mezi auditováním v databázích v Azure SQL Database a databázemi na SQL Serveru jsou:

- Díky možnosti nasazení spravované instance v Azure SQL Database funguje `.xel` auditování na úrovni serveru a ukládá soubory protokolu do úložiště objektů Blob Azure.
- V místním sql serveru / virtuálních počítačích audit funguje na úrovni serveru, ale ukládá události v protokolech událostí systému souborů/systému Windows.

XEvent auditování ve spravované instanci podporuje cíle úložiště objektů Blob Azure. Protokoly souborů a oken **nejsou podporovány**.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování úložiště objektů blob Azure jsou:

- Je k `TO URL` dispozici nová syntaxe a umožňuje zadat adresu URL `.xel` kontejneru úložiště objektů blob Azure, kde jsou umístěny soubory.
- Nová syntaxe `TO EXTERNAL MONITOR` je k dispozici povolit cíle protokolů Even Hub a Azure Monitor.
- Syntaxe `TO FILE` **není podporována,** protože databáze SQL Database nemá přístup ke sdíleným položkům systému Windows.
- Možnost vypnutí **není podporována**.
- `queue_delay`0 **není podporováno**.

## <a name="next-steps"></a>Další kroky

- Úplný seznam metod využití protokolu auditu naleznete v části [Začínáme s auditováním databáze SQL](sql-database-auditing.md).
- Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centru zabezpečení Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

<!--Image references-->









