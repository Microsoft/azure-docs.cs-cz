---
title: Registrace aplikací Azure Active Directory pro Azure API pro FHIR
description: V tomto kurzu se dozvíte, které aplikace je nutné zaregistrovat pro Azure API pro FHIR a FHIR Server pro Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: aa95dc5cc052fbff6c553de50f4f52dc5df850a5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398109"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registrace aplikací Azure Active Directory pro Azure API pro FHIR

Máte několik možností konfigurace, ze kterých si můžete vybrat, když nastavujete rozhraní API Azure pro FHIR nebo server FHIR pro Azure (OSS). V případě Open Source budete muset vytvořit vlastní registraci aplikace prostředků. Pro Azure API pro FHIR se tato aplikace prostředků vytvoří automaticky.

## <a name="application-registrations"></a>Registrace aplikací

Aby mohla aplikace pracovat s Azure AD, musí být zaregistrovaná. V kontextu serveru FHIR existují dva druhy registrací aplikací, které je možné diskutovat:

1. Registrace aplikací prostředků
1. Registrace klientských aplikací.

**Zdrojové aplikace** jsou reprezentace v Azure AD rozhraní API nebo prostředku, který je zabezpečený pomocí Azure AD, konkrétně se jedná o rozhraní Azure API pro FHIR. Aplikace prostředků pro rozhraní API Azure pro FHIR se vytvoří automaticky při zřízení služby, ale pokud používáte Open-Source Server, budete muset [zaregistrovat aplikaci prostředků](register-resource-azure-ad-client-app.md) v Azure AD. Tato aplikace prostředků bude mít identifikátor URI identifikátoru. Doporučuje se, aby tento identifikátor URI byl stejný jako identifikátor URI serveru FHIR. Tento identifikátor URI by měl být použit jako `Audience` pro server FHIR. Klientská aplikace může požádat o přístup k tomuto serveru FHIR při žádosti o token.

*Klientské aplikace* jsou registrace klientů, kteří budou žádat o tokeny. V protokolu OAuth 2,0 se často rozlišuje aspoň tři různé typy aplikací:

1. **Důvěrné klienty** , označované také jako webové aplikace ve službě Azure AD. Důvěrné klienty jsou aplikace, které používají [tok autorizačního kódu](../active-directory/azuread-dev/v1-protocols-oauth-code.md) k získání tokenu jménem přihlášeného uživatele, který prezentuje platné přihlašovací údaje. Nazývají se považovat za důvěrné klienty, protože můžou uchovávat tajný klíč a při výměně ověřovacího kódu pro tokeny můžou mít tento tajný klíč ve službě Azure AD. Vzhledem k tomu, že důvěrné klienty se můžou ověřit pomocí tajného klíče klienta, jsou důvěryhodní více než veřejní klienti a můžou mít delší životnost tokenů a musí mít k dispozici obnovovací token. Přečtěte si podrobnosti o tom, jak [zaregistrovat důvěrného klienta](register-confidential-azure-ad-client-app.md). Všimněte si, že je důležité zaregistrovat adresu URL odpovědi, na které klient obdrží autorizační kód.
1. **Veřejné klienty**. Jedná se o klienty, kteří nemůžou uchovávat tajný klíč. Obvykle se jedná o aplikaci v mobilním zařízení nebo o jednu stránku JavaScriptu, kde by uživatel mohl zjistit tajný klíč v klientovi. Veřejné klienty používají také tok autorizačního kódu, ale nemůžou při získání tokenu prezentovat tajný klíč a můžou mít kratší dobu životnosti a nemají obnovovací token. Přečtěte si podrobnosti o [registraci veřejného klienta](register-public-azure-ad-client-app.md).
1. Klienti služeb. Tito klienti získávají tokeny jménem sebe (ne jménem uživatele) pomocí [toku přihlašovacích údajů klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Obvykle reprezentují aplikace, které přistupují k serveru FHIR neinteraktivním způsobem. Příkladem může být proces ingestování. Při použití klienta služby není nutné spouštět proces získání tokenu s voláním `/authorize` koncového bodu. Klient služby může přejít rovnou ke `/token` koncovému bodu a prezentovat ID klienta a tajný klíč klienta pro získání tokenu. Přečtěte si podrobnosti o [registraci klienta služby](register-service-azure-ad-client-app.md) .

## <a name="next-steps"></a>Další kroky

V tomto přehledu jste prošli typy registrace aplikací, které možná budete potřebovat, abyste mohli pracovat s rozhraním API pro FHIR.

V závislosti na nastavení se podívejte na pokyny k registraci aplikací.

* [Registrace aplikace prostředků](register-resource-azure-ad-client-app.md)
* [Registrace důvěrné klientské aplikace](register-confidential-azure-ad-client-app.md)
* [Registrace veřejné klientské aplikace](register-public-azure-ad-client-app.md)
* [Registrace aplikace služby](register-service-azure-ad-client-app.md)

Po zaregistrování aplikací můžete nasadit rozhraní API Azure pro FHIR.

>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-powershell-quickstart.md)