---
title: Spravovat Azure Analysis Services | Microsoft Docs
description: Naučte se spravovat Analysis Services Server v Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6846eaedc7f20f5a7597ee889193e977e86a5ed6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146839"
---
# <a name="manage-analysis-services"></a>Správa služby Analysis Services
Po vytvoření Analysis Servicesho serveru v Azure můžou nastat některé úlohy správy a správy, které potřebujete udělat hned nebo po dobu provozu. Například spusťte zpracování na data aktualizace, řízení, kdo má přístup k modelům na vašem serveru, nebo můžete monitorovat stav svého serveru. Některé úlohy správy lze provádět pouze v Azure Portal, dalších v SQL Server Management Studio (SSMS) a některé úlohy lze provádět v obou.

## <a name="azure-portal"></a>Portál Azure
[Azure Portal](https://portal.azure.com/) je místo, kde můžete vytvářet a odstraňovat servery, monitorovat prostředky serveru, měnit velikost a spravovat, kdo má přístup k vašim serverům.  Pokud máte nějaké problémy, můžete také odeslat žádost o podporu.

![Získání názvu serveru v Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Připojení k serveru v Azure je stejné jako připojení k instanci serveru ve vaší vlastní organizaci. Z SSMS můžete provádět mnoho stejných úkolů, jako jsou například zpracování dat nebo vytváření skriptu pro zpracování, Správa rolí a používání prostředí PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Stažení a instalace SSMS
Pokud chcete získat nejnovější funkce a využít nejplynulejší prostředí při připojování k serveru Azure Analysis Services, ujistěte se, že používáte nejnovější verzi SSMS. 

[Stáhněte si SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Připojení pomocí SSMS
 Pokud používáte SSMS, ujistěte se, že před prvním připojením k serveru se ujistěte, že je vaše uživatelské jméno zahrnuté ve skupině Analysis Services Admins. Další informace najdete v tématu [Správci serveru a databáze uživatelů](#server-administrators-and-database-users) dále v tomto článku.

1. Než se připojíte, musíte získat název serveru. Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
    ![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. V **Průzkumníku objektů** SQL Server Management Studia klikněte na **Připojit** > **Analysis Services**.
3. V dialogovém okně **připojit k serveru** vložte do pole název serveru a pak v části **ověřování**zvolte jeden z následujících typů ověřování:   
    > [!NOTE]
    > Doporučuje se typ ověřování **Active Directory – univerzální s podporou vícefaktorového ověřování**.

    > [!NOTE]
    > Pokud se přihlásíte pomocí účtu Microsoft, Live ID, Yahoo, Gmail atd., nechte pole heslo prázdné. Po kliknutí na připojit se zobrazí výzva k zadání hesla.

    **Ověřování systému Windows** pro použití přihlašovacích údajů systému Windows doména \ uživatelské_jméno a hesla.

    **Ověřování hesla služby Active Directory** pro použití účtu organizace. Například při připojování z počítače, který není připojený k doméně.

    **Active Directory – univerzální s podporou vícefaktorového** ověřování, aby se používaly [neinteraktivní nebo Multi-Factor Authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Připojení v SQL Server Management Studiu](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Správci serveru a uživatelé databáze
V Azure Analysis Services existují dva typy uživatelů, správci serveru a uživatelé databáze. Oba typy uživatelů musí být ve vašem Azure Active Directory a musí být zadány e-mailovou adresou organizace nebo hlavním jménem uživatele. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Řešení potíží s připojením
Pokud při připojování pomocí SSMS narazíte na problémy, možná budete muset vymazat mezipaměť přihlášení. Na disk se nic neukládá do mezipaměti. Chcete-li vymazat mezipaměť, ukončete a znovu spusťte proces připojení. 

## <a name="next-steps"></a>Další kroky
Pokud jste ještě nenainstalovali tabelární model na nový server, je teď dobrý čas. Další informace najdete v tématu [Nasazení do služby Azure Analysis Services](analysis-services-deploy.md).

Pokud jste model nasadili na váš server, jste připraveni se k němu připojit pomocí klienta nebo prohlížeče. Další informace najdete v tématu [získání dat ze serveru Azure Analysis Services](analysis-services-connect.md).

