---
title: Auditování spravované instance SQL
description: Naučte se, jak začít s auditováním spravované instance Azure SQL pomocí T-SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: d8a6ead23e080b5e1e17403873e2dbaedc0ce177
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620354"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Začínáme s auditováním spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Auditování [spravované instance Azure SQL](sql-managed-instance-paas-overview.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu úložiště Azure. Auditování také:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Umožňuje a usnadňuje dodržování předpisů, i když soulad s těmito standardy nezaručuje. Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centrum zabezpečení Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde můžete najít nejaktuálnější seznam certifikátů dodržování předpisů.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Nastavte auditování pro server tak, aby Azure Storage

V následující části je popsána konfigurace auditování na spravované instanci.

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vytvořte **kontejner** Azure Storage, ve kterém jsou uložené protokoly auditu.

   1. Přejděte do účtu služby Azure Storage, do kterého chcete ukládat protokoly auditu.

      > [!IMPORTANT]
      > - Použijte účet úložiště ve stejné oblasti jako spravovanou instanci, aby nedocházelo ke čtení a zápisu mezi jednotlivými oblastmi. 
      > - Pokud je váš účet úložiště za Virtual Network nebo bránou firewall, přečtěte si téma [udělení přístupu z virtuální sítě](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).
      > - Pokud změníte dobu uchování z 0 (neomezené uchovávání) na jakoukoli jinou hodnotu, pamatujte na to, že doba uchovávání se bude vztahovat jenom na protokoly zapsané po změně hodnoty uchování (protokoly zapsané během období, kdy byla doba uchování nastavená na neomezený, se zachovají i po povolení uchování).

   1. V účtu úložiště přejděte na **Přehled** a klikněte na **objekty blob**.

      ![Widget objektů blob Azure](./media/auditing-configure/1_blobs_widget.png)

   1. V horní nabídce klikněte na **+ kontejner** a vytvořte nový kontejner.

      ![Ikona vytvoření kontejneru objektů BLOB](./media/auditing-configure/2_create_container_button.png)

   1. Zadejte **název**kontejneru, nastavte **úroveň veřejného přístupu** na **Private**a pak klikněte na **OK**.

      ![Vytvořit konfiguraci kontejneru objektů BLOB](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Zákazníci, kteří chtějí nakonfigurovat neměnné úložiště protokolů pro události auditu na úrovni serveru nebo databáze, by měli postupovat podle [pokynů Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes). (Pokud konfigurujete neměnné úložiště objektů blob, ujistěte se, že jste vybrali možnost **povolení dalších připojení** .)
  
3. Po vytvoření kontejneru pro protokoly auditu existují dva způsoby, jak ho nakonfigurovat jako cíl pro protokoly auditu: [pomocí T-SQL](#blobtsql) nebo [pomocí uživatelského rozhraní SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Konfigurace úložiště objektů BLOB pro protokoly auditu pomocí T-SQL:

     1. V seznamu kontejnery klikněte na nově vytvořený kontejner a pak klikněte na **vlastnosti kontejneru**.

        ![Tlačítko Vlastnosti kontejneru objektů BLOB](./media/auditing-configure/4_container_properties_button.png)

     1. Zkopírujte adresu URL kontejneru kliknutím na ikonu kopírování a uložte adresu URL (například v poznámkovém bloku) pro budoucí použití. Formát adresy URL kontejneru by měl být `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adresa URL pro kopírování kontejneru objektů BLOB](./media/auditing-configure/5_container_copy_name.png)

     1. Vygenerujte Azure Storage **token SAS** pro udělení oprávnění auditování spravované instance účtu úložiště:

        - Přejděte do účtu služby Azure Storage, kde jste kontejner vytvořili v předchozím kroku.

        - V nabídce **Nastavení úložiště** klikněte na **sdílený přístupový podpis** .

          ![Ikona sdíleného přístupového podpisu v nabídce nastavení úložiště](./media/auditing-configure/6_storage_settings_menu.png)

        - SAS nakonfigurujte následujícím způsobem:

          - **Povolené služby**: BLOB

          - **Počáteční datum**: Chcete-li se vyhnout problémům s časovými pásmy, použijte včerejší datum.

          - **Koncové datum**: vyberte datum, kdy vyprší platnost tohoto tokenu SAS.

            > [!NOTE]
            > Obnovte token po vypršení platnosti, abyste se vyhnuli selháním auditu.

          - Klikněte na **Vygenerovat SAS**.

            ![Konfigurace SAS](./media/auditing-configure/7_sas_configure.png)

        - Token SAS se zobrazí v dolní části. Zkopírujte token tak, že kliknete na ikonu kopírování a uložíte ho (například v poznámkovém bloku) pro budoucí použití.

          ![Kopírovat token SAS](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Odstraňte znak otazníku ("?") od začátku tokenu.

     1. Připojte se ke svojí spravované instanci prostřednictvím SQL Server Management Studio nebo jakéhokoli jiného podporovaného nástroje.

     1. Spusťte následující příkaz T-SQL k **Vytvoření nového přihlašovacího údaje** pomocí adresy URL kontejneru a tokenu SAS, který jste vytvořili v předchozích krocích:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Spuštěním následujícího příkazu T-SQL vytvořte nový audit serveru (vyberte vlastní název auditu a použijte adresu URL kontejneru, kterou jste vytvořili v předchozích krocích). Pokud není zadaný, `RETENTION_DAYS` Výchozí hodnota je 0 (neomezené uchovávání):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

        Pokračujte [vytvořením specifikace auditu serveru nebo specifikace auditu databáze](#createspec).

   - <a id="blobssms"></a>Konfigurace úložiště objektů BLOB pro protokoly auditu pomocí SQL Server Management Studio 18 (Preview):

     1. Připojte se ke spravované instanci pomocí uživatelského rozhraní SQL Server Management Studio.

     1. Rozbalte hlavní poznámku Průzkumník objektů.

     1. Rozbalte uzel **zabezpečení** , klikněte pravým tlačítkem na uzel **audity** a klikněte na **nový audit**:

        ![Rozbalte uzel zabezpečení a audit.](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Ujistěte se, že je v **cíli auditu** vybraná **Adresa URL** a klikněte na **Procházet**:

        ![Procházet Azure Storage](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Volitelné Přihlaste se ke svému účtu Azure:

        ![Přihlášení k Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. V rozevíracích seznamech vyberte předplatné, účet úložiště a kontejner objektů blob, nebo vytvořte vlastní kontejner kliknutím na **vytvořit**. Po dokončení klikněte na **OK**:

        ![Výběr předplatného Azure, účtu úložiště a kontejneru objektů BLOB](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Klikněte na **OK** v dialogovém okně **vytvořit audit** .

4. <a id="createspec"></a>Po nakonfigurování kontejneru objektů BLOB jako cíle pro protokoly auditu vytvořte a povolte specifikace auditu serveru nebo specifikace auditu databáze jako u SQL Server:

   - [Vytvoření specifikace auditu serveru – Průvodce T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvoření specifikace auditu databáze Průvodce T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Povolte audit serveru, který jste vytvořili v kroku 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Další informace:

- [Rozdíly v auditování mezi spravovanou instancí Azure SQL a databází v SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [VYTVOŘIT AUDIT SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Nastavení auditování serveru pro Event Hubs nebo Azure Monitor protokolů

Protokoly auditu ze spravované instance se dají odesílat do protokolů Azure Event Hubs nebo Azure Monitor. Tato část popisuje, jak nakonfigurovat tuto konfiguraci:

1. Přejděte v [Azure Portal](https://portal.azure.com/) do spravované instance.

2. Klikněte na **nastavení diagnostiky**.

3. Klikněte na **zapnout diagnostiku**. Pokud je diagnostika už povolená, zobrazí se místo toho **+ Přidat nastavení diagnostiky** .

4. V seznamu protokolů vyberte **SQLSecurityAuditEvents** .

5. Vyberte cíl pro události auditu: Event Hubs, protokoly Azure Monitor nebo obojí. Pro každý cíl nakonfigurujte požadované parametry (třeba pracovní prostor Log Analytics).

6. Klikněte na **Uložit**.

    ![Konfigurace nastavení diagnostiky](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Připojte se ke spravované instanci pomocí **SQL Server Management Studio (SSMS)** nebo jakéhokoli jiného podporovaného klienta.

8. Spusťte následující příkaz T-SQL pro vytvoření auditu serveru:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Vytvořte a povolte specifikace auditu serveru nebo specifikace auditu databáze jako u SQL Server:

   - [Vytvoření specifikace auditu serveru – Průvodce T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvoření specifikace auditu databáze Průvodce T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Povolte audit serveru vytvořený v kroku 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Využívat protokoly auditu

### <a name="consume-logs-stored-in-azure-storage"></a>Využívat protokoly uložené v Azure Storage

Existuje několik metod, pomocí kterých můžete zobrazit protokoly auditování objektů BLOB.

- Pomocí funkce System `sys.fn_get_audit_file` (T-SQL) Vraťte data protokolu auditu v tabulkovém formátu. Další informace o použití této funkce najdete v dokumentaci k [Sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Protokoly auditu můžete prozkoumat pomocí nástroje, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). V Azure Storage se protokoly auditování ukládají jako kolekce souborů objektů BLOB v kontejneru, který jste definovali pro uložení protokolů auditu. Další podrobnosti o hierarchii složky úložiště, konvencí pojmenování a formátu protokolu najdete v referenčních informacích o [formátu protokolu auditu objektů BLOB](https://go.microsoft.com/fwlink/?linkid=829599).

- Úplný seznam metod spotřeby protokolu auditu najdete v tématu [Začínáme s Azure SQL Database auditování](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Využívat protokoly uložené v Event Hubs

Pro využívání dat protokolů auditu z Event Hubs budete muset nastavit datový proud, který bude zpracovávat události, a zapsat je do cíle. Další informace najdete v dokumentaci k Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Využití a analýza protokolů uložených v protokolech Azure Monitor

Pokud jsou protokoly auditu zapisovány do protokolů Azure Monitor, jsou k dispozici v pracovním prostoru Log Analytics, kde můžete spustit pokročilé vyhledávání dat auditu. Jako výchozí bod přejděte do pracovního prostoru Log Analytics. V části **Obecné** klikněte na **protokoly** a zadejte jednoduchý dotaz, například: `search "SQLSecurityAuditEvents"` k zobrazení protokolů auditu.  

Protokoly Azure Monitor poskytují informace o provozu v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, které umožňují snadno analyzovat miliony záznamů napříč všemi vašimi úlohami a servery. Další užitečné informace o tom, jak hledat jazyk a příkazy protokolu Azure Monitor naleznete v tématu [Azure monitor v protokolech hledání](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Rozdíly v auditování mezi databázemi ve spravované instanci Azure SQL a databázemi v SQL Server

Hlavní rozdíly mezi auditováním v databázích ve službě Azure SQL Managed instance a databázích v SQL Server jsou:

- Pomocí spravované instance Azure SQL funguje auditování na úrovni serveru a ukládá `.xel` soubory protokolů ve službě Azure Blob Storage.
- V SQL Server Audit funguje na úrovni serveru, ale ukládá události do souborů protokolů událostí systému nebo Windows.

Auditování XEvent ve spravovaných instancích podporuje cíle služby Azure Blob Storage. Soubory a protokoly systému Windows nejsou **podporovány**.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování do úložiště objektů BLOB v Azure jsou:

- K `TO URL` dispozici je nová syntaxe, která umožňuje zadat adresu URL kontejneru úložiště objektů BLOB v Azure, ve kterém `.xel` jsou soubory umístěné.
- `TO EXTERNAL MONITOR`K dispozici je nová syntaxe umožňující Event Hubs a Azure monitor cíle protokolu.
- Syntaxe `TO FILE` není **podporovaná** , protože spravovaná instance Azure SQL nemůže přistupovat ke sdíleným složkám souborů systému Windows.
- Možnost vypnutí není **podporována**.
- `queue_delay` hodnota 0 není **podporována**.

## <a name="next-steps"></a>Další kroky

- Úplný seznam metod spotřeby protokolu auditu najdete v tématu [Začínáme s Azure SQL Database auditování](../../azure-sql/database/auditing-overview.md).
- Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centrum zabezpečení Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde můžete najít nejaktuálnější seznam certifikátů dodržování předpisů.

<!--Image references-->
