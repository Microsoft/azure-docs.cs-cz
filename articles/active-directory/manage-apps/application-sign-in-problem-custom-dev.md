---
title: Problémy s přihlášením k vlastní vyvinuté aplikaci | Dokumenty společnosti Microsoft
description: Běžné chyby, které mohou způsobit, že se nebudete moci přihlásit k aplikaci, kterou jste vyvinuli pomocí služby Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825238"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problémy s přihlášením k vlastní vyvinuté aplikaci

Existuje několik chyb, které by mohly způsobit, že se nebudete moci přihlásit do aplikace. Největším důvodem, proč se lidé setkávají s tímto problémem, jsou nesprávně nakonfigurované aplikace.

## <a name="errors-related-to--misconfigured-apps"></a>Chyby související s nesprávně nakonfigurovanými aplikacemi

* Ověřte, jak konfigurace na portálu odpovídají tomu, co máte ve vaší aplikaci. Konkrétně porovnejte ID klienta nebo aplikace, adresy URL odpovědí, tajné klíče klienta/klíče a identifikátor URI ID aplikace.

* Porovnejte prostředek, ke kterým požadujete přístup v kódu, s nakonfigurovanými oprávněními na kartě **Požadované prostředky** a ujistěte se, že požadujete pouze prostředky, které jste nakonfigurovali.

* Viz [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) pro všechny podobné chyby nebo problémy.

## <a name="next-steps"></a>Další kroky

[Průvodce pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Souhlas a integrace aplikací do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Souhlas a oprávnění pro konvergované aplikace Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Přetečení zásobníku azure a d.](https://stackoverflow.com/questions/tagged/azure-active-directory>)
