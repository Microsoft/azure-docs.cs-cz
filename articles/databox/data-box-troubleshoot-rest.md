---
title: Azure Data Box pro odstraňování potíží pomocí rozhraní REST | Dokumentace Microsoftu
description: Popisuje postup řešení potíží zobrazí v Azure Data Box, pokud se kopírování dat pomocí rozhraní REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: c5ceeb2e6419cab7945454087edd4c821db28343
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204223"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Řešit problémy spojené s úložiště objektů Blob v Azure Data Box

Tyto informace článku podrobnosti o tom, jak řešit problémy, se může zobrazit při použití úložiště objektů Blob pole Data prostřednictvím rozhraní REST pro vaše zařízení Data Box pro kopírování dat. Tyto problémy plochu při použití úložiště objektů Blob v poli Data s jinými aplikacemi nebo klientských knihoven, jako je například knihovna Průzkumníka služby Azure Storage a AzCopy, Azure Storage pro Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Chyby v Průzkumníku služby Storage

Tato část podrobně některých problémů, kterým čelí při použití Průzkumníka služby Azure Storage s úložištěm objektů Blob Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Nepovedlo se načíst podřízené prostředky. Hodnota pro jednu z hlaviček HTTP není ve správném formátu.|Z **upravit** nabídce vyberte možnost **cílové rozhraní API služby Azure Stack**. <br>Restartujte Průzkumníka služby Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Zkontrolujte, že název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se přidá do souboru hostitelů na tuto cestu: <li>`C:\Windows\System32\drivers\etc\hosts` na Windows nebo </li><li> `/etc/hosts` v Linuxu.</li>|
|Nepovedlo se načíst podřízené prostředky. <br>Podrobnosti: certifikát podepsaný svým držitelem |Importujte certifikát SSL pro vaše zařízení do Průzkumníka služby Azure Storage: <li>Stáhněte si certifikát z portálu Azure portal. Další informace najdete v části [stáhněte si certifikát](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Z **upravit** nabídce vyberte možnost **certifikáty SSL** a pak vyberte **importovat certifikáty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Chyby v AzCopy pro Windows

Tato část podrobně některých problémů, kterým čelí při použití nástroje AzCopy pro Windows s úložištěm objektů Blob Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Zablokování minutu, než se zobrazí tato chyba se zobrazí příkaz AzCopy: <br>Nepodařilo se vytvořit výčet adresáře https://... Vzdálený název nelze rozpoznat. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Zkontrolujte, že název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se přidá do souboru hostitelů na: `C:\Windows\System32\drivers\etc\hosts`.|
|Zablokování minutu, než se zobrazí tato chyba se zobrazí příkaz AzCopy: <br>Chyba parsování umístění zdroje. Nadřízené připojení bylo uzavřeno: Nepovedlo se navázat vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.|Naimportujte certifikát SSL pro vaše zařízení do úložiště certifikátů v systému. Další informace najdete v části [stáhněte si certifikát](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Chyby uvedené v AzCopy pro Linux

Tato část podrobně popisuje některé z problémů, kterým čelí při použití nástroje AzCopy pro Linux s úložištěm objektů Blob Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Zablokování 20 minut, než se zobrazí tato chyba se zobrazí příkaz AzCopy: <br>Chyba při analýze umístění zdroje `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Žádné odpovídající zařízení ani adresa|Zkontrolujte, že název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se přidá do souboru hostitelů na: `/etc/hosts`.|
|Zablokování 20 minut, než se zobrazí tato chyba se zobrazí příkaz AzCopy: <br>Chyba parsování zdrojové umístění... Nelze navázat připojení SSL.|Naimportujte certifikát SSL pro vaše zařízení do úložiště certifikátů v systému. Další informace najdete v části [stáhněte si certifikát](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Chyby uvedené v knihovně Azure Storage pro Python

Tato část podrobně popisuje některé z hlavních problémů při používání klienta Linux pro kopírování dat ve během nasazení disku Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Hodnota pro jednu z hlaviček HTTP není ve správném formátu. |Nainstalovaná verze knihovnu Microsoft Azure Storage pro Python nepodporuje zařízení Data Box. Viz požadavky na úložiště objektů Blob v Azure Data Box pro podporované verze.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Před spuštěním Pythonu, nastavte prostředí requests_ca_bundle na cestu k souboru certifikátu SSL s kódováním Base64 (viz postup [stáhněte si certifikát](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Příklad:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativně přidejte certifikát do úložiště certifikátů systému a potom nastavte tuto proměnnou prostředí k cestě daného úložiště. <br> Například na Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Běžné chyby

Tyto chyby se netýkají žádné aplikace.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Připojení vyprší časový limit. |Přihlaste se do zařízení Data Box a zkontrolujte, že je odemknuté. Pokaždé, když se zařízení restartuje, zůstane uzamčena, dokud se uživatel přihlásí.|

## <a name="next-steps"></a>Další postup

- Další informace o [požadavky na systém pole datový objekt Blob úložiště](data-box-system-requirements-rest.md).
