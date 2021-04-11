---
title: Připojení sdílené složky Azure k virtuálnímu počítači připojenému služba AD DS
description: Přečtěte si, jak připojit sdílenou složku k místním počítačům připojeným Active Directory Domain Services.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 1dd2768c7541312b68e2a44a35fc4260d67c5655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586994"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>4. část: připojení sdílené složky z virtuálního počítače připojeného k doméně

Než začnete s tímto článkem, ujistěte se, že jste dokončili předchozí článek, jak [nakonfigurovat oprávnění na úrovni adresářů a souborů přes protokol SMB](storage-files-identity-ad-ds-configure-permissions.md).

Proces popsaný v tomto článku ověří, že jsou správně nastavená vaše sdílená složka a přístupová oprávnění a že máte přístup ke sdílené složce Azure z virtuálního počítače připojeného k doméně. Platnost přiřazení role Azure na úrovni sdílení může trvat delší dobu. 

Přihlaste se ke klientovi pomocí pověření, ke kterým jste udělili oprávnění, jak je znázorněno na následujícím obrázku.

![Snímek obrazovky zobrazující přihlašovací obrazovku Azure AD pro ověřování uživatelů](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Požadavky na připojení

Než budete moci připojit sdílenou složku, ujistěte se, že jste prošli následujícími požadavky:

- Pokud připojujete sdílenou složku z klienta, který dříve připojil sdílenou složku pomocí klíče účtu úložiště, ujistěte se, že jste odpojili sdílenou složku, odebrali trvalá pověření klíče účtu úložiště a aktuálně používají služba AD DS pověření pro ověřování. Pokyny k vymazání připojené sdílené složky s klíčem účtu úložiště najdete na stránce s [nejčastějšími dotazy](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication).
- Váš klient musí mít přehled o vašem služba AD DS. Pokud je váš počítač nebo virtuální počítač ze sítě spravované pomocí služba AD DS, bude nutné povolit VPN, aby se dosáhlo služba AD DS ověřování.

Zástupné hodnoty nahraďte vlastními hodnotami a pak pomocí následujícího příkazu připojte sdílenou složku Azure. Vždy je nutné se připojit pomocí níže uvedené cesty. Použití CNAME pro připojení k souboru se nepodporuje pro ověřování na základě identity (služba AD DS nebo Azure služba AD DS).

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Pokud narazíte na problémy s připojením k služba AD DS přihlašovací údaje, přečtěte si téma [nepovedlo se připojit soubory Azure s přihlašovacími údaji služby AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) za pokyny.

Pokud se sdílená složka úspěšně připojí, pak jste úspěšně povolili a nakonfigurovali místní služba AD DS ověřování pro sdílené složky Azure.

## <a name="next-steps"></a>Další kroky

Pokud identita, kterou jste vytvořili v služba AD DS pro reprezentaci účtu úložiště, je v doméně nebo organizační jednotce, která vynutila otočení hesla, pokračujte dalším článkem, kde najdete pokyny k aktualizaci hesla:

[Aktualizujte heslo identity účtu úložiště v služba AD DS](storage-files-identity-ad-ds-update-password.md)