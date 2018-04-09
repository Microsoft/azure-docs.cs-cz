---
title: Připojení sdílené složky Azure a přístup k ní v systému Windows | Dokumentace Microsoftu
description: Připojení sdílené složky Azure a přístup k ní v systému Windows.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 8905b708101e78691c14168edf7afd659afa92a4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Připojení sdílené složky Azure a přístup k ní v systému Windows
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows a Windows Server. Tento článek ukazuje tři různé způsoby připojení sdílené složky Azure v systému Windows: pomocí uživatelského rozhraní Průzkumníka souborů, přes PowerShell a přes příkazový řádek. 

Aby bylo možné připojit sdílenou složku Azure mimo oblast, ve které je hostovaná, například v místním prostředí nebo jiné oblasti Azure, operační systém musí podporovat protokol SMB 3.0. 

Sdílené složky Azure můžete připojit v instalaci Windows na virtuálním počítači Azure nebo v místním prostředí. Následující tabulka uvádí, které verze operačního systému a v jakém prostředí podporují připojení sdílených složek:

| Verze systému Windows        | Verze protokolu SMB | Možnost připojit na virtuálním počítači Azure | Možnost připojit v místním prostředí |
|------------------------|-------------|-----------------------|----------------------|
| Pololetní kanál Windows Serveru<sup>1</sup> | SMB 3.0 | Ano | Ano |
| Windows 10<sup>2</sup>  | SMB 3.0 | Ano | Ano |
| Windows Server 2016    | SMB 3.0     | Ano                   | Ano                  |
| Windows 8.1            | SMB 3.0     | Ano                   | Ano                  |
| Windows Server 2012 R2 | SMB 3.0     | Ano                   | Ano                  |
| Windows Server 2012    | SMB 3.0     | Ano                   | Ano                  |
| Windows 7              | SMB 2.1     | Ano                   | Ne                   |
| Windows Server 2008 R2 | SMB 2.1     | Ano                   | Ne                   |

<sup>1</sup>Windows Server verze 1709.  
<sup>2</sup>Windows 10 verze 1507, 1607, 1703 a 1709.

> [!Note]  
> Vždy doporučujeme získat nejnovější aktualizaci KB pro vaši verzi systému Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Požadavky pro připojení sdílené složky Azure v systému Windows 
* **Název účtu úložiště:** Pro připojení sdílené složky Azure budete potřebovat název účtu úložiště.

* **Klíč účtu úložiště:** Pro připojení sdílené složky Azure budete potřebovat primární (nebo sekundární) klíč úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Ujistěte se, že je otevřený port 445:** Služba Soubory Azure používá protokol SMB. Protokol SMB komunikuje přes protokol TCP 445 – zkontrolujte, že brána firewall neblokuje port TCP 445 z klientského počítače.

## <a name="persisting-connections-across-reboots"></a>Zachování připojení mezi restartováními
### <a name="cmdkey"></a>CmdKey
Nejjednodušším způsobem, jak navazovat trvalá připojení, je uložit přihlašovací údaje účtu úložiště ve Windows pomocí nástroje příkazového řádku CmdKey. Následuje příklad příkazového řádku pro trvalé uložení přihlašovacích údajů účtu úložiště na virtuálním počítači:
```
C:\>cmdkey /add:<yourstorageaccountname>.file.core.windows.net /user:<domainname>\<yourstorageaccountname> /pass:<YourStorageAccountKeyWhichEndsIn==>
```
> [!Note]
> Název domény zde bude AZURE.

Nástroj CmdKey vám také umožní vypsat přihlašovací údaje, které uložil:

```
C:\>cmdkey /list
```
Výstup bude vypadat následovně:

```
Currently stored credentials:

Target: Domain:target=<yourstorageaccountname>.file.core.windows.net
Type: Domain Password
User: AZURE\<yourstorageaccountname>
```
Po trvalém uložení přihlašovacích údajů už je při připojování ke sdílené složce nebudete muset zadávat. Místo toho se můžete připojit bez zadávání přihlašovacích údajů.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Připojení sdílené složky Azure pomocí Průzkumníka souborů
> [!Note]  
> Mějte na paměti, že následující pokyny jsou ukázané na systému Windows 10 a ve starších vydaných verzích se můžou mírně lišit. 

1. **Otevřete Průzkumníka souborů:** Můžete to provést otevřením z nabídky Start nebo stisknutím klávesové zkratky Win+E.

2. **Na levé straně okna přejděte na položku Tento počítač. Tím se změní dostupné nabídky na pásu karet. Z nabídky Počítač vyberte Připojit síťovou jednotku**.
    
    ![Snímek obrazovky s rozevírací nabídkou Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Zkopírujte cestu UNC z podokna Připojit na webu Azure Portal.** 

    ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **Vyberte písmeno jednotky a zadejte cestu UNC.** 
    
    ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Použijte název účtu úložiště s předponou `Azure\` jako uživatelské jméno a klíč účtu úložiště jako heslo.**
    
    ![Snímek obrazovky s dialogovým oknem Přihlašovací údaje k síti](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Používejte sdílenou složku Azure, jak potřebujete**.
    
    ![Sdílená složka Azure je teď připojená](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Až budete připraveni sdílenou složku Azure odpojit, můžete to provést tak, že v Průzkumníku souborů kliknete pravým tlačítkem na položku sdílené složky v části Umístění v síti a vyberete Odpojit**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Připojení sdílené složky Azure pomocí PowerShellu
1. **Pomocí následujícího příkazu připojte sdílenou složku Azure:** Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` správnými informacemi.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Používejte sdílenou složku Azure, jak potřebujete**.

3. **Až budete hotovi, odpojte sdílenou složku Azure pomocí následujícího příkazu**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> U příkazu `New-PSDrive` můžete použít parametr `-Persist`, který zajistí, že sdílená složka Azure bude během připojení viditelná v celém operačním systému.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Připojení sdílené složky Azure pomocí příkazového řádku
1. **Pomocí následujícího příkazu připojte sdílenou složku Azure:** Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` správnými informacemi.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Používejte sdílenou složku Azure, jak potřebujete**.

3. **Až budete hotovi, odpojte sdílenou složku Azure pomocí následujícího příkazu**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Sdílenou složku Azure můžete nakonfigurovat tak, aby se po restartování automaticky znovu připojila díky uchování přihlašovacích údajů v systému Windows. Následující příkaz zajistí uchování přihlašovacích údajů:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech.

* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>Koncepční články a videa
* [Soubory Azure: hladký cloudový souborový systém SMB pro Windows a Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Jak používat Soubory Azure s Linuxem](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-files"></a>Podpora nástrojů pro Soubory Azure
* [Použití nástroje AzCopy s Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Použití Azure CLI s Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Řešení potíží se Soubory Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží se Soubory Azure – Linux](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Příspěvky na blozích
* [Služba Soubory Azure je teď obecně dostupná](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Uvnitř služby Soubory Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Představujeme službu Microsoft Azure File](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrace dat do služby Soubory Azure](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referenční informace
* [Klientská knihovna Storage pro .NET – referenční informace](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [REST API služby File – referenční informace](http://msdn.microsoft.com/library/azure/dn167006.aspx)
