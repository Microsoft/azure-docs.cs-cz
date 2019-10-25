---
title: Pochopení ověřování rozhraní API pomocí digitálních vláken Azure | Microsoft Docs
description: Naučte se, jak se pomocí digitálních vláken Azure připojit k rozhraním API a ověřit s nimi.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 53b53fa5185ade87dfdd21f0805641657d49d676
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800209"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Připojení k rozhraním API a ověřování pomocí nich

Digitální vlákna Azure používá k ověřování uživatelů a ochraně aplikací Azure Active Directory (Azure AD). Azure AD podporuje ověřování pro nejrůznější moderní architektury. Všechny tyto protokoly jsou založené na standardních protokolech OAuth 2,0 nebo OpenID Connect. Kromě toho můžou vývojáři použít Azure AD k vytváření aplikací pro jednoho tenanta a obchodní aplikace (LOB). Vývojáři můžou k vývoji víceklientské aplikací použít taky Azure AD.

Přehled služby Azure AD najdete na [stránce základy](https://docs.microsoft.com/azure/active-directory/fundamentals/) , kde najdete podrobné návody, koncepty a rychlé starty.

> [!TIP]
> Postupujte podle [kurzu](tutorial-facilities-setup.md) a nastavte a spusťte ukázkovou aplikaci pro digitální vlákna Azure.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Podrobné pokyny a snímky obrazovky najdete v [tomto rychlém](../active-directory/develop/quickstart-register-app.md)startu.

Služba Azure AD podporuje [pět scénářů primárních aplikací](../active-directory/develop/v2-app-types.md) :

* Jednostránkové aplikace (SPA): uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
* Webový prohlížeč do webové aplikace: uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
* Nativní aplikace do webového rozhraní API: nativní aplikace, která běží na telefonu, tabletu nebo počítači, musí ověřit uživatele a získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Webová aplikace do webového rozhraní API: webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Démon nebo serverová aplikace do webového rozhraní API: aplikace démona nebo serverová aplikace bez webového uživatelského rozhraní musí získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.

> [!IMPORTANT]
> Digitální vlákna Azure podporuje obě následující knihovny ověřování:
> * Novější [Knihovna Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Knihovna Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Volání digitálních vláken z webového rozhraní API střední vrstvy

Když vývojáři naprogramují řešení digitálních vláken, obvykle vytvoří aplikaci střední vrstvy nebo rozhraní API. Aplikace nebo rozhraní API pak zavolá pro podřízené rozhraní API digitální vlákna. Aby bylo možné podporovat tuto standardní architekturu webového řešení, nezapomeňte nejprve uživatele:

1. Ověřování pomocí aplikace střední vrstvy

1. Při ověřování se získal token jménem OAuth 2,0.

1. Získaný token se pak použije k ověřování nebo volání rozhraní API, která jsou dále podřízená za použití toku za běhu.

Pokyny k orchestraci služby Flow najdete v tématu [tok OAuth 2,0 za](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)běhu. Můžete si také zobrazit ukázky kódu při [volání webového rozhraní API pro příjem dat](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat a testovat digitální vlákna Azure pomocí procesu implicitního grantu OAuth 2,0, přečtěte si téma [Configure post](./how-to-configure-postman.md).

Pokud se chcete dozvědět víc o zabezpečení digitálních vláken Azure, přečtěte si téma [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).