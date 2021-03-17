---
title: Webová služba mobilní aplikace Azure MFA Server – Azure Active Directory
description: Nakonfigurujte server MFA k zasílání nabízených oznámení uživatelům pomocí aplikace Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6263491ce5b319c3968c542ebbaf00294c5152cd
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742421"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Povolení ověření přes mobilní aplikaci se serverem Azure Multi-Factor Authentication

Aplikace Microsoft Authenticator nabízí možnost dalšího ověření mimo pásmo. Místo vložení automatizovaného telefonního hovoru nebo serveru SMS uživateli během přihlašování Azure Multi-Factor Authentication do aplikace Microsoft Authenticator smartphone nebo tabletu uživatele pošle oznámení. Uživatel jednoduše v aplikaci **klepne na ověřit** (nebo zadá kód PIN a klepne na "ověřit") a dokončí přihlášení.

Použití mobilní aplikace pro dvojstupňové ověřování se upřednostňuje v případě, že je telefonní příjem nespolehlivý. Pokud aplikaci používáte jako generátor tokenu OAUTH, nevyžaduje připojení k síti ani k internetu.

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

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

- [Pokročilé scénáře se službou Azure Multi-Factor Authentication a sítěmi VPN třetích stran](howto-mfaserver-nps-vpn.md).
