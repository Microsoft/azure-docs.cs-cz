---
title: Použití sdílené složky Azure s Windows | Microsoft Docs
description: Naučte se používat sdílené složky Azure s Windows a Windows serverem. Používejte sdílené složky Azure s protokolem SMB 3,0 v instalacích s Windows místně nebo na virtuálních počítačích Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e864dcaa2a611746ae813a4f0adf8409fbc50871
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789784"
---
# <a name="use-an-azure-file-share-with-windows"></a>Použití sdílené složky Azure s Windows
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné bez problémů používat v systémech Windows a Windows Server. Tento článek popisuje důležité informace o používání sdílené složky Azure s Windows a Windows Serverem.

Aby bylo možné používat sdílenou složku Azure mimo oblast Azure, ve které je hostovaná, například v místním prostředí nebo jiné oblasti Azure, operační systém musí podporovat protokol SMB 3.0. 

Sdílené složky Azure můžete používat v instalaci Windows na virtuálním počítači Azure nebo v místním prostředí. Následující tabulka uvádí, které verze operačního systému a v jakém prostředí podporují přístup ke sdíleným složkám:

| Verze systému Windows        | Verze protokolu SMB | Možnost připojit na virtuálním počítači Azure | Místně připojit |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Yes | Yes |
| Windows 10<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Půlroční kanál Windows serveru<sup>2</sup> | SMB 3.0 | Yes | Yes |
| Windows Server 2016 | SMB 3.0 | Yes | Yes |
| Windows 8.1 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 R2 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 | SMB 3.0 | Yes | Yes |
| Systém Windows 7<sup>3</sup> | SMB 2.1 | Yes | No |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Yes | No |

<sup>1</sup> Windows 10, verze 1507, 1607, 1803, 1809, 1903, 1909 a 2004.  
<sup>2</sup> . Windows Server, verze 1809, 1903, 1909, 2004.  
<sup>3</sup> . Pravidelná podpora Microsoftu pro Windows 7 a Windows Server 2008 R2 skončila. Další podporu pro aktualizace zabezpečení je možné zakoupit jenom prostřednictvím [programu Extended Security Update (EVJ)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). Důrazně doporučujeme tyto operační systémy migrovat.

> [!Note]  
> Vždy doporučujeme získat nejnovější aktualizaci KB pro vaši verzi systému Windows.

## <a name="prerequisites"></a>Požadavky 

Zkontrolujte, že je otevřený port 445: Protokol SMB vyžaduje otevřený port TCP 445. Pokud je port 445 zablokovaný, připojení selžou. Můžete zjistit, jestli brána firewall blokuje port 445 pomocí `Test-NetConnection` rutiny. Další informace o tom, jak obejít blokovaný port 445, najdete v části [Příčina 1: port 445 je blokován](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) v naší příručce pro řešení potíží s Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Použití sdílené složky Azure s Windows
Pokud chcete používat sdílenou složku Azure s Windows, musíte ji buď připojit, což znamená přiřadit jí písmeno jednotky nebo cestu k přípojnému bodu, nebo k ní přistupovat přes její [cestu UNC](/windows/win32/fileio/naming-a-file). 

Tento článek používá klíč účtu úložiště pro přístup ke sdílené složce souborů. Klíč účtu úložiště je klíč správce pro účet úložiště, včetně oprávnění správce ke všem souborům a složkám v rámci sdílené složky, ke které přistupujete, a ke všem sdíleným složkám a dalším prostředkům úložiště (objektům blob, frontám, tabulkám atd.) obsaženým v účtu úložiště. Pokud to pro vaše zatížení nestačí, můžete použít [synchronizace souborů Azure](../file-sync/file-sync-planning.md) , nebo můžete použít [ověřování na základě identity přes protokol SMB](storage-files-active-directory-overview.md).

Při migraci obchodních aplikací očekávajících sdílenou složku SMB metodou „lift and shift“ do Azure se jako alternativa k provozu vyhrazeného souborového serveru Windows na virtuálním počítači Azure běžně používá sdílená složka Azure. Jedním z důležitých aspektů úspěšné migrace obchodní aplikace, která má používat sdílenou složku Azure, je to, že řada obchodních aplikací se spouští v kontextu vyhrazeného účtu služby s omezenými systémovými oprávněními, a ne v kontextu účtu správce virtuálního počítače. Proto je potřeba zajistit připojení a uložení přihlašovacích údajů pro sdílenou složku Azure z kontextu účtu služby, a nikoli účtu správce.

### <a name="mount-the-azure-file-share"></a>Připojení sdílené složky Azure

Azure Portal vám poskytne skript, který můžete použít k připojení sdílené složky přímo k hostiteli. Doporučujeme použít tento poskytnutý skript.

Postup získání tohoto skriptu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Přejděte do účtu úložiště, který obsahuje sdílenou složku, kterou chcete připojit.
1. Vyberte **Sdílené složky**.
1. Vyberte sdílenou složku, kterou chcete připojit.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Snímek obrazovky okna sdílené složky, sdílení souborů je zvýrazněné.":::

1. Vyberte **Connect** (Připojit).

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Snímek obrazovky ikony připojení pro sdílenou složku":::

1. Vyberte písmeno jednotky, ke které se má sdílená složka připojit.
1. Zkopírujte zadaný skript.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Snímek obrazovky okna připojit, tlačítko pro kopírování na skriptu je zvýrazněné.":::

1. Vložte skript do prostředí v hostiteli, ke kterému chcete sdílenou složku připojit, a spusťte ji.

Teď máte připojenou sdílenou složku Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Připojení sdílené složky Azure pomocí Průzkumníka souborů
> [!Note]  
> Mějte na paměti, že následující pokyny jsou ukázané na systému Windows 10 a ve starších vydaných verzích se můžou mírně lišit. 

1. Otevřete Průzkumníka souborů. Můžete to provést otevřením z nabídky Start nebo stisknutím klávesové zkratky Win+E.

1. V levé části okna přejděte na **Tento počítač** . Tím se změní dostupné nabídky na pásu karet. V nabídce počítač vyberte **mapovat síťovou jednotku**.
    
    ![Snímek obrazovky s rozevírací nabídkou Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Vyberte písmeno jednotky a zadejte cestu UNC, formát cesty UNC `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Příklad: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Použijte název účtu úložiště s předponou `AZURE\` jako uživatelské jméno a klíč účtu úložiště jako heslo.
    
    ![Snímek obrazovky s dialogovým oknem Přihlašovací údaje k síti](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Používejte sdílenou složku Azure, jak potřebujete.
    
    ![Sdílená složka Azure je teď připojená](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Až budete připraveni sdílenou složku Azure odpojit, můžete to provést tak, že v Průzkumníku souborů kliknete pravým tlačítkem na položku sdílené složky v části **Umístění v síti** a vyberete **Odpojit**.

### <a name="accessing-share-snapshots-from-windows"></a>Přístup ke snímkům sdílené složky z Windows
Pokud jste ručně nebo automaticky prostřednictvím skriptu nebo služby jako Azure Backup pořídili snímek sdílené složky, můžete ve Windows zobrazit předchozí verze sdílené složky, adresáře nebo konkrétního souboru ze sdílené složky. Snímek sdílené složky můžete pořídit pomocí [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)nebo [Azure Portal](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Výpis předchozích verzí
Přejděte k položce nebo nadřazené položce, kterou je potřeba obnovit. Dvojím kliknutím přejděte do požadovaného adresáře. Klikněte pravým tlačítkem a v nabídce vyberte **Vlastnosti**.

![Nabídka pro vybraný adresář zobrazená po kliknutí pravým tlačítkem](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Výběrem karty **Předchozí verze** zobrazte seznam snímků sdílené složky pro tento adresář. Načtení seznamu může trvat několik sekund v závislosti na rychlosti sítě a počtu snímků sdílené složky v adresáři.

![Karta Předchozí verze](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Výběrem možnosti **Otevřít** můžete otevřít konkrétní snímek. 

![Otevřený snímek](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Obnovení z předchozí verze
Výběrem možnosti **Obnovit** můžete rekurzivně zkopírovat obsah celého adresáře v době vytvoření snímku sdílené složky do původního umístění.

 ![Tlačítko Obnovit ve zprávě upozornění](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Zabezpečení Windows a Windows Serveru
Aby bylo možné připojit sdílenou složku Azure ve Windows, musí být přístupný port 445. Řada organizací port 445 blokuje kvůli bezpečnostním rizikům spojeným s protokolem SMB 1. Protokol SMB 1, označovaný také jako CIFS (Common Internet File System), je starší verze protokolu systému souborů, který je součástí Windows a Windows Serveru. Protokol SMB 1 je zastaralý, neefektivní a hlavně nezabezpečený protokol. Dobrou zprávou je, že služba Soubory Azure nepodporuje protokol SMB 1 a všechny podporované verze Windows a Windows Serveru umožňují odebrání nebo zakázání protokolu SMB 1. Vždy [důrazně doporučujeme](https://aka.ms/stopusingsmb1) ve Windows odebrat nebo zakázat klienta a server SMB 1 před použitím sdílených složek Azure v produkčním prostředí.

Následující tabulka obsahuje podrobné informace o stavu protokolu SMB 1 v jednotlivých verzích Windows:

| Verze systému Windows                           | Výchozí stav protokolu SMB 1 | Metoda zakázání/odebrání       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows Server verze 1709 nebo novější            | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows 10 verze 1709 nebo novější                | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows Server 2016                       | Povoleno              | Odebrání pomocí funkce Windows |
| Windows 10 verze 1507, 1607 a 1703 | Povoleno              | Odebrání pomocí funkce Windows |
| Windows Server 2012 R2                    | Povoleno              | Odebrání pomocí funkce Windows | 
| Windows 8.1                               | Povoleno              | Odebrání pomocí funkce Windows | 
| Windows Server 2012                       | Povoleno              | Zakázání pomocí registru       | 
| Windows Server 2008 R2                    | Povoleno              | Zakázání pomocí registru       |
| Windows 7                                 | Povoleno              | Zakázání pomocí registru       | 

### <a name="auditing-smb-1-usage"></a>Auditování využití protokolu SMB 1
> Platí pro Windows Server 2019, půlroční kanál Windows serveru (verze 1709 a 1803), Windows Server 2016, Windows 10 (verze 1507, 1607, 1703, 1709 a 1803), Windows Server 2012 R2 a Windows 8.1

Před odebráním protokolu SMB 1 z vašeho prostředí možná budete chtít auditovat jeho využití, abyste zjistili, jestli tato změna nezpůsobí, že někteří klienti přestanou fungovat. Při provedení jakéhokoli požadavku na sdílené složky SMB s použitím protokolu SMB 1 se zaznamená událost auditu do protokolu auditu v části `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Pokud chcete povolit podporu auditování na Windows Serveru 2012 R2 a ve Windows 8.1, nainstalujte minimálně aktualizaci [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Pokud chcete povolit auditování, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Odebrání protokolu SMB 1 z Windows Serveru
> Platí pro Windows Server 2019, Windows Server-půlroční kanál (verze 1709 a 1803), Windows Server 2016, Windows Server 2012 R2

Pokud chcete odebrat protokol SMB 1 z instance Windows Serveru, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Proces odebrání dokončíte restartováním serveru. 

> [!Note]  
> Počínaje Windows 10 a Windows Serverem verze 1709 není protokol SMB 1 ve výchozím nastavení nainstalovaný a funkce klienta a serveru SMB 1 zajišťují samostatné funkce Windows. Vždy doporučujeme ponechat server SMB 1 (`FS-SMB1-SERVER`) i klienta SMB 1 (`FS-SMB1-CLIENT`) nenainstalované.

### <a name="removing-smb-1-from-windows-client"></a>Odebrání protokolu SMB 1 z klienta Windows
> Platí pro Windows 10 (verze 1507, 1607, 1703, 1709 a 1803) a Windows 8.1.

Pokud chcete odebrat protokol SMB 1 z klienta Windows Serveru, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Proces odebrání dokončíte restartováním počítače.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Zakázání protokolu SMB 1 ve starších verzích Windows a Windows Serveru
> Platí pro Windows Server 2012, Windows Server 2008 R2 a Windows 7.

Ve starších verzích Windows a Windows Serveru není možné protokol SMB 1 zcela odebrat, ale prostřednictvím registru je možné ho zakázat. Pokud chcete zakázat protokol SMB 1, vytvořte v rámci `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` nový klíč registru `SMB1` typu `DWORD` s hodnotou `0`.

Můžete to také snadno provést pomocí následující rutiny PowerShellu:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Po vytvoření tohoto klíče registru musíte server restartovat, aby se protokol SMB 1 zakázal.

### <a name="smb-resources"></a>Zdroje informací k protokolu SMB
- [Ukončení používání protokolu SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Informační středisko k produktu SMB 1](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Zjišťování protokolu SMB 1 v prostředí pomocí DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [Zakázání protokolu SMB 1 prostřednictvím Zásad skupiny](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech:
- [Plánování nasazení Azure Files](storage-files-planning.md)
- [Nejčastější dotazy](./storage-files-faq.md)
- [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)