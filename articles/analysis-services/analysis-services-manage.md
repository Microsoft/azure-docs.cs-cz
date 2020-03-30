---
title: Správa služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje nástroje používané ke správě úloh správy a správy pro server Služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572799"
---
# <a name="manage-analysis-services"></a>Správa služby Analysis Services
Po vytvoření serveru Analysis Services v Azure může být některé úkoly správy a správy, které je třeba provést hned nebo někdy dolů na silnici. Můžete například spustit zpracování na data aktualizace, řídit, kdo má přístup k modelům na serveru, nebo sledovat stav serveru. Některé úlohy správy lze provádět jenom na webu Azure Portal, jiné v aplikaci SQL Server Management Studio (SSMS) a některé úlohy lze provést v jednom z nich.

## <a name="azure-portal"></a>portál Azure
[Azure Portal](https://portal.azure.com/) je místo, kde můžete vytvářet a odstraňovat servery, monitorovat prostředky serveru, měnit velikost a spravovat, kdo má přístup k vašim serverům.  Pokud máte nějaké problémy, můžete také odeslat žádost o podporu.

![Získání názvu serveru v Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Připojení k serveru v Azure je jako připojení k instanci serveru ve vaší organizaci. Ze služby SSMS můžete provádět mnoho stejných úloh, jako je například zpracování dat nebo vytvořit skript pro zpracování, spravovat role a používat prostředí PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Stažení a instalace SSMS
Chcete-li získat všechny nejnovější funkce a nejplynulejší prostředí při připojování k serveru Služby Azure Analysis Services, ujistěte se, že používáte nejnovější verzi SSMS. 

[Stáhnout sql server management studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Chcete-li se připojit k SSMS
 Při prvním připojení k serveru se ujistěte, že je vaše uživatelské jméno zahrnuto do skupiny Analysis Services Admins. Další informace naleznete [v tématu Správci serveru a uživatelé databáze](#server-administrators-and-database-users) dále v tomto článku.

1. Před připojením je třeba získat název serveru. Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
    ![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. V **Průzkumníku objektů** SQL Server Management Studia klikněte na **Připojit** > **Analysis Services**.
3. V dialogovém okně **Připojit k serveru** vložte název serveru a pak v okně **Ověřování**zvolte jeden z následujících typů ověřování:   
    > [!NOTE]
    > Doporučujese typ **ověřování, služba Active Directory – univerzální s podporou vícefaktorové**ověřování .

    > [!NOTE]
    > Pokud se přihlásíte pomocí účtu Microsoft, Live ID, Yahoo, Gmail atd., ponechte pole s heslem prázdné. Po kliknutí na tlačítko Připojit se zobrazí výzva k zadání hesla.

    **Ověřování systému Windows,** aby bylo používáno vaše pověření pro doménu a heslo systému Windows\uživatelské jméno a heslo.

    **Ověřování heslem služby Active Directory** pro použití účtu organizace. Například při připojování z počítače, který není připojen k doméně.

    **Služba Active Directory – univerzální s podporou vícefaktorové ověřování** pro použití [neinteraktivního nebo vícefaktorového ověřování](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Připojení v SQL Server Management Studiu](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Správci serveru a uživatelé databází
Ve službě Azure Analysis Services existují dva typy uživatelů, správci serveru a uživatelé databáze. Oba typy uživatelů musí být ve vaší službě Azure Active Directory a musí být určeny e-mailovou adresou organizace nebo upn. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Poradce při potížích s připojením
Při připojování pomocí SSMS, pokud narazíte na problémy, možná budete muset vymazat přihlašovací mezipaměti. Nic není uloženo do mezipaměti na disk. Chcete-li vymazat mezipaměť, zavřete a restartujte proces připojení. 

## <a name="next-steps"></a>Další kroky
Pokud jste ještě nenasadili tabulkový model na nový server, je teď vhodná doba. Další informace najdete v tématu [Nasazení do služby Azure Analysis Services](analysis-services-deploy.md).

Pokud jste na server nasadili model, můžete se k němu připojit pomocí klienta nebo prohlížeče. Další informace najdete v tématu [Získání dat ze serveru Azure Analysis Services](analysis-services-connect.md).

