---
title: Multi-Factor Authentication (MFA) a Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak Azure AD Privileged Identity Management (PIM) ověřuje službu Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa58c354198e648227b9b0b43d1f60546f87d32
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063067"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication a Privileged Identity Management

Pro všechny správce doporučujeme vyžadovat vícefaktorové ověřování (MFA). Tím se snižuje riziko útoku kvůli napadenému heslu.

Můžete vyžadovat, aby uživatelé při přihlášení dokončili výzvu k ověření pomocí aplikace Multi-Factor Authentication. Můžete také vyžadovat, aby uživatelé při aktivaci role v Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dokončili výzvu k ověřování pomocí služby Multi-Factor Authentication. Tímto způsobem se zobrazí výzva k tomu, že pokud uživatel po přihlášení nedokončil výzvu k ověření službou Multi-Factor Authentication, bude k tomu vyzván Privileged Identity Management.

> [!IMPORTANT]
> V současné době Azure Multi-Factor Authentication funguje jenom s pracovními nebo školními účty, ne s osobními účty Microsoft (obvykle se jedná o osobní účet, který se používá k přihlašování ke službám Microsoftu, jako je Skype, Xbox nebo Outlook.com). Z tohoto důvodu nemůže být kdokoli, kdo používá osobní účet, oprávněným správcem, protože nemůže k aktivaci svých rolí použít službu Multi-Factor Authentication. Pokud tito uživatelé potřebují dál spravovat úlohy pomocí účet Microsoft, prozatím je přezvyšte na trvalé správce.

## <a name="how-pim-validates-mfa"></a>Jak PIM ověřuje MFA

Existují dvě možnosti ověřování služby Multi-Factor Authentication, když uživatel aktivuje roli.

Nejjednodušší možností je spoléhat se na Azure Multi-Factor Authentication pro uživatele, kteří aktivují privilegovanou roli. Pokud to chcete provést, nejdřív ověřte, že uživatelé mají licenci, v případě potřeby a zaregistrovali se pro Azure Multi-Factor Authentication. Další informace o tom, jak nasadit Azure Multi-Factor Authentication, najdete v tématu [nasazení cloudových Multi-Factor Authentication Azure](../authentication/howto-mfa-getstarted.md). Doporučuje se, ale není to nutné, abyste službu Azure AD nakonfigurovali tak, aby při přihlášení vynutila ověřování službou Multi-Factor Authentication. Důvodem je to, že kontroly Multi-Factor Authentication budou provedeny Privileged Identity Management sám.

Případně, pokud se uživatelé ověřují místně, může být vaším poskytovatelem identity zodpovědný za službu Multi-Factor Authentication. Například pokud jste nakonfigurovali službu AD FS tak, aby vyžadovala ověřování pomocí čipové karty před přístupem k Azure AD, [zabezpečení cloudových prostředků pomocí azure Multi-Factor Authentication a AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci AD FS na odešlete deklarace identity do Azure AD. Když se uživatel pokusí o aktivaci role, Privileged Identity Management bude souhlasit s tím, že se pro uživatele po přijetí odpovídajících deklarací už ověřilo ověřování pomocí služby Multi-Factor Authentication.

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
