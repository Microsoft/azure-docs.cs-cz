---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83996787"
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
> Když v Azure Portal vytvoříte prostředek služby pro rozpoznávání, máte možnost vytvořit klíč předplatného s více službami (používá se u více služeb rozpoznávání) nebo klíč předplatného s jednou službou (používá se jenom pro konkrétní službu rozpoznávání). Vzhledem k tomu, že nástroj pro rozpoznávání formulářů je verze Preview, není zahrnutý v předplatném s více službami a předplatné s jednou službou nemůžete vytvořit, pokud nepoužijete odkaz v uvítacím e-mailu.

Když se dokončí nasazení prostředku pro rozpoznávání formulářů, vyhledejte ho a vyberte ze seznamu **všechny prostředky** na portálu. Klíč a koncový bod se umístí na stránku klíč a koncový bod prostředku v části Správa prostředků. Před tím, než budete pokračovat, uložte oba tyto složky do dočasného umístění.
