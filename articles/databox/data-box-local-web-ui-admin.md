---
title: Správa místního webového uživatelského rozhraní Microsoft Azure Data Box | Microsoft Docs v datech
description: Popisuje, jak používat místní webové uživatelské rozhraní při správě zařízení Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: 6df44afee26570dbdc5d64b449c4e33d6a0956ae
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885706"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Použití místního webového uživatelského rozhraní při správě Data Boxu

Článek popisuje část konfigurace a úlohy správy, které se dají provádět v Data Boxu. Data Box můžete spravovat přes uživatelské rozhraní webu Azure Portal a místní webové uživatelské rozhraní pro zařízení. Tento článek se zaměřuje na úlohy, které můžete provádět pomocí místního webového uživatelského rozhraní.

Místní webové uživatelské rozhraní pro Data Box se používá pro počáteční konfiguraci zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování Data Boxu, spuštění diagnostických testů, aktualizaci softwaru, zobrazení protokolů kopírování a generování balíčku protokolů pro podporu Microsoftu.

Tento článek obsahuje následující návody:

- Generování balíčku pro podporu
- Vypnutí nebo restartování zařízení
- Zobrazení dostupné kapacity zařízení
- Přeskočení ověření kontrolního součtu 

## <a name="generate-support-package"></a>Generování balíčku pro podporu

Pokud budete mít se zařízením jakékoliv problémy, můžete vytvořit ze systémových protokolů balíček pro podporu. Podpora Microsoftu používá tento balíček k řešení příslušných potíží. Pokud chcete vygenerovat balíček pro podporu, postupujte takto:

1. V místním webovém uživatelském rozhraní přejděte na **Kontaktovat podporu** a klikněte na **Vytvořit balíček pro podporu**.

    ![Vytvoření balíčku pro podporu 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Shromáždí se položky balíčku pro podporu. Tato operace trvá několik minut.

    ![Vytvoření balíčku pro podporu 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Po dokončení vytváření balíčku pro podporu klikněte na **Stáhnout balíček pro podporu**. 

    ![Vytvoření balíčku pro podporu 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Přejděte k umístění pro stahování a vyberte ho. Otevřete složku a zobrazte obsah.

    ![Vytvoření balíčku pro podporu 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Vypnutí nebo restartování zařízení

Data Box můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme před restartováním přepnout sdílené složky na hostiteli a potom na zařízení do offline režimu. Minimalizuje se tak možnost poškození dat. Při vypnutí zařízení zkontrolujte, že se nekopírují data.

Při vypnutí Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Klikněte na **Vypnout**.

    ![Vypnutí Data Boxu 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

    ![Vypnutí Data Boxu 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Zařízení po vypnutí zapněte tlačítkem napájení na předním panelu.

Při restartování Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Klikněte na **Restartovat**.

    ![Restartování Data Boxu 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

   Zařízení se vypne a restartuje.

## <a name="download-bom-or-manifest-files"></a>Stáhněte si BOM nebo soubory manifestu

Bill materiálu (Kusovníku) nebo soubory manifestu obsahují seznam souborů, které se zkopírují do zařízení Data Box. Tyto soubory jsou generovány při přípravě zařízení Data Box k odeslání.

Než začnete, ujistěte se, že vaše zařízení Data Box byla dokončena **přípravu k odeslání** kroku. Následujícím postupem stáhnout BOM nebo soubory manifestu:

1. Přejděte do místního webového uživatelského rozhraní pro vaše zařízení Data Box. Zobrazí se, že zařízení Data Box byla dokončena příprava k odeslání. Když se dokončí Příprava zařízení, stavu zařízení se zobrazí jako **připravené k odeslání**.

    ![Zařízení je připravené k odeslání](media/data-box-portal-admin/ready-to-ship.png)

2. Klikněte na tlačítko **seznamu pro stahování souborů** stáhnout seznam souborů, které byly zkopírovány na vaše zařízení Data Box.

    ![Klikněte na tlačítko Stáhnout seznam souborů](media/data-box-portal-admin/download-list-of-files.png)

3. V Průzkumníku souborů, zobrazí se tento samostatný seznam souborů se generují v závislosti na protokol použitý pro připojení k zařízení a používá typ úložiště Azure.

    ![Soubory protokolu úložiště typu a připojení](media/data-box-portal-admin/files-storage-connection-type.png)

   Následující tabulka mapuje názvy souborů pro typ služby Azure Storage a připojení protokol použitý.

    |Název souboru  |Typ služby Azure Storage  |Použít protokol připojení |
    |---------|---------|---------|
    |databoxe2etest_BlockBlob.txt     |Objekty blob bloku         |SMB/NFS         |
    |databoxe2etest_PageBlob.txt     |Objekty blob stránky         |SMB/NFS         |
    |databoxe2etest_AzFile-BOM.txt    |Soubory Azure         |SMB/NFS         |
    |databoxe2etest_PageBlock_Rest-BOM.txt     |Objekty blob stránky         |REST        |
    |databoxe2etest_BlockBlock_Rest-BOM.txt    |Objekty blob bloku         |REST         |

Tento seznam slouží k ověření soubory nahrát do účtu úložiště Azure, až zařízení Data Box se vrátí do datacentra Azure. Ukázkový soubor manifestu je uveden níže.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Tento soubor obsahuje seznam všech souborů, které byly zkopírovány na zařízení Data Box. V tomto souboru *crc64* hodnota má vztah k kontrolního součtu vygenerovaný pro odpovídající soubor.

## <a name="view-available-capacity-of-the-device"></a>Zobrazení dostupné kapacity zařízení

Dostupnou a využitou kapacitu zařízení můžete zobrazit na řídicím panelu zařízení. 

1. V místním webovém uživatelském rozhraní přejděte na **Zobrazit řídicí panel**.
2. V části **Připojit a kopírovat** se zobrazí volné a využité místo zařízení.

    ![Zobrazení dostupné kapacity](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Přeskočení ověření kontrolního součtu

Kontrolní součty jsou generovány pro vaše data ve výchozím nastavení při přípravě na odeslání. V některých výjimečných případech, v závislosti na typu dat (malé velikosti) může být pomalý výkon. V takových případech můžete kontrolní součet přeskočit. 

Důrazně doporučujeme kontrolní součet nezakazovat, pokud to výrazně neovlivňuje výkon.

1. V pravém horním rohu z místního webového uživatelského rozhraní vašeho zařízení přejděte na nastavení.

    ![Zakázání kontrolních součtů](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Zakázání** ověření kontrolního součtu
3. Klikněte na tlačítko **Použít**.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [spravovat Data Box prostřednictvím webu Azure Portal](data-box-portal-admin.md).

