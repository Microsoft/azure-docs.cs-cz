---
title: Azure Front Door
description: V tomto článku se dozvíte, jak nakonfigurovat hlavičku zabezpečení prostřednictvím modulu pravidel na frontách Azure.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 4d698375488d4dac551f0028883fc4e18a10d8ef
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323522"
---
# <a name="add-security-headers-with-rules-engine"></a>Přidání záhlaví zabezpečení s modulem pravidel

Implementací hlaviček zabezpečení zabráníte ohrožení zabezpečení založeného na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy nebo X-frame-Options. Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.

Následující příklad ukazuje, jak přidat hlavičku Content-Security-Policy do všech příchozích požadavků, které odpovídají cestě definované ve složce Route, ke které je přidružena konfigurace modulu pravidel. Tady povolujeme skripty z našeho důvěryhodného webu, aby je bylo **https://apis.contoso.com** možné spustit v naší aplikaci.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Přidání záhlaví Content-Security-Policy do Azure Portal

1. Před vytvořením tohoto konkrétního pravidla se dozvíte, jak [vytvořit frontu](quickstart-create-front-door.md) nebo [vytvořit modul pravidel](front-door-tutorial-rules-engine.md) , pokud použijete funkci AFD nebo modul pravidel.

2. Kliknutím na **Přidat** přidejte nové pravidlo. Zadejte název pravidla a potom klikněte na **Přidat**  >  **hlavičku odpovědi**akce.

3. Nastavte operátor, který se má **připojit** , aby se toto záhlaví přidalo jako odpověď na všechny příchozí požadavky do této trasy.

4. Přidejte název hlavičky: **Content-Security-Policy** a definujte hodnoty, které má tato hlavička přijmout. V tomto scénáři zvolíme *"skript-src" sami " https://apis.contoso.com .*

5. Jakmile přidáte všechna pravidla, která byste chtěli do vaší konfigurace, nezapomeňte přejít na upřednostňovanou trasu a přidružit konfiguraci modulu pravidel k vašemu pravidlu směrování. Tento krok je nutný k tomu, aby pravidlo fungovalo. 

![Ukázka portálu](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> V tomto scénáři jsme do tohoto pravidla nepřidali [podmínky shody](front-door-rules-engine-match-conditions.md) . Toto pravidlo bude platit pro všechny příchozí požadavky, které odpovídají cestě definované v pravidle směrování. Pokud byste chtěli, aby se použila jenom pro podmnožinu těchto požadavků, nezapomeňte do tohoto pravidla přidat konkrétní podmínky shody.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [modulu pravidel AFD](front-door-rules-engine.md). 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Další informace o [podmínkách shody](front-door-rules-engine-match-conditions.md) stroje pravidel
- Další informace najdete v [referenčních informacích](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)k rozhraní CLI pro modul AFD Rules. 
- Další informace najdete v [referenčních informacích k PowerShellu](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)pro modul AFD Rules. 
