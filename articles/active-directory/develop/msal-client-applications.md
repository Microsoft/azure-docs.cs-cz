---
title: Veřejné a důvěrné klientské aplikace (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si informace o veřejném klientovi a důvěrných klientských aplikacích v knihovně Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063752"
---
# <a name="public-client-and-confidential-client-applications"></a>Veřejné klientské a důvěrné klientské aplikace
Knihovna Microsoft Authentication Library (MSAL) definuje dva typy klientů: veřejné klienty a důvěrné klienty. Oba typy klientů se rozlišují podle možnosti bezpečného ověřování pomocí autorizačního serveru a udržují důvěrnost svých přihlašovacích údajů klienta. Na rozdíl od Azure AD Authentication Library (ADAL) používá s názvem *kontext ověřování* (což je připojení ke službě Azure AD).

- **Důvěrné klientské aplikace** jsou aplikace, které běží na serverech (webové aplikace, aplikace webového rozhraní API nebo i aplikace Service/démon). Považují se za obtížné přístup a z tohoto důvodu může uchovávat tajný klíč aplikace. Důvěrní klienti můžou uchovávat tajné kódy pro čas konfigurace. Každá instance klienta má odlišnou konfiguraci (včetně ID klienta a tajného klíče klienta). Tyto hodnoty jsou pro koncové uživatele obtížné extrahovat. Webová aplikace je nejčastějším tajným klientem. ID klienta se zveřejňuje prostřednictvím webového prohlížeče, ale tajný klíč se předává jenom v zadním kanálu a nikdy se nezveřejňuje.

    Důvěrné klientské aplikace: <BR>
    ![](media/msal-client-applications/web-app.png) ![ ](media/msal-client-applications/web-api.png) ![ Démon/služba webové aplikace Web API](media/msal-client-applications/daemon-service.png)

- **Veřejné klientské aplikace** jsou aplikace, které běží na zařízeních nebo stolních počítačích nebo ve webovém prohlížeči. Nejsou důvěryhodné k bezpečnému zachování tajných klíčů, takže mají přístup pouze k webovým rozhraním API jménem uživatele. (Podporují pouze veřejné toky klientů.) Veřejné klienty nemůžou uchovávat tajné klíče v době konfigurace, takže nemají tajné klíče klienta.

    Veřejné klientské aplikace: <BR>
    ![](media/msal-client-applications/desktop-app.png) ![ ](media/msal-client-applications/browserless-app.png) ![ Mobilní aplikace API pro desktopové aplikace v prohlížeči](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> V MSAL.js neexistují žádné oddělení veřejných a důvěrných klientských aplikací.  MSAL.js představuje klientské aplikace jako aplikace založené na uživatelských agentech, veřejné klienty, v nichž se klientský kód spustí v uživatelském agentovi, jako je webový prohlížeč. Tito klienti neukládají tajné kódy, protože kontext prohlížeče je otevřený.

## <a name="comparing-the-client-types"></a>Porovnání typů klientů
Tady jsou některé podobnosti a rozdíly mezi veřejnými klientskými a důvěrnými klientskými aplikacemi:

- Oba typy aplikací udržují mezipaměť tokenů uživatele a můžou získat token tiše (Pokud je už token v mezipaměti tokenu). Důvěrné klientské aplikace mají také mezipaměť tokenu aplikace pro tokeny, které jsou pro samotnou aplikaci.
- Oba typy aplikací spravují uživatelské účty a můžou získat účet z mezipaměti tokenů uživatele, získat účet z jeho identifikátoru nebo odebrat účet.
- Veřejné klientské aplikace mají čtyři možnosti, jak získat token (čtyři toky ověřování). Důvěrné klientské aplikace mají tři způsoby, jak získat token (a jeden ze způsobů, jak vypočítat adresu URL poskytovatele identity autorizačního bodu). Další informace najdete v tématu [získání tokenů](msal-acquire-cache-tokens.md).

Pokud jste použili ADAL, můžete si všimnout, že na rozdíl od kontextu ověřování ADAL v MSAL se ID klienta (označované také jako *ID aplikace* nebo *ID aplikace*) předává jednou při konstrukci aplikace. Nemusíte ho znovu předávat, když aplikace získá token. To platí pro veřejné i důvěrné klientské aplikace. Pro konstruktory důvěrných klientských aplikací se také předaly přihlašovací údaje klienta: tajný klíč, který sdílí se zprostředkovatelem identity.

## <a name="next-steps"></a>Další kroky
Projdeme si tyto možnosti:
- [Možnosti konfigurace klientské aplikace](msal-client-application-configuration.md)
- [Vytváření instancí klientských aplikací pomocí MSAL.NET](msal-net-initializing-client-applications.md)
- [Vytváření instancí klientských aplikací pomocí MSAL.js](msal-js-initializing-client-applications.md)
