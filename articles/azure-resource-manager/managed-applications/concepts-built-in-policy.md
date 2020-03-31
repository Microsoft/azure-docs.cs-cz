---
title: Nasazení přidružení pro spravovanou aplikaci pomocí zásad
description: Přečtěte si o nasazení přidružení pro spravovanou aplikaci pomocí služby Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650939"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Nasazení přidružení pro spravovanou aplikaci pomocí zásad Azure

Zásady Azure se můžou použít k nasazení přidružení k přidružení prostředků ke spravované aplikaci. V tomto článku popisujeme předdefinované zásady, které nasazuje přidružení a jak můžete použít tuto zásadu.

## <a name="built-in-policy-to-deploy-associations"></a>Integrované zásady pro nasazení přidružení

Nasazení přidružení pro spravovanou aplikaci je předdefinovaná zásada, kterou lze použít k nasazení přidružení k přidružení prostředku ke spravované aplikaci. Zásady přijímá tři parametry:

- ID spravované aplikace – toto ID je ID prostředku spravované aplikace, ke kterému musí být prostředky přidruženy.
- Typy prostředků, které je třeba přidružit – Tyto typy prostředků jsou seznamem typů prostředků, které mají být přidruženy ke spravované aplikaci. Ke spravované aplikaci můžete přidružit více typů prostředků pomocí stejné zásady.
- Předpona názvu přidružení - Tento řetězec je předponou, která má být přidána k názvu vytvářeného prostředku přidružení. Výchozí hodnota je "DeployedByPolicy".

Zásada používá deployifnotexists hodnocení. Spustí se poté, co poskytovatel prostředků zpracoval požadavek na vytvoření nebo aktualizaci prostředků vybraných typů prostředků a vyhodnocení vrátilo stavový kód úspěchu. Poté se prostředek přidružení nasadí pomocí nasazení šablony.
Další informace o přidružení, najdete [v tématu Azure vlastní zprostředkovatelé připojení prostředků](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Použití integrované zásady nasazení přidružení 

### <a name="prerequisites"></a>Požadavky
Pokud spravovaná aplikace potřebuje oprávnění k předplatnému k provedení akce, nasazení zásad prostředku přidružení by nefungovalo bez udělení oprávnění.

### <a name="policy-assignment"></a>Přiřazení zásad
Chcete-li použít předdefinované zásady, vytvořte přiřazení zásad a přiřaďte přidružení nasazení pro zásady spravované aplikace. Po úspěšném přiřazení zásady bude zásada identifikovat nekompatibilní prostředky a nasadit přidružení pro tyto prostředky.

![Přiřazení předdefinovaných zásad](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vývoje vlastních zprostředkovatelů prostředků Azure, zkuste se jich zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte ```azure-custom-providers``` značku, abyste získali rychlou odpověď!

## <a name="next-steps"></a>Další kroky

V tomto článku se dozvíte o použití předdefinované zásady k nasazení přidružení. Další informace najdete v těchto článcích:

- [Koncepty: Registrace prostředků vlastních poskytovatelů Azure](../custom-providers/concepts-resource-onboarding.md)
- [Kurz: Připojení zdrojů s vlastními poskytovateli](../custom-providers/tutorial-resource-onboarding.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků a nasazení vlastních prostředků](../custom-providers/create-custom-provider.md)
- [Postup: Přidání vlastních akcí do rozhraní API Azure REST](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do rozhraní API Azure REST](../custom-providers/custom-providers-resources-endpoint-how-to.md)
