---
title: Řešení potíží s Azure Data Box pro používání rozhraní REST| Dokumenty společnosti Microsoft
description: Popisuje, jak řešit problémy, které se zobrazí v Azure Data Box, když je kopírování dat prostřednictvím rozhraní REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297131"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Řešení problémů souvisejících s úložištěm objektů blob datové schránky Azure

Tento článek podrobně popisuje informace o tom, jak řešit problémy, které se můžou zobrazit při použití úložiště objektů blob datové schránky prostřednictvím rozhraní REST v datové schránce ke kopírování dat. Tyto problémy se objevují, když používáte úložiště objektů blob datové schránky s jinými aplikacemi nebo klientskými knihovnami, jako je Průzkumník úložiště Azure, AzCopy nebo knihovna Úložiště Azure pro Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Chyby, které se zořštili v Průzkumníku úložiště Azure

Tato část podrobně popisuje některé problémy, kterým čelíte při používání Průzkumníka úložiště Azure s úložištěm objektů blob datové schránky.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Nelze načíst podřízené prostředky. Hodnota jednoho ze záhlaví protokolu HTTP není ve správném formátu.|V nabídce **Úpravy** vyberte **Cílová virtuální tlačítka API zásobníku Azure**. <br>Restartujte Průzkumníka úložiště Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Zkontrolujte, zda `<accountname>.blob.<serialnumber>.microsoftdatabox.com` je název koncového bodu přidán do souboru hosts na této cestě: <li>`C:\Windows\System32\drivers\etc\hosts`ve Windows nebo </li><li> `/etc/hosts`na Linuxu.</li>|
|Nelze načíst podřízené prostředky. <br>Podrobnosti: certifikát podepsaný svým držitelem |Importujte certifikát TLS/SSL pro vaše zařízení do Průzkumníka úložiště Azure: <li>Stáhněte si certifikát z webu Azure Portal. Další informace naleznete v části [Stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>V nabídce **Úpravy** vyberte **Certifikáty SSL** a pak **importujte certifikáty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Chyby zjištěné v AzCopy pro Windows

Tato část podrobně popisuje některé problémy, kterým čelíte při používání AzCopy pro Windows s úložištěm objektů blob datové schránky.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Před zobrazením této chyby se zobrazí příkaz AzCopy, který zablokuje se po dobu jedné minuty: <br>Nepodařilo se vytvořit výčet adresáře https://... Vzdálený název nelze přeložit.`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Zkontrolujte, zda `<accountname>.blob.<serialnumber>.microsoftdatabox.com` je název koncového bodu `C:\Windows\System32\drivers\etc\hosts`přidán do souboru hosts na adrese: .|
|Před zobrazením této chyby se zobrazí příkaz AzCopy, který zablokuje se po dobu jedné minuty: <br>Při analýzě zdrojového umístění došlo k chybě. Základní připojení bylo uzavřeno: Nelze navázat vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.|Importujte certifikát TLS/SSL pro vaše zařízení do úložiště certifikátů systému. Další informace naleznete v části [Stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Chyby vidět v AzCopy pro Linux

Tato část podrobně popisuje některé problémy, kterým čelíte při používání AzCopy pro Linux s úložištěm blob datové schránky.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Před zobrazením této chyby se zobrazí příkaz AzCopy, který přestane reagovat na 20 minut: <br>Při analýzě zdrojového umístění `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`došlo k chybě. Žádné odpovídající zařízení ani adresa|Zkontrolujte, zda `<accountname>.blob.<serialnumber>.microsoftdatabox.com` je název koncového bodu `/etc/hosts`přidán do souboru hosts na adrese: .|
|Před zobrazením této chyby se zobrazí příkaz AzCopy, který přestane reagovat na 20 minut: <br>Při analýzě zdrojového umístění došlo k chybě... Připojení SSL nelze navázat.|Importujte certifikát TLS/SSL pro vaše zařízení do úložiště certifikátů systému. Další informace naleznete v části [Stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Chyby, které se zoavky pro Python udály

Tato část podrobně popisuje některé z hlavních problémů, kterým čelí při nasazení disku datové schránky při použití klienta Linux pro kopírování dat.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Hodnota jednoho ze záhlaví protokolu HTTP není ve správném formátu. |Nainstalovaná verze knihovny úložiště Microsoft Azure pro Python není podporována datovým schránkem. Požadavky na úložiště objektů blob Azure Data Box pro podporované verze.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Před spuštěním Pythonu nastavte proměnnou prostředí REQUESTS_CA_BUNDLE na cestu souboru certifikátu TLS s kódováním Base64 (viz [stažení certifikátu).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Například:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativně přidejte certifikát do úložiště certifikátů systému a nastavte tuto proměnnou prostředí na cestu tohoto úložiště. <br> Například na Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Běžné chyby

Tyto chyby nejsou specifické pro žádnou aplikaci.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Časový limit připojení vypršel. |Přihlaste se k zařízení Data Box a zkontrolujte, zda je odemčené. Kdykoli se zařízení restartuje, zůstane zamčené, dokud se někdo nepřihlásí.|

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [požadavcích na systém úložiště datové schránky blob](data-box-system-requirements-rest.md).
