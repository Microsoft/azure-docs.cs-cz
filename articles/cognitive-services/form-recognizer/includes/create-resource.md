---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 49feedaa087a89b2dfc5d90c7230b7abf23ed1ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815735"
---
Přejít na Azure Portal a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" vytvořit nový prostředek pro rozpoznávání formulářů " target="_blank"> vytvořit nový prostředek pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . V podokně **vytvořit** zadejte následující informace:

|    |    |
|--|--|
| **Název** | Popisný název prostředku. Doporučujeme použít popisný název, například *MyNameFormRecognizer*. |
| **Předplatné** | Vyberte předplatné Azure, kterému bylo uděleno oprávnění k přístupu. |
| **Umístění** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
| **Cenová úroveň** | Náklady na váš prostředek závisí na cenové úrovni, kterou si zvolíte, a na způsobu využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
| **Skupina prostředků** | [Skupina prostředků Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

> [!NOTE]
> Když v Azure Portal vytvoříte prostředek služby pro rozpoznávání, máte možnost vytvořit klíč předplatného s více službami (používá se u více služeb rozpoznávání) nebo klíč předplatného s jednou službou (používá se jenom pro konkrétní službu rozpoznávání). V předplatném s více službami se ale nezahrnuje aktuálně vydaný modul pro rozpoznávání formulářů.

Když se dokončí nasazení prostředku pro rozpoznávání formulářů, vyhledejte ho a vyberte ze seznamu **všechny prostředky** na portálu. Klíč a koncový bod se umístí na stránku klíč a koncový bod prostředku v části Správa prostředků. Před tím, než budete pokračovat, uložte oba tyto složky do dočasného umístění.
