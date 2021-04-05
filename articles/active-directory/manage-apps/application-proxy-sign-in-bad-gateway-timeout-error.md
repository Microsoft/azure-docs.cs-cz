---
title: Nepovedlo se získat přístup k této chybě podnikové aplikace s aplikací proxy aplikace
description: Řešení běžných potíží s přístupem k aplikacím Azure Proxy aplikací služby AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9fb68643c48c685194fa7ba1f1e5050d2d3cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254927"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Při použití aplikace proxy aplikací nejde získat přístup k této podnikové aplikaci.

Tento článek vám pomůže při řešení běžných problémů pro "tuto podnikovou aplikaci nelze použít" v aplikaci Azure Proxy aplikací služby AD.

## <a name="overview"></a>Přehled

Pokud se zobrazí tato chyba, vyhledejte stavový kód na chybové stránce. Tento kód je pravděpodobný jeden z následujících stavových kódů:

- **Časový limit brány**: služba proxy aplikací nemůže kontaktovat konektor. Tato chyba obvykle označuje problém s přiřazením konektoru, samotným konektorem nebo síťovými pravidly, která se nachází v konektoru.
- **Chybná brána**: konektor nemůže získat přístup k back-endové aplikaci. Tato chyba může znamenat chybnou konfiguraci aplikace.
- **Zakázáno**: uživatel nemá oprávnění pro přístup k aplikaci. K této chybě může dojít, když uživatel není přiřazen k aplikaci v Azure Active Directory, nebo pokud na back-endu uživatel nemá oprávnění pro přístup k aplikaci.

Chcete-li najít kód, podívejte se na text v levém dolním rohu chybové zprávy pro pole Stavový kód. Podívejte se také na další tipy v dolní části stránky.

![Příklad: Chyba časového limitu brány](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Podrobnosti o tom, jak řešit hlavní příčinu těchto chyb a další podrobnosti o navrhovaných opravách, najdete v příslušné části.

## <a name="gateway-timeout-errors"></a>Chyby časového limitu brány

K vypršení časového limitu brány dojde, když se služba pokusí spojit s konektorem a nedokáže v časovém intervalu. Tato chyba je obvykle způsobena aplikací přiřazenou skupině konektorů bez pracovních konektorů nebo některé porty vyžadované konektorem nejsou otevřeny.

## <a name="bad-gateway-errors"></a>Chybné chyby brány

Chybná chyba brány indikuje, že konektor nemůže získat přístup k back-endové aplikaci. Ujistěte se, že jste publikovali správnou aplikaci. Běžné chyby, které způsobují tuto chybu:

- Překlep nebo omyl v interní adrese URL
- Nepublikuje se kořen aplikace. Například publikování, `http://expenses/reimbursement` ale pokus o přístup `http://expenses`
- Problémy s konfigurací vynuceného delegování protokolu Kerberos (KCD)
- Problémy s back-end aplikací

## <a name="forbidden-errors"></a>Zakázané chyby

Pokud se zobrazí chyba zakázaná, uživatel není přiřazený k aplikaci. Tato chyba může být buď v Azure Active Directory, nebo v back-endové aplikaci.

Informace o tom, jak přiřadit uživatele k aplikaci v Azure, najdete v [dokumentaci ke konfiguraci](application-proxy-add-on-premises-application.md#test-the-application).

Pokud ověříte, že je uživatel přiřazený k aplikaci v Azure, zkontrolujte konfiguraci uživatele v back-endové aplikaci. Pokud používáte ověřování pomocí protokolu Kerberos omezené delegování/integrované ověřování systému Windows, přečtěte si pokyny na stránce věnované řešení potíží s KCD.

## <a name="check-the-applications-internal-url"></a>Ověřit interní adresu URL aplikace

V prvním rychlém kroku dvakrát zkontrolujte a opravte interní adresu URL tak, že aplikaci otevřete prostřednictvím **podnikových aplikací** a pak vyberete nabídku **proxy aplikací** . Ověřte, jestli interní adresa URL je ta, kterou používá vaše místní síť pro přístup k aplikaci.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Ověřte, že je aplikace přiřazená do skupiny pracovních konektorů.

Chcete-li ověřit, zda je aplikace přiřazena ke skupině pracovních konektorů:

1. Otevřete aplikaci na portálu tak, že přejdete na **Azure Active Directory**, kliknete na **podnikové aplikace** a pak na **všechny aplikace.** Otevřete aplikaci a v nabídce vlevo vyberte **proxy aplikace** .
1. Podívejte se do pole Skupina konektoru. Pokud ve skupině nejsou žádné aktivní konektory, zobrazí se upozornění. Pokud se nezobrazí žádná upozornění, přejděte na a ověřte, jestli jsou povolené všechny [požadované porty](application-proxy-add-on-premises-application.md) .
1. Pokud se zobrazuje nesprávná skupina konektorů, vyberte v rozevíracím seznamu správnou skupinu a potvrďte, že se už nezobrazí žádná upozornění. Pokud se zobrazí zamýšlená skupina konektorů, klikněte na zprávu upozornění a otevřete stránku se správou konektoru.
1. Tady je několik způsobů, jak podrobněji prozkoumat:

   - Přesunout aktivní konektor do skupiny: Pokud máte aktivní konektor, který by měl patřit do této skupiny a má řadu pohledů na cílovou back-end aplikaci, můžete konektor přesunout do přiřazené skupiny. Provedete to tak, že kliknete na konektor. V poli Skupina konektoru vyberte správnou skupinu pomocí rozevíracího seznamu a klikněte na Uložit.
   - Stažení nového konektoru pro tuto skupinu: na této stránce můžete získat odkaz pro [stažení nového konektoru](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Nainstalujte konektor na počítač s přímým dohledem na back-endové aplikaci. Konektor se obvykle nainstaluje na stejný server jako aplikace. Pomocí odkazu ke stažení konektoru Stáhněte konektor do cílového počítače. Potom klikněte na konektor a pomocí rozevíracího seznamu "skupina konektorů" se ujistěte, že patří do správné skupiny.
   - Prozkoumat neaktivní konektor: Pokud se konektor zobrazí jako neaktivní, nemůže se připojit ke službě. K této chybě obvykle dochází v důsledku blokování některých požadovaných portů. Chcete-li tento problém vyřešit, přejděte na příkaz a ověřte, zda jsou povoleny všechny požadované porty.

Po použití těchto kroků se ujistěte, že je aplikace přiřazena ke skupině s pracovními konektory, otestujte aplikaci znovu. Pokud to pořád nefunguje, pokračujte k další části.

## <a name="check-all-required-ports-are-open"></a>Ověřte, že jsou otevřené všechny požadované porty.

Ověřte, že jsou otevřené všechny požadované porty. Požadované porty najdete v části otevřené porty v [kurzu: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md). Pokud jsou všechny požadované porty otevřené, přejděte k další části.

## <a name="check-for-other-connector-errors"></a>Vyhledat další chyby konektoru

Pokud žádný z výše uvedeného problému nevyřešil, je dalším krokem hledání problémů nebo chyb pomocí samotného konektoru. V [dokumentu řešení potíží](./application-proxy-troubleshoot.md#connector-errors)se můžete podívat na některé běžné chyby.

Můžete se také podívat přímo na protokoly konektoru a identifikovat případné chyby. Mnohé z chybových zpráv sdílejí konkrétní doporučení pro opravy. Informace o tom, jak zobrazit protokoly, najdete v [dokumentaci ke konektorům](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Další řešení

Pokud výše uvedený problém nevyřešil, existuje několik různých možných příčin. Postup identifikace problému:

Pokud je vaše aplikace nakonfigurovaná tak, aby používala integrované ověřování systému Windows (IWA), otestujte aplikaci bez jednotného přihlašování. Pokud ne, přejděte k dalšímu odstavci. Chcete-li kontrolovat aplikaci bez jednotného přihlašování, otevřete aplikaci prostřednictvím **podnikových aplikací** a přejděte do nabídky **jednotného přihlašování** . Změňte rozevírací seznam z možnosti integrované ověřování systému Windows na zakázáno jednotné přihlašování Azure AD.

Nyní otevřete prohlížeč a pokuste se o přístup k aplikaci znovu. Měli byste být vyzváni k ověření a k načtení do aplikace. Pokud je možné provést ověření, je problém s konfigurací vynuceného delegování protokolu Kerberos (KCD), která umožňuje jednotné přihlašování. Další informace najdete na stránce Poradce při potížích s KCD.

Pokud se zobrazí chyba, přejděte na počítač, ve kterém je konektor nainstalovaný, otevřete prohlížeč a pokuste se připojit k interní adrese URL používané pro aplikaci. Konektor funguje jako jiný klient ze stejného počítače. Pokud se nemůžete spojit s aplikací, prozkoumejte, proč tento počítač není schopen se připojit k aplikaci, nebo použijte konektor na serveru, který má přístup k aplikaci.

Pokud se k aplikaci dostanete z tohoto počítače, vyhledejte problémy nebo chyby pomocí samotného konektoru. V [dokumentu řešení potíží](application-proxy-troubleshoot.md#connector-errors)se můžete podívat na některé běžné chyby. Můžete se také podívat přímo na protokoly konektoru a identifikovat případné chyby. Mnohé z našich chybových zpráv můžou sdílet konkrétnější doporučení pro opravy. Informace o tom, jak zobrazit protokoly, najdete v [dokumentaci ke konektorům](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Další kroky

[Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)