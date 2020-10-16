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
ms.openlocfilehash: 5035e511b0f3a105a047738fed1c5cc17e88d086
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114626"
---
# <a name="teams-interoperability"></a>Spolupráce týmů

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou komunikovat s týmy v rámci sdílení hlasu, videa a obrazovky.

Tato vzájemná funkční spolupráce umožňuje vytvářet vlastní aplikace Azure, které spojují uživatele s týmy na schůzkách. Uživatelé vašich vlastních aplikací nemusí mít k dispozici Azure Active Directory identit nebo týmů, aby mohli tuto funkci využívat. To je ideální pro zavedení zaměstnanců (kteří můžou být obeznámeni s týmy) a externími uživateli (pomocí vlastního prostředí aplikací), a to do bezproblémového prostředí pro schůzky. To vám umožňuje sestavovat podobné možnosti jako v následujícím příkladu:

1. Zaměstnanci používají týmy k naplánování schůzky
2. Vaše vlastní aplikace komunikační služby používá rozhraní API Microsoft Graph k přístupu k podrobnostem o schůzce.
3. Podrobnosti o schůzce se sdílí s externími uživateli prostřednictvím vlastní aplikace.
4. Externí uživatelé používají vlastní aplikaci k připojení k pracovním postupům (prostřednictvím komunikačních služeb, které volají klientské knihovny).

Architektura vysoké úrovně pro tento případ použití vypadá takto: 

![Architektura pro týmovou spolupráci](..//media/call-flows/teams-interop.png)

I když některé týmy vyhovují funkcím, jako je například vystouplý režim, a užitečných místnosti budou k dispozici pouze pro uživatele týmů, vaše vlastní aplikace bude mít přístup k základnímu možnosti sdílení zvuku, videa a obrazovky schůzky.

Když se uživatel komunikačních služeb připojí k pracovním postupům, zobrazí se v týmu týmy zobrazované jméno poskytnuté prostřednictvím volající klientské knihovny. Uživatel komunikačních služeb bude jinak považován za anonymního uživatele v týmech. Vaše vlastní aplikace by měla vzít v úvahu ověřování uživatelů a další bezpečnostní opatření k ochraně schůzek týmů. Nezapomeňte na důsledky zabezpečení při povolování anonymních uživatelů při připojování ke schůzkám a pomocí [Průvodce zabezpečením týmů](https://docs.microsoft.com/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) nakonfigurovat možnosti, které jsou k dispozici pro anonymní uživatele.

Upozorňujeme, že uživatelé komunikačních služeb můžou připojit schůzky plánovaných týmů, pokud jsou v [nastavení schůzky](https://docs.microsoft.com/microsoftteams/meeting-settings-in-teams)povolené anonymní spojení.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení volající aplikace ke schůzce týmu](../../quickstarts/voice-video-calling/get-started-teams-interop.md)
