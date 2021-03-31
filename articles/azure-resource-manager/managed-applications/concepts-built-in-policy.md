---
title: Nasazení přidružení pro spravovanou aplikaci pomocí zásad
description: Přečtěte si o nasazení přidružení pro spravovanou aplikaci pomocí Azure Policy služby.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650939"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Nasazení přidružení pro spravovanou aplikaci pomocí Azure Policy

Zásady Azure je možné použít k nasazení přidružení pro přidružení prostředků ke spravované aplikaci. V tomto článku jsme popsali předdefinované zásady, které nasazují přidružení a způsob, jakým můžete tyto zásady použít.

## <a name="built-in-policy-to-deploy-associations"></a>Integrovaná zásada pro nasazení přidružení

Nasazení přidružení pro spravovanou aplikaci je předdefinovaná zásada, která se dá použít k nasazení přidružení pro přidružení prostředku ke spravované aplikaci. Zásada akceptuje tři parametry:

- ID spravované aplikace – toto ID je ID prostředku spravované aplikace, ke které se prostředky musí přidružit.
- Typy prostředků, které se mají přidružit – tyto typy prostředků jsou seznam typů prostředků, které se mají přidružit ke spravované aplikaci. Pomocí stejných zásad můžete k spravované aplikaci přidružit více typů prostředků.
- Předpona názvu přidružení – tento řetězec představuje předponu, která se má přidat do názvu prostředku přidružení, který se vytváří. Výchozí hodnota je "DeployedByPolicy".

Tato zásada používá DeployIfNotExists vyhodnocení. Spustí se poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku vybraného typu prostředku a vyhodnocení vrátilo stavový kód úspěchu. Potom se prostředek přidružení nasadí pomocí nasazení šablony.
Další informace o přidruženích najdete v tématu věnovaném [registraci prostředků vlastních zprostředkovatelů Azure](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak používat vestavěnou zásadu nasazení přidružení 

### <a name="prerequisites"></a>Požadavky
Pokud spravovaná aplikace potřebuje k provedení akce oprávnění k předplatnému, nasazení zásad prostředku přidružení by nefungovalo bez udělení oprávnění.

### <a name="policy-assignment"></a>Přiřazení zásady
Pokud chcete použít předdefinovanou zásadu, vytvořte přiřazení zásady a přiřaďte přidružení nasazení pro zásadu spravované aplikace. Po úspěšném přiřazení zásady budou zásady identifikovat prostředky, které nedodržují předpisy, a nasazovat přidružení pro tyto prostředky.

![Přiřazení předdefinovaných zásad](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vývoje vlastních poskytovatelů prostředků Azure, zkuste je zeptatte na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, před odesláním proto nejprve proveďte kontrolu. Přidejte značku ```azure-custom-providers``` , abyste získali rychlou odpověď.

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte používat předdefinované zásady pro nasazení přidružení. Další informace najdete v těchto článcích:

- [Koncepty: registrace prostředků vlastních zprostředkovatelů Azure](../custom-providers/concepts-resource-onboarding.md)
- [Kurz: připojování prostředků s vlastními poskytovateli](../custom-providers/tutorial-resource-onboarding.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků a nasazení vlastních prostředků](../custom-providers/create-custom-provider.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
