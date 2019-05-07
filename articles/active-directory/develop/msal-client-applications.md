---
title: Klientské aplikace (knihovna Microsoft Authentication Library) | Azure
description: Další informace o veřejných klienta a důvěrnému klientovi aplikací v Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077137"
---
# <a name="public-client-and-confidential-client-applications"></a>Veřejné klienta a důvěrné klientské aplikace
Microsoft Authentication Library (MSAL) definuje dva typy klientů: veřejní klienti a důvěrní klienti. Dva klientské typy rozlišují podle jejich schopnost bezpečně ověřovat pomocí autorizačního serveru a zachovat utajení svých přihlašovacích údajů klienta.  Azure AD Authentication Library (ADAL) je naproti tomu koncept kontext ověřování (což je připojení ke službě Azure AD).

- **Důvěrné klientské aplikace** jsou aplikace, které běží na serverech (Web Apps, webové rozhraní API nebo i služby/démona aplikace). Jsou považovány za obtížný přístup a proto dokáže udržování tajný klíč aplikace. Důvěrní klienti budou moct uložení konfigurace času tajných kódů. Každá instance klienta má odlišné konfigurace (včetně ID klienta a tajný klíč). Tyto hodnoty je obtížné pro koncové uživatele k extrakci. Webová aplikace je nejběžnější důvěrnému klientovi. ID klienta je přístupný přes webový prohlížeč, ale je předán pouze v rámci zpětného kanálu a nikdy přímo vystavený tajný kód.

    Důvěrné klientské aplikace: <BR>
    ![Webová aplikace](media/msal-client-applications/web-app.png) ![webového rozhraní API](media/msal-client-applications/web-api.png) ![proces démon nebo službu](media/msal-client-applications/daemon-service.png)

- **Aplikace veřejným klientem** jsou aplikace, které běží na zařízení nebo stolní počítače nebo ve webovém prohlížeči. Nejsou důvěryhodné, aby bezpečně uchovávat tajné kódy aplikace a proto pouze přístup k webovým rozhraním API jménem uživatele (podporují se jenom toky veřejným klientem). Veřejní klienti nebudou moct uložení konfigurace času tajných kódů a díky tomu mají žádný tajný kód klienta.

    Veřejné klientské aplikace: <BR>
    ![Aplikace klasické pracovní plochy](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilní aplikace](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> V MSAL.js neexistuje žádná rozdělení veřejných, důvěrné klientské aplikace.  MSAL.js představuje klientské aplikace jako na základě uživatelského agenta aplikace veřejným klientem, ve kterém kód klienta je prováděn v uživatelský agent jako je webový prohlížeč.  Tito klienti nebudou ukládat tajné klíče, protože kontext prohlížeče je otevřeně dostupné.

## <a name="comparing-the-client-types"></a>Porovnání typů klienta
Existují některé commonalities a rozdíly mezi veřejným klientem a důvěrnému klientovi aplikace:

- Oba typy aplikací udržovat mezipaměť tokenu uživatele a můžete získat token tiše (v případech, kdy je token již v mezipaměť tokenu). Důvěrné klientské aplikace také mít mezipaměť tokenu v aplikaci pro tokeny, které jsou pro samotné aplikace.
- Obě Správa uživatelských účtů a můžete získat účty z mezipaměti token uživatele, uživatelského účtu z identifikátoru nebo odeberte účet.
- Veřejné klientské aplikace máte čtyři způsoby získávání tokenu (čtyři toky ověřování), zatímco důvěrnému klientovi aplikace mít tři (a jednu metodu pro výpočet adresy URL zprostředkovatele identity zajistí autorizaci koncového bodu). Další informace najdete v tématu scénáře a získávání tokenů.

Pokud jste použili v minulosti ADAL, můžete si všimnout, že rozporu kontextu ADAL pro ověřování, MSAL klienta, který ID (označuje také jako ID aplikace nebo ID aplikace) je předán jednou při konstrukci aplikace a nadále již nebudou musí opakovat při získávání tokenu. To platí i pro veřejné a důvěrné klientské aplikace. Konstruktory důvěrné klientské aplikace jsou předány také přihlašovací údaje pro klienta: tajný kód sdílejí u zprostředkovatele identity.

## <a name="next-steps"></a>Další postup
Další informace o:
- [Možnosti konfigurace klientské aplikace](msal-client-application-configuration.md)
- [Vytvoření klientské aplikace pomocí MSAL.NET](msal-net-initializing-client-applications.md).
- [Vytvoření klientské aplikace s využitím MSAL.js](msal-js-initializing-client-applications.md).
