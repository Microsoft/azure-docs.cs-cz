---
title: Azure MFA Server webové služby mobilní aplikace – Azure Active Directory
description: Nakonfigurujte server MFA k zasílání nabízených oznámení uživatelům pomocí aplikace Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cbe10eb88550f04ead22a64fbcc2f17548af02d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057379"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Povolení ověření přes mobilní aplikaci se serverem Azure Multi-Factor Authentication

Aplikace Microsoft Authenticator nabízí možnost dalšího ověření mimo pásmo. Namísto zahájení automatizovaného telefonního hovoru nebo odeslání SMS zprávy uživateli při přihlášení, doručí služba Azure Multi-Factor Authentication oznámení do aplikace Microsoft Authenticator na tablet nebo smartphone uživatele. Uživatel v aplikaci jednoduše dokončí přihlášení klepnutím na **Ověřit** (nebo zadá kód PIN a klepne na Ověřit).

Použití mobilní aplikace pro dvojstupňové ověřování se upřednostňuje v případě, že je telefonní příjem nespolehlivý. Pokud aplikaci používáte jako generátor tokenu OAUTH, nevyžaduje připojení k síti ani k internetu.

> [!IMPORTANT]
> Od 1. července 2019 společnost Microsoft již nabízí MFA Server pro nová nasazení. Noví zákazníci, kteří by chtěli požadovat použití vícefaktorového ověřování od jejich uživatelů by měla používat cloudové ověřování Azure Multi-Factor Authentication. Stávající zákazníci, kteří si aktivovali MFA Server před 1. července budou moct stáhnout nejnovější verzi aktualizace budoucí a vygenerovat aktivační přihlašovací údaje jako obvykle.

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

## <a name="next-steps"></a>Další postup

- [Pokročilé scénáře se službou Azure Multi-Factor Authentication a sítěmi VPN třetích stran](howto-mfaserver-nps-vpn.md).
