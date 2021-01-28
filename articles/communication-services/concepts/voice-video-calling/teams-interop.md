---
title: Týmy, které se účastní interoperability
titleSuffix: An Azure Communication Services concept document
description: Zapojit se do týmů na schůzkách
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 29eafcae9442215e23e80b946fc35314e23100d3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937246"
---
# <a name="teams-interoperability"></a>Interoperabilita Teams

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou komunikovat s týmy v rámci sdílení hlasu, videa a obrazovky.

Tato vzájemná funkční spolupráce umožňuje vytvářet vlastní aplikace Azure, které spojují uživatele s týmy na schůzkách. Uživatelé vašich vlastních aplikací nemusí mít k dispozici Azure Active Directory identit nebo týmů, aby mohli tuto funkci využívat. To je ideální pro zavedení zaměstnanců (kteří můžou být obeznámeni s týmy) a externími uživateli (pomocí vlastního prostředí aplikací), a to do bezproblémového prostředí pro schůzky. To vám umožňuje sestavovat podobné možnosti jako v následujícím příkladu:

1. Zaměstnanci používají týmy k naplánování schůzky
2. Vaše vlastní aplikace komunikační služby používá rozhraní API Microsoft Graph k přístupu k podrobnostem o schůzce.
3. Podrobnosti o schůzce se sdílí s externími uživateli prostřednictvím vlastní aplikace.
4. Externí uživatelé používají vlastní aplikaci k připojení k pracovním postupům (prostřednictvím komunikačních služeb, které volají klientské knihovny).

Architektura vysoké úrovně pro tento případ použití vypadá takto: 

![Architektura pro týmovou spolupráci](..//media/call-flows/teams-interop.png)

I když některé týmy vyhovují funkcím, jako je například vystouplý režim, a užitečných místnosti budou k dispozici pouze pro uživatele týmů, vaše vlastní aplikace bude mít přístup k základnímu možnosti sdílení zvuku, videa a obrazovky schůzky.

Když se uživatel komunikačních služeb připojí k pracovním postupům, zobrazí se v týmu týmy zobrazované jméno poskytnuté prostřednictvím volající klientské knihovny. Uživatel komunikačních služeb bude jinak považován za anonymního uživatele v týmech. Vaše vlastní aplikace by měla vzít v úvahu ověřování uživatelů a další bezpečnostní opatření k ochraně schůzek týmů. Nezapomeňte na důsledky zabezpečení při povolování anonymních uživatelů při připojování ke schůzkám a pomocí [Průvodce zabezpečením týmů](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) nakonfigurovat možnosti, které jsou k dispozici pro anonymní uživatele.

Uživatelé komunikačních služeb mohou připojit schůze plánovaných týmů, pokud jsou v [nastavení schůzky](/microsoftteams/meeting-settings-in-teams)povoleny anonymní spojení.

## <a name="teams-in-government-clouds-gcc"></a>Týmy v cloudech pro státní správu (RSZ)
Spolupráce s komunikačními službami Azure se v současnosti nepovoluje týmům v nasazeních pomocí [Microsoft 365ch státních institucí (RSZ)](/MicrosoftTeams/plan-for-government-gcc) . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení volající aplikace ke schůzce v Teams](../../quickstarts/voice-video-calling/get-started-teams-interop.md)