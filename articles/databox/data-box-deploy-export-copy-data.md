---
title: Kurz kopírování dat z Azure Data Boxu přes protokol SMB | Microsoft Docs
description: Zjistěte, jak zkopírovat data do Azure Data Boxu přes protokol SMB.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ecc6e1e1a543f3190e9f73512ca0b9ae45cc3fe9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335209"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Kurz: Kopírování dat z Azure Data Boxu přes protokol SMB (Preview)

Tento kurz popisuje, jak se pomocí místního webového uživatelského rozhraní připojit k Data Boxu a zkopírovat z něj data na místní server. Zařízení Data Box obsahuje data vyexportovaná z účtu Azure Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat z Data Boxu

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Zadali jste objednávku Azure Data Boxu.
    - V případě objednávky importu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-ordered.md).
    - V případě objednávky exportu si projděte [Kurz: Objednání Azure Data Boxu](data-box-deploy-export-ordered.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Máte hostitelský počítač, do kterého chcete zkopírovat data z Data Boxu. Hostitelský počítač musí splňovat tyto požadavky:
   * Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
   * Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud nemáte k dispozici připojení 10 GbE, použijte datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování.

## <a name="connect-to-data-box"></a>Připojení k Data Boxu

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Pokud používáte hostitelský počítač s Windows Serverem, připojte se k Data Boxu pomocí následujícího postupu.

1. Prvním krokem je ověření a zahájení relace. Přejděte do části **Připojit a kopírovat**. Výběrem **Získat přihlašovací údaje** získáte přihlašovací údaje pro přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště. 

    ![Získání přihlašovacích údajů sdílené složky](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. V dialogovém okně Přístup ke sdílené složce a kopírování dat zkopírujte **Uživatelské jméno** a **Heslo** odpovídající sdílené složce. Vyberte **OK**.
    
    ![Získání přihlašovacích údajů sdílené složky, přístup ke sdílené složce a kopírování dat](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Pokud chcete získat přístup ke sdíleným složkám přidruženým k vašemu účtu úložiště ( *exportbvtdataset2* v následujícím příkladu) z hostitelského počítače, otevřete okno příkazového řádku. Na příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    V závislosti na formátu vašich dat budou sdílené cesty vypadat podobně jako níže:
    - Objekt blob bloku Azure – `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Objekt blob stránky Azure – `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Soubory Azure – `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Po zobrazení výzvy zadejte heslo ke sdílené složce. Následující příklad ukazuje připojení ke sdílené složce pomocí předchozího příkazu.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Stiskněte Windows + R. V okně **Spustit** zadejte `\\<device IP address>`. Výběrem **OK** otevřete Průzkumníka souborů.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů, zadání IP adresy zařízení](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Sdílené složky by se teď měly zobrazit jako složky.
    
    ![Připojení ke sdílené složce přes Průzkumníka souborů, zobrazení sdílených složek](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Pokud používáte klienta Linuxu, připojte sdílenou složku SMB pomocí následujícího příkazu. Níže uvedený parametr vers představuje verzi protokolu SMB, kterou podporuje váš hostitel s Linuxem. Do následujícího příkazu vložte odpovídající verzi. Verze protokolu SMB, které Data Box podporuje, najdete v tématu věnovaném [podporovaným systémům souborů pro klienty Linuxu](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients). 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Kopírování dat z Data Boxu

Po připojení ke sdíleným složkám Data Boxu je dalším krokem zkopírování dat.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Po připojení ke sdílené složce SMB zahajte kopírování dat. Ke kopírování dat můžete použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí nástroje Robocopy je možné zahájit několik úloh kopírování najednou. 


Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

Po dokončení kopírování přejděte k **řídicímu panelu** a zkontrolujte využité a volné místo na zařízení.

Teď můžete přistoupit k odeslání Data Boxu do Microsoftu.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k Data Boxu
> * Kopírování dat z Data Boxu

V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-export-picked-up.md)