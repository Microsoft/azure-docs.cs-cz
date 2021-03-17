---
title: Sdílení a příjem dat ze služeb Azure Blob Storage a Azure Data Lake Storage
description: Naučte se, jak sdílet a přijímat data z Azure Blob Storage a Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: dc309e85373193e4f5d431f543ff3e59ea5bebc7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739258"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Sdílení a příjem dat ze služeb Azure Blob Storage a Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share podporuje sdílení na základě snímků z účtu úložiště. Tento článek vysvětluje, jak sdílet a přijímat data z Azure Blob Storage, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2.

Azure Data Share podporuje sdílení souborů, složek a systémů souborů z Azure Data Lake Gen1 a Azure Data Lake Gen2. Podporuje taky sdílení objektů blob, složek a kontejnerů z Azure Blob Storage. V současné době jsou podporovány pouze objekty blob bloku. Data, která z těchto zdrojů sdílíte, můžou Azure Data Lake Gen2 nebo Azure Blob Storage přijmout.

Když se v rámci sdílení na základě snímků sdílí systémy souborů, kontejnery nebo složky, můžou si příjemci dat vytvořit úplnou kopii sdílených dat. Nebo mohou použít funkci přírůstkového snímku ke kopírování pouze nových nebo aktualizovaných souborů. Funkce přírůstkového snímku je založena na době poslední změny souborů. 

Existující soubory, které mají stejný název, budou během snímku přepsány. Soubor, který je odstraněný ze zdroje, se v cíli neodstraní. Prázdné podsložky ve zdroji se nekopírují do cíle. 

## <a name="share-data"></a>Sdílení dat

Informace v následujících částech použijte ke sdílení dat pomocí Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Předpoklady pro sdílení dat

* Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
* Najděte přihlašovací e-mailovou adresu Azure vašeho příjemce. E-mailový alias příjemce nebude fungovat pro vaše účely.
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než je ten, kde vytvoříte prostředek pro sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Předpoklady pro zdrojový účet úložiště

* Účet služby Azure Storage. Pokud účet ještě nemáte, [vytvořte ho](../storage/common/storage-account-create.md).
* Oprávnění k zápisu do účtu úložiště. Oprávnění k zápisu se nachází v *Microsoft. Storage/storageAccounts/Write*. Je součástí role přispěvatele.
* Oprávnění k přidání přiřazení role k účtu úložiště. Toto oprávnění je v *Microsoft. Authorization/přiřazení rolí/zápis*. Je součástí role vlastníka. 

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. V levém horním rohu portálu otevřete nabídku a pak vyberte **vytvořit prostředek** (+).

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte **data Shared** a **Create (vytvořit**).

1. Zadejte základní podrobnosti prostředku Azure Data Share: 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure pro váš účet pro sdílení dat.|
    | Skupina prostředků | *test-Resource-Group* | Použijte existující skupinu prostředků nebo vytvořte skupinu prostředků. |
    | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet pro sdílení dat.
    | Název | *datashareaccount* | Pojmenujte svůj účet pro sdílení dat. |
    | | |

1. Vyberte **zkontrolovat + vytvořit**  >  **vytvořit** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty. 

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

### <a name="create-a-share"></a>Vytvoření sdílené složky

1. Přejít na stránku s **přehledem** sdílení dat.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Snímek obrazovky znázorňující Přehled sdílení dat":::

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.   

1. Zadejte podrobnosti pro vaši sdílenou složku. Zadejte název, typ sdílení, popis obsahu sdílení a podmínek použití (volitelné). 

    ![Snímek obrazovky zobrazující podrobnosti o sdílení dat](./media/enter-share-details.png "Zadejte podrobnosti sdílení dat.") 

1. Vyberte **Pokračovat**.

1. Chcete-li do sdílené složky přidat datové sady, vyberte možnost **přidat datové sady**. 

    ![Snímek obrazovky ukazující, jak do sdílené složky přidat datové sady](./media/datasets.png "Datové sady.")

1. Vyberte typ datové sady, který chcete přidat. Seznam typů datových sad závisí na tom, jestli jste v předchozím kroku vybrali sdílení na základě snímků nebo místní sdílení. 

    ![Snímek obrazovky s informacemi o tom, kde vybrat typ datové sady](./media/add-datasets.png "Přidejte datové sady.")    

1. Přejít na objekt, který chcete sdílet. Pak vyberte **přidat datové sady**. 

    ![Snímek obrazovky ukazující, jak vybrat objekt, který se má sdílet](./media/select-datasets.png "Vyberte datové sady.")    

1. Na kartě **příjemci** přidejte e-mailovou adresu příjemce dat, a to tak, že vyberete **Přidat příjemce**. 

    ![Snímek obrazovky ukazující, jak přidat e-mailové adresy příjemců](./media/add-recipient.png "Přidejte příjemce.") 

1. Vyberte **Pokračovat**.

1. Pokud jste vybrali typ sdílené složky snímků, můžete nastavit plán snímků, který aktualizuje data pro příjemce dat. 

    ![Snímek obrazovky znázorňující nastavení plánu snímků](./media/enable-snapshots.png "Povolit snímky.") 

1. Vyberte čas spuštění a interval opakování. 

1. Vyberte **Pokračovat**.

1. Na kartě **Revize + vytvořit** zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Potom vyberte **Vytvořit**.

Nyní jste vytvořili sdílenou složku Azure Data. Příjemce vaší sdílené složky může přijmout vaše pozvání. 

## <a name="receive-data"></a>Příjem dat

Následující části popisují, jak přijímat sdílená data.
### <a name="prerequisites-to-receive-data"></a>Předpoklady pro příjem dat
Než přijmete pozvánku ke sdílení dat, ujistěte se, že máte následující požadavky: 

* Předplatné Azure. Pokud předplatné nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Pozvánka z Azure Předmět e-mailu by měl být "pozvání Azure Data Share z *\<yourdataprovider\@domain.com>* ".
* Registrovaný [poskytovatel prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v:
    * Předplatné Azure, kde vytvoříte prostředek pro sdílení dat.
    * Předplatné Azure, kde se nachází vaše cílové úložiště dat Azure.

### <a name="prerequisites-for-a-target-storage-account"></a>Předpoklady pro cílový účet úložiště

* Účet služby Azure Storage. Pokud ho ještě nemáte, [vytvořte účet](../storage/common/storage-account-create.md). 
* Oprávnění k zápisu do účtu úložiště. Toto oprávnění je v *Microsoft. Storage/storageAccounts/Write*. Je součástí role přispěvatele. 
* Oprávnění k přidání přiřazení role k účtu úložiště. Toto přiřazení je v *Microsoft. Authorization/přiřazení rolí/zápis*. Je součástí role vlastníka.  

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Otevřít pozvánku

Můžete otevřít pozvánku z e-mailu nebo přímo z Azure Portal.

1. Pokud chcete otevřít pozvánku z e-mailu, podívejte se na pozvánku od vašeho poskytovatele dat na svou doručenou poštu. Pozvání od Microsoft Azure má "pozvání Azure Data Share z *\<yourdataprovider\@domain.com>* ". Vyberte **Zobrazit pozvánku** a zobrazí se vaše Pozvánka v Azure. 

   Pokud chcete otevřít pozvánku z Azure Portal, vyhledejte *pozvánky ke sdílení dat*. Zobrazí se seznam pozvánek ke sdílení dat.

   ![Snímek obrazovky zobrazující seznam pozvánek v Azure Portal.](./media/invitations.png "Seznam pozvánek") 

1. Vyberte sdílenou složku, kterou chcete zobrazit. 

### <a name="accept-an-invitation"></a>Přijmout pozvánku
1. Zkontrolujte všechna pole, včetně **podmínky použití**. Pokud souhlasíte s podmínkami, zaškrtněte políčko. 

   ![Snímek obrazovky znázorňující Podmínky použití oblast](./media/terms-of-use.png "Podmínky použití.") 

1. V části **cílový účet sdílení dat** vyberte předplatné a skupinu prostředků, do které nasadíte sdílenou složku dat. Pak vyplňte následující pole:

   * V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, který bude akceptovat vaši sdílenou složku. 

   * V poli **přijatý název sdílené složky** ponechte výchozí hodnotu, kterou zprostředkovatel dat zadal, nebo zadejte nový název přijaté sdílené složky. 

1. Vyberte **přijmout a konfigurovat**. Vytvoří se předplatné sdílení. 

   ![Snímek obrazovky s informacemi o tom, kde přijmout možnosti konfigurace](./media/accept-options.png "Přijmout možnosti") 

    Přijatá sdílená složka se zobrazí v účtu pro sdílení dat. 

    Pokud nechcete pozvánku přijmout, vyberte **odmítnout**. 

### <a name="configure-a-received-share"></a>Konfigurace přijaté sdílené složky
Podle kroků v této části můžete nakonfigurovat umístění pro příjem dat.

1. Na kartě datové **sady** zaškrtněte políčko vedle datové sady, ve které chcete přiřadit cíl. Vyberte možnost **mapovat k cíli** a zvolte cílové úložiště dat. 

   ![Snímek obrazovky znázorňující, jak namapovat na cíl](./media/dataset-map-target.png "Namapujte na cíl.") 

1. Vyberte cílové úložiště dat pro data. Soubory v cílovém úložišti dat, které mají stejnou cestu a název jako soubory v přijatých datech, budou přepsány. 

   ![Snímek obrazovky s informacemi o tom, kde vybrat cílový účet úložiště](./media/map-target.png "Cílové úložiště") 

1. Pokud pro sdílení na základě snímků používá poskytovatel dat plán snímků k pravidelné aktualizaci dat, můžete plán povolit z karty **plán snímků** . Zaškrtněte políčko vedle plánu snímku. Pak vyberte **Povolit**.

   ![Snímek obrazovky ukazující, jak povolit plán snímků](./media/enable-snapshot-schedule.png "Povolit plán snímků.")

### <a name="trigger-a-snapshot"></a>Aktivace snímku
Kroky v této části se vztahují jenom na sdílení na základě snímků.

1. Snímek můžete aktivovat na kartě **Podrobnosti** . Na kartě vyberte **spouštěcí snímek**. Můžete zvolit, že se má aktivovat úplný snímek nebo přírůstkový snímek dat. Pokud připravujete data od poskytovatele dat poprvé, vyberte možnost **úplné kopírování**. Při provádění snímku se další snímky nespustí až do dokončení předchozího.

   ![Snímek obrazovky znázorňující výběr snímku triggeru](./media/trigger-snapshot.png "Spustit snímek") 

1. Po *úspěšném* stavu posledního spuštění přejdete do cílového úložiště dat a zobrazíte přijatá data. Vyberte **datové sady** a potom vyberte odkaz cílová cesta. 

   ![Snímek obrazovky zobrazující mapování datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

### <a name="view-history"></a>Zobrazení historie
Historii snímků můžete zobrazit pouze ve sdílení na základě snímků. Chcete-li zobrazit historii, otevřete kartu **Historie** . Tady vidíte historii všech snímků, které se vygenerovaly během posledních 30 dnů. 

## <a name="storage-snapshot-performance"></a>Výkon snímku úložiště
Výkon snímku úložiště je ovlivněn řadou faktorů kromě počtu souborů a velikosti sdílených dat. Vždy se doporučuje provádět vlastní testování výkonu. Níže jsou uvedeny některé příklady faktorů, které mají vliv na výkon.

* Souběžný přístup ke zdrojovým a cílovým úložištím dat.  
* Umístění zdrojových a cílových úložišť dat. 
* V případě přírůstkového snímku může počet souborů ve sdílené datové sadě ovlivnit čas, který bude mít za následek vyhledání seznamu souborů s časem poslední změny po posledním úspěšném snímku. 


## <a name="next-steps"></a>Další kroky
Zjistili jste, jak sdílet a přijímat data z účtu úložiště pomocí služby Azure Data Share. Další informace o sdílení z jiných zdrojů dat najdete v tématu [podporovaná úložiště dat](supported-data-stores.md).