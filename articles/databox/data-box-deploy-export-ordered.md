---
title: Kurz pro export dat z Azure Data Box | Microsoft Docs
description: Přečtěte si o požadavcích na nasazení a o tom, jak exportovat data z Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: b514cce8128dc0b17b5cebf8f2dc42e2c4dd8c8e
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336891"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Kurz: vytvoření objednávky exportu pro Azure Data Box (Preview)

Azure Data Box je hybridní řešení, které umožňuje přesunout data z Azure do svého umístění. V tomto kurzu se dozvíte, jak vytvořit objednávku exportu pro Azure Data Box. Hlavním důvodem pro vytvoření objednávky exportu je zotavení po havárii, pokud dojde k ohrožení zabezpečení úložiště v případě, že je nutné obnovit zálohování.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady pro export
> * Objednat Data Box pro export
> * Sledovat objednávku exportu
> * Zrušit export objednávky

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Požadavky

Před objednáním zařízení dokončete následující požadavky na konfiguraci pro Data Box službu a zařízení.

### <a name="for-service"></a>Služba

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Ujistěte se, že máte existující skupinu prostředků, kterou můžete použít s vaším Azure Data Box.

* Ujistěte se, že váš účet Azure Storage, ze kterého chcete exportovat data, je jedním z podporovaných typů účtů úložiště, jak jsou popsány [podporované účty úložiště pro data box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:

* Máte k síti datacentra připojený hostitelský počítač. Data z Azure Data Box budete kopírovat do tohoto počítače. Na vašem hostitelském počítači musí být podporovaný operační systém, jak je popsáno v [systémových požadavcích na Azure Data Box](data-box-system-requirements.md).

* Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není k dispozici připojení s 10 GbEmi, je možné použít datové propojení s 1 GbEí, ale budou ovlivněny rychlosti kopírování.

## <a name="order-data-box-for-export"></a>Objednat Data Box pro export

Provedením následujících kroků na webu Azure Portal si objednejte zařízení.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).

2. Vyberte **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Vyberte **Azure Data Box**.

   ![Vytvoření prostředku](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Vyberte **Vytvořit**.

   ![Vytvoření prostředku Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Ověřte, jestli je služba Azure Data Box ve vaší oblasti dostupná. Zadejte nebo vyberte následující informace a pak vyberte **Použít**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Typ přenosu     | Vyberte **exportovat do Azure**.        |
    |Předplatné     | Vyberte pro službu Data Box předplatné se smlouvou Enterprise, předplatné CSP nebo předplatné Azure Sponsorship. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Skupina prostředků     |    Vyberte existující skupinu prostředků. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Zdrojová oblast Azure    |    Vyberte oblast Azure, ve které jsou vaše data aktuálně.         |
    |Cílová země     |     Vyberte zemi, ve které chcete zařízení dodávat.        |

   ![Vyberte nastavení Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Vyberte **Data Box**. Maximální použitelná kapacita pro jednu objednávku je 80 TB. V případě větších objemů dat můžete vytvořit více objednávek.

   ![Výběr kapacity Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. V poli **pořadí** zadejte **základní** podrobnosti o objednávce. Zadejte nebo vyberte následující informace a pak vyberte **Další**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné     | Předplatné se automaticky vyplní na základě vašeho předchozího výběru.|
    |Skupina prostředků | Skupina prostředků, kterou jste vybrali dříve. |
    |Název objednávky exportu     |  Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |

    ![Základy objednávky exportu](media/data-box-deploy-export-ordered/azure-data-box-export-order-storage-account-export-type.png)

    Vyberte **Další: výběr dat** pro pokračování.

7. V **výběru dat** vyberte **Přidat účet úložiště a typ exportu**.

    ![Přidat účet úložiště a typ exportu](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics.png)

8. V **možnosti vybrat Export** zadejte podrobnosti možnosti exportu. Zadejte nebo vyberte následující informace a vyberte **Přidat**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Účet úložiště     | Účet Azure Storage, ze kterého chcete exportovat data. |
    |Typ exportu     | Určuje typ dat, který se má exportovat ze **všech objektů** a **použít soubor XML**.<ul><li> **Všechny objekty** – určuje, že úloha Exportuje všechna data v závislosti na výběru **možností přenosu**.</li><li> **Použít soubor XML** – určuje soubor XML, který obsahuje sadu cest a předpon pro objekty BLOB a soubory, které mají být exportovány z účtu úložiště. Soubor XML musí být v kontejneru vybraného účtu úložiště a výběr ze sdílených složek se v současné době nepodporuje. Soubor musí být neprázdný soubor. XML.</li></ul>        |
    |Možnosti přenosu     |  Určuje možnosti přenosu dat z možností **Vybrat vše** , **všechny objekty blob** a **všechny soubory**. <ul><li> **Vybrat vše** – určuje, že se exportují všechny objekty BLOB a soubory Azure. Pokud používáte účet úložiště, který podporuje jenom objekty BLOB (Blob Storage účet), možnost **všechny soubory** nebude možné vybrat.</li><li> **Všechny objekty blob** – určuje, že se exportují jenom objekty blob bloku a stránky.</li><li> **Všechny soubory** – určuje, že všechny soubory jsou exportovány s výjimkou objektů BLOB. Typ účtu úložiště, který máte (GPv1 a GPv2, Premium Storage nebo BLOB Storage), určuje typy dat, která můžete exportovat. Další informace najdete v tématu [podporované účty úložiště pro export](../storage/common/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Zahrnout podrobný protokol     | Určuje, zda má být podrobný soubor protokolu, který obsahuje seznam všech souborů, které byly exportovány úspěšně.        |

    > [!NOTE]
    >
    > Pokud vyberete možnost **použít soubor XML** pro nastavení **typ exportu** , je nutné zkontrolovat, zda XML obsahuje platné cesty a předpony. Je nutné sestavit a dodat soubor XML.  Pokud je soubor neplatný nebo žádná data neodpovídají zadaným cestám, pořadí se ukončí s částečnými daty nebo se žádná data neexportují.

    Informace o tom, jak přidat soubor XML do kontejneru, naleznete v tématu [Export ORDER by using XML File](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Vybrat možnost exportu](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Příklad vstupu XML naleznete v tématu [Sample XML Input](data-box-deploy-export-ordered.md#sample-xml-file) .

9. V poli **Výběr dat** zkontrolujte nastavení a vyberte **Další:>zabezpečení**.

   ![Objednávka exportu, výběr dat](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

10. Pokud chcete povolit šifrování na základě softwaru, vyberte v části **zabezpečení** možnost **Povolit pro objednávku dvojité šifrování**. 

    Šifrování založené na softwaru se provádí kromě šifrování AES-256 dat na Data Box.

   
    > [!NOTE]
    > Povolení této možnosti může způsobit, že zpracování objednávek a kopírování dat trvá déle. Po vytvoření objednávky tuto možnost nemůžete změnit.

    ![Obrazovka zabezpečení pro import datového boxu, dvojité šifrování](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Chcete-li pokračovat, vyberte možnost **Další: kontaktní údaje** .

11. V **podrobnostech kontaktu** vyberte **+ Přidat dodací adresu** a zadejte informace o expedici.

    ![Přidat dodací adresu](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. Do pole **Přidat adresu příjemce** zadejte své křestní jméno a příjmení, jméno a poštovní adresu společnosti a platné telefonní číslo. Vyberte **Ověřit**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení.

    ![Ověřit dodací adresu](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Pokud se rozhodnete v oblasti, kde je k dispozici samoobslužné odeslání, můžete tuto možnost vybrat. Další informace o samoobslužném expedici najdete v tématu [použití samostatně spravovaného expedice](data-box-portal-customer-managed-shipping.md).

13. Po úspěšném ověření podrobností o expedici vyberte **Přidat dodací adresu** .

14. V **kontaktní údaje** zkontrolujte svou dodací adresu a e-mailovou adresu. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

    ![Podrobnosti o kontaktu](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Vyberte **Další: zkontrolovat a objednat>**. Aby bylo možné pokračovat v vytváření objednávky, je nutné přijmout podmínky a ujednání.

16. Vyberte **Objednat**. Vytvoření objednávky trvá několik minut.

    ![Pořadí potvrzení](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Export objednávky pomocí souboru XML

Pokud vyberete možnost **použít soubor XML** , můžete zadat konkrétní kontejnery a objekty BLOB (stránky a blok), které chcete exportovat. Pro formátování XML bude nutné postupovat podle pokynů v dokumentaci k [ukázkové tabulce souborů XML](#sample-xml-file) . Následující postup ukazuje, jak použít soubor XML pro export dat:

1. Jako **typ exportu** vyberte **použít soubor XML**. Jedná se o soubor XML, který určuje konkrétní objekty BLOB a soubory Azure, které chcete exportovat. Chcete-li přidat soubor XML, vyberte **kliknutím sem vyberte soubor XML**.

     ![Výběr možnosti exportu, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Vyberte **+ kontejner** pro vytvoření kontejneru.

    ![Výběr možnosti exportu, kontejnerů](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Na kartě **Nový kontejner** , která se odhlásí od pravé strany Azure Portal přidejte název kontejneru. Název musí být malými písmeny a může obsahovat číslice a pomlčky (-). Pak v rozevíracím seznamu vyberte **úroveň veřejného přístupu** . Doporučujeme vybrat možnost **privátní (neanonymní přístup)** a zabránit tak ostatním uživatelům v přístupu k vašim datům. Další informace o úrovních přístupu kontejneru najdete v tématu [oprávnění k přístupu kontejnerů](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Výběr možnosti exportu, nastavení nového kontejneru](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Vyberte **Vytvořit**.

   ![Vyberte možnost exportovat, vytvořit nový kontejner.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Pokud se kontejner úspěšně vytvoří, zobrazí se tato zpráva:

   ![Kontejner se úspěšně vytvořil.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Vyberte kontejner, který jste vytvořili, a dvakrát na něj klikněte.

   ![Zobrazit podrobnosti kontejneru](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Dvojím kliknutím na kontejner se zobrazí zobrazení vlastností kontejneru. Nyní chcete připojit (nebo vyhledat) soubor XML, který obsahuje seznam objektů BLOB nebo souborů Azure, které chcete exportovat. Vyberte **Nahrát**.

   ![Nahrát objekt blob do kontejneru](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Úspěšně jste přidali soubor XML do kontejneru. Budou exportovány pouze objekty BLOB a soubory Azure zadané v tomto souboru XML.

   ![Soubor XML přidaný do kontejneru](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejít na Data Box objednávku a pak přejít na **Přehled** a zobrazit stav. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Až se dokončí příprava zařízení, zahájí se kopírování dat z vybraných účtů úložiště. Na portálu se zobrazuje pořadí probíhajících **kopírování dat** .

![Data Box objednávka exportu, probíhá kopírování dat](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box kopíruje data ze zdrojových účtů úložiště. Jakmile je kopie dat dokončena, Data Box je uzamčena a na portálu se zobrazí pořadí ve stavu **dokončeno kopírování** .

![Data Box objednávka exportu, dokončení kopírování dat](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Pokud zařízení není k dispozici, zobrazí se oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Během přípravy zařízení dojde k následujícím akcím:

* Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
* Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
* Zařízení je uzamčené a dá se k němu dostat jenom pomocí hesla k odemknutí zařízení. Pokud chcete načíst heslo, musíte se přihlásit ke svému účtu Azure Portal a vybrat **Podrobnosti o zařízení**.

Společnost Microsoft potom připraví a odesílá vaše zařízení prostřednictvím oblastního dopravce. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Data Box objednávka exportu byla odeslána.](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Pokud jste vybrali samoobslužné odeslání, obdržíte e-mailové oznámení s dalšími kroky, až bude zařízení připravené na jeho výběr z datového centra. Další informace týkající se samoobslužného expedice najdete v tématu věnovaném [samoobslužné expedici](data-box-portal-customer-managed-shipping.md).

![Příprava na samoobslužnou správu připravená na vyzvednutí](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Zrušení objednávky

Tuto objednávku zrušíte tak, že v Azure Portal přejdete na **Přehled** a na panelu příkazů vyberete **Zrušit** .

Po navýšení objednávky ji můžete zrušit v jakémkoli bodě před zahájením zpracování pořadí.

Zrušení objednávky odstraníte tak, že přejdete na **Přehled** a na panelu příkazů vyberete **Odstranit** .

## <a name="sample-xml-file"></a>Ukázkový soubor XML

Následující kód XML ukazuje příklad názvů objektů blob, předpon objektů BLOB a souborů Azure obsažených ve formátu XML, který objednávka exportu používá, když vyberete možnost **použít soubor XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Mezi důležité body v souvislosti se soubory XML:

* Značky XML rozlišují velká a malá písmena a musí odpovídat přesně tak, jak je uvedeno v předchozí ukázce.
* Otevírají a uzavírající značky se musí shodovat.
* Nesprávná značka XML nebo formátování mohou vést k chybě exportu dat.
* Žádná data nebudou exportována, pokud jsou neplatné objekty blob nebo předpony souborů.

### <a name="examples-of-valid-blob-paths"></a>Příklady platných cest objektů BLOB

V následující tabulce jsou uvedeny příklady platných cest objektů BLOB:

   | Volič | Cesta objektu BLOB | Popis |
   | --- | --- | --- |
   | Začíná na |/ |Exportuje všechny objekty BLOB v účtu úložiště. |
   | Začíná na |/$root/ |Exportuje všechny objekty BLOB v kořenovém kontejneru. |
   | Začíná na |/containers |Exportuje všechny objekty BLOB v jakémkoli kontejneru, který začíná předponami **kontejnerů** . |
   | Začíná na |/container-name/ |Exportuje všechny objekty BLOB v kontejneru kontejneru **– název** |
   | Začíná na |/container-name/prefix |Exportuje všechny objekty BLOB v **kontejneru kontejneru – název** , který začíná **předponou** předpony. |
   | Je rovno |$root/logo.bmp |Export objektu BLOB **logo.bmp** do kořenového kontejneru |
   | Je rovno |8tbpageblob/mydata.txt |Export **mydata.txt** objektů BLOB v kontejneru **8tbpageblob** |

## <a name="sample-log-files"></a>Ukázkové soubory protokolů

Tato část poskytuje ukázkové soubory protokolů, které jsou generovány během exportu. Protokoly chyb se generují automaticky. Chcete-li vygenerovat podrobný soubor protokolu, je nutné při konfiguraci objednávky exportu vybrat možnost **zahrnout podrobný protokol** do Azure Portal.
Další informace o kopírování a podrobných protokolech najdete v tématu [kopírování protokolů](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
>
> * Předpoklady pro export
> * Objednat Data Box pro export
> * Sledovat objednávku exportu
> * Zrušit export objednávky

V dalším kurzu se dozvíte, jak Data Box nastavit.

> [!div class="nextstepaction"]
> [Nastavení Azure Data Boxu](./data-box-deploy-set-up.md)