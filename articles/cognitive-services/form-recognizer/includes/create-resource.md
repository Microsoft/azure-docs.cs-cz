---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205813"
---
Přejděte na portál <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Azure a vytvořte" target="_blank">nový prostředek nástroje <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>pro rozpoznávání formulářů a vytvořte nový prostředek nástroje pro rozpoznávání formulářů . V podokně **Vytvořit** zadejte následující informace:

|    |    |
|--|--|
| **Název** | Popisný název zdroje. Doporučujeme používat popisný název, například *MyNameFormRecognizer*. |
| **Předplatné** | Vyberte předplatné Azure, kterému byl udělen přístup. |
| **Umístění** | Umístění instance kognitivní služby. Různá umístění mohou zavést latenci, ale nemají žádný vliv na dostupnost za běhu vašeho prostředku. |
| **Cenová úroveň** | Náklady na váš prostředek závisí na cenové úrovni, kterou zvolíte, a na vašem využití. Další informace naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API .
| **Skupina prostředků** | [Skupina prostředků Azure,](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) která bude obsahovat váš prostředek. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

> [!IMPORTANT]
> Obvykle při vytváření prostředku kognitivní služby na webu Azure Portal máte možnost vytvořit klíč předplatného s více službami (používaný napříč více kognitivními službami) nebo klíč předplatného jedné služby (používá se pouze s konkrétní kognitivní službou). Protože však nástroj pro rozpoznávání formulářů je předběžná verze, není součástí předplatného s více službami a nelze vytvořit předplatné jedné služby, pokud nepoužijete odkaz uvedený v uvítacím e-mailu.

Po dokončení nasazení prostředku nástroje pro rozpoznávání formulářů vyhledejte a vyberte jej ze seznamu **Všechny prostředky** na portálu. Pak vyberte kartu **Rychlý start** a zobrazte data předplatného. Uložte hodnoty **Key1** a **Endpoint** do dočasného umístění. Použijete je v následujících krocích.
