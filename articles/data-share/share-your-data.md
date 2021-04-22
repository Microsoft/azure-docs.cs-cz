---
title: 'Kurz: sdílení mimo vaši organizaci – Azure Data Share'
description: Kurz – sdílení dat se zákazníky a partnery pomocí Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870872"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Kurz: Sdílení dat prostřednictvím služby Azure Data Share  

V tomto kurzu se naučíte, jak nastavit novou sdílenou složku Azure a začít sdílet data se zákazníky a partnery mimo vaši organizaci Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte instanci služby Data Share.
> * Přidejte do ní datové sady.
> * Povolte plán snímků pro sdílenou složku dat. 
> * Přidejte do ní příjemce. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlašovací e-mailová adresa Azure vašeho příjemce (pomocí e-mailového aliasu nebude fungovat).
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než je ten, který použijete k vytvoření prostředku pro sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="share-from-a-storage-account"></a>Sdílení z účtu úložiště

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](../storage/common/storage-account-create.md)
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli **Přispěvatel**.
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli **Vlastník**. 


### <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na jazyce SQL
Níže je uveden seznam požadavků pro sdílení dat ze zdroje SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Předpoklady pro sdílení z Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL DW)

* Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL DW) s tabulkami a zobrazeními, které chcete sdílet.
* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli **Přispěvatel**.
* **Azure Active Directory správce** systému SQL Server
* SQL Server přístup k bráně firewall. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte na SQL Server. V levém navigačním panelu vyberte *brány firewall a virtuální sítě* .
    1. Klikněte na **Ano** , pokud chcete, aby *služby a prostředky Azure měly přístup k tomuto serveru*.
    1. Klikněte na **+ Přidat IP adresu klienta**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres.
    1. Klikněte na **Uložit**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Předpoklady pro sdílení z fondu SQL Azure synapse Analytics (pracovní prostor)

* * Vyhrazený fond SQL ve službě Azure synapse Analytics (pracovní prostor) s tabulkami, které chcete sdílet. Sdílení zobrazení není aktuálně podporováno. Sdílení z fondu SQL bez serveru se v tuto chvíli nepodporuje.
* Oprávnění k zápisu do fondu SQL v pracovním prostoru synapse, který je k dispozici v *Microsoft. synapse/Workspaces/sqlPools/Write*. Toto oprávnění existuje v roli **Přispěvatel**.
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k fondu SQL pracovního prostoru synapse. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte do pracovního prostoru synapse. V levém navigačním panelu vyberte Správce SQL Active Directory a nastavte si ho jako **správce Azure Active Directory**.
    1. Otevřete synapse Studio, v levém navigačním panelu vyberte *Spravovat* . V části zabezpečení vyberte *řízení přístupu* . Přiřaďte roli správce pracovního prostoru **správce SQL** nebo **pracovní prostor** .
    1. V synapse studiu v levém navigačním panelu vyberte *vývoj* . Spusťte následující skript ve fondu SQL pro přidání identity spravovaného zdroje dat jako db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.  

* Přístup k bráně firewall v pracovním prostoru synapse To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte do pracovního prostoru synapse. Vyberte *brány firewall* z levé navigace.
    1. Kliknutím **na** *povolíte službám a prostředkům Azure přístup k tomuto pracovnímu prostoru*.
    1. Klikněte na **+ Přidat IP adresu klienta**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres.
    1. Klikněte na **Uložit**. 


### <a name="share-from-azure-data-explorer"></a>Sdílení z Azure Data Exploreru
* Cluster Azure Průzkumník dat s databázemi, které chcete sdílet.
* Oprávnění k zápisu do clusteru Azure Průzkumník dat, který se nachází v *Microsoft. Kusto/Clusters/Write*. Toto oprávnění existuje v roli **Přispěvatel**.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. V levém horním rohu portálu vyberte tlačítko nabídky a pak vyberte **vytvořit prostředek** (+).

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte sdílet data a vyberte **vytvořit**.

1. Vyplňte základní podrobnosti prostředku Azure Data Share s následujícími informacemi. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet pro sdílení dat.|
    | Skupina prostředků | *testresourcegroup* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet pro sdílení dat.
    | Name | *datashareaccount* | Zadejte název vašeho účtu pro sdílení dat. |
    | | |

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořte** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

Začněte přípravou prostředí pro rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

K vytvoření prostředku použijte tyto příkazy:

1. Pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set) nastavte předplatné na aktuální výchozí předplatné:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Pro registraci poskytovatele prostředků spusťte příkaz [AZ Provider Register](/cli/azure/provider#az_provider_register) :

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Spusťte příkaz [az group create](/cli/azure/group#az_group_create) a vytvořte skupinu prostředků, nebo použijte už existující skupinu prostředků:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Spuštěním příkazu [AZ datashare Account Create](/cli/azure/datashare/account#az_datashare_account_create) vytvořte účet pro sdílení dat:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Pomocí příkazu [AZ data Share Account list](/cli/azure/datashare/account#az_datashare_account_list) zobrazíte vaše účty pro sdílení dat:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Vytvoření sdílené složky

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte na stránku s přehledem sdílení dat.

    ![Sdílení dat](./media/share-receive-data.png "Sdílení dat") 

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti pro vaši sdílenou složku. Zadejte název, typ sdílení, popis obsahu sdílení a podmínek použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "Zadat podrobnosti o sdílené složce") 

1. Vyberte **Pokračovat**.

1. Chcete-li do sdílené složky přidat datové sady, vyberte možnost **přidat datové sady**. 

    ![Přidání datových sad do sdílené složky](./media/datasets.png "Datové sady")

1. Vyberte typ datové sady, který chcete přidat. V závislosti na typu sdílené položky (snímku nebo na místě), který jste vybrali v předchozím kroku, se zobrazí jiný seznam typů datových sad. Pokud sdílíte z Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL DW), budete vyzváni k zadání metody ověřování k vypsání tabulek. Vyberte ověřování AAD a zaškrtněte políčko dovolit, **aby sdílená data spouštěla skript "vytvořit uživatele" za jménem**. 

    ![AddDatasets](./media/add-datasets.png "Přidat datové sady")    

1. Přejděte k objektu, který chcete sdílet, a vyberte přidat datové sady. 

    ![SelectDatasets](./media/select-datasets.png "Vybrat datové sady")    

1. Na kartě příjemci zadejte do e-mailových adres vašeho příjemce dat a vyberte + Přidat příjemce. 

    ![AddRecipients](./media/add-recipient.png "Přidání příjemců") 

1. Vyberte **Pokračovat**.

1. Pokud jste vybrali typ sdílené složky snímků, můžete nakonfigurovat plán snímků pro poskytování aktualizací vašich dat příjemci dat. 

    ![EnableSnapshots](./media/enable-snapshots.png "Povolit snímky") 

1. Vyberte čas spuštění a interval opakování. 

1. Vyberte **Pokračovat**.

1. Na kartě Revize + vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Spuštěním příkazu [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) vytvořte sdílenou složku dat:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Pomocí příkazu [AZ Storage Container Create](/cli/azure/storage/container#az_storage_container_create) vytvořte kontejner pro sdílenou složku v předchozím příkazu:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Spuštěním příkazu [AZ datashare Create](/cli/azure/datashare#az_datashare_create) vytvořte sdílenou složku dat:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Pomocí příkazu [AZ datashare Pozvánka Create](/cli/azure/datashare/invitation#az_datashare_invitation_create) vytvořte pozvánku pro zadanou adresu:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředek nepotřebujete, přejdete na stránku s **přehledem sdílení dat** a výběrem **Odstranit** ho odeberete.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte, jak vytvořit sdílenou složku Azure a pozvat příjemce. Další informace o tom, jak může příjemce dat přijmout a přijmout sdílená data, najdete v kurzu přijímání a přijímání dat.

> [!div class="nextstepaction"]
> [Kurz: Přijetí a získání dat prostřednictvím služby Azure Data Share](subscribe-to-data-share.md)
