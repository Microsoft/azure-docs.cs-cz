---
title: Připojení sdílené složky Azure k virtuálnímu počítači připojenému služba AD DS
description: Přečtěte si, jak připojit sdílenou složku k místním počítačům připojeným Active Directory Domain Services.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 1b07025fb7ba21607f52c43bdde653429f7a7835
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268468"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>4. část: připojení sdílené složky z virtuálního počítače připojeného k doméně

Než začnete s tímto článkem, ujistěte se, že jste dokončili předchozí článek, jak [nakonfigurovat oprávnění na úrovni adresářů a souborů přes protokol SMB](storage-files-identity-ad-ds-configure-permissions.md).

Proces popsaný v tomto článku ověří, že jsou správně nastavená vaše sdílená složka a přístupová oprávnění a že máte přístup ke sdílené složce Azure z virtuálního počítače připojeného k doméně. Platnost přiřazení role RBAC na úrovni sdílené složky může trvat delší dobu. 

Přihlaste se ke klientovi pomocí pověření, ke kterým jste udělili oprávnění, jak je znázorněno na následujícím obrázku.

![Snímek obrazovky zobrazující přihlašovací obrazovku Azure AD pro ověřování uživatelů](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Požadavky na připojení

Než budete moci připojit sdílenou složku, ujistěte se, že jste prošli následujícími požadavky:

- Pokud připojujete sdílenou složku z klienta, který dříve připojil sdílenou složku pomocí klíče účtu úložiště, ujistěte se, že jste odpojili sdílenou složku, odebrali trvalá pověření klíče účtu úložiště a aktuálně používají služba AD DS pověření pro ověřování.
- Váš klient musí mít přehled o vašem služba AD DS. Pokud je váš počítač nebo virtuální počítač ze sítě spravované pomocí služba AD DS, bude nutné povolit VPN, aby se dosáhlo služba AD DS ověřování.

Zástupné hodnoty nahraďte vlastními hodnotami a pak pomocí následujícího příkazu připojte sdílenou složku Azure:

```cli
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Pokud narazíte na problémy s připojením k služba AD DS přihlašovací údaje, přečtěte si téma [nepovedlo se připojit soubory Azure s přihlašovacími údaji služby AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) za pokyny.

Pokud se sdílená složka úspěšně připojí, pak jste úspěšně povolili a nakonfigurovali místní služba AD DS ověřování pro sdílené složky Azure.

## <a name="next-steps"></a>Další kroky

Pokud identita, kterou jste vytvořili v služba AD DS pro reprezentaci účtu úložiště, je v doméně nebo organizační jednotce, která vynutila otočení hesla, pokračujte dalším článkem, kde najdete pokyny k aktualizaci hesla:

[Aktualizujte heslo identity účtu úložiště v služba AD DS](storage-files-identity-ad-ds-update-password.md)