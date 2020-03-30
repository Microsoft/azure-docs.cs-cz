---
title: MFA nebo 2FA a správa privilegovaných identit – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak azure ad privilegované správy identit (PIM) ověřuje vícefaktorové ověřování (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022137"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Vícefaktorové ověřování a správa privilegovaných identit

Doporučujeme, abyste pro všechny správce vyžadovali vícefaktorové ověřování (MFA). Tím se snižuje riziko útoku z důvodu kompromitovanéheslo.

Můžete vyžadovat, aby uživatelé při přihlášení dokončili výzvu s vícenásobným ověřováním. Můžete také vyžadovat, aby uživatelé dokončili výzvu vícefaktorového ověřování při aktivaci role ve službě Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Tímto způsobem, pokud uživatel nedokončil výzvu vícefaktorového ověřování při přihlášení, bude k tomu vyzván správcem privilegovaných identit.

> [!IMPORTANT]
> V tuto chvíli azure multifaktorové ověřování funguje jenom s pracovními nebo školními účty, ne s osobními účty Microsoftu (obvykle osobní účet, který se používá k přihlášení ke službám Microsoftu, jako je Skype, Xbox nebo Outlook.com). Z tohoto důvodu nemůže být oprávněným správcem kdokoli, kdo používá osobní účet, protože k aktivaci svých rolí nemůže používat vícefaktorové ověřování. Pokud tito uživatelé potřebují pokračovat ve správě úloh pomocí účtu Microsoft, povýšit je na stálé správce pro tuto chvíli.

## <a name="how-pim-validates-mfa"></a>Jak PIM ověřuje vícefaktorové ověřování

Existují dvě možnosti pro ověření vícefaktorového ověřování, když uživatel aktivuje roli.

Nejjednodušší možností je spoléhat se na azure vícefaktorové ověřování pro uživatele, kteří aktivují privilegovanou roli. Chcete-li to provést, nejprve zkontrolujte, zda tito uživatelé jsou licencovány, v případě potřeby a zaregistrovali pro Azure Multi-Factor Authentication. Další informace o nasazení azure vícefaktorového ověřování najdete v [tématu Nasazení cloudového vícefaktorového ověřování Azure](../authentication/howto-mfa-getstarted.md). Doporučuje se, ale není vyžadováno, nakonfigurovat Azure AD vynutit vícefaktorové ověřování pro tyto uživatele při přihlášení. Důvodem je, že kontroly vícefaktorového ověřování budou prováděny samotnou správou privilegovaných identit.

Případně pokud uživatelé ověřit místní můžete mít svého poskytovatele identity být zodpovědný za vícefaktorové ověřování. Pokud jste například nakonfigurovali služby AD Federation Services tak, aby před přístupem k Azure AD vyžadovaly ověřování na čipových kartě, [zabezpečení cloudových prostředků pomocí azure multifaktorového ověřování a služby AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci služby AD FS pro odesílání deklarací identity do Azure AD. Když se uživatel pokusí aktivovat roli, správa privilegovaných identit přijme, že vícefaktorové ověřování již bylo pro uživatele ověřeno, jakmile obdrží příslušné deklarace identity.

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD ve správě privilegovaných identit](pim-how-to-change-default-settings.md)
- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)
