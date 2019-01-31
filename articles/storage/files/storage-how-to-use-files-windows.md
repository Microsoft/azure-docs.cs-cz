---
title: Použití sdílené složky Azure s Windows | Microsoft Docs
description: Zjistěte, jak používat sdílenou složku Azure s Windows a Windows Serverem.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: renash
ms.subservice: files
ms.openlocfilehash: e3b0773da49499e2eaa8c9b9f59ced4ed26276ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465159"
---
# <a name="use-an-azure-file-share-with-windows"></a>Použití sdílené složky Azure s Windows
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné bez problémů používat v systémech Windows a Windows Server. Tento článek popisuje důležité informace o používání sdílené složky Azure s Windows a Windows Serverem.

Aby bylo možné používat sdílenou složku Azure mimo oblast Azure, ve které je hostovaná, například v místním prostředí nebo jiné oblasti Azure, operační systém musí podporovat protokol SMB 3.0. 

Sdílené složky Azure můžete používat v instalaci Windows na virtuálním počítači Azure nebo v místním prostředí. Následující tabulka uvádí, které verze operačního systému a v jakém prostředí podporují přístup ke sdíleným složkám:

| Verze systému Windows        | Verze protokolu SMB | Možnost připojit na virtuálním počítači Azure | Možnost připojit v místním prostředí |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | Ano | Ano |
| Windows 10<sup>1</sup> | SMB 3.0 | Ano | Ano |
| Pololetní kanál Windows Server<sup>2</sup> | SMB 3.0 | Ano | Ano |
| Windows Server 2016    | SMB 3.0     | Ano                   | Ano                  |
| Windows 8.1            | SMB 3.0     | Ano                   | Ano                  |
| Windows Server 2012 R2 | SMB 3.0     | Ano                   | Ano                  |
| Windows Server 2012    | SMB 3.0     | Ano                   | Ano                  |
| Windows 7              | SMB 2.1     | Ano                   | Ne                   |
| Windows Server 2008 R2 | SMB 2.1     | Ano                   | Ne                   |

<sup>1</sup>Windows 10 verze 1507, 1607, 1703, 1709, 1803 a 1809.  
<sup>2</sup>systému Windows Server verze 1709 a 1803.

> [!Note]  
> Vždy doporučujeme získat nejnovější aktualizaci KB pro vaši verzi systému Windows.

## <a name="prerequisites"></a>Požadavky 
* **Název účtu úložiště**: Připojení sdílené složky Azure, potřebujete název účtu úložiště.

* **Klíč účtu úložiště**: Připojení sdílené složky Azure, musíte primární (nebo sekundární) klíč úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Zkontrolujte, jestli je port 445 otevřený**: Protokol SMB vyžaduje port TCP 445 být otevřená. připojení se nezdaří, pokud je port 445 blokovaný. Ke kontrole, jestli vaše brána firewall neblokuje port 445, můžete použít rutinu `Test-NetConnection`. Následující kód PowerShellu předpokládá, že máte nainstalovaný modul AzureRM PowerShell. Další informace najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Nezapomeňte nahradit `<your-storage-account-name>` a `<your-resoure-group-name>` odpovídajícími názvy pro váš účet úložiště.

    ```PowerShell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Pokud připojení proběhne úspěšně, měl by se zobrazit následující výstup:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Výše uvedený příkaz vrátí aktuální IP adresu účtu úložiště. Není zaručeno, že tato IP adresa zůstane stejná, a kdykoli se může změnit. Nekódujte pevně tuto IP adresu do skriptů ani do konfigurace brány firewall. 

## <a name="using-an-azure-file-share-with-windows"></a>Použití sdílené složky Azure s Windows
Pokud chcete používat sdílenou složku Azure s Windows, musíte ji buď připojit, což znamená přiřadit jí písmeno jednotky nebo cestu k přípojnému bodu, nebo k ní přistupovat přes její [cestu UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

Na rozdíl od ostatních sdílených složek SMB, se kterými jste možná pracovali, jako jsou například sdílené složky SMB hostované na Windows Serveru, serveru Linux Samba nebo zařízení NAS, sdílené složky Azure v současné době nepodporují ověřování protokolu Kerberos pomocí identity Active Directory (AD) ani Azure Active Directory (AAD). Na této funkci však [pracujeme](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles). Místo toho musíte ke sdílené složce Azure přistupovat pomocí klíče účtu úložiště, ve kterém se sdílená složka nachází. Klíč účtu úložiště je klíč správce pro účet úložiště zahrnující oprávnění správce ke všem souborům a složkám v rámci sdílené složky, ke které přistupujete, a ke všem sdíleným složkám a dalším prostředkům úložiště (objekty blob, fronty, tabulky atd.) ve vašem účtu úložiště. Pokud to pro vaši úlohu není dostatečné, v přechodném období do veřejného zpřístupnění chybějícího ověřování protokolu Kerberos založeného na AAD a podpory ACL může tento nedostatek vyřešit [Synchronizace souborů Azure](storage-files-planning.md#data-access-method).

Při migraci obchodních aplikací očekávajících sdílenou složku SMB metodou „lift and shift“ do Azure se jako alternativa k provozu vyhrazeného souborového serveru Windows na virtuálním počítači Azure běžně používá sdílená složka Azure. Jedním z důležitých aspektů úspěšné migrace obchodní aplikace, která má používat sdílenou složku Azure, je to, že řada obchodních aplikací se spouští v kontextu vyhrazeného účtu služby s omezenými systémovými oprávněními, a ne v kontextu účtu správce virtuálního počítače. Proto je potřeba zajistit připojení a uložení přihlašovacích údajů pro sdílenou složku Azure z kontextu účtu služby, a nikoli účtu správce.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Trvalé uložení přihlašovacích údajů sdílené složky Azure ve Windows  
Nástroj [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) umožňuje uložit přihlašovací údaje účtu úložiště v rámci Windows. To znamená, že při pokusu o přístup ke sdílené složce Azure přes cestu UNC nebo její připojení nebudete muset zadávat přihlašovací údaje. Pokud chcete uložit přihlašovací údaje vašeho účtu úložiště, spusťte následující příkazy PowerShellu, ve kterých podle potřeby nahraďte `<your-storage-account-name>` a `<your-resoure-group-name>`.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

Uložení přihlašovacích údajů pro účet úložiště nástrojem cmdkey můžete ověřit pomocí parametru list:

```PowerShell
cmdkey /list
```

Pokud jsou přihlašovací údaje pro vaši sdílenou složku Azure úspěšně uložené, očekávaný výstup je následující (seznam může obsahovat další uložené klíče):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Teď byste měli mít možnost připojit sdílenou složku nebo k ní získat přístup, aniž byste museli zadávat další přihlašovací údaje.

#### <a name="advanced-cmdkey-scenarios"></a>Pokročilé scénáře s nástrojem cmdkey
V případě nástroje cmdkey existují další dva scénáře, které byste měli zvážit: ukládání přihlašovacích údajů pro jiného uživatele (například k účtu služby) na počítači a ukládání přihlašovacích údajů na vzdáleném počítači s využitím vzdálené komunikace PowerShellu.

Uložení přihlašovacích údajů pro jiného uživatele na počítači je velmi snadné: když jste přihlášeni ke svému účtu, stačí spustit následující příkaz PowerShellu:

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Tím se otevře nové okno PowerShellu v kontextu uživatele vašeho účtu služby (nebo uživatelského účtu). Pak můžete použít nástroj cmdkey, jak je popsáno [výše](#persisting-azure-file-share-credentials-in-windows).

Uložení přihlašovacích údajů na vzdáleném počítači s využitím vzdálené komunikace PowerShellu však není možné, protože nástroj cmdkey neumožňuje přístup ke svému úložišti přihlašovacích údajů, když je uživatel přihlášený přes vzdálenou komunikaci PowerShellu, a to ani za účelem jejich přidání. Doporučujeme přihlásit se k počítači pomocí [Vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Připojení sdílené složky Azure pomocí PowerShellu
Spuštěním následujících příkazů v normální relaci PowerShellu (tj. bez zvýšených oprávnění) připojte sdílenou složku Azure. Nezapomeňte nahradit `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` a `<desired-drive-letter>` odpovídajícími údaji.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Při použití možnosti `-Persist` v rutině `New-PSDrive` bude možné sdílenou složku znovu připojit pouze při spuštění, pokud jsou přihlašovací údaje uložené. Přihlašovací údaje můžete uložit pomocí nástroje cmdkey, jek je [popsáno výše](#persisting-azure-file-share-credentials-in-windows). 

V případě potřeby můžete sdílenou složku Azure odpojit pomocí následující rutiny PowerShellu.

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Připojení sdílené složky Azure pomocí Průzkumníka souborů
> [!Note]  
> Mějte na paměti, že následující pokyny jsou ukázané na systému Windows 10 a ve starších vydaných verzích se můžou mírně lišit. 

1. Otevřete Průzkumníka souborů. Můžete to provést otevřením z nabídky Start nebo stisknutím klávesové zkratky Win+E.

2. Na levé straně okna přejděte na položku **Tento počítač**. Tím se změní dostupné nabídky na pásu karet. Z nabídky Počítač vyberte **Připojit síťovou jednotku**.
    
    ![Snímek obrazovky s rozevírací nabídkou Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Zkopírujte cestu UNC z podokna **Připojit** na webu Azure Portal. 

    ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Vyberte písmeno jednotky a zadejte cestu UNC. 
    
    ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Použijte název účtu úložiště s předponou `AZURE\` jako uživatelské jméno a klíč účtu úložiště jako heslo.
    
    ![Snímek obrazovky s dialogovým oknem Přihlašovací údaje k síti](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Používejte sdílenou složku Azure, jak potřebujete.
    
    ![Sdílená složka Azure je teď připojená](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Až budete připraveni sdílenou složku Azure odpojit, můžete to provést tak, že v Průzkumníku souborů kliknete pravým tlačítkem na položku sdílené složky v části **Umístění v síti** a vyberete **Odpojit**.

### <a name="accessing-share-snapshots-from-windows"></a>Přístup ke snímkům sdílené složky z Windows
Pokud jste ručně nebo automaticky prostřednictvím skriptu nebo služby jako Azure Backup pořídili snímek sdílené složky, můžete ve Windows zobrazit předchozí verze sdílené složky, adresáře nebo konkrétního souboru ze sdílené složky. Snímek sdílené složky můžete pořídit pomocí webu [Azure Portal](storage-how-to-use-files-portal.md), [Azure PowerShellu](storage-how-to-use-files-powershell.md) nebo [Azure CLI](storage-how-to-use-files-cli.md).

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
| Windows Server 2019 (Preview)             | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows Server verze 1709 nebo novější            | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows 10 verze 1709 nebo novější                | Zakázáno             | Odebrání pomocí funkce Windows |
| Windows Server 2016                       | Povoleno              | Odebrání pomocí funkce Windows |
| Windows 10 verze 1507, 1607 a 1703 | Povoleno              | Odebrání pomocí funkce Windows |
| Windows Server 2012 R2                    | Povoleno              | Odebrání pomocí funkce Windows | 
| Windows 8.1                               | Povoleno              | Odebrání pomocí funkce Windows | 
| Windows Server 2012                       | Povoleno              | Zakázání pomocí registru       | 
| Windows Server 2008 R2                    | Povoleno              | Zakázání pomocí registru       |
| Windows 7                                 | Povoleno              | Zakázání pomocí registru       | 

### <a name="auditing-smb-1-usage"></a>Auditování využití protokolu SMB 1
> Platí pro Windows Server 2019 (Preview), pololetní kanál Windows Serveru (verze 1709 a 1803), Windows Server 2016, Windows 10 (verze 1507, 1607, 1703, 1709 a 1803), Windows Server 2012 R2 a Windows 8.1.

Před odebráním protokolu SMB 1 z vašeho prostředí možná budete chtít auditovat jeho využití, abyste zjistili, jestli tato změna nezpůsobí, že někteří klienti přestanou fungovat. Při provedení jakéhokoli požadavku na sdílené složky SMB s použitím protokolu SMB 1 se zaznamená událost auditu do protokolu auditu v části `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Pokud chcete povolit podporu auditování na Windows Serveru 2012 R2 a ve Windows 8.1, nainstalujte minimálně aktualizaci [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Pokud chcete povolit auditování, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Odebrání protokolu SMB 1 z Windows Serveru
> Platí pro Windows Server 2019 (Preview), pololetní kanál Windows Serveru (verze 1709 a 1803), Windows Server 2016 a Windows Server 2012 R2.

Pokud chcete odebrat protokol SMB 1 z instance Windows Serveru, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

Proces odebrání dokončíte restartováním serveru. 

> [!Note]  
> Počínaje Windows 10 a Windows Serverem verze 1709 není protokol SMB 1 ve výchozím nastavení nainstalovaný a funkce klienta a serveru SMB 1 zajišťují samostatné funkce Windows. Vždy doporučujeme ponechat server SMB 1 (`FS-SMB1-SERVER`) i klienta SMB 1 (`FS-SMB1-CLIENT`) nenainstalované.

### <a name="removing-smb-1-from-windows-client"></a>Odebrání protokolu SMB 1 z klienta Windows
> Platí pro Windows 10 (verze 1507, 1607, 1703, 1709 a 1803) a Windows 8.1.

Pokud chcete odebrat protokol SMB 1 z klienta Windows Serveru, spusťte v relaci PowerShellu se zvýšenými oprávněními následující rutinu:

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Proces odebrání dokončíte restartováním počítače.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Zakázání protokolu SMB 1 ve starších verzích Windows a Windows Serveru
> Platí pro Windows Server 2012, Windows Server 2008 R2 a Windows 7.

Ve starších verzích Windows a Windows Serveru není možné protokol SMB 1 zcela odebrat, ale prostřednictvím registru je možné ho zakázat. Pokud chcete zakázat protokol SMB 1, vytvořte v rámci `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` nový klíč registru `SMB1` typu `DWORD` s hodnotou `0`.

Můžete to také snadno provést pomocí následující rutiny PowerShellu:

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Po vytvoření tohoto klíče registru musíte server restartovat, aby se protokol SMB 1 zakázal.

### <a name="smb-resources"></a>Zdroje informací k protokolu SMB
- [Ukončení používání protokolu SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Informační středisko k produktu SMB 1](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Zjišťování protokolu SMB 1 v prostředí pomocí DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Zakázání protokolu SMB 1 prostřednictvím Zásad skupiny](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Další postup
Další informace o službě Soubory Azure najdete na těchto odkazech:
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      
