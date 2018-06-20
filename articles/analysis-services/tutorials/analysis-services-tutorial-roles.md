---
title: Kurz – Konfigurace správce serveru a uživatelských rolí služby Azure Analysis Services | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: fcedba0a5b8a9684216f181a6d16f86044edb961
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651305"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Kurz: Konfigurace správce serveru a uživatelských rolí

 V tomto kurzu se pomocí SQL Server Management Studia (SSMS) připojíte k serveru v Azure a nakonfigurujete správce serveru a role modelové databáze. Zároveň se seznámíte s jazykem [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL je skriptovací jazyk založený na JSON pro tabulkové modely na úrovni kompatibility 1200 a vyšších. Dá se použít k automatizaci mnoha úkolů při tabulkovém modelování. Jazyk TMSL se často používá s PowerShellem, v tomto kurzu ale použijete editor dotazů XMLA v SQL Server Management Studiu. V tomto kurzu dokončíte tyto úkoly: 
  
> [!div class="checklist"]
> * Získání názvu serveru z portálu
> * Připojení k serveru pomocí SQL Server Management Studia
> * Přidání uživatele nebo skupiny do role správce serveru 
> * Přidání uživatele nebo skupiny do role správce modelové databáze
> * Přidání nové role modelové databáze a přidání uživatele nebo skupiny

Další informace o zabezpečení uživatelů ve službě Azure Analysis Services najdete v článku o [ověřování a oprávněních uživatelů](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Požadavky

- Ve vašem předplatném je služba Azure Active Directory.
- Ve vašem předplatném je vytvořený [server Azure Analysis Services](../analysis-services-create-server.md).
- Máte oprávnění [správce serveru](../analysis-services-server-admins.md).
- Na server je [přidaný ukázkový model adventureworks](../analysis-services-create-sample-model.md).
- [Máte nainstalovanou nejnovější verzi SQL Server Management Studia](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k [portálu](https://portal.azure.com/).

## <a name="get-server-name"></a>Získání názvu serveru
Abyste se k serveru mohli připojit z SQL Server Management Studia, potřebujete napřed název serveru. Název serveru můžete získat z portálu.

Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
   ![Získání názvu serveru v Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Připojení v SQL Server Management Studiu

Pro zbývající úkoly použijete SQL Server Management Studio, které umožňuje připojení k serveru a jeho správu.

1. V **Průzkumníku objektů** SQL Server Management Studia klikněte na **Připojit** > **Analysis Services**.

    ![Připojení](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. Do pole **Název serveru** v dialogovém okně **Připojit k serveru** vložte název serveru zkopírovaný z portálu. V poli **Ověřování** zvolte **Active Directory Universal s podporou vícefaktorového ověřování**, zadejte svůj uživatelský účet a klikněte na **Připojit**.
   
    ![Připojení v SQL Server Management Studiu](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Doporučuje se použít možnost Active Directory Universal s podporou vícefaktorového ověřování. Tento typ ověřování podporuje [neinteraktivní a vícefaktorové ověřování](../../sql-database/sql-database-ssms-mfa-authentication.md). 

3. Rozbalením **Průzkumníka objektů** zobrazte objekty serveru. Kliknutím pravým tlačítkem zobrazte vlastnosti serveru.
   
    ![Připojení v SQL Server Management Studiu](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Přidání uživatelského účtu do role správce serveru

V rámci tohoto úkolu přidáte do role správce serveru uživatelský nebo skupinový účet ze služby Azure AD.

1. V **Průzkumníku objektů** klikněte pravým tlačítkem na název serveru a pak klikněte na **Vlastnosti**. 
2. V okně **Analysis Server – vlastnosti** klikněte na **Zabezpečení** > **Přidat**.
3. V okně **Vyberte uživatele nebo skupinu** zadejte uživatelský nebo skupinový účet ve službě Azure AD a klikněte na **Přidat**. 
   
     ![Přidání správce serveru](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Kliknutím na **OK** zavřete okno **Analysis Server – vlastnosti**.

    > [!TIP]
    > Správce serveru můžete přidat také pomocí možnosti **Správci Analysis Services** na portálu. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Přidání uživatele do role správce modelové databáze

V rámci tohoto úkolu přidáte do role Internet Sales Administrator, která už v modelu existuje, uživatelský nebo skupinový účet. Tato role má oprávnění k úplnému řízení (správce) ukázkové modelové databáze adventureworks. V tomto úkolu se používá příkaz [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) jazyka TMSL ve skriptu, který je pro vás vytvořený.

1. V **Průzkumníku objektů** rozbalte **Databáze** > **adventureworks** > **Role**. 
2. Klikněte pravým tlačítkem na **Internet Sales Administrator**, pak klikněte na **Skriptovat roli jako** > **VYTVOŘIT NEBO NAHRADIT pro** > **Nové okno editoru dotazů**.

    ![Nové okno Editoru dotazů](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. V dotazu **XMLAQuery** změňte hodnotu **"memberName":** na uživatelský nebo skupinový účet ve službě Azure AD. Standardně se zahrne účet, pod kterým jste přihlášení; svůj vlastní účet ale nemusíte přidávat, protože správcem serveru už jste.

    ![Skript TMSL v dotazu XMLA](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Stisknutím klávesy **F5** tento skript spusťte.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Přidání nové role modelové databáze a přidání uživatele nebo skupiny

V rámci tohoto úkolu použijete příkaz [Create](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/create-command-tmsl?view=sql-analysis-services-2017) ve skriptu TMSL k vytvoření nové role Internet Sales Global, určíte této roli oprávnění ke *čtení* a přidáte uživatelský nebo skupinový účet ze služby Azure AD.

1. V **Průzkumníku objektů** klikněte pravým tlačítkem na **adventureworks** a pak klikněte na **Nový dotaz** > **XMLA**. 
2. Zkopírujte a vložte do editoru dotazů následující skript TMSL:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Hodnotu objektu **"memberName": \"globalsales@adventureworks.com\"** změňte na uživatelský nebo skupinový účet ve službě Azure AD.
4. Stisknutím klávesy **F5** tento skript spusťte.

## <a name="verify-your-changes"></a>Ověření změn

1. V **Průzkumníku objektů** klikněte na název serveru a pak na **Aktualizovat**, nebo stiskněte klávesu **F5**.
2. Rozbalte **Databáze** > **adventureworks** > **Role**. Ověřte, že se zobrazují změny uživatelského účtu a nové role, které jste provedli v předchozích úkolech.   

    ![Ověření v Průzkumníku objektů](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už uživatelské nebo skupinové účty a role nepotřebujete, můžete je odstranit. Pomocí možností **Vlastnosti role** > **Členství** odeberte uživatelské účty, nebo klikněte pravým tlačítkem na roli a pak klikněte na **Odstranit**.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak se připojit k serveru Azure AD a prozkoumat ukázkové modelové databáze adventureworks a vlastnosti v SQL Server Management Studiu. Zároveň jste se naučili přidat uživatele nebo skupiny do existujících a nových rolí pomocí SQL Server Management Studia a skriptů TMSL. Teď, když máte pro server a ukázkovou modelovou databázi nakonfigurovaná uživatelská oprávnění, můžete se k nim spolu s dalšími uživateli připojit pomocí klientských aplikací, jako je Power BI. Pokud chcete vědět víc, pokračujte dalším kurzem. 

> [!div class="nextstepaction"]
> [Kurz: Připojení pomocí Power BI Desktopu](analysis-services-tutorial-pbid.md)

