---
title: Nasazení přidružení pro vlastního zprostředkovatele Azure pomocí zásad
description: Přečtěte si o nasazení přidružení pro vlastního poskytovatele pomocí Azure Policy služby.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932642"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Nasazení přidružení pro vlastního zprostředkovatele pomocí zásad Azure

Zásady Azure se dají použít k nasazení přidružení k přidružení prostředků k vlastnímu poskytovateli. V tomto článku jsme popsali předdefinované zásady, které nasazují přidružení a způsob, jakým můžete tyto zásady použít.

## <a name="built-in-policy-to-deploy-associations"></a>Integrovaná zásada pro nasazení přidružení

Nasazení přidružení pro vlastního zprostředkovatele je předdefinovaná zásada, která se dá použít k nasazení přidružení pro přidružení prostředku k vlastnímu poskytovateli. Zásada akceptuje tři parametry:

- ID vlastního poskytovatele – toto ID je ID prostředku vlastního poskytovatele, ke kterému se prostředky musí přidružit.
- Typy prostředků, které se mají přidružit – tyto typy prostředků jsou seznam typů prostředků, které se mají přidružit k vlastnímu poskytovateli. Pomocí stejné zásady můžete přidružit více typů prostředků k vlastnímu zprostředkovateli.
- Předpona názvu přidružení – tento řetězec představuje předponu, která se má přidat do názvu prostředku přidružení, který se vytváří. Výchozí hodnota je "DeployedByPolicy".

Tato zásada používá DeployIfNotExists vyhodnocení. Spustí se poté, co poskytovatel prostředků zpracuje požadavek na vytvoření nebo aktualizaci prostředku a vyhodnocení vrátilo stavový kód úspěchu. Potom se prostředek přidružení nasadí pomocí nasazení šablony.
Další informace o přidruženích najdete v tématu věnovaném [registraci prostředků vlastních zprostředkovatelů Azure](./concepts-custom-providers-resourceonboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak používat vestavěnou zásadu nasazení přidružení 

### <a name="prerequisites"></a>Předpoklady
Pokud vlastní zprostředkovatel potřebuje k provedení akce oprávnění k oboru zásady, nasazení zásad prostředku přidružení nefunguje bez udělení oprávnění.

### <a name="policy-assignment"></a>Přiřazení zásad
Pokud chcete použít předdefinovanou zásadu, vytvořte přiřazení zásady a přiřaďte přidružení nasazení pro vlastní zásady poskytovatele. Tato zásada pak identifikuje prostředky, které nedodržují předpisy, a nasadí přidružení těchto prostředků.

![Přiřazení předdefinovaných zásad](media/builtin-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vývoje vlastních poskytovatelů prostředků Azure, zkuste je zeptatte na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, před odesláním proto nejprve proveďte kontrolu. Přidejte ```azure-custom-providers``` značek, abyste získali rychlou odezvu.

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte používat předdefinované zásady pro nasazení přidružení. Další informace najdete v těchto článcích:

- [Koncepty: registrace prostředků vlastních zprostředkovatelů Azure](./concepts-custom-providers-resourceonboarding.md)
- [Kurz: připojování prostředků s vlastními poskytovateli](./tutorial-custom-providers-resource-onboarding.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-custom-providers-101.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
