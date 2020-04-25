---
title: 'Kurz: přijetí dat pro příjem & – Azure Data Share'
description: Kurz – přijetí a příjem dat pomocí Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4dff48f909cd3febbbb7e92dcf96070020b8f57c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145134"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Kurz: přijetí a příjem dat pomocí Azure Data Share  

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
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure se subjektem s názvem "pozvání ke sdílení **<yourdataprovider@domain.com>** dat Azure od".
* Zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek pro sdílení dat a předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="receive-data-into-a-storage-account"></a>Příjem dat do účtu úložiště: 

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli vlastníka.  

### <a name="receive-data-into-a-sql-based-source"></a>Příjem dat do zdroje založeného na jazyce SQL:

* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k Azure SQL Database nebo Azure SQL Data Warehouse. To lze provést pomocí následujících kroků: 
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

* Cluster Azure Průzkumník dat ve stejném datovém centru Azure jako cluster Průzkumník dat poskytovatele dat: Pokud ho ještě nemáte, můžete vytvořit [cluster Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Pokud si nejste jisti datovým centrem Azure v clusteru poskytovatele dat, můžete cluster vytvořit později v procesu.
* Oprávnění k zápisu do clusteru Azure Průzkumník dat, který se nachází v *Microsoft. Kusto/Clusters/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role do clusteru Azure Průzkumník dat, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli vlastníka. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

1. Můžete otevřít pozvánku z e-mailu nebo přímo z Azure Portal. 

   Pokud chcete otevřít pozvánku z e-mailu, Projděte si doručenou poštu od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com> **. Kliknutím na **Zobrazit pozvánku** zobrazíte pozvánku v Azure. 

   Chcete-li otevřít pozvánku přímo z Azure Portal, vyhledejte v Azure Portal **pozvánky ke sdílení dat** . Tím přejdete do seznamu pozvání ke sdílení dat.

   ![Zasílán](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
1. Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

   ![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

1. V části *cílový účet sdílení dat*vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

   V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

   V poli **přijatý název sdílené složky** můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

   ![Cílový účet pro sdílení dat](./media/target-data-share.png "Cílový účet pro sdílení dat") 

1. Až budete souhlasit s podmínkami použití a zadáte umístění pro sdílenou složku, vyberte možnost *přijmout a konfigurovat*. Vytvoří se předplatné sdílení.

   U sdílení na základě snímků vás další obrazovka vyzve k výběru cílového účtu úložiště, do kterého se mají data kopírovat. 

   ![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

   Pokud chcete pozvánku přijmout teď, ale později nakonfigurovat cílové úložiště dat, vyberte *přijmout a nakonfigurovat později*. Pokud chcete později pokračovat v konfiguraci úložiště, přečtěte si článek [Konfigurace stránky mapování datových sad](how-to-configure-mapping.md) , kde najdete podrobné pokyny pro obnovení konfigurace sdílené složky. 

   Informace o tom, jak obnovit konfiguraci sdílení dat, najdete v tématu věnovaném místnímu sdílení na stránce [Konfigurace mapování datových sad](how-to-configure-mapping.md) . 

   Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

## <a name="configure-storage"></a>Konfigurace úložiště
1. V části *nastavení cílového úložiště*vyberte předplatné, skupinu prostředků a účet úložiště, do kterého chcete data přijímat. 

   ![Nastavení cílového úložiště](./media/target-storage-settings.png "Cílové úložiště") 

1. Pokud chcete získat pravidelnou aktualizaci vašich dat, ujistěte se, že jste povolili nastavení snímku. Všimněte si, že se zobrazí jenom plán nastavení snímků, pokud ho poskytovatel dat zahrnul do sdílené složky dat. 

   ![Nastavení snímku](./media/snapshot-settings.png "Nastavení snímku") 

1. Vyberte *Uložit*. 

> [!IMPORTANT]
> Pokud přijímáte data založená na jazyce SQL a chcete tato data přijímat do zdroje založeného na jazyce SQL, přejděte na téma [Konfigurace mapování datových sad](how-to-configure-mapping.md) s návodem, jak se naučíte konfigurovat SQL Server jako cíl pro datovou sadu. 

## <a name="trigger-a-snapshot"></a>Aktivace snímku
Tyto kroky platí pouze pro sdílení na základě snímků.

1. Snímek můžete aktivovat na kartě přijaté sdílené složky – > podrobnosti výběrem **spouštěcího snímku**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

   ![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném*stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady**a klikněte na odkaz v cílové cestě. 

   ![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

## <a name="view-history"></a>Zobrazení historie
Pokud chcete zobrazit historii snímků, přejděte k části přijaté sdílené složky – historie >. Tady najdete historii všech snímků vygenerovaných za posledních 60 dnů. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak přijmout a získat sdílenou složku Azure Data. Další informace o konceptech Azure Data Share najdete v článku [Koncepty: terminologie Azure Data Share](terminology.md).