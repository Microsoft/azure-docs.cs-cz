---
title: Vícefaktorové ověřování (MFA) a PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak Azure AD Privileged Identity Management (PIM) ověří vícefaktorové ověřování (MFA).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8831a838a403d58d8673c6400e0dda06c03dc69f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578794"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Vícefaktorové ověřování (MFA) a PIM

Doporučujeme vyžadovat vícefaktorové ověřování (MFA) pro vaše správce. Tím se snižuje riziko útoků kvůli ohrožení zabezpečení hesla.

Může vyžadovat, aby uživatelé dokončit ověřovacím testem MFA při přihlášení. Můžete také vyžadovat, aby uživatelé dokončit ověřovacím testem MFA při aktivaci role v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Tímto způsobem, pokud uživatel nedokončil výzvu vícefaktorové ověřování, pokud jsou přihlášení, se výzva k tomu pomocí PIM.

> [!IMPORTANT]
> Klikněte pravým tlačítkem myši Přejít k pokladně, Azure MFA funguje jenom v pracovní nebo školní účty, ne na účty Microsoft (obvykle pomocí osobního účtu, který se používá k přihlášení ke službám Microsoftu, jako je Skype, Xbox, Outlook.com atd.). Z tohoto důvodu nikdo jiný nemohl pomocí účtu Microsoft nemůže být správce, protože nemohou používat vícefaktorové ověřování pro aktivovaly jejich role. Pokud tito uživatelé muset pokračovat ve správě úloh pomocí účtu Microsoft, jejich povýšení na trvalí správci teď.

## <a name="how-pim-validates-mfa"></a>Způsob, jakým PIM ověřuje MFA

Existují dvě možnosti pro ověřování MFA, když uživatel aktivuje roli.

Nejjednodušší možnost je využívají Azure MFA pro uživatele, kteří jsou aktivace privilegovaných rolí. Chcete-li to provést, nejprve zkontrolujte, že tito uživatelé mají licenci, v případě potřeby a jste se zaregistrovali pro Azure MFA. Další informace o tom, jak nasadit Azure MFA, naleznete v tématu [nasazení cloudového ověřování Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Je doporučeno, ale není nutné nakonfigurovat služby Azure AD k vynucení vícefaktorového ověřování pro tyto uživatele při přihlášení. Je to proto, že MFA kontroly se pošle PIM, samotného.

Případně pokud se uživatelé ověřují místně může mít zprostředkovatele identity zodpovídat za MFA. Pokud jste nakonfigurovali federační služby AD vyžadují ověřování pomocí čipové karty před přístupem k Azure AD, například [zabezpečení cloudových prostředků pomocí ověřování Azure Multi-Factor Authentication a AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci služby AD FS k posílání deklarací identit do služby Azure AD. Když se uživatel pokusí aktivovat roli, bude přijímat PIM, že vícefaktorové ověřování již byl ověřen pro uživatele, jakmile obdrží odpovídající deklarace identity.

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
