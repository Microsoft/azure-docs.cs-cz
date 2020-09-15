---
title: 'Kurz: přidání hlaviček zabezpečení s modulem pravidel – přední vrátka Azure'
description: V tomto článku se dozvíte, jak nakonfigurovat hlavičku zabezpečení prostřednictvím modulu pravidel na frontách Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 204a7676fd03466929fc67a0879ff28e0318d21d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085228"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Kurz: Přidání záhlaví zabezpečení s modulem pravidel

V tomto kurzu se dozvíte, jak implementovat hlavičky zabezpečení, aby se zabránilo ohrožení zabezpečení založeného na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy nebo X-frame-Options. Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.

Následující příklad ukazuje, jak přidat hlavičku Content-Security-Policy do všech příchozích požadavků, které odpovídají cestě definované ve složce Route, ke které je přidružena konfigurace modulu pravidel. Tady povolujeme skripty z našeho důvěryhodného webu, aby je bylo **https://apiphany.portal.azure-api.net** možné spustit v naší aplikaci.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Nakonfigurujte zásady Content-Security-Policy v rámci modulu pravidel.

## <a name="prerequisites"></a>Předpoklady

* Před dokončením kroků v tomto kurzu musíte nejprve vytvořit službu Front Door. Další informace najdete v tématu [Rychlý start: Vytvoření služby Front Door](quickstart-create-front-door.md).
* Pokud se jedná o první použití funkce stroje pravidel, přečtěte si téma Jak [nastavit modul pravidel](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Přidání záhlaví Content-Security-Policy do Azure Portal

1. Kliknutím na **Přidat** přidejte nové pravidlo. Zadejte název pravidla a potom klikněte na **Přidat**  >  **hlavičku odpovědi**akce.

1. Nastavte operátor, který se má **připojit** , aby se toto záhlaví přidalo jako odpověď na všechny příchozí požadavky do této trasy.

1. Přidejte název hlavičky: **Content-Security-Policy** a definujte hodnoty, které má tato hlavička přijmout. V tomto scénáři zvolíme *"skript-src" sami " https://apiphany.portal.azure-api.net .*

1. Jakmile přidáte všechna pravidla, která byste chtěli do vaší konfigurace, nezapomeňte přejít na upřednostňovanou trasu a přidružit konfiguraci modulu pravidel k vašemu pravidlu směrování. Tento krok je nutný k tomu, aby pravidlo fungovalo. 

![Ukázka portálu](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> V tomto scénáři jsme do tohoto pravidla nepřidali [podmínky shody](front-door-rules-engine-match-conditions.md) . Toto pravidlo bude platit pro všechny příchozí požadavky, které odpovídají cestě definované v pravidle směrování. Pokud byste chtěli, aby se použila jenom pro podmnožinu těchto požadavků, nezapomeňte do tohoto pravidla přidat konkrétní **podmínky shody** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste nakonfigurovali záhlaví zabezpečení pomocí stroje pravidel. Pokud už pravidlo nechcete, můžete ho odebrat kliknutím na Odstranit pravidlo.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Odstranění pravidla":::

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak nakonfigurovat Firewall webových aplikací pro vaše přední dveře, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Firewall webových aplikací a Front Door](front-door-waf.md)