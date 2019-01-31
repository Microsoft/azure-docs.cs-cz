---
title: Připojení sdílené složky Azure přes protokol SMB v systému macOS | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure přes protokol SMB v systému macOS.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: c393942112f42dc0d56388b8beac44b4287bca23
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475700"
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
* **Název účtu úložiště**: Připojení sdílené složky Azure, potřebujete název účtu úložiště.

* **Klíč účtu úložiště**: Připojení sdílené složky Azure, musíte primární (nebo sekundární) klíč úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Zkontrolujte, jestli je port 445 otevřený**: Protokol SMB komunikuje přes port TCP 445. Na klientském počítači (Mac) zkontrolujte, že brána firewall neblokuje port TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Připojení sdílené složky Azure přes Finder
1. **Otevřete Finder**: Finder je otevřít v systému macOS ve výchozím nastavení, ale můžete zajistit, že je aktuálně vybranou aplikaci kliknutím "ikona obličeje macOS" do doku:  
    ![Ikona obličeje macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Vyberte "Připojení k serveru" v nabídce "Go"**: Použijte cestu UNC z [požadavky](#preq), převeďte počáteční dvojité zpětné lomítko (`\\`) k `smb://` a všechna ostatní zpětná lomítka (`\`) na lomítka (`/`). Odkaz by měl vypadat nějak takto: ![Dialogové okno "Připojení k serveru"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Použijte název účtu úložiště a klíč účtu úložiště po zobrazení výzvy k zadání uživatelského jména a hesla**: Po kliknutí na "Připojit" v dialogovém okně "Připojení k serveru", zobrazí se výzva k zadání uživatelského jména a hesla (automaticky se vyplní uživatelské jméno macOS). Máte možnost uložit název účtu úložiště a klíč účtu úložiště do klíčenky macOS.

4. **Používejte sdílenou složku Azure podle potřeby**: Po nahrazení sdílenou složku název a klíč účtu úložiště v uživatelské jméno a heslo, bude možné připojit sdílenou složku. Můžete ji používat stejně, jako běžně používáte místní složky nebo sdílené složky, včetně přetahování souborů do sdílené složky:

    ![Snímek připojené sdílené složky Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Připojení sdílené složky Azure přes Terminál
1. Nahraďte `<storage-account-name>` s názvem účtu úložiště. Po zobrazení výzvy zadejte klíč účtu úložiště a heslo. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Používejte sdílenou složku Azure podle potřeby**: Sdílená složka Azure se připojí na přípojný bod zadaný v předchozím příkazu.  

    ![Snímek připojené sdílené složky Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Další postup
Další informace o službě Soubory Azure najdete na těchto odkazech.

* [Apple Support Article – How to connect with File Sharing on your Mac](https://support.apple.com/HT204445) (Článek podpory Apple – Jak se na Macu připojit pomocí Sdílení souborů)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Řešení potíží v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)    
