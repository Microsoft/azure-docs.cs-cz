---
title: Problémy s přihlášením k aplikaci vytvořené vlastním | Microsoft Docs
description: Běžné chyby, které by mohly způsobit, že se nebudete moct přihlásit k aplikaci, kterou jste vyvinuli pomocí Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfc2a39c6bd3b68df7feb978d2548ad67631235
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84759126"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problémy s přihlašováním k aplikaci vytvořené pomocí vlastního vývoje

Došlo k několika chybám, které by mohly způsobit, že se nebudete moct přihlásit k aplikaci. Největší důvod, proč se uživatelé setkali s tímto problémem, jsou špatně nakonfigurované aplikace.

## <a name="errors-related-to--misconfigured-apps"></a>Chyby související s nesprávně nakonfigurovanými aplikacemi

* Ověřte, jak se obě konfigurace na portálu shodují s tím, co máte ve své aplikaci. Konkrétně Porovnejte ID klienta/aplikace, adresy URL odpovědi, tajné klíče klienta a klíče a identifikátor URI ID aplikace.

* Porovnejte prostředek, ke kterému požadujete přístup, v kódu s nakonfigurovanými oprávněními na kartě **požadované prostředky** , abyste se ujistili, že budete požadovat jenom prostředky, které jste nakonfigurovali.

* Podobné chyby nebo problémy najdete v tématu [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) .

## <a name="next-steps"></a>Další kroky

[Příručka pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Souhlas a integrace aplikací do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Oprávnění a souhlas v koncovém bodu Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
