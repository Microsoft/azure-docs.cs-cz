---
title: Registrace aplikace s koncovým bodem Azure AD verze 2.0 | Microsoft Docs
description: Zde se dozvíte, jak registrovat aplikaci u Microsoftu, aby bylo povolené přihlášení a přístup ke službám Microsoftu pomocí koncového bodu Azure AD verze 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 20e19664963f66954f9d46a1b596a34b7d744f48
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093219"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Rychlý start: Registrace aplikace s koncovým bodem v2.0 Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Pokud budete chtít sestavit aplikaci, která přijímá přihlášení přes osobní účet Microsoft (MSA) i pracovní nebo školní účet (Azure AD), budete muset zaregistrovat aplikaci pomocí koncového bodu Azure Active Directory (Azure AD) verze 2.0. V tuto chvíli není možné používat žádné existující aplikace, které případně máte, s Azure AD nebo MSA – bude potřeba vytvořit úplně novou.

Ne všechny funkce a scénáře služby Azure AD jsou podporovány koncovým bodem verze 2.0. Pokud chcete zjistit, jestli máte použít koncový bod verze 2.0, přečtěte si informace o [omezeních verze 2.0](active-directory-v2-limitations.md).

> [!NOTE]
> Registrujete novou aplikaci? Vyzkoušejte nové prostředí **Registrace aplikací (Preview)** na webu Azure Portal. Začněte tím, že si přečtete téma [Registrace aplikace (Preview)](quickstart-register-app.md).

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Krok 1: Přihlaste se k portálu pro registraci aplikací Microsoftu

1. Přejděte na portál pro registraci aplikací Microsoftu na [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Přihlaste se pomocí osobního nebo pracovního nebo školního účtu Microsoft. Pokud ani jeden nemáte, zaregistrujte si nový osobní účet.
1. Hotovo? Měli byste teď vidět seznam aplikací Microsoftu, který je pravděpodobně prázdný. To teď změníme.

## <a name="step-2-register-an-app"></a>Krok 2: Registrace aplikace

1. Vyberte **Přidat aplikaci** a pojmenujte ji.
    Portál přiřadí vaší aplikaci globálně jedinečné ID aplikace, které použijete později ve vašem kódu. Pokud vaše aplikace obsahuje komponentu na straně serveru, který potřebuje přístupové tokeny pro volání rozhraní API (myslíte, že: Office, Azure nebo vašeho vlastního webového rozhraní API), bude potřeba vytvořit **tajný klíč aplikace** i zde.
1. Dále přidejte **Platformy**, které vaše aplikace bude používat.
    * Pro webové aplikace zadejte **Identifikátor URI pro přesměrování**, kam se můžou posílat zprávy přihlášení.
    * U mobilních aplikací si poznamenejte výchozí identifikátor URI pro přesměrování, který se automaticky vytvořil.
    * Pro webová rozhraní API se pro vás automaticky vytvoří výchozí obor pro přístup k webovému rozhraní API.
        Můžete přidat další obory pomocí tlačítka **Přidat obor**. Můžete také pomocí formuláře **Předběžně autorizované aplikace** přidat jakékoliv aplikace předběžně autorizované k používání vašeho webového rozhraní API.
1. Volitelně můžete přizpůsobit vzhled a chování stránky přihlášení v oddílu **Profil**. 
1. **Uložte** změny, než budete pokračovat.

> [!NOTE]
> Když zaregistrujete aplikaci pomocí [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), aplikace se zaregistruje v domovském tenantovi účtu, který používáte k přihlášení na portál. To znamená, že nelze zaregistrovat aplikaci ve vašem tenantovi Azure AD pomocí osobního účtu Microsoft. Pokud si výslovně přejete registrovat aplikaci v konkrétním tenantovi, přihlaste se pomocí účtu původně vytvořeného v tomto tenantovi.

## <a name="next-steps"></a>Další postup

Teď, když máte aplikaci Microsoft, můžete provést jeden rychlý start (quickstart2) verze 2.0. Zde je několik doporučení:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
