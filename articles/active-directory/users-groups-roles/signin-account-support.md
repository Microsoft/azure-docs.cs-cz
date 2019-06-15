---
title: Jak lze zjistit, pokud přihlašovací stránku Azure AD přijímá účty Microsoft | Dokumentace Microsoftu
description: Na obrazovce pro zasílání zpráv odráží vyhledávání uživatelské jméno při přihlašování
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60468198"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Možnosti přihlášení pro účty Microsoftu v Azure Active Directory

Microsoft 365 přihlašovací stránku služby Azure Active Directory (Azure AD) podporuje pracovní nebo školní účty a účty Microsoft, ale v závislosti na situaci uživatele, může být jeden nebo druhý nebo obojí. Například na přihlašovací stránku Azure AD podporuje:

* Aplikace, které přijímají přihlášení z obou typů účtu
* Organizace, které akceptují hosty

## <a name="identification"></a>Identifikace
Můžete zjistit, jestli přihlašovací stránku, kterou používá vaše organizace podporuje účty Microsoft prohlédněte text nápovědy pole uživatelské jméno. Když je text nápovědy ve stavu "E-mail, telefon nebo Skype", přihlašovací stránka podporuje účty Microsoft.

![Rozdíl mezi účet přihlašovací stránky](./media/signin-account-support/ui-prompt.png)

[Další možnosti přihlašování fungují pouze u osobních účtů Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ale nelze použít pro přihlášení k pracovním nebo školním prostředkům účtu.

## <a name="next-steps"></a>Další postup

[Přizpůsobení přihlášení brandingu](../fundamentals/add-custom-domain.md)