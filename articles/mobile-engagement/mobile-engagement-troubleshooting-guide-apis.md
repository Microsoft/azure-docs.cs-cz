---
title: Řešení potíží s průvodce – rozhraní API Azure Mobile Engagement.
description: Řešení potíží s příručky pro Azure Mobile Engagement – rozhraní API
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: 8d51aaf5196eabe18d92b34d97e98f9f665f2527
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-guide-for-api-issues"></a>Průvodce řešením potíží pro problémy rozhraní API
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Následují možných problémů se můžete setkat s jak správci komunikovat s Azure Mobile Engagement přes rozhraní API.

## <a name="syntax-issues"></a>Syntaxe problémy
### <a name="issue"></a>Problém
* Chyby syntaxe pomocí rozhraní API (nebo neočekávané chování).

### <a name="causes"></a>Způsobí, že
* Syntaxe problémy:
  * Ujistěte se, že Zkontrolujte syntaxi konkrétní rozhraní API používáte potvrďte, že možnost je k dispozici.
  * Běžné problémy s možností využití rozhraní API je zaměnit rozhraní Reach API a rozhraní API Push (většinu úloh by měl provést s rozhraní Reach API místo Push API). 
  * Další běžné problém s integraci sady SDK a využití rozhraní API je zaměnit klíč SDK a klíč rozhraní API.
  * Skripty, které se připojují k rozhraní API je třeba odeslat data minimálně každých 10 minut nebo připojení bude časový limit (zejména v běžné monitorování API skripty naslouchání pro data). Chcete-li zabránit vypršení časových limitů, máte skript odeslání příkazu ping protokolu XMPP každých 10 minut do relace – udržování připojení se serverem.

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentaci k rozhraní API][Link 4]
* [Informace o protokolu XMPP protokolu](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Nelze provádět stejné akce k dispozici v uživatelském rozhraní Azure Mobile Engagement pomocí rozhraní API
### <a name="issue"></a>Problém
* Akce, která funguje z uživatelského rozhraní Azure Mobile Engagement nefunguje z rozhraní API služby související Azure Mobile Engagement.

### <a name="causes"></a>Způsobí, že
* Potvrzení, že můžete provádět stejné akce z uživatelského rozhraní Azure Mobile Engagement ukazuje, že jste správně spojili tuto funkci Azure Mobile Engagement pomocí sady SDK.

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentace uživatelského rozhraní][Link 1]

## <a name="error-messages"></a>Chybové zprávy
### <a name="issue"></a>Problém
* Kódy chyb pomocí rozhraní API zobrazí za běhu, nebo v protokolech.

### <a name="causes"></a>Způsobí, že
* Tady je seznam složený běžné čísel kódy stavu rozhraní API pro referenční a předběžné řešení problémů:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Další informace najdete v tématech
* [Dokumentace k rozhraní API – pro podrobné chyby na každé konkrétní rozhraní API][Link 4]

## <a name="silent-failures"></a>Tiché selhání
### <a name="issue"></a>Problém
* Rozhraní API akce se nezdaří s žádná chybová zpráva se zobrazí v době běhu nebo v protokolech.

### <a name="causes"></a>Způsobí, že
* Mnoho položek bude v uživatelském rozhraní Azure Mobile Engagement zakázáno, pokud není integrován správně, ale bude nezdaří bez upozornění z rozhraní API, proto nezapomeňte otestovat stejné funkce z uživatelského rozhraní a zkontrolujte, pokud funguje.
* Azure Mobile Engagement a mnoho pokročilých funkcí Azure Mobile Engagement se pokoušíte použít, musí být jednotlivě integrovat do vaší aplikace pomocí sady SDK jako samostatné kroky než budete moci použít.

### <a name="see-also"></a>Další informace najdete v tématech
* [Průvodce odstraňováním potíží s - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

