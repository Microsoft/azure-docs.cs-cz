---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: a37087c90b6c9b3629402c7a8c2fa5861e46ae9a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592503"
---
Když je udělen přístup k použití modulu pro rozpoznávání formulář, dostanete Uvítacího e-mailu s několika odkazy a prostředky. Použijte odkaz "Webu Azure portal" v této zprávě otevřete na webu Azure portal a vytvořte prostředek formuláře Rozlišovače. V **vytvořit** podokně zadejte následující informace:

|    |    |
|--|--|
| **Název** | Popisný název pro váš prostředek. Doporučujeme použít popisný název, například *MyNameFormRecognizer*. |
| **Předplatné** | Vyberte předplatné Azure, která udělil přístup. |
| **Location** | Umístění vaší instanci služby cognitive Services. Různá umístění mohou zavést latence, ale mít vliv na běhovou dostupnost vašeho prostředku. |
| **Cenová úroveň** | Náklady na váš prostředek závisí na zvolené cenové úrovni a využití. Další informace najdete v tématu rozhraní API [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Skupina prostředků** | [Skupiny prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek. Můžete vytvořit novou skupinu nebo ho přidat do existující skupiny. |

> [!IMPORTANT]
> Za normálních okolností při vytváření prostředku služby Cognitive Services na webu Azure Portal, máte možnost vytvořit klíč víc služeb předplatného (použít napříč více služeb cognitive services) nebo klíč předplatného jednoúčelovou (používá se pouze s konkrétní služby cognitive Services). Ale protože Rozlišovač formuláře je verze preview, není zahrnutý v předplatném s více službami a jednoúčelovou předplatné nelze vytvořit, pokud nechcete použít odkaz uvedený v uvítacím e-mailu.

Po dokončení nasazení prostředku Rozlišovač formuláře vyhledejte a vyberte ho z **všechny prostředky** seznamu na portálu. Vyberte **klíče** kartu k zobrazení klíče předplatného. Ani jeden klíč vám poskytne vaší aplikaci přístup k prostředku. Zkopírujte hodnotu **klíč 1**. Použijete ho v další části.