---
title: MFA nebo 2FA a Privileged Identity Management – Azure AD | Microsoft Docs
description: Přečtěte si, jak Azure AD Privileged Identity Management (PIM) ověřuje službu Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94834995"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication a Privileged Identity Management

Pro všechny správce doporučujeme vyžadovat vícefaktorové ověřování (MFA). Tím se snižuje riziko útoku kvůli napadenému heslu.

Můžete vyžadovat, aby uživatelé při přihlášení dokončili výzvu k ověření pomocí aplikace Multi-Factor Authentication. Můžete také vyžadovat, aby uživatelé při aktivaci role v Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dokončili výzvu k ověřování pomocí služby Multi-Factor Authentication. Tímto způsobem se zobrazí výzva k tomu, že pokud uživatel po přihlášení nedokončil výzvu k ověření službou Multi-Factor Authentication, bude k tomu vyzván Privileged Identity Management.

> [!IMPORTANT]
> Služba Azure AD Multi-Factor Authentication v současnosti funguje jenom s pracovními nebo školními účty, ne s osobními účty Microsoft (obvykle se jedná o osobní účet, který se používá k přihlašování ke službám Microsoftu, jako je Skype, Xbox nebo Outlook.com). Z tohoto důvodu nemůže být kdokoli, kdo používá osobní účet, oprávněným správcem, protože nemůže k aktivaci svých rolí použít službu Multi-Factor Authentication. Pokud tito uživatelé potřebují dál spravovat úlohy pomocí účet Microsoft, prozatím je přezvyšte na trvalé správce.

## <a name="how-pim-validates-mfa"></a>Jak PIM ověřuje MFA

Existují dvě možnosti ověřování služby Multi-Factor Authentication, když uživatel aktivuje roli.

Nejjednodušší možností je spoléhat se na Multi-Factor Authentication Azure AD pro uživatele, kteří aktivují privilegovanou roli. Pokud to chcete provést, nejdřív ověřte, že uživatelé mají licenci, v případě potřeby a zaregistrovali pro Azure AD Multi-Factor Authentication. Další informace o tom, jak nasadit Multi-Factor Authentication služby Azure AD, najdete v tématu [nasazení cloudové Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md). Doporučuje se, ale není to nutné, abyste službu Azure AD nakonfigurovali tak, aby při přihlášení vynutila ověřování službou Multi-Factor Authentication. Důvodem je to, že kontroly Multi-Factor Authentication budou provedeny Privileged Identity Management sám.

Případně, pokud se uživatelé ověřují místně, může být vaším poskytovatelem identity zodpovědný za službu Multi-Factor Authentication. Například pokud jste nakonfigurovali službu AD FS tak, aby vyžadovala ověřování pomocí čipové karty před přístupem k Azure AD, [zabezpečení cloudových prostředků pomocí Azure ad Multi-Factor Authentication a AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny ke konfiguraci AD FS pro odesílání deklarací do služby Azure AD. Když se uživatel pokusí o aktivaci role, Privileged Identity Management bude souhlasit s tím, že se pro uživatele po přijetí odpovídajících deklarací už ověřilo ověřování pomocí služby Multi-Factor Authentication.

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
