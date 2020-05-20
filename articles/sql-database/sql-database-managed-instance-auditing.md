---
title: Auditování spravované instance
description: Naučte se, jak začít s Azure SQL Database auditování spravované instance pomocí T-SQL.
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
ms.openlocfilehash: 7656944af16db650ec1fea36f0bdefc81b99922e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654623"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Začínáme s auditováním spravované instance Azure SQL Database

Auditování [spravované instance](sql-database-managed-instance.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage. Auditování také:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Umožňuje a usnadňuje dodržování předpisů, i když soulad s těmito standardy nezaručuje. Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centrum zabezpečení Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Nastavení auditování serveru do služby Azure Storage

V následující části je popsána konfigurace auditování na spravované instanci.

1. Přejít na [Azure Portal](https://portal.azure.com).
2. Vytvořte **kontejner** Azure Storage, ve kterém jsou uložené protokoly auditu.

   1. Přejděte do Azure Storage, kam chcete ukládat protokoly auditu.

      > [!IMPORTANT]
      > Použijte účet úložiště ve stejné oblasti jako spravovanou instanci, aby nedocházelo ke čtení a zápisu mezi jednotlivými oblastmi. Pokud je váš účet úložiště za Virtual Network nebo bránou firewall, přečtěte si téma [udělení přístupu z virtuální sítě](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network).

   1. V účtu úložiště přejděte na **Přehled** a klikněte na **objekty blob**.

      ![Widget Azure Blob](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. V horní nabídce klikněte na **+ kontejner** a vytvořte nový kontejner.

      ![Ikona vytvoření kontejneru objektů BLOB](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Zadejte **název**kontejneru, nastavte úroveň veřejného přístupu na **Private**a pak klikněte na **OK**.

      ![Vytvořit konfiguraci kontejneru objektů BLOB](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Zákazník, který chce nakonfigurovat neměnné úložiště protokolů pro události auditu na úrovni serveru nebo databáze, by měl postupovat podle [pokynů Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Ujistěte se prosím, že jste při konfiguraci neměnného úložiště objektů BLOB vybrali možnost **povolení dalších připojení** ).
  
3. Po vytvoření kontejneru pro protokoly auditu existují dva způsoby, jak ho nakonfigurovat jako cíl pro protokoly auditu: [pomocí T-SQL](#blobtsql) nebo [pomocí uživatelského rozhraní SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Konfigurace úložiště blogu pro protokoly auditu pomocí T-SQL:

     1. V seznamu kontejnery klikněte na nově vytvořený kontejner a pak klikněte na **vlastnosti kontejneru**.

        ![Tlačítko Vlastnosti kontejneru objektů BLOB](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Zkopírujte adresu URL kontejneru kliknutím na ikonu kopírování a uložte adresu URL (například v poznámkovém bloku) pro budoucí použití. Formát adresy URL kontejneru by měl být`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adresa URL pro kopírování kontejneru objektů BLOB](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Vygenerujte Azure Storage **token SAS** pro udělení oprávnění auditování spravované instance účtu úložiště:

        - Přejděte na účet Azure Storage, kde jste kontejner vytvořili v předchozím kroku.

        - V nabídce nastavení úložiště klikněte na **sdílený přístupový podpis** .

          ![Ikona sdíleného přístupového podpisu v nabídce nastavení úložiště](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - SAS nakonfigurujte následujícím způsobem:

          - **Povolené služby**: BLOB

          - **Počáteční datum**: aby nedocházelo k problémům souvisejícím s časovými pásmy, doporučujeme použít včerejší datum.

          - **Koncové datum**: vyberte datum, kdy vyprší platnost tohoto tokenu SAS.

            > [!NOTE]
            > Obnovte token po vypršení platnosti, abyste se vyhnuli selháním auditu.

          - Klikněte na **Vygenerovat SAS**.
            
            ![Konfigurace SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknutí na generovat SAS se token SAS zobrazuje dole. Zkopírujte token tak, že kliknete na ikonu kopírování a uložíte ho (například v poznámkovém bloku) pro budoucí použití.

          ![Kopírovat token SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Odstraňte znak otazníku ("?") od začátku tokenu.

     1. Připojte se ke spravované instanci prostřednictvím SQL Server Management Studio (SSMS) nebo jakéhokoli jiného podporovaného nástroje.

     1. Spusťte následující příkaz T-SQL k **Vytvoření nového přihlašovacího údaje** pomocí adresy URL kontejneru a tokenu SAS, který jste vytvořili v předchozích krocích:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Spuštěním následujícího příkazu T-SQL vytvořte nový audit serveru (vyberte vlastní název auditu, použijte adresu URL kontejneru, kterou jste vytvořili v předchozích krocích). Pokud není zadaný, `RETENTION_DAYS` Výchozí hodnota je 0 (neomezené uchovávání):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Pokračování [vytvořením specifikace auditu serveru nebo specifikace auditu databáze](#createspec)

   - <a id="blobssms"></a>Nakonfigurujte úložiště objektů BLOB pro protokoly auditu pomocí SQL Server Management Studio (SSMS) 18 (Preview):

     1. Připojte se ke spravované instanci pomocí uživatelského rozhraní SQL Server Management Studio (SSMS).

     1. Rozbalte hlavní poznámku Průzkumník objektů.

     1. Rozbalte uzel **zabezpečení** , klikněte pravým tlačítkem na uzel **audits** a klikněte na nový audit:

        ![Rozbalte uzel zabezpečení a audit.](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Ujistěte se, že je v **cíli auditu** vybraná možnost Adresa URL a klikněte na **Procházet**:

        ![Procházet Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. Volitelné Přihlaste se ke svému účtu Azure:

        ![Přihlášení k Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. V rozevíracích seznamech vyberte předplatné, účet úložiště a kontejner objektů blob, nebo vytvořte vlastní kontejner kliknutím na **vytvořit**. Po dokončení klikněte na **OK**:

        ![Výběr předplatného Azure, účtu úložiště a kontejneru objektů BLOB](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klikněte na **OK** v dialogovém okně vytvořit audit.

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

- [Rozdíly v auditování mezi izolovanými databázemi, elastickými fondy a spravovanými instancemi v Azure SQL Database a databázích v SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [VYTVOŘIT AUDIT SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Nastavení auditování serveru do centra událostí nebo protokolů Azure Monitor

Protokoly auditu ze spravované instance je možné odesílat i do hub nebo Azure Monitor protokolů. Tato část popisuje, jak nakonfigurovat tuto konfiguraci:

1. Přejděte na webu [Azure Portal](https://portal.azure.com/) do spravované instance.

2. Klikněte na **nastavení diagnostiky**.

3. Klikněte na **zapnout diagnostiku**. Pokud je už Diagnostika povolená, zobrazí se místo toho *možnost + Přidat diagnostiku* .

4. V seznamu protokolů vyberte **SQLSecurityAuditEvents** .

5. Vyberte cíl pro události auditu – centrum událostí, protokoly Azure Monitor, nebo obojí. Pro každý cíl nakonfigurujte požadované parametry (třeba pracovní prostor Log Analytics).

6. Klikněte na **Uložit**.

    ![Konfigurace nastavení diagnostiky](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

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

- Úplný seznam metod spotřeby protokolu auditu najdete v tématu [Začínáme s auditováním služby SQL Database](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Využívání protokolů uložených v centru událostí

Aby bylo možné využívat protokoly auditu z centra událostí, budete muset nastavit datový proud, který bude zpracovávat události a zapsat je do cíle. Další informace najdete v dokumentaci k Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Využití a analýza protokolů uložených v protokolech Azure Monitor

Pokud jsou protokoly auditu zapisovány do protokolů Azure Monitor, jsou k dispozici v pracovním prostoru Log Analytics, kde můžete spustit pokročilé vyhledávání dat auditu. Jako výchozí bod přejděte do pracovního prostoru Log Analytics a v části *Obecné* klikněte na *protokoly* a zadejte jednoduchý dotaz, například: `search "SQLSecurityAuditEvents"` k zobrazení protokolů auditu.  

Protokoly Azure Monitor poskytují informace o provozu v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, které umožňují snadno analyzovat miliony záznamů napříč všemi vašimi úlohami a servery. Další užitečné informace o tom, jak hledat jazyk a příkazy protokolu Azure Monitor naleznete v tématu [Azure monitor v protokolech hledání](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Rozdíly v auditování mezi databázemi v Azure SQL Database a databázích v SQL Server

Hlavní rozdíly mezi auditováním v databázích v Azure SQL Database a databázích v SQL Server jsou:

- S možností nasazení Managed instance v Azure SQL Database funguje auditování na úrovni serveru a ukládá `.xel` soubory protokolů v úložišti objektů BLOB v Azure.
- V SQL Server místní nebo virtuální počítače funguje audit na úrovni serveru, ale ukládá události do souborů protokolů událostí systému nebo Windows.

Auditování XEvent ve spravované instanci podporuje cíle služby Azure Blob Storage. Soubory a protokoly systému Windows nejsou **podporovány**.

Hlavní rozdíly v `CREATE AUDIT` syntaxi pro auditování do úložiště objektů BLOB v Azure jsou:

- K `TO URL` dispozici je nová syntaxe, která umožňuje zadat adresu URL kontejneru úložiště objektů BLOB v Azure, ve kterém `.xel` jsou soubory umístěné.
- `TO EXTERNAL MONITOR`K dispozici je nová syntaxe, která umožňuje, aby byly cílení na rozbočovač a Azure monitor protokoly.
- Syntaxe `TO FILE` není **podporována** , protože SQL Database nemá přístup ke sdíleným složkám souborů systému Windows.
- Možnost vypnutí není **podporována**.
- `queue_delay`hodnota 0 není **podporována**.

## <a name="next-steps"></a>Další kroky

- Úplný seznam metod spotřeby protokolu auditu najdete v tématu [Začínáme s auditováním služby SQL Database](sql-database-auditing.md).
- Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centrum zabezpečení Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

<!--Image references-->









