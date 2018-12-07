---
title: Správa služby Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o správě serveru služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993929"
---
# <a name="manage-analysis-services"></a>Správa služby Analysis Services
Po vytvoření serveru služby Analysis Services v Azure, může být administrace a správa úlohy, které je potřeba provést hned nebo nějakou dobu mimo provoz cestách. Například spusťte zpracování pro aktualizaci dat, řízení, kdo může přistupovat modely na serveru nebo monitorování stavu vašeho serveru. Některé úlohy správy lze provést pouze webu Azure Portal, ostatní v SQL Server Management Studio (SSMS) a některé úkoly lze provést v jednom.

## <a name="azure-portal"></a>portál Azure
[Azure portal](https://portal.azure.com/) je, kde můžete vytvořit a odstranit servery, monitorovat prostředky serveru, změna velikosti a spravovat, kdo má přístup k vašim serverům.  Pokud máte nějaké problémy, můžete také odeslat žádost o podporu.

![Získání názvu serveru v Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Připojení k serveru v Azure je stejné jako připojení k instanci serveru ve vaší vlastní organizaci. Z aplikace SSMS můžete provádět množství stejných úkolů, jako je zpracování dat nebo vytvořit skript zpracování, spravovat role a pomocí Powershellu.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Stažení a instalace SSMS
Pokud chcete získat všechny nejnovější funkce a možnosti nejhladší při připojování k serveru Azure Analysis Services, ujistěte se, že používáte nejnovější verzi SSMS. 

[Stáhněte si SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Připojit pomocí SSMS
 Při použití aplikace SSMS, před připojením k serveru poprvé, ujistěte se, že vaše uživatelské jméno je součástí skupiny Správci Analysis Services. Další informace najdete v tématu [správci serveru a databáze uživatelů](#server-administrators-and-database-users) dále v tomto článku.

1. Než připojíte, musíte získat název serveru. Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
    ![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. V **Průzkumníku objektů** SQL Server Management Studia klikněte na **Připojit** > **Analysis Services**.
3. V **připojit k serveru** dialogové okno, vložte název serveru, pak v **ověřování**, zvolte jednu z následujících typů ověřování:   
    > [!NOTE]
    > Typ ověřování, **univerzální podporující vícefaktorové ověřování služby Active Directory –**, doporučuje se.

    > [!NOTE]
    > Pokud se přihlásíte Account Microsoft Live ID, Yahoo, Gmail, atd., ponechejte pole pro heslo prázdné. Zobrazí se výzva k zadání hesla po kliknutí na tlačítko Připojit.

    **Ověřování Windows** k použijte přihlašovací údaje Windows doména\uživatelské jméno a heslo.

    **Ověřování hesla Active Directory** používat účet organizace. Například při připojování z jiné domény počítač připojený.

    **Univerzální podporující vícefaktorové ověřování služby Active Directory –** používat [ověřování službou Multi-Factor Authentication nebo jako neinteraktivní](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Připojení v SQL Server Management Studiu](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Správce serveru a databáze uživatelů
Ve službě Azure Analysis Services existují dva typy uživatelů, správci serveru a databázi uživatelů. Oba typy uživatelů musí být ve službě Azure Active Directory a musí být určena organizační e-mailovou adresu nebo hlavní název uživatele. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Řešení potíží s problémy s připojením
Když se připojujete pomocí aplikace SSMS, pokud narazíte na problémy, budete muset vymazat mezipaměť přihlášení. Nic se uloží do mezipaměti na disku. Vymazání mezipaměti, ukončete a restartujte proces připojit. 

## <a name="next-steps"></a>Další postup
Pokud už jste nenasadili tabulkový model na nový server, teď je vhodná doba. Další informace najdete v tématu [Nasazení do služby Azure Analysis Services](analysis-services-deploy.md).

Pokud jste nasadili model na server, budete připraveni k připojení k němu pomocí klienta nebo prohlížeče. Další informace najdete v tématu [získání dat ze serveru Azure Analysis Services](analysis-services-connect.md).

