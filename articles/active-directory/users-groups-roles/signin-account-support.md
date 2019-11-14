---
title: Přijměte na přihlašovací stránce Azure AD účty Microsoft | Microsoft Docs
description: Jak zpráva na obrazovce odráží vyhledávání uživatelského jména během přihlašování
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024290"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Možnosti přihlášení pro účty Microsoft v Azure Active Directory

Přihlašovací stránka Microsoft 365 pro Azure Active Directory (Azure AD) podporuje pracovní nebo školní účty a účty Microsoft, ale v závislosti na situaci uživatele to může být jedna nebo druhá. Například přihlašovací stránka Azure AD podporuje:

* Aplikace, které přijímají přihlášení z obou typů účtů
* Organizace, které přijímají hosty

## <a name="identification"></a>Identifikace
Můžete zjistit, jestli přihlašovací stránka, kterou vaše organizace používá, podporuje účty Microsoft, a to tak, že se podíváte na text nápovědy v poli uživatelské jméno. Pokud text nápovědy říká "E-mail, telefon nebo Skype", přihlašovací stránka podporuje účty Microsoft.

![Rozdíl mezi přihlašovacími stránkami účtu](./media/signin-account-support/ui-prompt.png)

[Další možnosti přihlašování fungují jenom pro osobní účty Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ale nedají se použít k přihlašování k prostředkům v pracovním nebo školním účtu.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení brandingu přihlašování](../fundamentals/add-custom-domain.md)