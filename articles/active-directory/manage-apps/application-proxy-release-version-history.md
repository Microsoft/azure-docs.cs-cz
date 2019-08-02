---
title: 'Proxy aplikací služby AD Azure: Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny verze Azure Proxy aplikací služby AD a popisuje nové funkce a opravené problémy.
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
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693900"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy aplikací služby AD Azure: Historie vydaných verzí
V tomto článku jsou uvedeny verze a funkce služby Azure Active Directory (Azure AD) proxy aplikací, které byly vydány. Tým Azure AD pravidelně aktualizuje proxy aplikace s novými funkcemi a funkcemi. Konektory proxy aplikací se aktualizují automaticky, když se uvolní nová verze. 

Doporučujeme, abyste se ujistili, že pro vaše konektory jsou povolené automatické aktualizace, abyste měli jistotu, že máte nejnovější funkce a opravy chyb. Společnost Microsoft poskytuje přímou podporu pro poslední verzi konektoru a jednu verzi.

Tady je seznam souvisejících prostředků:

Resource |  Podrobnosti
--------- | --------- |
Jak povolit proxy aplikace | V tomto [kurzu](application-proxy-add-on-premises-application.md)jsou popsány předpoklady pro povolení proxy aplikací a instalace a registrace konektoru.
Principy konektorů Proxy aplikací Azure AD | Přečtěte si další informace o [správě konektorů](application-proxy-connectors.md) a o tom, jak konektory [automaticky upgradují](application-proxy-connectors.md#automatic-updates).
Stažení konektoru služby Azure Proxy aplikací služby AD |  [Stáhněte si nejnovější konektor](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stav verze

20. září 2018: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Přidání podpory protokolu WebSocket pro aplikaci QlikSense Další informace o tom, jak integrovat QlikSense s proxy aplikací, najdete v tomto [návodu](application-proxy-qlik.md). 
- Vylepšený průvodce instalací, který usnadňuje konfiguraci odchozího proxy serveru. 
- Jako výchozí protokol pro konektory nastavte TLS 1,2. 
- Přidala se nová licenční smlouva s koncovým uživatelem (EULA).  

### <a name="fixed-issues"></a>Oprava potíží

- Opravili jsme chybu, která způsobila nevracení paměti v konektoru.
- Aktualizace verze Azure Service Bus, která zahrnuje opravu chyby pro problémy s časovým limitem konektoru.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stav verze

19. ledna 2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží

- Přidání podpory pro vlastní domény, které potřebují překlad domény v souboru cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stav verze 

25. května 2017: Vydáno ke stažení 

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 

Vylepšená kontrola nad omezeními odchozího připojení konektorů. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stav verze

15. dubna 2017: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Zjednodušená připojování a správa s menším počtem požadovaných portů. Proxy aplikace nyní vyžaduje otevření pouze dvou standardních odchozích portů: 443 a 80. Proxy aplikace i nadále používá pouze odchozí připojení, takže stále nepotřebujete žádné součásti v DMZ. Podrobnosti najdete v naší [dokumentaci ke konfiguraci](application-proxy-add-on-premises-application.md).  
- Pokud to vaše externí proxy server nebo brána firewall podporuje, můžete teď síť otevřít pomocí DNS místo rozsahu IP adres. Služba proxy aplikací vyžaduje připojení pouze k *. msappproxy.net a *. servicebus.windows.net.


## <a name="earlier-versions"></a>Starší verze

Pokud používáte aplikační konektor proxy verze starší než 1.5.36.0, aktualizujte na nejnovější verzi, abyste měli jistotu, že máte nejnovější plně podporované funkce.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vzdáleném přístupu k místním aplikacím prostřednictvím Azure proxy aplikací služby AD](application-proxy.md).
- Pokud chcete začít používat proxy aplikace, [Přečtěte si kurz: Přidejte místní aplikaci pro vzdálený přístup prostřednictvím proxy](application-proxy-add-on-premises-application.md)aplikací.
