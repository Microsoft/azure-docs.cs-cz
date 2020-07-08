---
title: Připojení sdílené složky Azure přes protokol SMB v systému macOS | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure přes protokol SMB v systému macOS.
author: RenaShahMSFT
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 87271cfc602ff81a65a63426360f3bc0a8a8f030
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509825"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Připojení sdílené složky Azure přes protokol SMB v systému macOS
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systému macOS El Capitan 10.11 a novějším s použitím standardního protokolu SMB 3. Tento článek ukazuje dva různé způsoby připojení sdílené složky Azure v systému macOS: pomocí uživatelského rozhraní Finder a pomocí Terminálu.

> [!Note]  
> Před připojením sdílené složky Azure přes protokol SMB doporučujeme zakázat podepisování paketů SMB. Pokud to neuděláte, můžete při přistupování ke sdílené složce Azure ze systému macOS dosahovat nízkého výkonu. Připojení SMB bude šifrované, takže to nijak neovlivní zabezpečení vašeho připojení. Podepisování paketů SMB zakážete spuštěním následujících příkazů v Terminálu, jak je popsáno v tomto článku [Apple support article on disabling SMB packet signing](https://support.apple.com/HT205926) (Článek podpory Apple o zakázání podepisování paketů SMB):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Požadavky pro připojení sdílené složky Azure v systému macOS
* **Název účtu úložiště**: Pokud chcete připojit sdílenou složku Azure, budete potřebovat název účtu úložiště.

* **Klíč účtu úložiště**: Pokud chcete připojit sdílenou složku Azure, budete potřebovat primární (nebo sekundární) klíč úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Ujistěte se, že je otevřený port 445:** Protokol SMB komunikuje přes port TCP 445. Na klientském počítači (Mac) zkontrolujte, že brána firewall neblokuje port TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Připojení sdílené složky Azure přes Finder
1. **Otevřete Finder:** Finder je v systému macOS otevřený standardně, ale můžete se ujistit, že je aktuálně vybranou aplikací, kliknutím na ikonu obličeje macOS v Docku:  
    ![Ikona obličeje macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **V nabídce Přejít vyberte připojit k serveru**: použijte cestu UNC z požadavků, převeďte počáteční dvojité zpětné lomítko ( `\\` ) na `smb://` a všechna ostatní zpětná lomítka ( `\` ) na předávané lomítka ( `/` ). Odkaz by měl vypadat následovně: ![Dialogové okno Connect to Server (Připojit k serveru)](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Po zobrazení výzvy k zadání uživatelského jména a hesla použijte název účtu úložiště a klíč úložiště:** Po kliknutí na Connect (Připojit) v dialogovém okně Connect to Server (Připojit k serveru) se zobrazí výzva k zadání uživatelského jména a hesla (automaticky se vyplní uživatelské jméno macOS). Máte možnost uložit název účtu úložiště a klíč účtu úložiště do klíčenky macOS.

4. **Použijte sdílenou složku Azure podle potřeby**: po nahrazení názvu sdílené složky a klíče účtu úložiště v pro uživatelské jméno a heslo bude sdílená složka připojená. Můžete ji používat stejně, jako běžně používáte místní složky nebo sdílené složky, včetně přetahování souborů do sdílené složky:

    ![Snímek připojené sdílené složky Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Připojení sdílené složky Azure přes Terminál
1. Nahraďte  `<storage-account-name>`   názvem vašeho účtu úložiště. Po zobrazení výzvy zadejte klíč účtu úložiště a heslo. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Použijte sdílenou složku Azure podle potřeby**: sdílená složka Azure bude připojena k přípojnému bodu určenému předchozím příkazem.  

    ![Snímek připojené sdílené složky Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech.

* [Apple Support Article – How to connect with File Sharing on your Mac](https://support.apple.com/HT204445) (Článek podpory Apple – Jak se na Macu připojit pomocí Sdílení souborů)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Řešení potíží v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)    
