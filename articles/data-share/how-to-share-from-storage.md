---
title: Sdílení a příjem dat ze služeb Azure Blob Storage a Azure Data Lake Storage
description: Přečtěte si, jak sdílet a přijímat data z Azure Blob Storage a Azure Data Lake Storage
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: da1683ec48fcae10ff74163a7db089c30ddd7aad
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219900"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Sdílení a příjem dat ze služeb Azure Blob Storage a Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share podporuje sdílení na základě snímků z účtu úložiště. Tento článek vysvětluje, jak sdílet a přijímat data z následujících zdrojů: Azure Blob Storage, Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2.

Azure Data Share podporuje sdílení souborů, složek a systémů souborů z Azure Data Lake Gen1 a Azure Data Lake Gen2. Podporuje taky sdílení objektů blob, složek a kontejnerů z Azure Blob Storage. V tuto chvíli se podporuje jenom objekt blob bloku. Data, která z těchto zdrojů sdílíte, můžete přijmout do Azure Data Lake Gen2 nebo Azure Blob Storage.

Když jsou systémy souborů, kontejnery nebo složky sdíleny ve sdílení založeném na snímcích, příjemce dat si může vytvořit úplnou kopii sdílených dat nebo využít možnost přírůstkového snímku pro kopírování pouze nových nebo aktualizovaných souborů. Přírůstkový snímek je založen na době poslední změny souborů. Existující soubory se stejným názvem budou přepsány.

## <a name="share-data"></a>Sdílení dat

### <a name="prerequisites-to-share-data"></a>Předpoklady pro sdílení dat

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlašovací e-mailová adresa Azure vašeho příjemce (pomocí e-mailového aliasu nebude fungovat).
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než je ten, který použijete k vytvoření prostředku pro sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="prerequisites-for-source-storage-account"></a>Požadavky na zdrojový účet úložiště

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](../storage/common/storage-account-create.md)
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel.
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník. 

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

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání. 

## <a name="receive-data"></a>Příjem dat

### <a name="prerequisites-to-receive-data"></a>Předpoklady pro příjem dat
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure se subjektem s názvem "pozvání ke sdílení dat Azure od **<yourdataprovider@domain.com>** ".
* Zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném Azure, kde vytvoříte prostředek pro sdílení dat a předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="prerequisites-for-target-storage-account"></a>Předpoklady pro cílový účet úložiště

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](../storage/common/storage-account-create.md). 
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel. 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli Vlastník.  

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

1. Vyberte cílové úložiště dat, ve kterém chcete data vykládat. Všechny datové soubory v cílovém úložišti dat se stejnou cestou a názvem budou přepsány. 

   ![Cílový účet úložiště](./media/map-target.png "Cílové úložiště") 

1. Pokud pro sdílení na základě snímků vytvořil poskytovatel dat plán snímků, který poskytuje pravidelnou aktualizaci dat, můžete také povolit plán snímků výběrem karty **plán snímku** . Zaškrtněte políčko vedle plánu snímku a vyberte **+ Povolit**.

   ![Povolit plán snímků](./media/enable-snapshot-schedule.png "Povolit plán snímků")

### <a name="trigger-a-snapshot"></a>Aktivace snímku
Tyto kroky platí pouze pro sdílení na základě snímků.

1. Snímek můžete aktivovat výběrem karty **Podrobnosti** a **snímku triggeru**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

   ![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném*stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady**a klikněte na odkaz v cílové cestě. 

   ![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

### <a name="view-history"></a>Zobrazení historie
Tento krok platí jenom pro sdílení na základě snímků. Chcete-li zobrazit historii snímků, vyberte kartu **Historie** . Tady najdete historii všech snímků, které se vygenerovaly za posledních 30 dní. 

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak sdílet a přijímat data z účtu úložiště pomocí služby Azure Data Share. Pokud se chcete dozvědět víc o sdílení z jiných zdrojů dat, pokračujte na [podporovaná úložiště dat](supported-data-stores.md).