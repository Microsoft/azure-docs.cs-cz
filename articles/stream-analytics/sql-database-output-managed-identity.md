---
title: Použití spravovaných identit pro přístup k Azure SQL Database-Azure Stream Analytics
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit úlohu Azure Stream Analytics do výstupu služby Azure SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598238"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Použití spravovaných identit pro přístup k Azure SQL Database z úlohy Azure Stream Analytics (Preview)

Azure Stream Analytics podporuje [spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) pro Azure SQL Database výstupní jímky. Spravované identity odstraňují omezení metod ověřování založených na uživatelích, například nutnost opětovného ověření z důvodu změny hesla nebo vypršení platnosti tokenu uživatele, ke kterému dochází každých 90 dní. Když odeberete nutnost ručního ověřování, vaše nasazení Stream Analytics můžou být plně automatizovaná.

Spravovaná identita je spravovaná aplikace zaregistrovaná v Azure Active Directory, která představuje danou Stream Analytics úlohu. Spravovaná aplikace se používá k ověření cílového prostředku. V tomto článku se dozvíte, jak povolit spravovanou identitu pro Azure SQL Database výstupy Stream Analytics úlohy prostřednictvím Azure Portal.

## <a name="prerequisites"></a>Požadavky

Tato funkce vyžaduje následující:

- Azure Stream Analytics úloha.

- Prostředek Azure SQL Database.

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

1. Přejděte na prostředek Azure SQL Database a vyberte SQL Server, pod kterou je databáze. Název SQL Server můžete najít vedle pole *název serveru* na stránce Přehled prostředku. 

1. V části **Nastavení**vyberte **Správce služby Active Directory** . Pak vyberte **nastavit správce**. 

   ![Stránka pro správu služby Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Na stránce Správce služby Active Directory vyhledejte uživatele nebo skupinu pro správce SQL Server a klikněte na **Vybrat**.  

   ![Přidat správce služby Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

1. Vyberte **Uložit** na stránce **Správce služby Active Directory** . Proces změny správce trvá několik minut.  

## <a name="create-a-database-user"></a>Vytvoření uživatele databáze

Dále ve svém SQL Database vytvoříte uživatele databáze s omezením, který je namapován na Azure Active Directory identitu. Uživatel databáze s omezením nemá přihlášení k hlavní databázi, ale mapuje se na identitu v adresáři, který je přidružen k databázi. Azure Active Directory identitou může být individuální uživatelský účet nebo skupina. V takovém případě chcete pro úlohu Stream Analytics vytvořit uživatele databáze s omezením. 

1. Připojte se k databázi SQL pomocí SQL Server Management Studio. **Uživatelské jméno** je Azure Active Directory uživatel s oprávněním **ALTER ANY User** . Příkladem je správce, který jste nastavili na SQL Server. Používejte **Azure Active Directory – Universal s** ověřováním MFA. 

   ![Připojení k SQL Serveru](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Název serveru se `<SQL Server name>.database.windows.net` může v různých oblastech lišit. Například oblast Čína by měla použít `<SQL Server name>.database.chinacloudapi.cn` .
 
   Konkrétní SQL Database můžete zadat tak, že v **možnosti > vlastnosti připojení > připojit k databázi**.  

   ![Vlastnosti připojení SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Při prvním připojení se může zobrazit toto okno:

   ![Nové okno pravidla brány firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Pokud ano, v Azure Portal klikněte na prostředek SQL Server. V části **zabezpečení** otevřete stránku **brány firewall a virtuální síť** . 
   1. Přidejte nové pravidlo s libovolným názvem pravidla.
   1. Pro *Počáteční IP*adresu použijte *z* IP adresy z **nového okna pravidla brány firewall** .
   1. Pro *koncovou IP*adresu *použijte IP adresu* z **nového okna pravidla brány firewall** . 
   1. Vyberte **Uložit** a pokuste se o připojení znovu z SQL Server Management Studio. 

1. Jakmile budete připojeni, vytvořte uživatele databáze s omezením. Následující příkaz SQL vytvoří uživatele databáze s omezením, který má stejný název jako vaše Stream Analyticsová úloha. Nezapomeňte zahrnout hranaté závorky kolem *ASA_JOB_NAME*. Použijte následující syntaxi T-SQL a spusťte dotaz. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Udělení oprávnění pro úlohy Stream Analytics

Úloha Stream Analytics má oprávnění ze spravované identity pro **připojení** k vašemu prostředku SQL Database. Nejpravděpodobnější je, že by bylo efektivní dovolit, aby úloha Stream Analytics spouštěla příkazy, jako je **Select**. Tato oprávnění můžete udělit Stream Analytics úlohy pomocí SQL Server Management Studio. Další informace najdete v referenčních informacích o [grantu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) .

Případně můžete kliknout pravým tlačítkem na databázi SQL v SQL Server Management Studio a vybrat **vlastnosti > oprávnění**. V nabídce oprávnění uvidíte úlohu Stream Analytics, kterou jste dříve přidali, a můžete podle potřeby ručně udělit nebo odepřít oprávnění.

## <a name="create-an-azure-sql-database-output"></a>Vytvoření výstupu Azure SQL Database

Teď, když je vaše spravovaná identita nakonfigurovaná, jste připraveni přidat Azure SQL Database jako výstup do úlohy Stream Analytics.

1. Vraťte se do úlohy Stream Analytics a přejděte na stránku **výstupy** v části **topologie úloh**. 

1. Vyberte **přidat > SQL Database**. V okně Vlastnosti výstupu jímky výstupní SQL Database v rozevíracím seznamu režim ověřování vyberte **spravovaná identita** .

1. Vyplňte zbytek vlastností. Další informace o vytváření výstupů SQL Database najdete v tématu [Vytvoření výstupu SQL Database pomocí Stream Analytics](stream-analytics-define-outputs.md#sql-database). Po dokončení vyberte **Uložit**. 

## <a name="next-steps"></a>Další kroky

* [Porozumění výstupům z Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md)
