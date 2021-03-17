---
title: 'Kurz: přijetí dat pro příjem & – Azure Data Share'
description: Kurz – přijetí a příjem dat pomocí Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017045"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Kurz: Přijetí a získání dat prostřednictvím služby Azure Data Share  

V tomto kurzu se dozvíte, jak přijmout pozvánku ke sdílení dat pomocí Azure Data Share. Naučíte se, jak přijímat data, která s vámi sdílíte, a jak povolit pravidelný interval aktualizace, abyste měli jistotu, že budete mít vždycky nejnovější snímek dat, která s vámi někdo sdílí. 

> [!div class="checklist"]
> * Jak přijmout pozvánku ke sdílené složce Azure Data
> * Vytvoření účtu Azure Data Share
> * Zadejte cíl pro vaše data.
> * Vytvořte předplatné pro vaši sdílenou složku dat pro plánovanou aktualizaci.

## <a name="prerequisites"></a>Požadavky
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure se subjektem s názvem "pozvání ke sdílení dat Azure od **<yourdataprovider@domain.com>** ".
* Zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek pro sdílení dat a předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="receive-data-into-a-storage-account"></a>Příjem dat do účtu úložiště

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](../storage/common/storage-account-create.md). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník.  

### <a name="receive-data-into-a-sql-based-target"></a>Příjem dat do cíle založeného na SQL serveru
Pokud se rozhodnete přijímat data do Azure SQL Database, najdete níže seznam požadavků Azure synapse Analytics. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Předpoklady pro příjem dat do Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL DW)
Pro konfiguraci požadavků můžete postupovat podle podrobných [ukázek](https://youtu.be/aeGISgK1xro) .

* Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL DW).
* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli **Přispěvatel**. 
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k Azure SQL Database nebo ke službě Azure synapse Analytics. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte na SQL Server a nastavte si ho jako **správce Azure Active Directory**.
    1. Připojení k Azure SQL Database/datový sklad pomocí [Editoru dotazů](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) nebo SQL Server Management Studio Azure Active Directory ověřování. 
    1. Spuštěním následujícího skriptu přidejte spravovanou identitu sdílené dat jako db_datareader, db_datawriter db_ddladmin. Je nutné se připojit pomocí služby Active Directory a SQL Server ověřování. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.         

* SQL Server přístup k bráně firewall. To lze provést pomocí následujících kroků: 
    1. V systému SQL Server v Azure Portal přejděte na *brány firewall a virtuální sítě* .
    1. Klikněte na **Ano** , pokud chcete, aby *služby a prostředky Azure měly přístup k tomuto serveru*.
    1. Klikněte na **+ Přidat IP adresu klienta**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres.
    1. Klikněte na **Uložit**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Předpoklady pro příjem dat do fondu SQL Azure synapse Analytics (pracovní prostor)

* Vyhrazený fond SQL ve službě Azure synapse Analytics (pracovní prostor). Příjem dat do fondu SQL bez serveru se momentálně nepodporuje.
* Oprávnění k zápisu do fondu SQL v pracovním prostoru synapse, který je k dispozici v *Microsoft. synapse/Workspaces/sqlPools/Write*. Toto oprávnění existuje v roli **Přispěvatel**.
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup ke fondu SQL pracovního prostoru synapse. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte do pracovního prostoru synapse. V levém navigačním panelu vyberte Správce SQL Active Directory a nastavte si ho jako **správce Azure Active Directory**.
    1. Otevřete synapse Studio, v levém navigačním panelu vyberte *Spravovat* . V části zabezpečení vyberte *řízení přístupu* . Přiřaďte roli správce pracovního prostoru **správce SQL** nebo **pracovní prostor** .
    1. V synapse studiu v levém navigačním panelu vyberte *vývoj* . Spusťte následující skript ve fondu SQL a přidejte identitu spravovaného zdroje dat jako db_datareader, db_datawriter db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.  

* Přístup k bráně firewall v pracovním prostoru synapse To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte do pracovního prostoru synapse. Vyberte *brány firewall* z levé navigace.
    1. Kliknutím **na** *povolíte službám a prostředkům Azure přístup k tomuto pracovnímu prostoru*.
    1. Klikněte na **+ Přidat IP adresu klienta**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres.
    1. Klikněte na **Uložit**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Příjem dat do clusteru Azure Průzkumník dat: 

* Cluster Azure Průzkumník dat ve stejném datovém centru Azure jako cluster Průzkumník dat poskytovatele dat: Pokud ho ještě nemáte, můžete vytvořit [cluster Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal). Pokud si nejste jisti datovým centrem Azure v clusteru poskytovatele dat, můžete cluster vytvořit později v procesu.
* Oprávnění k zápisu do clusteru Azure Průzkumník dat, který se nachází v *Microsoft. Kusto/Clusters/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role do clusteru Azure Průzkumník dat, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Můžete otevřít pozvánku z e-mailu nebo přímo z Azure Portal. 

   Pokud chcete otevřít pozvánku z e-mailu, Projděte si doručenou poštu od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com>**. Kliknutím na **Zobrazit pozvánku** zobrazíte pozvánku v Azure. 

   Chcete-li otevřít pozvánku přímo z Azure Portal, vyhledejte v Azure Portal **pozvánky ke sdílení dat** . Tato akce přejde do seznamu pozvání ke sdílení dat.

   ![Seznam pozvánek](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Připravte prostředí Azure CLI a pak si prohlédněte své pozvánky.

Začněte tím, že připravíte prostředí pro rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Spuštěním příkazu [AZ datashare Consumer pozvánky](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) zobrazíte vaše aktuální pozvánky:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Zkopírujte ID pozvánky pro použití v další části.

---

## <a name="accept-invitation"></a>Přijmout pozvánku

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

   ![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

1. V části *cílový účet sdílení dat* vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

   V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

   V poli **přijatý název sdílené složky** můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

   Jakmile souhlasíte s podmínkami použití a zadáte účet pro sdílení dat pro správu přijaté sdílené složky, vyberte **přijmout a nakonfigurovat**. Vytvoří se předplatné sdílení. 

   ![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

   Tato akce přejde k přijaté sdílené složce v účtu pro sdílení dat. 

   Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ datashare Consumer Share-Subscription Create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) vytvořte sdílenou složku dat.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Konfigurace přijaté sdílené složky

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li nakonfigurovat, kde chcete přijímat data, postupujte podle následujících kroků.

1. Vyberte kartu **datové sady** . Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte možnost **+ mapovat na cíl** a zvolte cílové úložiště dat. 

   ![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

1. Vyberte cílový typ úložiště dat, do kterého chcete data založit. Budou přepsány všechny datové soubory nebo tabulky v cílovém úložišti dat se stejnou cestou a názvem. 

   V případě místního sdílení vyberte v zadaném umístění úložiště dat. Umístění je datové centrum Azure, kde se nachází zdrojové úložiště dat poskytovatele dat. Po namapování datové sady můžete pro přístup k datům sledovat odkaz v cílové cestě.

   ![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

1. Pokud pro sdílení na základě snímků vytvořil poskytovatel dat plán snímků, který poskytuje pravidelnou aktualizaci dat, můžete také povolit plán snímků výběrem karty **plán snímku** . Zaškrtněte políčko vedle plánu snímku a vyberte **+ Povolit**.

   ![Povolit plán snímků](./media/enable-snapshot-schedule.png "Povolit plán snímků")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí těchto příkazů můžete nakonfigurovat, kam chcete přijímat data.

1. Spuštěním příkazu [AZ datashare Consumer Share-Subscription list-source-DataSet](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) Získejte ID datové sady:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Spuštěním příkazu [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) vytvořte účet úložiště pro tuto sdílenou složku dat:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Pomocí příkazu [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) Získejte ID účtu úložiště:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. ID objektu zabezpečení účtu získáte pomocí následujícího příkazu:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Pomocí příkazu [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create) vytvořte přiřazení role pro hlavní povinný účet:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Vytvořte proměnnou pro mapování na základě ID datové sady:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Pomocí příkazu [AZ datashare Consumer DataSet-Mapping Create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) vytvořte mapování datové sady:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Spuštěním příkazu [AZ datashare Consumer Share-Subscription Synchronization](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) spusťte synchronizaci datových sad.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Spuštěním příkazu [AZ datashare Consumer Share-Subscription Synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) zobrazíte seznam synchronizací:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Pomocí příkazu [AZ datashare Consumer Share-Subscription list-source-Share-Synchronization-](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) Settings zobrazte nastavení synchronizace nastavené ve sdílené složce.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Aktivace snímku

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Tyto kroky platí pouze pro sdílení na základě snímků.

1. Snímek můžete aktivovat výběrem karty **Podrobnosti** a **snímku triggeru**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

   ![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném* stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady** a klikněte na odkaz v cílové cestě. 

   ![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Spusťte příkaz [AZ datashare Consumer Trigger Create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) a aktivujte snímek:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Tento příkaz použijte pouze pro sdílení na základě snímků.

---

## <a name="view-history"></a>Zobrazení historie
Tento krok platí jenom pro sdílení na základě snímků. Chcete-li zobrazit historii snímků, vyberte kartu **Historie** . Tady najdete historii všech snímků, které se vygenerovaly za posledních 30 dní.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředek nepotřebujete, přejdete na stránku s **přehledem sdílení dat** a výběrem **Odstranit** ho odeberete.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak přijmout a získat sdílenou složku Azure Data. Další informace o konceptech Azure Data Share najdete v terminologii Azure Data Share.

> [!div class="nextstepaction"]
> [Koncepty Azure Data Share](terminology.md)