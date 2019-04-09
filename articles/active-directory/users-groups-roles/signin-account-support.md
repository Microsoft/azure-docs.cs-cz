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
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288588"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Možnosti přihlášení pro účty Microsoftu v Azure Active Directory

Microsoft 365 přihlašovací stránku služby Azure Active Directory (Azure AD) podporuje alespoň jeden pracovní nebo školní účty a účty Microsoft, v závislosti na situaci, pro podporu:

* Aplikace, které přijímají přihlášení z obou typů účtu
* Organizace, které akceptují hosty

## <a name="identification"></a>Identifikace
Můžete zjistit, jestli přihlašovací stránku, kterou používá vaše organizace podporuje účty Microsoft prohlédněte text nápovědy pole uživatelské jméno. Když je text nápovědy ve stavu "E-mail, telefon nebo Skype", přihlašovací stránka podporuje účty Microsoft.

![Rozdíl mezi účet přihlašovací stránky](./media/signin-account-support/ui-prompt.png)

[Další možnosti přihlašování fungují pouze u osobních účtů Microsoft](index.yml) , ale nelze použít pro přihlášení k pracovním nebo školním prostředkům účtu.

## <a name="next-steps"></a>Další postup

[Přizpůsobení přihlášení brandingu](../fundamentals/add-custom-domain.md)