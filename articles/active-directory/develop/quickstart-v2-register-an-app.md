---
title: Registrace aplikace s koncovým bodem v2.0 Azure AD pomocí portálu | Dokumentace Microsoftu
description: Postup registrace aplikace v Microsoft pro povolení přihlášení a přístup ke službám Microsoftu pomocí koncového bodu v2.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506546"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Postup registrace aplikace s koncovým bodem v2.0
Vytvořit aplikaci, která přijímá i osobní účet Microsoft (MSA) a pracovní nebo školní přihlašovací účet (Azure AD), budete nejdřív muset registrace aplikace v Microsoft. V tuto chvíli není možné používat všechny existující aplikace, které máte uzavřeny s Azure AD nebo MSA -, je potřeba vytvořit úplně novou.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány bodem v2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Přejděte na portál pro registraci aplikace Microsoft
Nejprve přejděte do portálu pro registraci aplikací Microsoftu na [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Přihlaste se pomocí buď osobní nebo pracovního nebo školního účtu Microsoft. Pokud nemáte buď, zaregistrujte si nový osobní účet.

Hotovo? Můžete by měla hledat v seznamu aplikací Microsoftu, což je pravděpodobně prázdná. Teď Změníme.

Klikněte na tlačítko **přidat aplikaci**a pojmenujte ho. Na portálu, přiřadí se vaše aplikace globálně jedinečné ID aplikace, které použijete později ve vašem kódu. Pokud vaše aplikace obsahuje komponentu na straně serveru, který potřebuje přístupové tokeny pro volání rozhraní API (myslíte: Office, Azure nebo vašeho vlastního webového rozhraní API), budete chtít vytvořit **tajný klíč aplikace** i zde.

V dalším kroku přidejte **platformy** , která vaše aplikace bude používat.

* Pro webové aplikace, zadejte **identifikátor URI pro přesměrování** odešle zprávy přihlášení.
* Pro mobilní aplikace poznamenejte výchozí přesměrování, které pro vás automaticky vytvořit identifikátor URI.
* Pro webová rozhraní API se pro vás automaticky vytvoří výchozí obor pro přístup k webovému rozhraní API. Můžete také přidat další obory pomocí **přidání oboru** tlačítko. Můžete také přidat všechny aplikace, které obsahují předem oprávnění k používání vašeho webového rozhraní API pomocí **předem autorizované aplikace** formuláře. 

Volitelně můžete přizpůsobit vzhled a chování stránky přihlášení v **profilu** oddílu. Ujistěte se, že klikněte na tlačítko **Uložit** než budete pokračovat.

> [!NOTE]
> Při vytváření aplikace s využitím [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), aplikace se zaregistruje v domovském tenantovi účet, který používáte k přihlášení na portál. To znamená, že nelze zaregistrovat aplikaci ve vašem tenantovi Azure AD pomocí osobního účtu Microsoft. Pokud chcete explicitně registrace aplikace v konkrétní tenanta, přihlaste se pomocí účtu původně vytvořil v tomto tenantovi.


## <a name="build-a-quickstart-app"></a>Sestavení aplikace rychlý start
Teď, když máte aplikaci Microsoft, můžete dokončit jeden v2.0 kurzy rychlý start. Zde je několik doporučení:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

