---
title: 'Proxy aplikací Azure AD: Historie verzí | Dokumentace Microsoftu'
description: Tento článek obsahuje seznam všech verzí Proxy aplikací Azure AD a popisuje nové funkce a opravené problémy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b54d57a13f0f2b47633a25f1a44efdfa17d11a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595060"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy aplikací Azure AD: Historie vydaných verzí
Tento článek obsahuje seznam verzí a funkce Proxy aplikací Azure Active Directory (Azure AD), které byly vydány. Tým služby Azure AD pravidelně aktualizuje Proxy aplikace s novými funkcemi a funkce. Konektory Proxy aplikací se automaticky aktualizují po vydání nové verze.

Tady je seznam souvisejících prostředků:

Resource |  Podrobnosti
--------- | --------- |
Jak povolit Proxy aplikací | Požadavky na povolení Proxy aplikací a instalace a registrace konektoru jsou popsané v tomto [kurzu](application-proxy-add-on-premises-application.md).
Principy konektorů Proxy aplikací Azure AD | Další informace o [connector management](application-proxy-connectors.md) a jak konektory [automatický upgrade](application-proxy-connectors.md#automatic-updates).
Stažení konektoru Proxy aplikace Azure AD |  [Stáhněte si nejnovější connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stav verze

20. září 2018: Všeobecně dostupné ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Přidání podpory protokolu WebSocket pro QlikSense aplikace. Další informace o tom, jak integrovat QlikSense Proxy aplikací, najdete v tomto [návod](application-proxy-qlik.md). 
- Vylepšení Průvodce instalací, aby bylo snazší konfigurace odchozího proxy serveru. 
- Pro konektory nastavit jako výchozím protokolem TLS 1.2. 
- Přidání nové smlouvy licence s koncovým uživatelem (EULA).  

### <a name="fixed-issues"></a>Oprava potíží

- Je opravená chyba, která způsobila některé nevracení paměti v konektoru.
- Aktualizovat verzi služby Azure Service Bus, která zahrnuje opravy chyb pro konektor vypršení časového limitu problémy.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stav verze

19. ledna 2018: Všeobecně dostupné ke stažení

### <a name="fixed-issues"></a>Oprava potíží

- Přidání podpory pro vlastní domény, které vyžadují překlad domény v souboru cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stav verze 

25. května 2017: Všeobecně dostupné ke stažení 

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 

Lepší kontrolu nad limity konektory odchozí připojení. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stav verze

15. dubna 2017: Všeobecně dostupné ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Zjednodušená registrace a správa pomocí méně požadované porty. Proxy aplikací teď vyžaduje otevření pouze dva standardní odchozích portech: 443 a 80. Proxy aplikací dál používat jenom odchozí připojení, takže stále není nutné žádné součásti v hraniční síti. Podrobnosti najdete v tématu naše [dokumentaci konfigurační](application-proxy-add-on-premises-application.md).  
- Pokud se podporuje externí proxy nebo bránou firewall, teď můžete otevřít vaše síť DNS místo rozsah IP adres. Služby Proxy aplikace vyžadovat připojení ke službě *. msappproxy.net a *. servicebus.windows.net pouze.


## <a name="earlier-versions"></a>Starší verze

Pokud používáte vyšla verze konektoru Proxy aplikace dříve než 1.5.36.0, aktualizujte na nejnovější verzi, abyste získali nejnovější funkce plně podporované.

## <a name="next-steps"></a>Další kroky
- Další informace o [vzdáleného přístupu k místním aplikacím přes Azure AD Application Proxy](application-proxy.md).
- Pokud chcete začít používat Proxy aplikací, najdete v článku [kurzu: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací](application-proxy-add-on-premises-application.md).
