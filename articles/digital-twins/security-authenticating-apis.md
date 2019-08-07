---
title: Vysvětlení ověřování rozhraní API pro digitální vlákna Azure | Microsoft Docs
description: Použití digitálních vláken Azure k připojení a ověřování v rozhraních API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: lyhughes
ms.openlocfilehash: c0b4b6a13143f613bec64c8507f1726e2450be44
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815553"
---
# <a name="connect-and-authenticate-to-apis"></a>Připojení a ověřování v rozhraních API

Digitální vlákna Azure používá k ověřování uživatelů a ochraně aplikací Azure Active Directory (Azure AD). Azure AD podporuje ověřování pro nejrůznější moderní architektury. Všechny tyto protokoly jsou založené na standardních protokolech OAuth 2,0 nebo OpenID Connect. Kromě toho můžou vývojáři použít Azure AD k vytváření aplikací pro jednoho tenanta a obchodní aplikace (LOB). Vývojáři můžou k vývoji víceklientské aplikací použít taky Azure AD.

Přehled služby Azure AD najdete na [stránce základy](https://docs.microsoft.com/azure/active-directory/fundamentals/index) , kde najdete podrobné návody, koncepty a rychlé starty.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Podrobné pokyny a snímky obrazovky najdete v [tomto rychlém](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)startu.

Služba Azure AD podporuje [pět scénářů primárních aplikací](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) :

* Jednostránkové aplikace (SPA): Uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
* Webový prohlížeč do webové aplikace: Uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
* Nativní aplikace do webového rozhraní API: Nativní aplikace, která běží na telefonu, tabletu nebo počítači, musí ověřit uživatele a získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Webové aplikace do webového rozhraní API: Webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Démon nebo serverová aplikace do webového rozhraní API: Aplikace démona nebo serverová aplikace bez webového uživatelského rozhraní potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.

Knihovna ověřování Windows Azure nabízí mnoho způsobů, jak získat tokeny služby Active Directory. Podrobnosti o knihovně a ukázkách kódu najdete v [tomto článku](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Volání digitálních vláken z webového rozhraní API střední vrstvy

Když vývojáři naprogramují řešení digitálních vláken, obvykle vytvoří aplikaci střední vrstvy nebo rozhraní API. Aplikace nebo rozhraní API pak zavolá pro podřízené rozhraní API digitální vlákna. Aby bylo možné podporovat tuto standardní architekturu webového řešení, nezapomeňte nejprve uživatele:

1. Ověřování pomocí aplikace střední vrstvy

1. Při ověřování se získal token jménem OAuth 2,0.

1. Získaný token se pak použije k ověřování nebo volání rozhraní API, která jsou dále podřízená za použití toku za běhu.

Pokyny k orchestraci služby Flow najdete v tématu [tok OAuth 2,0 za](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)běhu. Můžete si také zobrazit ukázky kódu při [volání webového rozhraní API pro příjem dat](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Další postup

Pokud chcete konfigurovat a testovat digitální vlákna Azure pomocí procesu implicitního grantu OAuth 2,0, přečtěte si téma [Configure post](./how-to-configure-postman.md).

Pokud se chcete dozvědět víc o zabezpečení digitálních vláken Azure, přečtěte si téma [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).