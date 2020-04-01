---
title: 'Kurz: Sdílení mimo vaši organizaci – Azure Data Share'
description: Kurz – Sdílení dat se zákazníky a partnery pomocí Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083044"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Kurz: Sdílení dat pomocí služby Azure Data Share  

V tomto kurzu se dozvíte, jak nastavit novou službu Azure Data Share a začít sdílet data se zákazníky a partnery mimo vaši organizaci Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte instanci služby Data Share.
> * Přidejte do ní datové sady.
> * Povolte plán snímků pro sdílenou složku dat. 
> * Přidejte do ní příjemce. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Přihlašovací e-mailová adresa příjemce Azure (pomocí e-mailového aliasu nebude fungovat).
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než které použijete k vytvoření prostředku sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště:

* Účet Azure Storage: Pokud ho ještě nemáte, můžete si vytvořit [účet Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft.Storage/storageAccounts/write*. Toto oprávnění existuje v roli přispěvatele.
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft.Authorization/role assignments/write*. Toto oprávnění existuje v roli vlastníka. 


### <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na SQL:

* Azure SQL Database nebo Azure Synapse Analytics (dříve Azure SQL Data Warehouse) s tabulkami a zobrazeními, které chcete sdílet.
* Oprávnění k zápisu do databází na serveru SQL, který je k dispozici v *microsoft.sql/servers/databases/write*. Toto oprávnění existuje v roli přispěvatele.
* Oprávnění pro sdílenou složku dat pro přístup k datovému skladu. To lze provést pomocí následujících kroků: 
    1. Nastavte si jako Správce služby Azure Active Directory pro SQL server.
    1. Připojte se k Azure SQL Database/Data Warehouse pomocí Azure Active Directory.
    1. Pomocí Editoru dotazů (preview) můžete spustit následující skript a přidat jako db_datareader spravovanou identitu prostředku sdílení dat. Musíte se připojit pomocí služby Active Directory a nikoli pomocí ověřování serveru SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Všimněte si, že *<share_acc_name>* je název prostředku sdílení dat. Pokud jste ještě nevytvořili prostředek sdílení dat, můžete se k tomuto požadovanému webu vrátit později.  

* Uživatel databáze Azure SQL s přístupem "db_datareader" k navigaci a výběru tabulek nebo zobrazení, které chcete sdílet. 

* Přístup k bráně IP SQL Server Firewall klienta. To lze provést pomocí následujících kroků: 
    1. Na SQL serveru na Webu Azure Portal přejděte na *firewally a virtuální sítě.*
    1. Kliknutím **na** přepínač povolíte přístup ke službám Azure.
    1. Klepněte na tlačítko **+Přidat IP klienta** a klepněte na tlačítko **Uložit**. IP adresa klienta se může změnit. Tento proces může být nutné opakovat při příštím sdílení dat SQL z webu Azure Portal. Můžete také přidat rozsah IP adres. 

### <a name="share-from-azure-data-explorer"></a>Sdílení z Průzkumníka dat Azure
* Cluster Průzkumníka dat Azure s databázemi, které chcete sdílet.
* Oprávnění k zápisu do clusteru Azure Data Explorer, který se nachází v *Microsoft.Kusto/clusters/write*. Toto oprávnění existuje v roli přispěvatele.
* Oprávnění k přidání přiřazení role do clusteru Azure Data Explorer, který se nachází v *Microsoft.Authorization/role assignments/write*. Toto oprávnění existuje v roli vlastníka.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Vytvoření účtu sdílení dat

Vytvořte prostředek Azure Data Share ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte sdílení dat a vyberte **Vytvořit**.

1. Vyplňte základní podrobnosti o prostředku Azure Data Share s následujícími informacemi. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Name (Název) | *datashareacount* | Zadejte název účtu sdílení dat. |
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet sdílení dat.|
    | Skupina prostředků | *skupina testovacích prostředků* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet sdílení dat.
    | | |

1. Chcete-li zřídit účet sdílení dat, vyberte **možnost Vytvořit.** Zřizování nového účtu sdílení dat obvykle trvá přibližně 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **Přejít na prostředek**.

## <a name="create-a-data-share"></a>Vytvoření sdílené složky dat

1. Přejděte na stránku Přehled sdílení dat.

    ![Sdílení dat](./media/share-receive-data.png "Sdílení dat") 

1. Vyberte **Spustit sdílení dat**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti o sdílení dat. Zadejte název, typ sdílené položky, popis obsahu sdílené položky a podmínky použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "Zadat podrobnosti sdílení") 

1. Vyberte **Pokračovat**

1. Chcete-li přidat datové sady do sdílené složky dat, vyberte **Přidat datové sady**. 

    ![Datové sady](./media/datasets.png "Datové sady")

1. Vyberte typ datové sady, který chcete přidat. Zobrazí se jiný seznam typů datových sad v závislosti na typu sdílené složky (snímek nebo na místě), který jste vybrali v předchozím kroku. Pokud sdílení z Azure SQL Database nebo Azure SQL Data Warehouse, budete vyzváni k zadání některých přihlašovacích údajů SQL. Ověřte pomocí uživatele, který jste vytvořili jako součást předpokladů.

    ![Přidat datové sady](./media/add-datasets.png "Přidat datové sady")    

1. Přejděte na objekt, který chcete sdílet, a vyberte "Přidat datové sady". 

    ![SelectDatové sady](./media/select-datasets.png "Vybrat datové sady")    

1. Na kartě Příjemci zadejte e-mailové adresy příjemce dat tak, že vyberete možnost + Přidat příjemce. 

    ![Přidat příjemce](./media/add-recipient.png "Přidání příjemců") 

1. Vyberte **Pokračovat**

1. Pokud jste vybrali typ sdílené složky snímku, můžete nakonfigurovat plán snímků tak, aby poskytoval aktualizace dat vašemu spotřebiteli dat. 

    ![Povolit snímky](./media/enable-snapshots.png "Povolení snímků") 

1. Vyberte počáteční čas a interval opakování. 

1. Vyberte **Pokračovat**

1. Na kartě Revize + Vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vybrat **vytvořit**

Vaše Azure Data Share byla vytvořena a příjemce sdílené složky dat je teď připraven přijmout vaši pozvánku. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit Azure Data Share a pozvat příjemce. Chcete-li se dozvědět o tom, jak může příjemce dat přijímat a přijímat sdílenou složku dat, pokračujte v kurzu [přijmout a přijímat data.](subscribe-to-data-share.md) 
