---
title: Kopírování dat do úložiště objektů Blob v Azure Data Box přes rozhraní REST API | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do úložiště objektů Blob v Azure Data Box přes rozhraní REST API
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 88880fbfe0f6020a0a982c42b6df0e9eb71b2fc9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550548"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Kurz: Kopírování dat do úložiště objektů Blob v Azure Data Box přes rozhraní REST API  

Tento kurz popisuje postupy pro připojení k úložišti objektů Blob v Azure Data Box přes rozhraní REST API přes *http* nebo *https*. Jakmile budete připojeni, kroky potřebné k zkopírovat data do úložiště objektů Blob pole Data a připravte zařízení Data Box k odeslání, jsou rovněž popsány.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k úložišti pole datový objekt Blob přes *http* nebo *https*
> * Kopírování dat do Data Boxu
> * Příprava k odeslání

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Nastavení Azure Data Box](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **dodáno**.
3. Můžete si [požadavky na systém pro úložiště objektů Blob Data Box](data-box-system-requirements-rest.md) a jste obeznámeni s podporovanou verzí rozhraní API, sady SDK a nástroje.
4. Máte přístup k hostitelskému počítači, který obsahuje data, který chcete zkopírovat do zařízení Data Box. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, je možné použít 1 GbE dat propojení ale rychlosti kopírování bude mít vliv.
5. [Stáhněte si nástroj AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) v hostitelském počítači. AzCopy budete používat ke kopírování dat do úložiště objektů Blob v Azure Data Box z hostitelského počítače.


## <a name="connect-to-data-box-blob-storage"></a>Připojení k úložišti objektů Blob Data Box

Můžete připojit k úložišti objektů Blob Data Box přes *http* nebo *https*. Obecně platí *https* je zabezpečené a doporučený způsob, jak připojit k úložišti objektů Blob Data Box. *Http* se používá při připojení přes důvěryhodné sítě. V závislosti na tom, jestli se připojujete k poli datový objekt Blob úložiště přes *http* nebo *https*, postup může být jiný.

## <a name="connect-via-http"></a>Připojení přes protokol http

Připojení k úložišti objektů Blob Data pole rozhraní REST API přes *http* vyžaduje následující kroky:

- Přidat IP adresu zařízení a na vzdáleného hostitele koncového bodu služby objektů blob
- Konfigurace softwaru třetích stran a ověření připojení

Každý z těchto kroků je popsán v následujících částech.

#### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Přidat IP adresu zařízení a na vzdáleného hostitele koncového bodu služby objektů blob

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

#### <a name="configure-partner-software-and-verify-connection"></a>Nakonfigurujte partnerský software a ověření připojení

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Připojení přes protokol https

Připojení k úložišti objektů Blob v Azure storage rozhraní REST API přes protokol https vyžaduje následující kroky:

- Stáhněte si certifikát z webu Azure portal
- Příprava hostitelském počítači pro vzdálenou správu
- Přidat IP adresu zařízení a na vzdáleného hostitele koncového bodu služby objektů blob
- Konfigurace softwaru třetích stran a ověření připojení

Každý z těchto kroků je popsán v následujících částech.

### <a name="download-certificate"></a>Stáhnout certifikát

Stáhněte si certifikát pomocí webu Azure portal.

1. Přihlaste se na webu Azure portal.
2. Přejděte na svoji objednávku Data Box a přejděte do **Obecné > Podrobnosti o zařízení**.
3. V části **přihlašovací údaje zařízení**, přejděte na stránku **přístup přes rozhraní API** do zařízení. Klikněte na **Stáhnout**. Touto akcí se stáhne  **<your order name>.cer** soubor certifikátu. **Uložit** tento soubor. Tento certifikát nainstaluje na klientský nebo hostitelský počítač, který budete používat pro připojení k zařízení.

    ![Stáhněte si certifikát na webu Azure portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="prepare-the-host-for-remote-management"></a>Příprava hostitelském pro vzdálenou správu

Následující postup použijte k připravit klienta Windows pro vzdálené připojení, které používá *https* relace:

- Importujte soubor .cer do kořenového úložiště klienta nebo na vzdáleného hostitele.
- Přidat IP adresu zařízení a objektů blob koncového bodu služby do souboru hostitelů na klientovi Windows.

Každá z předchozích postupů, je popsána níže.

#### <a name="import-the-certificate-on-the-remote-host"></a>Importujte certifikát na vzdáleném hostiteli

Prostředí Windows PowerShell nebo uživatelské rozhraní systému Windows Server můžete použít k importu a nainstalujte certifikát na hostitelském systému.

**Pomocí PowerShellu**

1. Spusťte relaci prostředí Windows PowerShell jako správce.
2. Na příkazovém řádku zadejte:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

**Pomocí uživatelského rozhraní systému Windows Server**

1.  Klikněte pravým tlačítkem na soubor .cer a vyberte **instalace certifikátu**. Otevře se Průvodce importem certifikátu.
2.  Pro **Store umístění**vyberte **místního počítače**a potom klikněte na tlačítko **Další**.

    ![Import certifikátu pomocí Powershellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Vyberte **všechny certifikáty umístit v následujícím úložišti**a potom klikněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a potom klikněte na tlačítko **Další**.

    ![Import certifikátu pomocí Powershellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Klikněte na **Dokončit**. Zobrazí se zpráva s oznámením, že import proběhl úspěšně.

    ![Import certifikátu pomocí Powershellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

### <a name="to-add-device-ip-address-and-blob-service-endpoint-to-the-remote-host"></a>Přidat IP adresu zařízení a na vzdáleného hostitele koncového bodu služby objektů blob

Jak postupovat, jsou shodné s co jste použili při připojování přes *http*.

### <a name="configure-partner-software-to-establish-connection"></a>Nakonfigurujte partnerský software k navázání připojení

Jak postupovat, jsou shodné s co jste použili při připojování přes *http*. Jediným rozdílem je, že byste měli nechat *možnost protokolu http použijte* nezaškrtnuté.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Po připojení k úložišti objektů Blob Data Box, dalším krokem je zkopírovat data. Před kopírování dat rozmyslete si následující věci:

-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení úložiště Azure a Data Boxu](data-box-limits.md).
- Pokud se data, která se nahrává Data Box, odešle současně jiné aplikace mimo zařízení Data Box, to může způsobit poškození odeslání úlohy selhání a data.

V tomto kurzu AzCopy slouží ke kopírování dat do úložiště objektů Blob Data Box. Ke zkopírování dat můžete také použít Průzkumníka služby Azure Storage (Pokud dáváte přednost nástroj založeným na GUI) nebo partnerský software.
Postup kopírování obsahuje následující kroky:

- Vytvoření kontejneru
- Nahrát obsah složky do úložiště objektů Blob Data Box
- Nahrání upravených souborů do úložiště objektů Blob Data Box

Každý z těchto kroků je podrobně popsány v následující části.

### <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty BLOB se vždy nahrávají do kontejneru. Kontejnery organizovat skupiny objektů BLOB podobně, jako organizujete soubory do složek ve vašem počítači. Postupujte podle těchto pokynů vytvořte kontejner objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů blob.
3. Klikněte pravým tlačítkem na **kontejnery objektů Blob**a v místní nabídce vyberte **vytvořit kontejner objektů Blob**.

   ![Vytvořit kontejnery místní nabídka objektu blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Textové pole se zobrazí pod **kontejnery objektů Blob** složky. Zadejte název kontejneru objektů blob. Zobrazit [vytvoření kontejneru a nastavení oprávnění](/articles/storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) informace o pravidlech a omezení pro pojmenování kontejnerů objektů blob.
5. Stisknutím klávesy **Enter** po dokončení vytvoření kontejneru objektů blob nebo **Esc** zrušit. Po úspěšném vytvoření kontejneru objektů blob, se zobrazí v části **kontejnery objektů Blob** složku pro vybraný účet úložiště.

   ![Vytvoření kontejneru objektů BLOB](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Nahrát obsah složky do úložiště objektů Blob Data Box

Pomocí nástroje AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů Blob ve Windows nebo Linuxu. Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Nahraďte `<key>` klíčem svého účtu. Klíč účtu na webu Azure Portal, přejděte do účtu úložiště. Přejděte na **Nastavení > přístupové klíče**, vyberte klíč a vložte ho do příkazu AzCopy.

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje. Aktualizuje zdrojovou cestu ke svému adresáři dat a nahraďte `data-box-storage-account-name` v cílové adresy URL s názvem účtu úložiště spojené s vašeho zařízení Data Box.

Pokud chcete obsah zadaného adresáře nahrát do úložiště objektů blob rekurzivně, zadejte možnost `--recursive` (Linux) nebo `/S` (Windows). Když spustíte AzCopy s některou z těchto možností, nahrají se také všechny podsložky a soubory, které obsahují.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Nahrání upravených souborů do úložiště objektů Blob Data Box

Pomocí AzCopy můžete nahrát soubory podle jejich čas poslední změny. Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Pokud chcete nahrát pouze aktualizované nebo nové soubory, přidejte do příkazu AzCopy parametr `--exclude-older` (Linux) nebo `/XO` (Windows).

Pokud chcete zkopírovat pouze zdrojové prostředky, které neexistují v cíli, zadejte v příkazu AzCopy parametry `--exclude-older` i `--exclude-newer` (Linux) nebo `/XO` i `/XN` (Windows). AzCopy nahraje pouze aktualizovaná data na základě jejich časového razítka.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO


## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Připojení k úložišti pole datový objekt Blob přes *http* nebo *https*
> * Kopírování dat do Data Boxu
> * Příprava k odeslání

Přejděte k dalšímu kurzu se naučíte k odeslání vašeho zařízení Data Box zpět společnosti Microsoft.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)