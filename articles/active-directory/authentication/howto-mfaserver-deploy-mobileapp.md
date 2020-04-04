---
title: Webová služba Mobilní aplikace Azure MFA Server – Azure Active Directory
description: Nakonfigurujte server MFA k zasílání nabízených oznámení uživatelům pomocí aplikace Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785c4ccf591ce6a7a50a3d144fb1740121c73ce6
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653192"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Povolení ověření přes mobilní aplikaci se serverem Azure Multi-Factor Authentication

Aplikace Microsoft Authenticator nabízí možnost dalšího ověření mimo pásmo. Místo automatického telefonního hovoru nebo SMS uživateli během přihlášení, Azure Multi-Factor Authentication odešle oznámení do aplikace Microsoft Authenticator na smartphonu nebo tabletu uživatele. Uživatel jednoduše klepne **na ověřit** (nebo zadá PIN kód a klepne na "Ověřit") v aplikaci k dokončení jejich přihlášení.

Použití mobilní aplikace pro dvojstupňové ověřování se upřednostňuje v případě, že je telefonní příjem nespolehlivý. Pokud aplikaci používáte jako generátor tokenu OAUTH, nevyžaduje připojení k síti ani k internetu.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

> [!IMPORTANT]
> Pokud máte nainstalovaný Azure Multi-Factor Authentication Server v8.x nebo novější, většina následujících kroků se nevyžaduje. Ověřování přes mobilní aplikaci můžete nastavit podle pokynů v části [Konfigurace mobilní aplikace](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Požadavky

Pokud chcete použít aplikaci Microsoft Authenticator, musíte mít Azure Multi-Factor Authentication Server v8.x nebo novější.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurace serveru Azure Multi-Factor Authentication

1. V konzole Multi-Factor Authentication Serveru klikněte na ikonu User Portal. Pokud mají uživatelé povolení řídit své metody ověřování, zaškrtněte možnost **Mobilní aplikace** na kartě Nastavení v části **Povolit uživatelům výběr metody**. Bez povolení této funkce musí koncoví uživatelé pro dokončení aktivace mobilní aplikace kontaktovat podporu.
2. Zaškrtněte políčko **Povolit uživatelům aktivaci mobilní aplikace**.
3. Zaškrtněte políčko **Povolit zápis uživatele**.
4. Klikněte na ikonu **Mobilní aplikace**.
5. Do pole **Název účtu** vyplňte název společnosti nebo organizace, který se pro tento účet zobrazí v mobilní aplikaci.
   ![Konfigurace MFA Serveru – nastavení mobilní aplikace](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Další kroky

- [Pokročilé scénáře s azure multi-factor authentication a virtuálními sítěmi třetích stran](howto-mfaserver-nps-vpn.md).
