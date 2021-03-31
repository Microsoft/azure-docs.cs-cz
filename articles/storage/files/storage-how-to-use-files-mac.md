---
title: Připojení sdílené složky Azure přes protokol SMB v systému macOS | Microsoft Docs
description: Naučte se, jak připojit sdílenou složku Azure přes protokol SMB s macOS pomocí vyhledávačů nebo terminálu. Služba Soubory Azure je snadno použitelný cloudový systém souborů od Microsoftu.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326061"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Připojení sdílené složky Azure přes protokol SMB v systému macOS
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit pomocí standardního protokolu SMB 3 v oboru macOS High Sierra 10.13 +. Tento článek ukazuje dva různé způsoby připojení sdílené složky Azure v systému macOS: pomocí uživatelského rozhraní Finder a pomocí Terminálu.

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
1. Nahraďte `<storage-account-name>` , `<storage-account-key>` a `<share-name>` odpovídajícími hodnotami pro vaše prostředí.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Použijte sdílenou složku Azure podle potřeby**: sdílená složka Azure bude připojena k přípojnému bodu určenému předchozím příkazem.  

    ![Snímek připojené sdílené složky Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Další kroky
* [Připojení počítače Mac ke sdíleným počítačům a serverům – Podpora Apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)