---
title: Sdílení a příjem dat ze služeb Azure SQL Database a Azure Synapse Analytics
description: Informace o tom, jak sdílet a přijímat data z Azure SQL Database a Azure synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 1bf5966ab3e4bb62c2be302a7791cadad9761a70
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150389"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Sdílení a příjem dat ze služeb Azure SQL Database a Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share podporuje sdílení na základě snímků Azure SQL Database a Azure synapse Analytics (dříve Azure SQL DW). Tento článek vysvětluje, jak sdílet a přijímat data z těchto zdrojů.

Azure Data Share podporuje sdílení tabulek nebo zobrazení z Azure SQL Database a Azure synapse Analytics (dříve Azure SQL DW). Příjemci dat se můžou rozhodnout, že data přijměte do Azure Data Lake Storage Gen2 nebo Azure Blob Storage jako soubor CSV nebo Parquet, a také do Azure SQL Database a Azure synapse Analytics jako tabulky.

Při přijímání dat do Azure Data Lake Store Gen2 nebo Azure Blob Storage zapíše úplné snímky obsah cílového souboru, pokud už existuje.
Když se do tabulky přijímají data a cílová tabulka ještě neexistuje, vytvoří Azure Data Share tabulku SQL se zdrojovým schématem. Pokud cílová tabulka již existuje se stejným názvem, bude vynechána a přepíše se nejnovějším úplným snímkem. Přírůstkové snímky se aktuálně nepodporují.

## <a name="share-data"></a>Sdílení dat

### <a name="prerequisites-to-share-data"></a>Předpoklady pro sdílení dat

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlašovací e-mailová adresa Azure vašeho příjemce (pomocí e-mailového aliasu nebude fungovat).
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než je ten, který použijete k vytvoření prostředku pro sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="prerequisites-for-sql-source"></a>Předpoklady pro zdroj SQL
Níže je uveden seznam požadavků pro sdílení dat ze zdroje SQL. Můžete také postupovat podle [ukázky krok za krokem](https://youtu.be/hIE-TjJD8Dc) a nakonfigurovat požadované součásti.

* Azure SQL Database nebo Azure synapse Analytics (dříve SQL Data Warehouse) s tabulkami a zobrazeními, které chcete sdílet.
* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli Přispěvatel.
* Oprávnění ke sdílení dat pro přístup k datovému skladu. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte na SQL Server a nastavte si ho jako správce Azure Active Directory.
    1. Připojení k Azure SQL Database/datový sklad pomocí [Editoru dotazů](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal#connect-using-azure-active-directory) nebo SQL Server Management Studio Azure Active Directory ověřování. 
    1. Spusťte následující skript, který přidá identitu spravovaného prostředku sdílené dat jako db_datareader. Je nutné se připojit pomocí služby Active Directory a SQL Server ověřování. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.  

* Uživatel Azure SQL Database s přístupem "db_datareader", který umožňuje procházet a vybírat tabulky a zobrazení, která chcete sdílet. 

* SQL Server přístup k bráně firewall. To lze provést pomocí následujících kroků: 
    1. V systému SQL Server v Azure Portal přejděte na *brány firewall a virtuální sítě* .
    1. Klikněte na **Ano** , pokud chcete, aby *služby a prostředky Azure měly přístup k tomuto serveru*.
    1. Klikněte na **+ Přidat IP adresu klienta**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres.
    1. Klikněte na **Uložit**. 

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. V levém horním rohu portálu vyberte tlačítko nabídky a pak vyberte **vytvořit prostředek** (+).

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte sdílet data a vyberte **vytvořit**.

1. Vyplňte základní podrobnosti prostředku Azure Data Share s následujícími informacemi. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet pro sdílení dat.|
    | Skupina prostředků | *test-Resource-Group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet pro sdílení dat.
    | Název | *datashareaccount* | Zadejte název vašeho účtu pro sdílení dat. |
    | | |

1. Vyberte **zkontrolovat + vytvořit**a pak **vytvořte** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

### <a name="create-a-share"></a>Vytvoření sdílené složky

1. Přejděte na stránku s přehledem sdílení dat.

    ![Sdílení dat](./media/share-receive-data.png "Sdílení dat") 

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti pro vaši sdílenou složku. Zadejte název, typ sdílení, popis obsahu sdílení a podmínek použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "Zadat podrobnosti o sdílené složce") 

1. Vyberte **Pokračovat**.

1. Chcete-li do sdílené složky přidat datové sady, vyberte možnost **přidat datové sady**. 

    ![Přidání datových sad do sdílené složky](./media/datasets.png "Datové sady")

1. Vyberte typ datové sady, který chcete přidat. V závislosti na typu sdílené položky (snímku nebo na místě), který jste vybrali v předchozím kroku, se zobrazí jiný seznam typů datových sad. 

    ![AddDatasets](./media/add-datasets.png "Přidat datové sady")    

1. Vyberte svůj SQL Server, zadejte přihlašovací údaje a vyberte **Další** a přejděte k objektu, který chcete sdílet, a vyberte přidat datové sady. 

    ![SelectDatasets](./media/select-datasets-sql.png "Vybrat datové sady")    

1. Na kartě příjemci zadejte do e-mailových adres vašeho příjemce dat a vyberte + Přidat příjemce. 

    ![AddRecipients](./media/add-recipient.png "Přidání příjemců") 

1. Vyberte **Pokračovat**.

1. Pokud jste vybrali typ sdílené složky snímků, můžete nakonfigurovat plán snímků pro poskytování aktualizací vašich dat příjemci dat. 

    ![EnableSnapshots](./media/enable-snapshots.png "Povolit snímky") 

1. Vyberte čas spuštění a interval opakování. 

1. Vyberte **Pokračovat**.

1. Na kartě Revize + vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání. 

## <a name="receive-data"></a>Příjem dat

### <a name="prerequisites-to-receive-data"></a>Předpoklady pro příjem dat
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure se subjektem s názvem "pozvání ke sdílení dat Azure od **<yourdataprovider@domain.com>** ".
* Zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek pro sdílení dat a předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="prerequisites-for-target-storage-account"></a>Předpoklady pro cílový účet úložiště
Pokud se rozhodnete přijímat data do Azure Storage, níže je uvedený seznam požadavků.

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník.  

### <a name="prerequisites-for-sql-target"></a>Předpoklady pro cíl SQL
Pokud se rozhodnete přijímat data do Azure SQL Database, najdete níže seznam požadavků Azure synapse Analytics. Můžete také postupovat podle [ukázky krok za krokem](https://youtu.be/aeGISgK1xro) a nakonfigurovat požadované součásti.

* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění pro spravovanou identitu prostředku sdílení dat pro přístup k Azure SQL Database nebo ke službě Azure synapse Analytics. To lze provést pomocí následujících kroků: 
    1. V Azure Portal přejděte na SQL Server a nastavte si ho jako správce Azure Active Directory.
    1. Připojení k Azure SQL Database/datový sklad pomocí [Editoru dotazů](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal#connect-using-azure-active-directory) nebo SQL Server Management Studio Azure Active Directory ověřování. 
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

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Otevřít pozvánku

1. Můžete otevřít pozvánku z e-mailu nebo přímo z Azure Portal. 

   Pokud chcete otevřít pozvánku z e-mailu, Projděte si doručenou poštu od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com> **. Kliknutím na **Zobrazit pozvánku** zobrazíte pozvánku v Azure. 

   Chcete-li otevřít pozvánku přímo z Azure Portal, vyhledejte v Azure Portal **pozvánky ke sdílení dat** . Tím přejdete do seznamu pozvání ke sdílení dat.

   ![Seznam pozvánek](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

### <a name="accept-invitation"></a>Přijmout pozvánku
1. Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

   ![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

1. V části *cílový účet sdílení dat*vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

   V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

   V poli **přijatý název sdílené složky** můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

   Jakmile souhlasíte s podmínkami použití a zadáte účet pro sdílení dat pro správu přijaté sdílené složky, vyberte **přijmout a nakonfigurovat**. Vytvoří se předplatné sdílení. 

   ![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

   Tím přejdete na přijatý podíl v účtu pro sdílení dat. 

   Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

### <a name="configure-received-share"></a>Konfigurace přijaté sdílené složky
Chcete-li nakonfigurovat, kde chcete přijímat data, postupujte podle následujících kroků.

1. Vyberte kartu **datové sady** . Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte možnost **+ mapovat na cíl** a zvolte cílové úložiště dat. 

   ![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

1. Vyberte cílové úložiště dat, ve kterém chcete data vykládat. Budou přepsány všechny datové soubory nebo tabulky v cílovém úložišti dat se stejnou cestou a názvem. 

   ![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště dat") 

1. Pokud pro sdílení na základě snímků vytvořil poskytovatel dat plán snímků, který poskytuje pravidelnou aktualizaci dat, můžete také povolit plán snímků výběrem karty **plán snímku** . Zaškrtněte políčko vedle plánu snímku a vyberte **+ Povolit**.

   ![Povolit plán snímků](./media/enable-snapshot-schedule.png "Povolit plán snímků")

### <a name="trigger-a-snapshot"></a>Aktivace snímku
Tyto kroky platí pouze pro sdílení na základě snímků.

1. Snímek můžete aktivovat výběrem karty **Podrobnosti** a **snímku triggeru**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. U zdrojů SQL se podporuje jenom úplný snímek.

   ![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném*stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady**a klikněte na odkaz v cílové cestě. 

   ![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

### <a name="view-history"></a>Zobrazení historie
Tento krok platí jenom pro sdílení na základě snímků. Chcete-li zobrazit historii snímků, vyberte kartu **Historie** . Tady najdete historii všech snímků, které se vygenerovaly za posledních 30 dní. 

## <a name="supported-data-types"></a>Podporované datové typy
Když sdílíte data ze zdroje SQL, používá se následující mapování SQL Server datových typů k dočasným datovým typům Azure Data Share během procesu snímku. 

| SQL Server datový typ | Typ provizorního datového sdílení dat Azure |
|:--- |:--- |
| bigint |Int64 |
| binární |Byte [] |
| bit |Logická hodnota |
| char |Řetězec, znak [] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary (max)) |Byte [] |
| Float |dvojité |
| image |Byte [] |
| int |Int32 |
| papír |Decimal |
| nchar |Řetězec, znak [] |
| ntext |Řetězec, znak [] |
| numerické |Decimal |
| nvarchar |Řetězec, znak [] |
| real |Jednoduché |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |Řetězec, znak [] |
| time |TimeSpan |
| časové razítko |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |Řetězec |

>[!NOTE]
> 1. Pro datové typy, které jsou mapovány na mezihodnotový průběžný typ, aktuálně snímek podporuje přesnost až na 28. Pokud máte data, která vyžadují přesnost větší než 28, zvažte převod na řetězec. 
> 1.  Pokud sdílíte data z Azure SQL Database do služby Azure synapse Analytics, nejsou podporované všechny datové typy. Podrobnosti najdete [v tabulkách s datovými typy v synapse fondu SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types) . 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>Always Encrypted SQL nebo maskování dynamických dat
Služba Azure Data Share v současné době nepodporuje databáze SQL Azure s nakonfigurovaným Always Encrypted. 

Pro zdrojové tabulky SQL s dynamickým maskou dat se data zobrazí na straně příjemce.

## <a name="sql-snapshot-performance"></a>Výkon snímku SQL
Výkon snímku SQL je ovlivněn řadou faktorů. Vždy se doporučuje provádět vlastní testování výkonu. Níže jsou uvedeny některé příklady faktorů, které mají vliv na výkon.

* Hardwarová konfigurace (například virtuální jádra, paměť, DWU) zdrojového a cílového úložiště dat SQL. 
* Souběžný přístup ke zdrojovým a cílovým úložištím dat. Pokud sdílíte více tabulek a zobrazení ze stejného úložiště dat SQL nebo pokud do stejného úložiště dat SQL přijmete více tabulek a zobrazení, bude to mít vliv na výkon.   
* Umístění zdrojových a cílových úložišť dat. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Řešení potíží se selháním snímku SQL
Nejběžnější příčinou selhání snímku je to, že sdílená data nemají oprávnění ke zdrojovému nebo cílovému úložišti dat. Aby bylo možné udělit oprávnění ke sdílení dat zdrojovému nebo cílovému úložišti dat SQL, je nutné spustit zadaný skript SQL při připojování k databázi SQL pomocí ověřování Azure Active Directory. Pokud chcete vyřešit další selhání snímku SQL, přečtěte si téma [řešení potíží se selháním snímku](data-share-troubleshoot.md#snapshot-failed).

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak sdílet a přijímat data ze zdrojů SQL pomocí služby Azure Data Share. Pokud se chcete dozvědět víc o sdílení z jiných zdrojů dat, pokračujte na [podporovaná úložiště dat](supported-data-stores.md).



