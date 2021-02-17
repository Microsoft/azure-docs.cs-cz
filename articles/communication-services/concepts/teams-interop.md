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
ms.openlocfilehash: 34d2a18d2e556b7eb9916d058c4d80fe0956ba55
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554748"
---
# <a name="teams-interoperability"></a>Interoperabilita Teams

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou s týmy komunikovat prostřednictvím hlasu, videa, chatu a sdílení obrazovky.

Vzájemná funkční spolupráce týmů umožňuje vytvářet vlastní aplikace, které spojují uživatele s týmy na schůzkách. Uživatelé vašich vlastních aplikací nemusí mít k dispozici Azure Active Directory identit nebo týmů, aby mohli tuto funkci využívat. To je ideální pro zavedení zaměstnanců (kteří můžou být obeznámeni s týmy) a externími uživateli (pomocí vlastního prostředí aplikací), a to do bezproblémového prostředí pro schůzky. Příklad:

1. Zaměstnanci používají týmy k naplánování schůzky 
1. Podrobnosti o schůzce se sdílí s externími uživateli prostřednictvím vlastní aplikace.
   * **Použití Graph API** Vaše vlastní aplikace komunikační služby používá rozhraní API Microsoft Graph k přístupu k podrobnostem o schůzce, které se mají sdílet. 
   * **Používání dalších možností** Například odkaz na schůzku můžete zkopírovat z kalendáře v Microsoft Teams.
1. Externí uživatelé používají vlastní aplikaci k připojení k pracovním postupům (prostřednictvím volání komunikačních služeb a klientských knihoven chatu).

Architektura vysoké úrovně pro tento případ použití vypadá takto: 

![Architektura pro týmovou spolupráci](./media/call-flows/teams-interop.png)

I když některé týmy vyhovují funkcím, jako je například vystouplý režim, a užitečných místnosti budou k dispozici pouze pro uživatele týmů, vaše vlastní aplikace bude mít přístup k základním funkcím zvukového zvuku, videa, chatu a sdílení obrazovky.

Když se uživatel komunikačních služeb připojí k pracovním postupům, zobrazí se v týmu týmy zobrazované jméno poskytnuté prostřednictvím volající klientské knihovny. Uživatel komunikačních služeb bude jinak považován za anonymního uživatele v týmech.  Vaše vlastní aplikace by měla vzít v úvahu ověřování uživatelů a další bezpečnostní opatření k ochraně schůzek týmů. Nezapomeňte na důsledky zabezpečení při povolování anonymních uživatelů při připojování ke schůzkám a pomocí [Průvodce zabezpečením týmů](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) nakonfigurovat možnosti, které jsou k dispozici pro anonymní uživatele.

Komunikace týmů služby Communications je v současnosti ve verzi Private Preview. V případě všeobecně dostupného přístupu se uživatelům komunikačních služeb bude zacházeno jako "uživatelé s externím přístupem". Přečtěte si další informace o externím přístupu při [volání, chatu a spolupráci s lidmi mimo vaši organizaci v Microsoft Teams](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations).

Uživatelé komunikačních služeb mohou připojit schůze plánovaných týmů, pokud jsou v [nastavení schůzky](/microsoftteams/meeting-settings-in-teams)povoleny anonymní spojení.

## <a name="teams-in-government-clouds-gcc"></a>Týmy v cloudech pro státní správu (RSZ)
Interoperabilita komunikačních služeb Azure není v současnosti kompatibilní s nasazeními týmů pomocí [Microsoft 365ch státních institucí (RSZ)](/MicrosoftTeams/plan-for-government-gcc) . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení volající aplikace ke schůzce v Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
