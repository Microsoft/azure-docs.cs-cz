---
title: Zaregistrovat aplikaci s koncovým bodem v2.0 Azure AD pomocí portálu | Microsoft Docs
description: Postup registrace aplikace se společností Microsoft pro povolení přihlášení a přístup ke službám Microsoft pomocí koncového bodu v2.0
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 7c24271553f131e67711a3e8d914049e704e919e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Postup registrace aplikace s koncovým bodem v2.0
Sestavit aplikaci, která přijímá jak osobní účet Microsoft (MSA) & pracovní nebo školní účet (Azure AD) přihlásit, budete nejprve muset zaregistrovat aplikaci se společností Microsoft. V tomto okamžiku nebudete moci používat všechny existující aplikace, které jste uzavřeli s Azure AD nebo MSA - budete muset vytvořit nové.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0. Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Přejděte na portál pro registraci aplikace Microsoft
První, přejděte na portál pro registraci aplikace Microsoft v [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Přihlaste se pomocí buď osobní nebo pracovní nebo školní účet Microsoft. Pokud nemáte buď, zaregistrujte si nový osobní účet.

Provést? By měl nyní se díváte v seznamu aplikací Microsoftu, což je pravděpodobně prázdná. Umožňuje změnit.

Klikněte na tlačítko **přidat aplikaci**a pojmenujte ho.  Na portálu přiřadí globálně jedinečné ID aplikace, které budete používat později v kódu aplikace.  Pokud vaše aplikace obsahuje serverovou komponentu potřebného přístupových tokenů pro volání rozhraní API (vezměte v úvahu: Office, Azure nebo vlastní webové rozhraní API), budete chtít vytvořit **tajný klíč aplikace** i zde.

Dál přidejte **platformy** , aplikace bude používat.

* Pro webové aplikace, zadejte **identifikátor URI pro přesměrování** kde lze odesílat zprávy přihlášení.
* Pro mobilní aplikace poznamenejte výchozí přesměrování, které se automaticky vytvoří identifikátor URI.
* Pro webové rozhraní API se pro vás automaticky vytvoří výchozí obor pro přístup k rozhraní Web API. Můžete přidat další obory pomocí **přidat obor** tlačítko. Můžete také přidat všechny aplikace, které předem oprávnění používat vašeho webového rozhraní API pomocí **předem oprávnění aplikací** formuláře. 

Volitelně můžete přizpůsobit vzhled a chování stránky přihlášení v **profil** části. Ujistěte se, klikněte na **Uložit** než budete pokračovat.

> [!NOTE]
> Při vytváření aplikace pomocí [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), aplikace bude zaregistrována v domácí klientovi účtu, který používáte k přihlášení k portálu. To znamená, že nemůže zaregistrovat aplikaci v klientovi služby Azure AD pomocí osobního účtu Microsoft. Pokud chcete explicitně zaregistrovat aplikaci v konkrétní klienta, přihlaste se pomocí účtu, který původně vytvořil v něm.


## <a name="build-a-quickstart-app"></a>Sestavení aplikace rychlý start
Teď, když máte aplikaci Microsoft, můžete dokončit jeden z kurzů v2.0 rychlý start.  Zde je několik doporučení:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

