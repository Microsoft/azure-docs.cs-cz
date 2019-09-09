---
title: Multi-Factor Authentication (MFA) a PIM-Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804300"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Multi-Factor Authentication (MFA) a PIM

Pro všechny správce doporučujeme vyžadovat vícefaktorové ověřování (MFA). Tím se snižuje riziko útoku kvůli napadenému heslu.

Můžete vyžadovat, aby uživatelé při přihlášení dokončili výzvu MFA. Můžete také vyžadovat, aby uživatelé při aktivaci role v Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dokončili výzvu MFA. V případě, že uživatel nedokončil výzvu MFA při přihlášení, zobrazí se jim výzva k tomu, aby to udělal správce osobních informací.

> [!IMPORTANT]
> Azure MFA teď funguje jenom s pracovními nebo školními účty, ne s účty Microsoft (obvykle se jedná o osobní účet, který se používá k přihlašování ke službám Microsoftu, jako je Skype, Xbox, Outlook.com atd.). Z tohoto důvodu nemůže být kdokoli, kdo používá účet Microsoft, neoprávněným správcem, protože nemůže k aktivaci svých rolí použít vícefaktorové ověřování. Pokud tito uživatelé potřebují dál spravovat úlohy pomocí účet Microsoft, prozatím je přezvyšte na trvalé správce.

## <a name="how-pim-validates-mfa"></a>Jak PIM ověřuje MFA

Existují dvě možnosti ověřování MFA, když uživatel aktivuje roli.

Nejjednodušší možností je spoléhat na Azure MFA pro uživatele, kteří aktivují privilegovanou roli. Pokud to chcete provést, nejdřív ověřte, že uživatelé mají licenci, pokud je to potřeba, a jestli jsou zaregistrovaná pro Azure MFA. Další informace o tom, jak nasadit Azure MFA, najdete v tématu [nasazení cloudové Multi-Factor Authentication Azure](../authentication/howto-mfa-getstarted.md). Doporučuje se, ale není potřeba, abyste službu Azure AD nakonfigurovali tak, aby při přihlášení vynutila MFA pro tyto uživatele. Důvodem je to, že kontroly MFA budou provedeny samotným PIM.

Případně, pokud se uživatelé ověřují místně, může být vaším poskytovatelem identity zodpovědný za MFA. Například pokud jste nakonfigurovali službu AD FS tak, aby vyžadovala ověřování pomocí čipové karty před přístupem k Azure AD, [zabezpečení cloudových prostředků pomocí azure Multi-Factor Authentication a AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci AD FS na odešlete deklarace identity do Azure AD. Když se uživatel pokusí o aktivaci role, PIM ověří, že MFA již bylo ověřeno pro uživatele, jakmile obdrží příslušné deklarace identity.

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
