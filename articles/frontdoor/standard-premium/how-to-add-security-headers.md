---
title: Konfigurace záhlaví zabezpečení se sadou pravidel pro Azure front-end Standard/Premium (Preview)
description: Tento článek poskytuje pokyny k použití sady pravidel ke konfiguraci hlaviček zabezpečení.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099107"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Konfigurace záhlaví zabezpečení se sadou pravidel pro Azure front-end Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

V tomto článku se dozvíte, jak implementovat hlavičky zabezpečení, aby nedocházelo k ohrožením zabezpečení založenému na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy nebo X-frame-Options. Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.

Následující příklad ukazuje, jak přidat hlavičku Content-Security-Policy do všech příchozích požadavků, které odpovídají cestě v trase. Tady povolujeme skripty z našeho důvěryhodného webu, aby je bylo **https://apiphany.portal.azure-api.net** možné spustit v naší aplikaci.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

* Než budete moct nakonfigurovat konfiguraci hlaviček zabezpečení, musíte nejdřív vytvořit přední dveře. Další informace najdete v tématu [Rychlý start: Vytvoření služby Front Door](create-front-door-portal.md).
* Přečtěte si, jak [nastavit sadu pravidel](how-to-configure-rule-set.md) , pokud jste předtím nepoužívali funkci sady pravidel.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Přidání záhlaví Content-Security-Policy do Azure Portal

1. Přejít na profil služby Azure front-end Standard/Premium a v části nastavení vyberte **sadu pravidel** **.**

1. Vyberte **Přidat** a přidejte novou sadu pravidel. Zadejte pro pravidlo **název** a potom zadejte **název** pravidla. Vyberte **přidat akci** a pak vyberte **Hlavička odpovědi**.

1. Nastavte operátor, který se má **připojit** , aby se toto záhlaví přidalo jako odpověď na všechny příchozí žádosti pro tuto trasu.

1. Přidejte název hlavičky: **Content-Security-Policy** a definujte hodnoty, které má tato hlavička přijmout. V tomto scénáři zvolíme *"skript-src" sami https://apiphany.portal.azure-api.net*.

1. Jakmile přidáte všechna pravidla, která byste chtěli do vaší konfigurace, nezapomeňte přidružení sady pravidel k trase. Tento krok je *nutný* , pokud chcete, aby sada pravidel mohla provést akci. 

> [!NOTE]
> V tomto scénáři jsme do tohoto pravidla nepřidali [podmínky shody](concept-rule-set-match-conditions.md) . Toto pravidlo bude platit pro všechny příchozí požadavky, které odpovídají cestě definované v přidružené trase. Pokud byste chtěli, aby se použila jenom pro podmnožinu těchto požadavků, nezapomeňte do tohoto pravidla přidat konkrétní **podmínky shody** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="deleting-a-rule"></a>Odstranění pravidla

V předchozích krocích jste nakonfigurovali záhlaví Content-Security-Policy pomocí sady pravidel. Pokud už pravidlo nechcete, můžete vybrat název sady pravidel a pak vybrat odstranit pravidlo. 

### <a name="deleting-a-rule-set"></a>Odstranění sady pravidel

Pokud chcete sadu pravidel odstranit, ujistěte se, že ji před odstraněním zrušíte přidružení ze všech tras. Podrobné pokyny k odstranění sady pravidel najdete v tématu [Konfigurace sady pravidel](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nakonfigurovat Firewall webových aplikací pro přední dveře, najdete v tématu [Brána Firewall webových aplikací a přední dveře](../../web-application-firewall/afds/afds-overview.md).
