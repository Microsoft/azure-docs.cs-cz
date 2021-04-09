---
title: Použití spravovaných identit pro přístup k Azure SQL Database nebo Azure synapse Analytics – Azure Stream Analytics
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit vaši Azure Stream Analyticsovou úlohu pro Azure SQL Database nebo výstup Azure synapse Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487912"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Použití spravovaných identit pro přístup k Azure SQL Database nebo ke službě Azure synapse Analytics z úlohy Azure Stream Analytics (Preview)

Azure Stream Analytics podporuje [spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) pro Azure SQL Database a výstupní jímky Azure synapse Analytics. Spravované identity odstraňují omezení metod ověřování založených na uživatelích, například nutnost opětovného ověření z důvodu změny hesla nebo vypršení platnosti tokenu uživatele, ke kterému dochází každých 90 dní. Když odeberete nutnost ručního ověřování, vaše nasazení Stream Analytics můžou být plně automatizovaná.

Spravovaná identita je spravovaná aplikace zaregistrovaná v Azure Active Directory, která představuje danou Stream Analytics úlohu. Spravovaná aplikace se používá k ověření cílového prostředku. V tomto článku se dozvíte, jak povolit spravovanou identitu pro Azure SQL Database nebo výstupy Azure synapse Analytics Stream Analytics úlohy prostřednictvím Azure Portal.

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k připojení vaší Stream Analytics úlohy do fondu SQL Azure SQL Database nebo Azure synapse Analytics pomocí spravovaného režimu ověřování identity. 

- Nejdřív vytvoříte spravovanou identitu přiřazenou systémem pro Stream Analyticsovou úlohu. To je identita vaší úlohy v Azure Active Directory.  

- Přidejte Správce služby Active Directory do pracovního prostoru SQL serveru nebo synapse, který umožňuje ověřování Azure AD (spravované identity) pro daný prostředek.

- Dále vytvořte obsaženého uživatele reprezentujícího identitu Stream Analytics úlohy v databázi. Pokaždé, když Stream Analytics úloha spolupracuje s vaším prostředkem databáze SQL DB nebo synapse databáze SQL, bude se jednat o identitu, na které se budou kontrolovat oprávnění Stream Analytics úlohy.

- Udělte vašemu Stream Analytics úloze oprávnění k přístupu k vašim fondům SQL SQL Database nebo synapse.

- Nakonec přidejte své Azure SQL Database nebo Azure synapse Analytics jako výstup do úlohy Stream Analytics.

## <a name="prerequisites"></a>Požadavky

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

K použití této funkce jsou potřeba následující:

- Azure Stream Analytics úloha.

- Prostředek Azure SQL Database.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

K použití této funkce jsou potřeba následující:

- Azure Stream Analytics úloha.

- Fond SQL Azure synapse Analytics.

- Účet Azure Storage, který je [nakonfigurován pro vaši úlohu Stream Analytics](azure-synapse-analytics-output.md).

- Poznámka: služba MSI úložiště účtu Stream Analytics integrovaná s synapse SQL MSI není momentálně k dispozici.

---

## <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Nejdřív vytvoříte spravovanou identitu pro svou Azure Stream Analytics úlohu.

1. V [Azure Portal](https://portal.azure.com)otevřete Azure Stream Analytics úlohu.

1. V levém navigačním panelu vyberte **spravovanou identitu** nacházející se v části **Konfigurovat**. Potom zaškrtněte políčko vedle **použít spravovanou identitu přiřazenou systémem** a vyberte **Uložit**.

   ![Vybrat spravovanou identitu přiřazenou systémem](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   Instanční objekt pro identitu Stream Analytics úlohy se vytvoří v Azure Active Directory. Životní cyklus nově vytvořené identity se spravuje v Azure. Po odstranění úlohy Stream Analytics se služba Azure automaticky odstraní přidružená identita (tj. instanční objekt).

1. Při uložení konfigurace se ID objektu (OID) instančního objektu zobrazí jako ID objektu zabezpečení, jak je znázorněno níže: 

   ![ID objektu zobrazené jako ID objektu zabezpečení](./media/sql-db-output-managed-identity/principal-id.png)

   Instanční objekt má stejný název jako Stream Analytics úloha. Například pokud je název úlohy *MyASAJob*, název instančního objektu je také *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Vybrat Správce služby Active Directory

Po vytvoření spravované identity vyberete Správce služby Active Directory.

1. Přejděte ke svému Azure SQL Database nebo prostředku fondu SQL Azure synapse Analytics a vyberte pracovní prostor SQL Server nebo synapse, pod kterým je prostředek. Odkaz na tyto informace najdete na stránce Přehled prostředků vedle *názvu serveru* nebo *názvu pracovního prostoru*.

1. V části **Nastavení** klikněte na **Správce služby Active Directory** nebo **Správce služby SQL Active Directory** a pro SQL Server a synapse pracovní prostor. Pak vyberte **nastavit správce**.

   ![Stránka pro správu služby Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Na stránce Správce služby Active Directory vyhledejte uživatele nebo skupinu pro správce SQL Server a klikněte na **Vybrat**. Toto bude uživatel, který bude moci vytvořit **uživatele databáze s omezením** v následující části.

   ![Přidat správce služby Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   Na stránce Správce služby Active Directory se zobrazují všichni členové a skupiny služby Active Directory. Vybarvení uživatelů nebo skupin nelze vybrat, protože nejsou podporovány jako správci Azure Active Directory. Seznam podporovaných správců najdete v části **Azure Active Directory funkcí a omezeních**    [použití Azure Active Directory ověřování pro ověřování pomocí SQL Database nebo synapse Azure](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Vyberte **Uložit** na stránce **Správce služby Active Directory** . Proces změny správce trvá několik minut.

## <a name="create-a-contained-database-user"></a>Vytvořit uživatele databáze s omezením

V dalším kroku vytvoříte uživatele databáze s omezením ve službě Azure SQL nebo Azure synapse Database, která je namapovaná na Azure Active Directory identitu. Uživatel databáze s omezením nemá přihlášení k primární databázi, ale mapuje se na identitu v adresáři, který je přidružen k databázi. Azure Active Directory identitou může být individuální uživatelský účet nebo skupina. V takovém případě chcete pro úlohu Stream Analytics vytvořit uživatele databáze s omezením. 

Další informace najdete v následujícím článku, kde najdete informace o integraci služby Azure AD: [univerzální ověřování pomocí SQL Database a Azure synapse Analytics (podpora SSMS pro MFA)](../azure-sql/database/authentication-mfa-ssms-overview.md) .

1. Připojte se k databázi SQL Azure nebo Azure synapse pomocí SQL Server Management Studio. **Uživatelské jméno** je Azure Active Directory uživatel s oprávněním **ALTER ANY User** . Příkladem je správce, který jste nastavili na SQL Server. Používejte **Azure Active Directory – Universal s** ověřováním MFA. 

   ![Připojení k SQL Serveru](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Název serveru se `<SQL Server name>.database.windows.net` může v různých oblastech lišit. Například oblast Čína by měla použít `<SQL Server name>.database.chinacloudapi.cn` .
 
   Konkrétní databázi Azure SQL nebo Azure synapse můžete zadat tak, že kliknete na **možnosti > vlastnosti připojení > připojit k databázi**.  

   ![Vlastnosti připojení SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Při prvním připojení se může zobrazit toto okno:

   ![Nové okno pravidla brány firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Pokud ano, v Azure Portal klikněte na prostředek pracovního prostoru SQL Server/synapse. V části **zabezpečení** otevřete stránku **brány firewall a virtuální síť/brány firewall** . 
   1. Přidejte nové pravidlo s libovolným názvem pravidla.
   1. Pro *Počáteční IP* adresu použijte *z* IP adresy z **nového okna pravidla brány firewall** .
   1. Pro *koncovou IP* adresu *použijte IP adresu* z **nového okna pravidla brány firewall** . 
   1. Vyberte **Uložit** a pokuste se o připojení znovu z SQL Server Management Studio. 

1. Jakmile budete připojeni, vytvořte uživatele databáze s omezením. Následující příkaz SQL vytvoří uživatele databáze s omezením, který má stejný název jako vaše Stream Analyticsová úloha. Nezapomeňte zahrnout hranaté závorky kolem *ASA_JOB_NAME*. Použijte následující syntaxi T-SQL a spusťte dotaz. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Chcete-li ověřit, zda jste uživatele databáze s omezením přidali správně, spusťte následující příkaz v SSMS v příslušné databázi a zkontrolujte, zda je *ASA_JOB_NAME* pod sloupcem "název".

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Aby Azure Active Directory Microsoftu mohl ověřit, jestli má Stream Analytics úlohy přístup k SQL Database, musíme pro komunikaci s databází poskytnout Azure Active Directory oprávnění. Provedete to tak, že přejdete na stránku brány firewall a virtuální sítě/brány firewall v Azure Portal znovu a povolíte možnost Povolit službám a prostředkům Azure přístup k tomuto serveru nebo pracovnímu prostoru.

   ![Brána firewall a virtuální síť](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Udělení oprávnění pro úlohy Stream Analytics

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Po vytvoření uživatele databáze s omezením a přístupu ke službám Azure na portálu, jak je popsáno v předchozí části, má vaše úloha Stream Analytics oprávnění ze spravované identity pro **připojení** k vašemu prostředku Azure SQL Database prostřednictvím spravované identity. Doporučujeme udělit oprávnění k **výběru** a **vložení** do úlohy Stream Analytics, jak budou potřeba později v pracovním postupu Stream Analytics. Oprávnění **Select** umožňuje úloze testovat připojení k tabulce ve službě Azure SQL Database. Po nakonfigurování vstupu a výstupu služby Azure SQL Database jsou oprávnění k **vložení** umožňovat testování koncových Stream Analytics dotazů.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Po vytvoření uživatele databáze s omezením a přístupu ke službám Azure na portálu, jak je popsáno v předchozí části, má vaše úloha Stream Analytics oprávnění ze spravované identity pro **připojení** k vašemu prostředku databáze Azure synapse prostřednictvím spravované identity. Doporučujeme, abyste dál udělili oprávnění k **hromadným operacím** pro **Výběr**, **vložení** a správu databáze pro Stream Analytics úlohy, které budou potřeba později v pracovním postupu Stream Analytics. Oprávnění **Select** umožňuje úloze otestovat připojení k tabulce v databázi Azure synapse. Oprávnění k **hromadnému provádění operací** **Vložit** a spravovat databázi umožňují testování koncových Stream Analytics dotazů po nakonfigurování vstupu a výstupu databáze Azure synapse.

Chcete-li udělit oprávnění pro **hromadné operace správy databáze** , bude nutné udělit všechna oprávnění, která jsou označena jako **řízení** v rámci [odvozená oprávnění databáze](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) pro Stream Analytics úlohy. Toto oprávnění budete potřebovat, protože úloha Stream Analytics provádí příkaz **copy** , který vyžaduje [správu hromadných operací a vkládání databáze](/sql/t-sql/statements/copy-into-transact-sql).

---

Tato oprávnění můžete udělit Stream Analytics úlohy pomocí SQL Server Management Studio. Další informace najdete v referenčních informacích o GRANTu (Transact-SQL).

Chcete-li udělit oprávnění pouze určitým tabulkám nebo objektům v databázi, použijte následující syntaxi T-SQL a spusťte dotaz. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Případně můžete kliknout pravým tlačítkem na databázi SQL Azure nebo Azure synapse v SQL Server Management Studio a vybrat **vlastnosti > oprávnění**. V nabídce oprávnění uvidíte úlohu Stream Analytics, kterou jste dříve přidali, a můžete podle potřeby ručně udělit nebo odepřít oprávnění.

Chcete-li si prohlédnout všechna oprávnění, která jste přidali do uživatele *ASA_JOB_NAME* , spusťte následující příkaz v SSMS v rámci související databáze: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Vytvoření výstupu Azure SQL Database nebo synapse Azure

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Teď, když je vaše spravovaná identita nakonfigurovaná, jste připraveni přidat Azure SQL Database nebo výstup Azure synapse do úlohy Stream Analytics.

Ujistěte se, že jste v SQL Database vytvořili tabulku s odpovídajícím výstupním schématem. Název této tabulky je jedna z požadovaných vlastností, které musí být vyplněny při přidání výstupu SQL Database do úlohy Stream Analytics. Ujistěte se také, že úloha má oprávnění k **výběru** a **vložení** pro otestování připojení a spouštění dotazů Stream Analytics. Pokud jste to ještě neudělali, přečtěte si část [udělení oprávnění k úloze udělit Stream Analytics](#grant-stream-analytics-job-permissions) . 

1. Vraťte se do úlohy Stream Analytics a přejděte na stránku **výstupy** v části **topologie úloh**. 

1. Vyberte **přidat > SQL Database**. V okně Vlastnosti výstupu jímky výstupní SQL Database v rozevíracím seznamu režim ověřování vyberte **spravovaná identita** .

1. Vyplňte zbytek vlastností. Další informace o vytváření výstupů SQL Database najdete v tématu [Vytvoření výstupu SQL Database pomocí Stream Analytics](sql-database-output.md). Po dokončení vyberte **Uložit**.

1. Po kliknutí na **Uložit** by se měl automaticky aktivovat test připojení k vašemu prostředku. Po úspěšném dokončení máte úspěšně nakonfigurovanou úlohu Stream Analytics, abyste se mohli připojit Azure SQL Database nebo synapse SQL Database pomocí režimu spravovaného ověřování identity. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Teď, když je nakonfigurovaná vaše spravovaná identita a účet úložiště, jste připraveni přidat do úlohy Stream Analytics Azure SQL Database nebo synapse výstup Azure.

Ujistěte se, že jste vytvořili tabulku v databázi Azure synapse s příslušným výstupním schématem. Název této tabulky je jedna z požadovaných vlastností, které je třeba vyplnit při přidání výstupu Azure synapse do úlohy Stream Analytics. Ujistěte se také, že úloha má oprávnění k **výběru** a **vložení** pro otestování připojení a spouštění dotazů Stream Analytics. Pokud jste to ještě neudělali, přečtěte si část [udělení oprávnění k úloze udělit Stream Analytics](#grant-stream-analytics-job-permissions) .

1. Vraťte se do úlohy Stream Analytics a přejděte na stránku **výstupy** v části **topologie úloh**.

1. Vyberte **přidat > Azure synapse Analytics**. V okně Vlastnosti výstupu jímky výstupní SQL Database v rozevíracím seznamu režim ověřování vyberte **spravovaná identita** .

1. Vyplňte zbytek vlastností. Další informace o vytváření výstupu Azure synapse najdete v tématu o [výstupu Azure synapse Analytics z Azure Stream Analytics](azure-synapse-analytics-output.md). Po dokončení vyberte **Uložit**.

1. Po kliknutí na **Uložit** by se měl automaticky aktivovat test připojení k vašemu prostředku. Po úspěšném dokončení budete připraveni začít s používáním spravované identity pro váš prostředek Azure synapse Analytics pomocí Stream Analytics. 

---

## <a name="remove-managed-identity"></a>Odebrat spravovanou identitu

Spravovaná identita vytvořená pro Stream Analytics úlohu se odstraní jenom v případě, že se úloha odstraní. Neexistuje způsob, jak odstranit spravovanou identitu, aniž byste úlohu odstranili. Pokud už nechcete používat spravovanou identitu, můžete pro výstup změnit metodu ověřování. Spravovaná identita bude i nadále existovat, dokud se úloha neodstraní, a použije se, pokud se rozhodnete znovu použít spravované ověřování identity.

## <a name="next-steps"></a>Další kroky

* [Porozumění výstupům z Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Výstup služby Azure synapse Analytics z Azure Stream Analytics](azure-synapse-analytics-output.md)
