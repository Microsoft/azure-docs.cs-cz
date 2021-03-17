---
title: Týmy, které se účastní interoperability
titleSuffix: An Azure Communication Services concept document
description: Zapojit se do týmů na schůzkách
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b7d992961fc165ce643bd0d6aba4d8a9bda1d38e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495585"
---
# <a name="teams-interoperability"></a>Interoperabilita Teams

> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat [interoperabilitu tenanta týmů](../concepts/teams-interop.md), vyplňte [Tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou s týmy komunikovat prostřednictvím hlasu, videa, chatu a sdílení obrazovky.

Vzájemná funkční spolupráce týmů umožňuje vytvářet vlastní aplikace, které spojují uživatele s týmy na schůzkách. Uživatelé vašich vlastních aplikací nemusí mít k dispozici Azure Active Directory identit nebo týmů, aby mohli tuto funkci využívat. To je ideální pro zavedení zaměstnanců (kteří můžou být obeznámeni s týmy) a externími uživateli (pomocí vlastního prostředí aplikací), a to do bezproblémového prostředí pro schůzky. Například:

1. Zaměstnanci používají týmy k naplánování schůzky 
1. Podrobnosti o schůzce se sdílí s externími uživateli prostřednictvím vlastní aplikace.
   * **Použití Graph API** Vaše vlastní aplikace komunikační služby používá rozhraní API Microsoft Graph k přístupu k podrobnostem o schůzce, které se mají sdílet. 
   * **Používání dalších možností** Například odkaz na schůzku můžete zkopírovat z kalendáře v Microsoft Teams.
1. Externí uživatelé používají vlastní aplikaci k připojení k pracovním postupům (prostřednictvím volání komunikačních služeb a klientských knihoven chatu).

Architektura vysoké úrovně pro tento případ použití vypadá takto: 

![Architektura pro týmovou spolupráci](./media/call-flows/teams-interop.png)

I když některé týmy vyhovují funkcím, jako je například vystouplý režim, a užitečných místnosti budou k dispozici pouze pro uživatele týmů, vaše vlastní aplikace bude mít přístup k základním funkcím zvukového zvuku, videa, chatu a sdílení obrazovky. Chat na schůzku bude mít přístup k vlastnímu uživateli aplikace, když se nachází ve volání. Nebudou moci odesílat ani přijímat zprávy před připojením nebo po ukončení volání. 

Když se uživatel komunikačních služeb připojí k pracovním postupům, zobrazí se v týmu týmy zobrazované jméno poskytnuté prostřednictvím volající klientské knihovny. Uživatel komunikačních služeb bude jinak považován za anonymního uživatele v týmech.  Vaše vlastní aplikace by měla vzít v úvahu ověřování uživatelů a další bezpečnostní opatření k ochraně schůzek týmů. Nezapomeňte na důsledky zabezpečení při povolování anonymních uživatelů při připojování ke schůzkám a pomocí [Průvodce zabezpečením týmů](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) nakonfigurovat možnosti, které jsou k dispozici pro anonymní uživatele.

Komunikace týmů služby Communications je v současnosti ve verzi Private Preview. V případě všeobecně dostupného přístupu se uživatelům komunikačních služeb bude zacházeno jako "uživatelé s externím přístupem". Přečtěte si další informace o externím přístupu při [volání, chatu a spolupráci s lidmi mimo vaši organizaci v Microsoft Teams](/microsoftteams/communicate-with-users-from-other-organizations).

Uživatelé komunikačních služeb mohou připojit schůze plánovaných týmů, pokud jsou v [nastavení schůzky](/microsoftteams/meeting-settings-in-teams)povoleny anonymní spojení.

## <a name="teams-in-government-clouds-gcc"></a>Týmy v cloudech pro státní správu (RSZ)
Interoperabilita komunikačních služeb Azure není v současnosti kompatibilní s nasazeními týmů pomocí [Microsoft 365ch státních institucí (RSZ)](/MicrosoftTeams/plan-for-government-gcc) . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení volající aplikace ke schůzce v Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)