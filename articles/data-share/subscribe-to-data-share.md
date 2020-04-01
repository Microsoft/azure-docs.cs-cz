---
title: 'Kurz: Přijímání & přijímat data – Azure Data Share'
description: Kurz – přijímání a přijímání dat pomocí služby Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083105"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Kurz: Přijímání a přijímání dat pomocí služby Azure Data Share  

V tomto kurzu se dozvíte, jak přijmout pozvánku ke sdílení dat pomocí služby Azure Data Share. Dozvíte se, jak přijímat data sdílená s vámi, a také jak povolit interval pravidelné aktualizace, abyste měli vždy k tomu nejnovější snímek dat, která jsou s vámi sdílena. 

> [!div class="checklist"]
> * Jak přijmout pozvánku ke sdílené složce Azure Data Share
> * Vytvoření účtu Azure Data Share
> * Určení cíle pro vaše data
> * Vytvoření předplatného sdílené datové složky pro plánovanou aktualizaci

## <a name="prerequisites"></a>Požadavky
Než budete moci přijmout pozvánku ke sdílené správě dat, musíte zřídit několik prostředků Azure, které jsou uvedeny níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou všechny požadavky dokončeny. 

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure s **<yourdataprovider@domain.com>** předmětem s názvem "Pozvánka ke sdílené složce Azure z ".
* Zaregistrujte [poskytovatele prostředků Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek sdílení dat a předplatné Azure, kde se nacházejí vaše cílové úložiště dat Azure.

### <a name="receive-data-into-a-storage-account"></a>Příjem dat do účtu úložiště: 

* Účet Azure Storage: Pokud ho ještě nemáte, můžete si vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft.Storage/storageAccounts/write*. Toto oprávnění existuje v roli přispěvatele. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft.Authorization/role assignments/write*. Toto oprávnění existuje v roli vlastníka.  

### <a name="receive-data-into-a-sql-based-source"></a>Příjem dat do zdroje založeného na SQL:

* Oprávnění k zápisu do databází na serveru SQL, který je k dispozici v *microsoft.sql/servers/databases/write*. Toto oprávnění existuje v roli přispěvatele. 
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k Azure SQL Database nebo Azure SQL Data Warehouse. To lze provést pomocí následujících kroků: 
    1. Nastavte si jako Správce služby Azure Active Directory pro SQL server.
    1. Připojte se k Azure SQL Database/Data Warehouse pomocí Azure Active Directory.
    1. Pomocí Editoru dotazů (preview) můžete spustit následující skript a přidat spravovanou identitu sdílení dat jako "db_datareader, db_datawriter db_ddladmin". Musíte se připojit pomocí služby Active Directory a nikoli pomocí ověřování serveru SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Všimněte si, že *<share_acc_name>* je název prostředku sdílení dat. Pokud jste ještě nevytvořili prostředek sdílení dat, můžete se k tomuto požadovanému webu vrátit později.         

* Přístup k bráně IP SQL Server Firewall klienta. To lze provést pomocí následujících kroků: 
    1. Na SQL serveru na Webu Azure Portal přejděte na *firewally a virtuální sítě.*
    1. Kliknutím **na** přepínač povolíte přístup ke službám Azure.
    1. Klepněte na tlačítko **+Přidat IP klienta** a klepněte na tlačítko **Uložit**. IP adresa klienta se může změnit. Tento proces může být nutné opakovat při příštím přijímání dat do cíle SQL z webu Azure Portal. Můžete také přidat rozsah IP adres. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Příjem dat do clusteru Průzkumníka dat Azure: 

* Cluster Azure Data Explorer ve stejném datovém centru Azure jako cluster Průzkumník dat poskytovatele dat: Pokud ho ještě nemáte, můžete vytvořit [cluster Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Pokud neznáte datové centrum Azure clusteru poskytovatele dat, můžete vytvořit cluster později v procesu.
* Oprávnění k zápisu do clusteru Azure Data Explorer, který je k dispozici v *Microsoft.Kusto/clusters/write*. Toto oprávnění existuje v roli přispěvatele. 
* Oprávnění k přidání přiřazení role do clusteru Azure Data Explorer, který se nachází v *Microsoft.Authorization/role assignments/write*. Toto oprávnění existuje v roli vlastníka. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

1. Zkontrolujte, zda v doručené poště není pozvánka od poskytovatele dat. Pozvánka je z Microsoft Azure s názvem **Azure Data Share pozvánka od <yourdataprovider@domain.com> **. Název sdílené položky si poznamenejte a ujistěte se, že přijímáte správnou sdílenou složku, pokud existuje více pozvánek. 

1. Vyberte **v nabídce pozvánku,** abyste viděli pozvánku v Azure. Tím přejdete do zobrazení Přijaté sdílené složky.

   ![Pozvánky](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
1. Zkontrolujte všechna pole, včetně **Podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste uvedli, že souhlasíte. 

   ![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

1. V části *Cílový účet sdílení dat*vyberte předplatný a skupinu prostředků, do kterých budete nasazovat sdílenou složku dat. 

   V poli **Účet sdílení dat** vyberte Vytvořit **nový,** pokud nemáte existující účet sdílení dat. V opačném případě vyberte existující účet sdílení dat, do kterého chcete přijmout sdílenou složku dat. 

   Pro pole **Přijaté jméno sdílené složky** můžete ponechat výchozí nastavení zadané daty nebo zadat nový název přijaté sdílené složky. 

   ![Cílový účet sdílení dat](./media/target-data-share.png "Cílový účet sdílení dat") 

1. Jakmile souhlasíte s podmínkami použití a zadáte umístění sdílené položky, vyberte možnost *Přijmout a nakonfigurovat*. Bude vytvořeno předplatné sdílení.

   Pro sdílení na základě snímků, další obrazovka vás vyzve k výběru cílového účtu úložiště pro vaše data, která mají být zkopírovány do. 

   ![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

   Pokud chcete pozvánku přijmout nyní, ale nakonfigurujte cílové úložiště dat později, vyberte *Přijmout a nakonfigurovat později*. Chcete-li pokračovat v konfiguraci úložiště později, [najdete v tématu konfigurace mapování datových sad](how-to-configure-mapping.md) stránku podrobné kroky, jak obnovit konfiguraci sdílení dat. 

   Informace o sdílení na místě najdete v tématu konfigurace stránky [mapování datových sad,](how-to-configure-mapping.md) kde najdete podrobné kroky, jak obnovit konfiguraci sdílené složky dat. 

   Pokud pozvánku nechcete přijmout, vyberte *možnost Odmítnout*. 

## <a name="configure-storage"></a>Konfigurace úložiště
1. V části *Nastavení cílového úložiště*vyberte účet Předplatných, Prostředků a úložiště, do kterých chcete data dostávat. 

   ![Nastavení cílového úložiště](./media/target-storage-settings.png "Cílové úložiště") 

1. Chcete-li dostávat pravidelnou aktualizaci dat, ujistěte se, že jste povolili nastavení snímku. Všimněte si, že plán nastavení snímku se zobrazí pouze v případě, že ho váš poskytovatel dat zahrnul do sdílené složky dat. 

   ![Nastavení snímku](./media/snapshot-settings.png "Nastavení snímku") 

1. Vyberte *Uložit*. 

> [!IMPORTANT]
> Pokud přijímáte data založená na SQL a chcete tato data přijímat do zdroje založeného na SQL, navštivte [stránku konfigurace průvodce mapováním datové sady,](how-to-configure-mapping.md) kde se dozvíte, jak nakonfigurovat SQL Server jako cíl pro vaši datovou sadu. 

## <a name="trigger-a-snapshot"></a>Aktivace snímku
Tyto kroky platí pouze pro sdílení na základě snímku.

1. Snímek můžete spustit na kartě Přijaté sdílené složky -> podrobnosti výběrem **možnosti Aktivační událost**. Zde můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte úplnou kopii. 

   ![Snímek aktivační události](./media/trigger-snapshot.png "Snímek aktivační události") 

1. Pokud je stav posledního spuštění *úspěšný*, přejděte do cílového úložiště dat a zobrazte přijatá data. Vyberte **Datové sady**a klikněte na odkaz v cílové cestě. 

   ![Datové sady pro spotřebitele](./media/consumer-datasets.png "Mapování datových sad spotřebitelů") 

## <a name="view-history"></a>Zobrazení historie
Chcete-li zobrazit historii snímků, přejděte na položku Přijaté sdílené složky -> historie. Zde najdete historii všech snímků, které byly generovány za posledních 60 dní. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak přijmout a přijímat Azure Data Share. Další informace o konceptech Azure Data Share najdete na [Koncepty: Terminologie sdílení dat Azure](terminology.md).