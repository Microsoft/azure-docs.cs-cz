---
title: 'Kurz: přijetí dat pro příjem & – Azure Data Share'
description: Kurz – přijetí a příjem dat pomocí Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 4d4ef9f1d80604373463450adf597f8202611175
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216568"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Kurz: Přijetí a získání dat prostřednictvím služby Azure Data Share  

V tomto kurzu se dozvíte, jak přijmout pozvánku ke sdílení dat pomocí Azure Data Share. Naučíte se, jak přijímat data, která s vámi sdílíte, a jak povolit pravidelný interval aktualizace, abyste měli jistotu, že budete mít vždycky nejnovější snímek dat, která s vámi někdo sdílí. 

> [!div class="checklist"]
> * Jak přijmout pozvánku ke sdílené složce Azure Data
> * Vytvoření účtu Azure Data Share
> * Zadejte cíl pro vaše data.
> * Vytvořte předplatné pro vaši sdílenou složku dat pro plánovanou aktualizaci.

## <a name="prerequisites"></a>Předpoklady
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure se subjektem s názvem "pozvání ke sdílení dat Azure od **<yourdataprovider@domain.com>** ".
* Zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek pro sdílení dat a předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="receive-data-into-a-storage-account"></a>Příjem dat do účtu úložiště: 

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](../storage/common/storage-account-create.md). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník.  

### <a name="receive-data-into-a-sql-based-target"></a>Příjem dat do cíle založeného na SQL:

* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k Azure SQL Database nebo ke službě Azure synapse Analytics. To lze provést pomocí následujících kroků: 
    1. Nastavte jako správce Azure Active Directory pro SQL Server.
    1. Připojte se k Azure SQL Database/datový sklad pomocí Azure Active Directory.
    1. Pomocí Editoru dotazů (Preview) spusťte následující skript, který přidá spravovanou identitu pro sdílení dat jako db_datareader, db_datawriter db_ddladmin. Je nutné se připojit pomocí služby Active Directory a SQL Server ověřování. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.         

* IP adresa klienta SQL Server přístup k bráně firewall. To lze provést pomocí následujících kroků: 
    1. V systému SQL Server v Azure Portal přejděte na *brány firewall a virtuální sítě* .
    1. Kliknutím na přepínač **zapnuto** povolíte přístup ke službám Azure.
    1. Klikněte na **+ Přidat IP adresu klienta** a klikněte na **Uložit**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím doručování dat do cíle SQL z Azure Portal. Můžete také přidat rozsah IP adres. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Příjem dat do clusteru Azure Průzkumník dat: 

* Cluster Azure Průzkumník dat ve stejném datovém centru Azure jako cluster Průzkumník dat poskytovatele dat: Pokud ho ještě nemáte, můžete vytvořit [cluster Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal). Pokud si nejste jisti datovým centrem Azure v clusteru poskytovatele dat, můžete cluster vytvořit později v procesu.
* Oprávnění k zápisu do clusteru Azure Průzkumník dat, který se nachází v *Microsoft. Kusto/Clusters/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role do clusteru Azure Průzkumník dat, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

1. Můžete otevřít pozvánku z e-mailu nebo přímo z Azure Portal. 

   Pokud chcete otevřít pozvánku z e-mailu, Projděte si doručenou poštu od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com> **. Kliknutím na **Zobrazit pozvánku** zobrazíte pozvánku v Azure. 

   Chcete-li otevřít pozvánku přímo z Azure Portal, vyhledejte v Azure Portal **pozvánky ke sdílení dat** . Tím přejdete do seznamu pozvání ke sdílení dat.

   ![Seznam pozvánek](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
1. Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

   ![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

1. V části *cílový účet sdílení dat*vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

   V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

   V poli **přijatý název sdílené složky** můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

   Jakmile souhlasíte s podmínkami použití a zadáte účet pro sdílení dat pro správu přijaté sdílené složky, vyberte **přijmout a nakonfigurovat**. Vytvoří se předplatné sdílení. 

   ![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

   Tím přejdete na přijatý podíl v účtu pro sdílení dat. 

   Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

## <a name="configure-received-share"></a>Konfigurace přijaté sdílené složky
Chcete-li nakonfigurovat, kde chcete přijímat data, postupujte podle následujících kroků.

1. Vyberte kartu **datové sady** . Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte možnost **+ mapovat na cíl** a zvolte cílové úložiště dat. 

   ![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

1. Vyberte cílový typ úložiště dat, do kterého chcete data založit. Budou přepsány všechny datové soubory nebo tabulky v cílovém úložišti dat se stejnou cestou a názvem. 

   V případě místního sdílení vyberte v zadaném umístění úložiště dat. Umístění je datové centrum Azure, kde se nachází zdrojové úložiště dat poskytovatele dat. Po namapování datové sady můžete pro přístup k datům sledovat odkaz v cílové cestě.

   ![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

1. Pokud pro sdílení na základě snímků vytvořil poskytovatel dat plán snímků, který poskytuje pravidelnou aktualizaci dat, můžete také povolit plán snímků výběrem karty **plán snímku** . Zaškrtněte políčko vedle plánu snímku a vyberte **+ Povolit**.

   ![Povolit plán snímků](./media/enable-snapshot-schedule.png "Povolit plán snímků")

## <a name="trigger-a-snapshot"></a>Aktivace snímku
Tyto kroky platí pouze pro sdílení na základě snímků.

1. Snímek můžete aktivovat výběrem karty **Podrobnosti** a **snímku triggeru**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

   ![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném*stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady**a klikněte na odkaz v cílové cestě. 

   ![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

## <a name="view-history"></a>Zobrazení historie
Tento krok platí jenom pro sdílení na základě snímků. Chcete-li zobrazit historii snímků, vyberte kartu **Historie** . Tady najdete historii všech snímků, které se vygenerovaly za posledních 30 dní. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak přijmout a získat sdílenou složku Azure Data. Další informace o konceptech Azure Data Share najdete v článku [Koncepty: terminologie Azure Data Share](terminology.md).