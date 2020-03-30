---
title: Přijímá přihlašovací stránka Azure AD účty Microsoft | Dokumenty společnosti Microsoft
description: Jak zasílání zpráv na obrazovce odráží vyhledávání uživatelského jména během přihlášení
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024290"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Možnosti přihlášení k účtům Microsoft ve službě Azure Active Directory

Přihlašovací stránka Microsoft 365 pro Azure Active Directory (Azure AD) podporuje pracovní nebo školní účty a účty Microsoft, ale v závislosti na situaci uživatele to může být jedno nebo druhé nebo obojí. Například přihlašovací stránka Azure AD podporuje:

* Aplikace, které přijímají přihlášení z obou typů účtů
* Organizace, které přijímají hosty

## <a name="identification"></a>Identifikace
Můžete zjistit, zda přihlašovací stránka, kterou vaše organizace používá, podporuje účty Microsoft, můžete zjistit pomocí textu nápovědy v poli uživatelského jména. Pokud text nápovědy říká "E-mail, telefon nebo Skype", přihlašovací stránka podporuje účty Microsoft.

![Rozdíl mezi přihlašovacími stránkami účtu](./media/signin-account-support/ui-prompt.png)

[Další možnosti přihlášení fungují pouze pro osobní účty Microsoft,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) ale nelze je použít k přihlášení k prostředkům pracovního nebo školního účtu.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení přihlašovací značky](../fundamentals/add-custom-domain.md)