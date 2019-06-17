---
title: Klientské aplikace (knihovna Microsoft Authentication Library) | Azure
description: Další informace o veřejných klienta a důvěrnému klientovi aplikací v Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430828"
---
# <a name="public-client-and-confidential-client-applications"></a>Veřejné klienta a důvěrné klientské aplikace
Microsoft Authentication Library (MSAL) definuje dva typy klientů: veřejní klienti a důvěrní klienti. Dva klientské typy rozlišují podle jejich schopnost bezpečně ověřovat pomocí autorizačního serveru a zachovat utajení svých přihlašovacích údajů klienta. Naproti tomu Azure AD Authentication Library (ADAL) používá, co se nazývá *kontextu ověřování* (což je připojení ke službě Azure AD).

- **Důvěrné klientské aplikace** jsou aplikace, které běží na serverech (webové aplikace, aplikace webového rozhraní API nebo dokonce i aplikace služby/démona). Budou považovány za obtížné přístup a z tohoto důvodu umožňující zachovat tajný klíč aplikace. Důvěrní klienti může obsahovat tajné kódy konfigurace času. Každá instance klienta má odlišné konfigurace (včetně ID klienta a tajný kód klienta). Tyto hodnoty je obtížné pro koncové uživatele k extrakci. Webová aplikace je nejběžnější důvěrnému klientovi. ID klienta je přístupný přes webový prohlížeč, ale je předán pouze v rámci zpětného kanálu a nikdy přímo vystavený tajný kód.

    Důvěrné klientské aplikace: <BR>
    ![Webová aplikace](media/msal-client-applications/web-app.png) ![webového rozhraní API](media/msal-client-applications/web-api.png) ![proces démon nebo službu](media/msal-client-applications/daemon-service.png)

- **Aplikace veřejným klientem** jsou aplikace, které běží na zařízení nebo stolní počítače nebo ve webovém prohlížeči. Nejsou důvěryhodné, aby bezpečně uchovávat tajné kódy aplikace, aby pouze přístup k webovým rozhraním API jménem uživatele. (Podporují pouze veřejné klienta toky.) Veřejní klienti neudrží konfigurace času tajné kódy, abyste nemuseli tajné klíče klienta.

    Veřejné klientské aplikace: <BR>
    ![Aplikace klasické pracovní plochy](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilní aplikace](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> V MSAL.js neexistuje žádná rozdělení veřejných, důvěrné klientské aplikace.  MSAL.js představuje klientské aplikace jako aplikace založené na agentovi na uživatele, veřejné klienty, ve kterých je klientský kód proveden v uživatelského agenta, jako je webový prohlížeč. Tito klienti nejsou tajných kódů uložit, protože kontext prohlížeče je otevřeně dostupné.

## <a name="comparing-the-client-types"></a>Porovnání typů klienta
Tady jsou některé podobnosti a rozdíly mezi veřejným klientem a důvěrnému klientovi aplikace:

- Oba typy aplikace udržovat mezipaměť tokenu uživatele a můžete získat token tiše (Pokud je již token mezipaměť tokenu). Důvěrnému klientovi aplikace mají také mezipaměť tokenu v aplikaci pro tokeny, které jsou pro samotné aplikace.
- Oba typy aplikací Správa uživatelských účtů a můžete získat účet z mezipaměti token uživatele, uživatelského účtu z identifikátoru nebo odeberte účet.
- Aplikace veřejným klientem mají čtyři způsoby, jak získat token (čtyři toky ověřování). Důvěrnému klientovi aplikace mají tři způsoby, jak získat token (a jedním ze způsobů pro výpočet adresy URL zprostředkovatele identity zajistí autorizaci koncového bodu). Další informace najdete v tématu [získávání tokenů](msal-acquire-cache-tokens.md).

Pokud jste použili ADAL, můžete si všimnout, že na rozdíl od kontextu ADAL pro ověřování, v MSAL ID klienta (také nazývané *ID aplikace* nebo *ID aplikace*) je předán jednou při konstrukci aplikace. Není nutné znovu předána, když aplikace získá token. To platí pro obě veřejných, důvěrné klientské aplikace. Konstruktory důvěrnému klientovi aplikace jsou předány také přihlašovací údaje pro klienta: tajný kód sdílejí u zprostředkovatele identity.

## <a name="next-steps"></a>Další postup
Další informace o:
- [Možnosti konfigurace klientské aplikace](msal-client-application-configuration.md)
- [Vytvoření klientské aplikace pomocí MSAL.NET](msal-net-initializing-client-applications.md)
- [Vytvoření klientské aplikace s využitím MSAL.js](msal-js-initializing-client-applications.md)
