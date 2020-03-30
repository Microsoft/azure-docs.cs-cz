---
title: Veřejné a důvěrné klientské aplikace (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Informace o veřejných klientských a důvěrných klientských aplikacích naleznete v knihovně Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084118"
---
# <a name="public-client-and-confidential-client-applications"></a>Veřejné klientské a důvěrné klientské aplikace
Knihovna Microsoft Authentication Library (MSAL) definuje dva typy klientů: veřejné klienty a důvěrné klienty. Tyto dva typy klientů se odlišují podle jejich schopnosti bezpečně se ověřit pomocí autorizačního serveru a zachovat důvěrnost jejich klientských pověření. Naproti tomu Azure AD Ověřování Library (ADAL) používá to, co se nazývá *kontext ověřování* (což je připojení k Azure AD).

- **Důvěrné klientské aplikace** jsou aplikace, které běží na serverech (webové aplikace, aplikace webového rozhraní API nebo dokonce aplikace služby/daemonu). Jsou považovány za obtížně přístupné, a proto jsou schopny udržet aplikaci v tajnosti. Důvěrní klienti mohou uchovávat tajné kódy v době konfigurace. Každá instance klienta má odlišnou konfiguraci (včetně ID klienta a tajného klíče klienta). Tyto hodnoty je obtížné pro koncové uživatele extrahovat. Webová aplikace je nejběžnějším důvěrným klientem. ID klienta je vystaveno prostřednictvím webového prohlížeče, ale tajný klíč je předán pouze v zadním kanálu a nikdy přímo vystaven.

    Důvěrné klientské aplikace: <BR>
    ![Daemon/služba webového rozhraní API webové aplikace](media/msal-client-applications/web-app.png) ![](media/msal-client-applications/web-api.png) ![](media/msal-client-applications/daemon-service.png)

- **Veřejné klientské aplikace** jsou aplikace, které běží na zařízeních nebo stolních počítačích nebo ve webovém prohlížeči. Nejsou důvěryhodné bezpečně zachovat tajné kódy aplikací, takže přístup pouze webová rozhraní API jménem uživatele. (Podporují pouze toky veřejných klientů.) Veřejní klienti nemohou obsahovat tajné kódy v době konfigurace, takže nemají tajné klíče klientů.

    Veřejné klientské aplikace: <BR>
    ![Desktopová](media/msal-client-applications/desktop-app.png) ![aplikace](media/msal-client-applications/browserless-app.png) ![Rozhraní API bez prohlížeče Mobilní aplikace](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> V MSAL.js neexistuje oddělení veřejných a důvěrných klientských aplikací.  MSAL.js představuje klientské aplikace jako aplikace založené na uživatelském agentovi, veřejné klienty, ve kterých je klientský kód spuštěn v uživatelském agentovi, jako je webový prohlížeč. Tito klienti neukládají tajné klíče, protože kontext prohlížeče je otevřeně přístupný.

## <a name="comparing-the-client-types"></a>Porovnání typů klientů
Zde jsou některé podobnosti a rozdíly mezi veřejným klientem a důvěrnými klientskými aplikacemi:

- Oba druhy aplikace udržují mezipaměť tokenů uživatele a mohou získat token tiše (když je token již v mezipaměti tokenu). Důvěrné klientské aplikace mají také mezipaměť tokenů aplikace pro tokeny, které jsou určeny pro samotnou aplikaci.
- Oba typy aplikací spravují uživatelské účty a můžou získat účet z mezipaměti uživatelských tokenů, získat účet z jeho identifikátoru nebo účet odebrat.
- Veřejné klientské aplikace mají čtyři způsoby, jak získat token (čtyři toky ověřování). Důvěrné klientské aplikace mají tři způsoby, jak získat token (a jeden způsob, jak vypočítat adresu URL poskytovatele identity autorizovat koncový bod). Další informace naleznete v [tématu Získání tokenů](msal-acquire-cache-tokens.md).

Pokud jste použili ADAL, můžete si všimnout, že na rozdíl od kontextu ověřování ADAL, v MSAL ID klienta (také volal *ID aplikace* nebo *ID aplikace*) je předán jednou při konstrukci aplikace. Nemusí být předána znovu, když aplikace získá token. To platí jak pro veřejné, tak pro důvěrné klientské aplikace. Konstruktory důvěrných klientských aplikací jsou také předány pověření klienta: tajný klíč, které sdílejí s poskytovatelem identity.

## <a name="next-steps"></a>Další kroky
Projdeme si tyto možnosti:
- [Možnosti konfigurace klientských aplikací](msal-client-application-configuration.md)
- [Vytváření instancí klientských aplikací pomocí MSAL.NET](msal-net-initializing-client-applications.md)
- [Vytváření instancí klientských aplikací pomocí souboru MSAL.js](msal-js-initializing-client-applications.md)
