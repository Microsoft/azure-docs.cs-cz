---
title: 'Kurz: Kopírování do úložiště objektů Blob pomocí rest API'
titleSuffix: Azure Data Box
description: Zjistěte, jak zkopírovat data do úložiště objektů blob datové schránky Azure prostřednictvím api REST
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: alkohli
ms.openlocfilehash: 7642c009a5bcd1d00efb432975fff5a65c7ba340
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297202"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Kurz: Kopírování dat do úložiště objektů blob datové schránky Azure prostřednictvím api REST  

Tento kurz popisuje postupy pro připojení k úložišti objektů blob datové schránky Azure přes REST API přes *http* nebo *https*. Po připojení jsou také popsány kroky potřebné ke kopírování dat do úložiště blob datové schránky a přípravě datové schránky k odeslání.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k úložišti objektů blob datové schránky přes *http* nebo *https*
> * Kopírování dat do Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurz: Nastavení datové schránky Azure](data-box-deploy-set-up.md).
2. Obdrželi jste Data Box a stav objednávky na portálu je **Doručeno**.
3. Zkontrolovali jste [systémové požadavky pro úložiště objektů blob datové schránky](data-box-system-requirements-rest.md) a jste obeznámeni s podporovanými verzemi api, sad ek sad SDK a nástrojů.
4. Máte přístup k hostitelskému počítači, který má data, která chcete zkopírovat do datové schránky. Hostitelský počítač musí splňovat tyto požadavky:
    - Spusťte [podporovaný operační systém](data-box-system-requirements.md).
    - Musí být připojený k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud připojení 10 GbE není k dispozici, lze použít datové spojení 1 GbE, ale rychlost kopírování bude ovlivněna.
5. [Stáhněte si AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) na hostitelském počítači. AzCopy použijete ke kopírování dat do úložiště objektů blob datové schránky Azure z hostitelského počítače.


## <a name="connect-via-http-or-https"></a>Připojte se přes http nebo https

K úložišti objektů blob datové schránky se můžete připojit přes *http* nebo *https*.

- *Https* je bezpečný a doporučený způsob připojení k úložišti objektů blob datové schránky.
- *Http* se používá při připojování přes důvěryhodné sítě.

Postup připojení se liší, když se připojíte k úložišti objektů blob datové schránky přes *http* nebo *https*.

## <a name="connect-via-http"></a>Připojení přes http

Připojení k datová datová data blob úložiště REST API přes *http* vyžaduje následující kroky:

- Přidání koncového bodu služby IP a služby objektu blob do vzdáleného hostitele
- Konfigurace softwaru jiných výrobců a ověření připojení

Každý z těchto kroků je popsán v následujících částech.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Přidání ip adresy zařízení a koncového bodu služby objektů blob

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurace partnerského softwaru a ověření připojení

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Připojte se přes https

Připojení k úložišti objektů BLOB Azure REST API přes https vyžaduje následující kroky:

- Stažení certifikátu z webu Azure Portal
- Import certifikátu na klienta nebo vzdáleného hostitele
- Přidání koncového bodu služby IP a služby objektu BLOB do klienta nebo vzdáleného hostitele
- Konfigurace softwaru jiných výrobců a ověření připojení

Každý z těchto kroků je popsán v následujících částech.

### <a name="download-certificate"></a>Stáhnout certifikát

Ke stažení certifikátu použijte portál Azure.

1. Přihlaste se k portálu Azure Portal.
2. Přejděte do objednávky datové schránky a přejděte na **podrobnosti o obecné> zařízení**.
3. V části **Přihlašovací údaje zařízení**přejděte na přístup k zařízení rozhraní **API.** Klepněte na tlačítko **Stáhnout**. Tato akce stáhne ** \<název objednávky>.cer** soubor certifikátu. **Uložte** tento soubor. Tento certifikát nainstalujete do klientského nebo hostitelského počítače, který použijete k připojení k zařízení.

    ![Stažení certifikátu na webu Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Import certifikátu 

Přístup k úložišti objektů blob datové schránky přes protokol HTTPS vyžaduje pro zařízení certifikát TLS/SSL. Způsob, jakým je tento certifikát k dispozici klientské aplikaci, se liší v jednotlivých aplikacích a napříč operačními systémy a distribucemi. Některé aplikace mohou získat přístup k certifikátu po importu do úložiště certifikátů systému, zatímco jiné aplikace tento mechanismus nevyužívají.

Konkrétní informace pro některé aplikace jsou uvedeny v této části. Další informace o dalších aplikacích naleznete v dokumentaci k použité aplikaci a použitému operačnímu systému.

Následujícím postupem importujte `.cer` soubor do kořenového úložiště klienta Windows nebo Linux. V systému Windows můžete k importu a instalaci certifikátu do systému použít prostředí Windows PowerShell nebo Windows Server UI.

#### <a name="use-windows-powershell"></a>Práce s Windows PowerShellem

1. Spusťte relaci prostředí Windows PowerShell jako správce.
2. Na příkazovém řádku zadejte:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Použití uj.

1.   Klepněte pravým `.cer` tlačítkem myši na soubor a vyberte **možnost Instalovat certifikát**. Tato akce spustí Průvodce importem certifikátu.
2.   V **umístění obchodu**vyberte místní **počítač**a klepněte na tlačítko **Další**.

    ![Import certifikátu pomocí PowerShellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.   Vyberte **Umístit všechny certifikáty do následujícího obchodu**a klepněte na tlačítko **Procházet**. Přejděte do kořenového úložiště vzdáleného hostitele a klepněte na tlačítko **Další**.

    ![Import certifikátu pomocí PowerShellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.   Klikněte na **Finish** (Dokončit). Zobrazí se zpráva, která informuje o tom, že import byl úspěšný.

    ![Import certifikátu pomocí PowerShellu](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Použití systému Linux

Způsob importu certifikátu se liší podle distribuce.

Několik, jako je Ubuntu a `update-ca-certificates` Debian, používá příkaz.  

- Přejmenujte soubor certifikátu zakódovaný base64 tak, aby měl příponu, `.crt` a zkopírujte jej do souboru `/usr/local/share/ca-certificates directory`.
- Spusťte příkaz `update-ca-certificates`.

Příkaz používají `update-ca-trust` nejnovější verze RHEL, Fedory a CentOS.

- Zkopírujte soubor certifikátu do adresáře. `/etc/pki/ca-trust/source/anchors`
- Spusťte `update-ca-trust`.

Podrobnosti naleznete v dokumentaci specifické pro vaši distribuci.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Přidání ip adresy zařízení a koncového bodu služby objektů blob 

Stejným postupem [přidejte ip adresu zařízení a koncový bod služby blob při připojování přes *http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Konfigurace partnerského softwaru a ověření připojení

Postupujte podle pokynů ke [konfiguraci partnerského softwaru, který jste použili při připojování přes *protokol http*](#configure-partner-software-and-verify-connection). Jediným rozdílem je, že byste měli nechat *možnost Use http* nezaškrtnutou.

## <a name="copy-data-to-data-box"></a>Kopírování dat do Data Boxu

Jakmile jste připojeni k úložišti objektů blob datové schránky, dalším krokem je kopírování dat. Před kopírováním dat si přečtěte následující aspekty:

* Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení úložiště Azure a Data Boxu](data-box-limits.md).
* Pokud data, která jsou nahrávána datovou schránkou, jsou nahrávána jinými aplikacemi mimo Datovou schránku, může to vést k selhání úloh nahrávání a poškození dat.
* Ujistěte se, že udržujete kopii zdrojových dat, dokud nepotvrdíte, že datová schránka přenesla vaše data do Služby Azure Storage.

V tomto kurzu AzCopy slouží ke kopírování dat do úložiště objektů blob datové schránky. Ke kopírování dat můžete taky použít Azure Storage Explorer (pokud dáváte přednost nástroji založenému na grafickém uživatelském rozhraní) nebo partnerského softwaru.

Postup kopírování má následující kroky:

- Vytvoření kontejneru
- Nahrání obsahu složky do úložiště objektů blob datové schránky
- Nahrání upravených souborů do úložiště objektů blob datové schránky

Každý z těchto kroků je podrobně popsán v následujících částech.

### <a name="create-a-container"></a>Vytvoření kontejneru

Prvním krokem je vytvoření kontejneru, protože objekty BLOB jsou vždy odeslány do kontejneru. Kontejnery uspořádají skupiny objektů BLOB, jako je uspořádání souborů ve složkách v počítači. Podle těchto kroků vytvořte kontejner objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů blob.
3. Klepněte pravým tlačítkem myši na **objekt blob a**v místní nabídce vyberte **příkaz Vytvořit kontejner objektů blob**.

   ![Vytvoření kontextové nabídky kontejnerů objektů blob](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Pod složkou **Kontejnery objektů blob** se zobrazí textové pole. Zadejte název kontejneru objektů blob. Viz [Vytvoření kontejneru a nastavení oprávnění](../storage/blobs/storage-quickstart-blobs-dotnet.md) pro informace o pravidlech a omezení pojmenování kontejnerů objektů blob.
5. Stisknutím **klávesy Enter** po dokončení vytvořte kontejner objektů blob nebo **esc** zrušit. Po úspěšném vytvoření kontejneru objektů blob se zobrazí ve složce **Kontejnery objektů blob** pro vybraný účet úložiště.

   ![Kontejner objektů blob vytvořen](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Nahrání obsahu složky do úložiště objektů blob datové schránky

Pomocí AzCopy můžete nahrát všechny soubory ve složce do úložiště objektů Blob ve Windows nebo Linuxu. Pokud chcete nahrát všechny objekty blob ve složce, zadejte následující příkaz AzCopy:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Nahraďte `<key>` pomocí klíče účtu. Pokud chcete klíč svého účtu získat, přejděte na portál Azure na účet úložiště. Přejděte na **Nastavení > přístupové klávesy**, vyberte klávesu a vložte ji do příkazu AzCopy.

Pokud zadaný cílový kontejner neexistuje, AzCopy ho vytvoří a soubor do něj nahraje. Aktualizujte zdrojovou cestu do datového adresáře a v cílové adrese URL nahraďte `data-box-storage-account-name` název účtu úložiště přidruženého k datové schránce.

Pokud chcete obsah zadaného adresáře nahrát do úložiště objektů blob rekurzivně, zadejte možnost `--recursive` (Linux) nebo `/S` (Windows). Když spustíte AzCopy s některou z těchto možností, nahrají se také všechny podsložky a soubory, které obsahují.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Nahrání upravených souborů do úložiště objektů blob datové schránky

Pomocí aplikace AzCopy můžete nahrávat soubory na základě jejich času poslední změny. Pokud to chcete vyzkoušet, upravte nebo vytvořte nové soubory ve zdrojovém adresáři pro účely testování. Pokud chcete nahrát pouze aktualizované nebo nové soubory, přidejte do příkazu AzCopy parametr `--exclude-older` (Linux) nebo `/XO` (Windows).

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

Pokud během operace připojení nebo kopírování dojde k chybám, [přečtěte si článek Řešení problémů s úložištěm objektů blob datové schránky](data-box-troubleshoot-rest.md).

Dalším krokem je příprava zařízení k odeslání.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Připojení k úložišti objektů blob datové schránky přes *http* nebo *https*
> * Kopírování dat do Data Boxu


V dalším kurzu se dozvíte, jak Data Box odeslat zpět do Microsoftu.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu do Microsoftu](./data-box-deploy-picked-up.md)
