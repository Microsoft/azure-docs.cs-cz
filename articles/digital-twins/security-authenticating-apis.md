---
title: Pochopení ověřování rozhraní API pomocí digitálních vláken Azure | Microsoft Docs
description: Naučte se, jak se pomocí digitálních vláken Azure připojit k rozhraním API a ověřit s nimi.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: lyhughes
ms.openlocfilehash: ad51fbe7d2f8e8f115adf03d6333c0747765ee43
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338611"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Připojení k rozhraním API a ověřování pomocí nich

Digitální vlákna Azure používá k ověřování uživatelů a ochraně aplikací Azure Active Directory (Azure AD). Azure AD podporuje ověřování pro nejrůznější moderní architektury. Všechny tyto protokoly jsou založené na standardních protokolech OAuth 2,0 nebo OpenID Connect. Kromě toho můžou vývojáři použít Azure AD k vytváření aplikací pro jednoho tenanta a obchodní aplikace (LOB). Vývojáři můžou k vývoji víceklientské aplikací použít taky Azure AD.

Přehled služby Azure AD najdete na [stránce základy](https://docs.microsoft.com/azure/active-directory/fundamentals/) , kde najdete podrobné návody, koncepty a rychlé starty.

> [!TIP]
> Postupujte podle [kurzu](tutorial-facilities-setup.md) a nastavte a spusťte ukázkovou aplikaci pro digitální vlákna Azure.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Podrobné pokyny a snímky obrazovky najdete v [tomto rychlém](../active-directory/develop/quickstart-register-app.md)startu.

Služba Azure AD podporuje [pět scénářů primárních aplikací](../active-directory/develop/v2-app-types.md) :

* Jednostránkové aplikace (SPA): Uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
* Webový prohlížeč do webové aplikace: Uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
* Nativní aplikace do webového rozhraní API: Nativní aplikace, která běží na telefonu, tabletu nebo počítači, musí ověřit uživatele a získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Webové aplikace do webového rozhraní API: Webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Démon nebo serverová aplikace do webového rozhraní API: Aplikace démona nebo serverová aplikace bez webového uživatelského rozhraní potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.

Knihovna ověřování Windows Azure nabízí mnoho způsobů, jak získat tokeny služby Active Directory. Podrobnosti o knihovně a ukázkách kódu najdete [na wikiwebu ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Volání digitálních vláken z webového rozhraní API střední vrstvy

Když vývojáři naprogramují řešení digitálních vláken, obvykle vytvoří aplikaci střední vrstvy nebo rozhraní API. Aplikace nebo rozhraní API pak zavolá pro podřízené rozhraní API digitální vlákna. Aby bylo možné podporovat tuto standardní architekturu webového řešení, nezapomeňte nejprve uživatele:

1. Ověřování pomocí aplikace střední vrstvy

1. Při ověřování se získal token jménem OAuth 2,0.

1. Získaný token se pak použije k ověřování nebo volání rozhraní API, která jsou dále podřízená za použití toku za běhu.

Pokyny k orchestraci služby Flow najdete v tématu [tok OAuth 2,0 za](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)běhu. Můžete si také zobrazit ukázky kódu při [volání webového rozhraní API pro příjem dat](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat a testovat digitální vlákna Azure pomocí procesu implicitního grantu OAuth 2,0, přečtěte si téma [Configure post](./how-to-configure-postman.md).

Pokud se chcete dozvědět víc o zabezpečení digitálních vláken Azure, přečtěte si téma [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).