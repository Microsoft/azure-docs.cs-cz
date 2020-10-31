---
title: Jak odesílat události z Auth0 do Azure pomocí Azure Event Grid
description: Jak ukončit události z Auth0 do služeb Azure pomocí Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077859"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrace Azure Event Grid s Auth0

Tento článek popisuje, jak propojit účty Auth0 a Azure vytvořením tématu Event Grid partnera.

Úplný seznam událostí, které Auth0 podporuje, najdete v tématu [kódy typů událostí Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) .

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Odeslání událostí z Auth0 do Azure Event Grid
Odeslání událostí Auth0 do Azure:

1. Povolit poskytovatele prostředků služby Event Grid
1. Nastavení tématu partnera Auth0 na řídicím panelu Auth0
1. Aktivace tématu partner v Azure
1. Přihlášení k odběru událostí z Auth0

Další informace o těchto konceptech najdete v tématu Event Grid [Koncepty](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid
Pokud jste Event Grid dříve nepoužívali, budete muset zaregistrovat Event Grid poskytovatele prostředků. Pokud jste Event Grid dříve používali, přejděte k další části.

Na webu Azure Portal:
1. V nabídce vlevo vyberte odběry.
1. Vyberte předplatné, které používáte pro Event Grid
1. V nabídce vlevo v části nastavení vyberte poskytovatelé prostředků.
1. Najít Microsoft. EventGrid
1. Vybrat registraci
1. Aktualizace pro ujištění, že se stav změní na registrováno

### <a name="set-up-an-auth0-partner-topic"></a>Nastavení tématu partnera Auth0
Součástí procesu integrace je nastavení Auth0 pro použití jako zdroje událostí (Tento krok se děje ve vašem [řídicím panelu Auth0](https://manage.auth0.com/)).

1. Přihlaste se k [řídicímu panelu Auth0](https://manage.auth0.com/).
1. Přejděte k protokolům > datových proudů.
1. Klikněte na + vytvořit Stream.
1. Vyberte Azure Event Grid a zadejte jedinečný název pro nový datový proud.
1. Vytvořte zdroj události zadáním ID předplatného Azure, oblasti Azure a názvu skupiny prostředků. 
1. Klikněte na Uložit.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Aktivace tématu Auth0 partner v Azure
Aktivace tématu Auth0 v Azure umožňuje událostem tok z Auth0 do Azure.

1. Přihlaste se k webu Azure Portal.
1. `Partner Topics`V horní části vyhledejte a klikněte na možnost `Event Grid Partner Topics` služby.
1. Klikněte na téma, které odpovídá streamu, který jste vytvořili na řídicím panelu Auth0.
1. Potvrďte, že `Source` pole odpovídá vašemu účtu Auth0.
1. Klikněte na tlačítko aktivovat.

### <a name="subscribe-to-auth0-events"></a>Přihlášení k odběru událostí Auth0

#### <a name="create-an-event-handler"></a>Vytvoření obslužné rutiny události
K otestování vašeho partnerského tématu budete potřebovat obslužnou rutinu události. Přejít k předplatnému Azure a aktivovat službu, která je podporovaná jako [obslužná rutina události](event-handlers.md) , jako je třeba [funkce Azure Functions](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Přihlášení k odběru vašeho tématu Auth0 partner
Přihlášení k odběru vašeho partnera Auth0 vám umožní sdělit Event Grid, kde chcete, aby Auth0 události.

1. V okně partnerských témat pro integraci Auth0 vyberte + odběr události v horní části.
1. Na stránce Vytvořit odběr události:
    1. Zadejte název odběru události.
    1. Vyberte službu Azure nebo Webhook, který jste vytvořili pro typ koncového bodu.
    1. Postupujte podle pokynů pro konkrétní službu.
    1. Klikněte na Vytvořit.

## <a name="testing"></a>Testování
Vaše integrace tématu Auth0 Partnerská témata s Azure by měla být připravená k použití.

### <a name="verify-the-integration"></a>Ověřit integraci
Chcete-li ověřit, zda integrace funguje podle očekávání:

1. Přihlaste se k řídicímu panelu Auth0.
1. Přejděte k protokolům > datových proudů.
1. Klikněte na datový proud Event Grid.
1. V datovém proudu klikněte na kartu stav. Datový proud by měl být aktivní a Pokud nevidíte žádné chyby, datový proud funguje.

Zkuste [vyvoláním kterékoli z akcí Auth0, které aktivují událost,](https://auth0.com/docs/logs/references/log-event-type-codes) která se má publikovat, aby se zobrazil tok událostí.

## <a name="delivery-attempts-and-retries"></a>Pokusy o doručení a opakování
Události Auth0 se doručují do Azure prostřednictvím mechanismu streamování. Každá událost je odeslána, protože je aktivována v Auth0. Pokud Event Grid nedokáže událost přijmout, Auth0 se znovu pokusí události doručit třikrát. V opačném případě Auth0 zaznamená selhání doručení do svého systému.

## <a name="next-steps"></a>Další kroky

- [Téma Auth0 partner](auth0-overview.md)
- [Témata o partnerech – přehled](partner-events-overview.md)
- [Staňte se Event Grid partnerem](partner-onboarding-overview.md)