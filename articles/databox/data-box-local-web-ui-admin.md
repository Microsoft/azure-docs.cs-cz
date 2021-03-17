---
title: Správa Azure Data Box/Azure Data Box Heavy pomocí místního webového uživatelského rozhraní
description: Popisuje způsob použití místního webového uživatelského rozhraní pro správu Data Box a Data Box Heavy zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: e84f39fa5b9245d1874e60d651156e99c0885040
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678917"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>Použití místního webového uživatelského rozhraní ke správě Data Box a Data Box Heavy

Tento článek popisuje některé úlohy konfigurace a správy prováděné na Data Box a Data Box Heavy zařízeních. Zařízení Data Box a Data Box Heavy můžete spravovat prostřednictvím uživatelského rozhraní Azure Portal a místního webového uživatelského rozhraní pro dané zařízení. Tento článek se zaměřuje na úlohy prováděné pomocí místního webového uživatelského rozhraní.

Místní webové uživatelské rozhraní pro Data Box a pro Data Box Heavy se používá pro počáteční konfiguraci zařízení. Můžete také použít místní webové uživatelské rozhraní k vypnutí nebo restartování zařízení, spuštění diagnostických testů, aktualizace softwaru, zobrazení kopírování protokolů, mazání místních dat ze zařízení a vygenerování balíčku pro podporu pro podpora Microsoftu. Na zařízení Data Box Heavy se dvěma nezávislými uzly můžete přistupovat ke dvěma samostatným místním webovým uživatelská rozhraníám, které odpovídají každému uzlu zařízení.

## <a name="generate-support-package"></a>Generování balíčku pro podporu

Pokud budete mít se zařízením jakékoliv problémy, můžete vytvořit ze systémových protokolů balíček pro podporu. Podpora Microsoftu používá tento balíček k řešení příslušných potíží.

Chcete-li vytvořit balíček pro podporu, proveďte následující kroky:

1. V místním webovém uživatelském rozhraní klikněte na **kontaktovat podporu**. Volitelně můžete vybrat **Zahrnout výpisy paměti**. Pak vyberte **vytvořit balíček pro podporu**.

    Výpis paměti je obsah paměti zařízení, uložený po selhání systému.

    Nevybírejte možnost **Zahrnout výpisy paměti** , pokud se na ni nežádá podpora. Shromažďování balíčku podpory obsahujícího výpisy paměti trvá příliš dlouho a jsou zahrnutá citlivá data.

    ![Vytvoření balíčku pro podporu 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    Shromáždí se položky balíčku pro podporu. Tato operace trvá několik minut, pokud zahrnete pouze systémové protokoly. Pokud zahrnete výpisy paměti, trvá to hodně déle.

    ![Vytvoření balíčku pro podporu 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Po vytvoření balíčku podpory vyberte **Stáhnout balíček pro podporu**.

    ![Vytvořit balíček pro podporu 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Přejděte k umístění pro stahování a vyberte ho. Otevřete složku a zobrazte obsah.

    ![Vytvoření balíčku pro podporu 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Mazání místních dat ze zařízení

Místní webové uživatelské rozhraní můžete použít k mazání místních dat ze zařízení, než je vrátíte do datacentra Azure.

> [!IMPORTANT]
> Mazání dat nelze vrátit zpět. Než smažete místní data ze zařízení, nezapomeňte soubory zálohovat.

Pokud chcete vymazat místní data ze zařízení, proveďte tyto kroky:

1. V místním webovém uživatelském rozhraní přejdete na **Vymazat data**.
2. Zadejte heslo zařízení a vyberte **Vymazat data**.

    ![Možnost smazání dat pro zařízení](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. Na příkazovém řádku s potvrzením klikněte na **Ano** a pokračujte. Mazání dat může trvat až 50 minut.

   Před vymazáním ze zařízení nezapomeňte zálohovat místní data. Mazání dat nelze vrátit zpět.

    ![Výzva k vymazání dat – výzva k potvrzení](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Vypnutí nebo restartování zařízení

Zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme, abyste před restartováním převedli sdílené složky na hostiteli a pak na zařízení. Tím se minimalizuje jakákoli možnost poškození dat. Zajistěte, aby kopírování dat neprobíhalo při vypnutí zařízení.

Pokud chcete zařízení vypnout, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.

2. Vyberte **vypnout**.

    ![Vypnutí Data Boxu 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte výběrem **OK** .

    ![Vypnutí Data Boxu 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Zařízení po vypnutí zapněte tlačítkem napájení na předním panelu.

Při restartování Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Vyberte **restartovat**.

    ![Restartování Data Boxu 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte výběrem **OK** .

   Zařízení se vypne a restartuje.

## <a name="download-bom-or-manifest-files"></a>Stažení souboru kusovníku nebo manifestu

KUSOVNÍK nebo soubory manifestu obsahují seznam souborů, které jsou zkopírovány do Data Box nebo Data Box Heavy. Tyto soubory jsou vygenerovány pro pořadí importu při přípravě zařízení k odeslání.

Než začnete, postupujte podle těchto kroků a Stáhněte soubory kusovníku nebo manifestu pro vaše pořadí importu:

1. Přejít k místnímu webovému uživatelskému rozhraní pro vaše zařízení. Ověřte, že zařízení dokončilo **Příprava k odeslání** krok. Až se dokončí příprava zařízení, zobrazí se stav zařízení jako **připravený k odeslání**.

    ![Zařízení připravené k dodání](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Vyberte **Stáhnout seznam souborů** a Stáhněte si seznam souborů, které jste zkopírovali v data box.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. V Průzkumníkovi souborů se generují samostatné seznamy souborů v závislosti na protokolu používaném pro připojení k zařízení a používaném typu Azure Storage.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Soubory pro typ úložiště a protokol připojení](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   Následující tabulka mapuje názvy souborů na typ Azure Storage a použitý protokol připojení.

    |Název souboru  |Typ Azure Storage  |Použitý protokol připojení |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Objekty blob bloku         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Objekty blob stránky         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Soubory Azure         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Objekty blob stránky         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Objekty blob bloku         |REST         |

Pomocí tohoto seznamu ověříte soubory nahrané do Azure Storage účtu poté, co se Data Box vrátí do datacentra Azure. Ukázkový soubor manifestu je uveden níže.

> [!NOTE]
> Na Data Box Heavy jsou k dispozici dvě sady souborů (soubory KUSOVNÍKŮ) odpovídající dvěma uzlům na zařízení.

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

Tento soubor obsahuje seznam všech souborů, které byly zkopírovány na Data Box nebo Data Box Heavy. V tomto souboru se hodnota *crc64* vztahuje k kontrolnímu součtu vygenerovanému pro odpovídající soubor.

## <a name="view-available-capacity-of-the-device"></a>Zobrazení dostupné kapacity zařízení

Dostupnou a využitou kapacitu zařízení můžete zobrazit na řídicím panelu zařízení.

1. V místním webovém uživatelském rozhraní přejděte na **Zobrazit řídicí panel**.
2. V části **Připojit a kopírovat** se zobrazí volné a využité místo zařízení.

    ![Zobrazení dostupné kapacity](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Přeskočení ověření kontrolního součtu

Při přípravě na dodávání se pro vaše data generují kontrolní součty ve výchozím nastavení. Ve výjimečných případech v závislosti na datovém typu (malé velikosti souborů) může být výkon pomalý. V takových případech můžete kontrolní součet přeskočit.

Výpočet kontrolního součtu během Příprava na odeslání je proveden pouze pro objednávky importu, nikoli pro objednávky exportu.

Důrazně doporučujeme, abyste kontrolní součet nezakázali, pokud neovlivníte výkon vážně.

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení, přejít na **Nastavení**.

    ![Zakázání kontrolních součtů](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Zakázání** ověření kontrolního součtu
3. Vyberte **Použít**.

> [!NOTE]
> Možnost přeskočit výpočet kontrolního součtu je k dispozici, pouze pokud je Azure Data Box odemčená. Tato možnost se nezobrazuje, když je zařízení zamčené.

## <a name="enable-smb-signing"></a>Povolit podepisování SMB

Podepisování SMB (Server Message Block) je funkce, prostřednictvím které se komunikace pomocí protokolu SMB může digitálně podepsat na úrovni paketů. Toto podepisování zabraňuje útokům, které mění pakety SMB při přenosu.

Další informace týkající se podepisování protokolu SMB najdete v tématu [Přehled podepisování bloků zpráv serveru](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Povolení podepisování SMB v zařízení Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

    ![Otevřít nastavení](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** Podepisování SMB.

    ![Povolit podepisování SMB](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Vyberte **Použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="enable-backup-operator-privileges"></a>Povolit oprávnění operátora zálohování

Ve výchozím nastavení mají uživatelé webového uživatelského rozhraní oprávnění operátora zálohování u sdílených složek SMB. Pokud to nechcete, pomocí **oprávnění povolit back-operator** zakažte nebo povolte oprávnění.

Informace najdete v tématu operátory zálohování ve [skupinách zabezpečení služby Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Povolení oprávnění operátora zálohování v zařízení Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

   ![Otevřít nastavení Data Box-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** Oprávnění operátora zálohování.

   ![Povolit oprávnění operátora zálohování](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Vyberte použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="enable-acls-for-azure-files"></a>Povolení seznamů ACL pro soubory Azure

Metadata souborů se ve výchozím nastavení přenesou, když uživatelé odesílají data prostřednictvím protokolu SMB do svého Data Box. Metadata obsahují seznamy řízení přístupu (ACL), atributy souboru a časová razítka. Pokud to nechcete, můžete tuto funkci zakázat nebo povolit pomocí **seznamů ACL pro soubory Azure** .

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> Chcete-li přenést metadata se soubory, musíte být operátorem zálohování. Když použijete tuto funkci, zajistěte, aby místní uživatelé webového uživatelského rozhraní byly operátory zálohování. Viz [povolení oprávnění operátora zálohování](#enable-backup-operator-privileges).

Povolení přenosu seznamů ACL pro soubory Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

    ![Otevřít nastavení Data Box – 2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** Seznamy ACL pro soubory Azure.

     ![Povolení seznamů ACL pro soubory Azure](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Vyberte **Použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="enable-tls-11"></a>Povolit TLS 1,1

Ve výchozím nastavení používá Azure Data Box k šifrování protokol TLS (Transport Layer Security) 1,2, protože je bezpečnější než TSL 1,1. Pokud ale vy nebo vaši klienti používáte prohlížeč pro přístup k datům, která nepodporují protokol TLS 1,2, můžete povolit TLS 1,1.

Další informace související s protokolem TLS najdete v tématu [Azure Data box Gateway Security](../databox-gateway/data-box-gateway-security.md).

Povolení TLS 1,1 ve vašem zařízení Azure:

1. V pravém horním rohu místního webového uživatelského rozhraní vašeho zařízení vyberte **Nastavení**.

    ![Otevřít nastavení Data Box-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Povolit** TLS 1,1.

    ![Povolit TLS 1,1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Vyberte **Použít**.
4. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
5. Vyberte **restartovat**.

## <a name="next-steps"></a>Další kroky

- Naučte se [spravovat data box a data box Heavy prostřednictvím Azure Portal](data-box-portal-admin.md).
