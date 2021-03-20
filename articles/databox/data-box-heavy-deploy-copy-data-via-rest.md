---
title: 'Kurz: kopírování dat do Azure Data Box BLOB Storage přes rozhraní REST API'
description: V tomto kurzu se naučíte připojit se k Azure Data Box BLOB Storage pomocí rozhraní REST API přes protokol HTTP nebo https a pak kopírovat data z Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: e2fc174147b99b7b952c6d10084cfc969dacf5a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949135"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Kurz: kopírování dat do Azure Data Box BLOB Storage přes rozhraní REST API  

Tento kurz popisuje postupy pro připojení k Azure Data Box BLOB Storage prostřednictvím rozhraní REST API přes *protokol HTTP* nebo *https*. Po připojení jsou popsány kroky potřebné ke zkopírování dat do úložiště objektů BLOB Data Box.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Připojení k Data Box BLOB Storage přes *http* nebo *https*
> * Kopírování dat do Data Boxu Heavy

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Nastavení Azure Data Boxu Heavy](data-box-heavy-deploy-set-up.md).
2. Obdrželi jste Data Box Heavy a stav objednávky na portálu je **Doručeno**.
3. Zkontrolovali jste [požadavky na systém pro úložiště objektů Blob data box](data-box-system-requirements-rest.md) a znáte podporované verze rozhraní API, sad SDK a nástrojů.
4. Máte přístup k hostitelskému počítači, který obsahuje data, která chcete zkopírovat do Data Box Heavy. Hostitelský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Největší rychlosti kopírování je možné dosáhnout použitím dvou paralelních připojení 40 GbE (jedno na uzel). Pokud nemáte k dispozici připojení 40 GbE, doporučujeme použít alespoň dvě připojení 10 GbE (jedno na uzel). 
5. [Stáhněte si AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) na hostitelském počítači. Pomocí AzCopy se zkopírují data do Azure Data Box úložiště objektů BLOB z hostitelského počítače.


## <a name="connect-via-http-or-https"></a>Připojení přes HTTP nebo https

Pomocí *protokolu HTTP* nebo *https* se můžete připojit k data box úložišti objektů BLOB.

- *Protokol HTTPS* je zabezpečený a doporučený způsob, jak se připojit k data box úložišti objektů BLOB.
- *Protokol HTTP* se používá při připojování přes důvěryhodné sítě.

Postup připojení se liší v případě, že se připojíte k Data Box úložišti objektů BLOB pomocí *protokolu HTTP* nebo *https*.

## <a name="connect-via-http"></a>Připojit přes http

Připojení k Data Box rozhraní REST API pro úložiště objektů BLOB přes *protokol HTTP* vyžaduje následující kroky:

- Přidání koncového bodu služby IP zařízení a služby BLOB Service ke vzdálenému hostiteli
- Konfigurace softwaru třetí strany a ověření připojení

Každý z těchto kroků je popsaný v následujících částech.

> [!IMPORTANT]
> Pro Data Box Heavy budete muset opakovat všechny pokyny k připojení, abyste se připojili k druhému uzlu.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Přidat IP adresu zařízení a koncový bod služby BLOB Service

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Konfigurace partnerského softwaru a ověření připojení

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Připojit prostřednictvím protokolu https

Připojení k rozhraní REST API služby Azure Blob Storage přes HTTPS vyžaduje následující kroky:

- Stáhnout certifikát z Azure Portal
- Import certifikátu na klienta nebo vzdáleném hostiteli
- Přidání koncového bodu služby IP zařízení a služby BLOB Service do klienta nebo vzdáleného hostitele
- Konfigurace softwaru třetí strany a ověření připojení

Každý z těchto kroků je popsaný v následujících částech.

> [!IMPORTANT]
> Pro Data Box Heavy budete muset opakovat všechny pokyny k připojení, abyste se připojili k druhému uzlu.

### <a name="download-certificate"></a>Stáhnout certifikát

K stažení certifikátu použijte Azure Portal.

1. Přihlaste se k portálu Azure Portal.
2. Přejděte do objednávky Data Box a přejděte na **obecné > informace o zařízení**.
3. V části **přihlašovací údaje pro zařízení** přejděte na **rozhraní API přístup** k zařízení. Klikněte na **Stáhnout**. Tato akce stáhne soubor certifikátu **\<your order name> . cer** . **Uložte** tento soubor. Tento certifikát nainstalujete na klienta nebo na hostitelský počítač, který budete používat pro připojení k zařízení.

    ![Stáhnout certifikát v Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Import certifikátu 

Přístup k Data Box BLOB Storage přes HTTPS vyžaduje pro zařízení certifikát TLS/SSL. Způsob, jakým je tento certifikát dostupný pro klientskou aplikaci, se liší od aplikace až po aplikace a pro různé operační systémy a distribuce. Některé aplikace mohou získat přístup k certifikátu po jeho importu do úložiště certifikátů systému, zatímco jiné aplikace tento mechanismus nevyužívají.

Konkrétní informace pro některé aplikace jsou uvedené v této části. Další informace o dalších aplikacích naleznete v dokumentaci k aplikaci a používanému operačnímu systému.

Pomocí následujícího postupu naimportujte `.cer` soubor do kořenového úložiště klienta se systémem Windows nebo Linux. V systému Windows můžete k importu a instalaci certifikátu do systému použít Windows PowerShell nebo uživatelské rozhraní Windows serveru.

#### <a name="use-windows-powershell"></a>Práce s Windows PowerShellem

1. Spusťte relaci Windows PowerShellu jako správce.
2. Na příkazovém řádku zadejte:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Použít uživatelské rozhraní systému Windows Server

1.  Klikněte na soubor pravým tlačítkem `.cer` a vyberte **nainstalovat certifikát**. Tato akce spustí Průvodce importem certifikátu.
2.  V poli **umístění úložiště** vyberte **místní počítač** a pak klikněte na **Další**.

    ![Import certifikátu pomocí PowerShellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Vyberte možnost **umístit všechny certifikáty do následujícího úložiště** a pak klikněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a klikněte na **Další**.

    ![Import certifikátu pomocí PowerShellu 2](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Klikněte na **Finish** (Dokončit). Zobrazí se zpráva oznamující, že import proběhl úspěšně.

    ![Import certifikátu pomocí PowerShellu 3](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Použití systému Linux

Způsob importu certifikátu se liší podle distribuce.

> [!IMPORTANT]
> Pro Data Box Heavy budete muset opakovat všechny pokyny k připojení, abyste se připojili k druhému uzlu.

Několik, například Ubuntu a Debian, použijte `update-ca-certificates` příkaz.  

- Přejmenujte soubor certifikátu s kódováním base64 na `.crt` rozšíření a zkopírujte jej do souboru `/usr/local/share/ca-certificates directory` .
- Spusťte příkaz `update-ca-certificates`.

Poslední verze RHEL, Fedora a CentOS používají `update-ca-trust` příkaz.

- Zkopírujte soubor certifikátu do `/etc/pki/ca-trust/source/anchors` adresáře.
- Spusťte `update-ca-trust`.

Podrobnosti najdete v dokumentaci specifické pro vaši distribuci.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Přidat IP adresu zařízení a koncový bod služby BLOB Service 

[Při připojování přes *protokol HTTP* použijte stejný postup při přidávání IP adresy zařízení a koncového bodu služby BLOB Service](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurace partnerského softwaru a ověření připojení

Postupujte podle pokynů ke [konfiguraci partnerského softwaru, který jste použili při připojování přes *protokol HTTP*](#configure-partner-software-and-verify-connection). Jediným rozdílem je, že byste měli nechat *možnost použít protokol HTTP* nezaškrtnutou.

## <a name="copy-data-to-data-box-heavy"></a>Kopírování dat do Data Boxu Heavy

Až budete připojeni k úložišti objektů BLOB Data Box, je dalším krokem kopírování dat. Před kopírováním dat si přečtěte následující skutečnosti:

-  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v [úložišti Azure a omezeních data box Heavy](data-box-limits.md).
- Pokud jsou data odesílaná pomocí Data Box Heavy souběžně odeslána jinými aplikacemi mimo Data Box Heavy, může to vést k selhání úlohy a poškození dat.

V tomto kurzu se AzCopy používá ke kopírování dat do úložiště objektů BLOB Data Box. Můžete také použít Průzkumník služby Azure Storage (Pokud dáváte přednost nástroji založenému na grafickém uživatelském rozhraní) nebo partnerskému softwaru ke zkopírování dat.

Postup kopírování má následující kroky:

- Vytvoření kontejneru
- Nahrání obsahu složky do Data Boxho úložiště objektů BLOB
- Nahrání upravených souborů do Data Box BLOB Storage


Každý z těchto kroků je podrobně popsán v následujících částech.

> [!IMPORTANT]
> Pro Data Box Heavy budete muset opakovat všechny pokyny pro kopírování a kopírovat data do druhého uzlu.

### <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty blob jsou vždycky nahrány do kontejneru. Kontejnery organizují skupiny objektů blob, stejně jako organizujete soubory ve složkách v počítači. Pomocí těchto kroků můžete vytvořit kontejner objektů BLOB.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů BLOB.
3. Klikněte pravým tlačítkem na **kontejnery objektů BLOB** a v místní nabídce vyberte **vytvořit kontejner objektů BLOB**.

   ![Místní nabídka pro vytvoření kontejnerů objektů BLOB](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Pod složkou **kontejnerů objektů BLOB** se zobrazí textové pole. Zadejte název kontejneru objektů blob. Podívejte se na téma [vytvoření kontejneru a nastavení oprávnění](../storage/blobs/storage-quickstart-blobs-dotnet.md) pro informace o pravidlech a omezeních pro pojmenování kontejnerů objektů BLOB.
5. Pokud chcete vytvořit kontejner objektů blob, stiskněte klávesu **ENTER** , nebo operaci zrušte stisknutím klávesy **ESC** . Po úspěšném vytvoření kontejneru objektů BLOB se zobrazí ve složce **kontejnery objektů BLOB** pro vybraný účet úložiště.

   ![Kontejner objektů BLOB byl vytvořen.](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Nahrání obsahu složky do Data Boxho úložiště objektů BLOB

Pomocí AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů BLOB v systému Windows nebo Linux. Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

Nahraďte `<key>` klíčem účtu. Klíč účtu získáte tak, že v Azure Portal přejdete do svého účtu úložiště. Přejděte na **nastavení > přístupové klíče**, vyberte klíč a vložte ho do příkazu AzCopy.

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje. Aktualizujte zdrojovou cestu k adresáři dat a `data-box-storage-account-name` v cílové adrese URL nahraďte názvem účtu úložiště, který je přidružený k vašemu data box.

Pokud chcete obsah zadaného adresáře nahrát do úložiště objektů blob rekurzivně, zadejte možnost `--recursive` (Linux) nebo `/S` (Windows). Když spustíte AzCopy s některou z těchto možností, nahrají se také všechny podsložky a soubory, které obsahují.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Nahrání upravených souborů do Data Box BLOB Storage

Pomocí AzCopy můžete nahrávat soubory na základě času poslední změny. Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Pokud chcete nahrát pouze aktualizované nebo nové soubory, přidejte do příkazu AzCopy parametr `--exclude-older` (Linux) nebo `/XO` (Windows).

Pokud chcete zkopírovat pouze zdrojové prostředky, které neexistují v cíli, zadejte v příkazu AzCopy parametry `--exclude-older` i `--exclude-newer` (Linux) nebo `/XO` i `/XN` (Windows). AzCopy nahraje pouze aktualizovaná data na základě jejich časového razítka.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Pokud během operace připojení nebo kopírování dojde k nějakým chybám, přečtěte si téma [řešení potíží s data box BLOB Storage](data-box-troubleshoot-rest.md).

Dalším krokem je příprava zařízení k odeslání.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Předpoklady
> * Připojení k Data Box BLOB Storage přes *http* nebo *https*
> * Kopírování dat do Data Boxu Heavy


V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu heavy do Microsoftu](./data-box-heavy-deploy-picked-up.md)
