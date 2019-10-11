---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: c2d3119283b5b75eeca6b0e0737f62eee00e81c6
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237320"
---
Pokud jste udělili přístup k používání nástroje pro rozpoznávání formulářů, obdržíte uvítací e-mail s několika odkazy a prostředky. Pomocí odkazu Azure Portal v této zprávě otevřete Azure Portal a vytvořte prostředek pro rozpoznávání formulářů. V podokně **vytvořit** zadejte následující informace:

|    |    |
|--|--|
| **Název** | Popisný název prostředku. Doporučujeme použít popisný název, například *MyNameFormRecognizer*. |
| **Předplatné** | Vyberte předplatné Azure, kterému bylo uděleno oprávnění k přístupu. |
| **Umístění** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
| **Cenová úroveň** | Náklady na váš prostředek závisí na cenové úrovni, kterou si zvolíte, a na způsobu využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
| **Skupina prostředků** | [Skupina prostředků Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

> [!IMPORTANT]
> Když v Azure Portal vytvoříte prostředek služby pro rozpoznávání, máte možnost vytvořit klíč předplatného s více službami (používá se u více služeb rozpoznávání) nebo klíč předplatného s jednou službou (používá se jenom pro konkrétní službu rozpoznávání). Vzhledem k tomu, že nástroj pro rozpoznávání formulářů je verze Preview, není zahrnutý v předplatném s více službami a předplatné s jednou službou nemůžete vytvořit, pokud nepoužijete odkaz v uvítacím e-mailu.

Když se dokončí nasazení prostředku pro rozpoznávání formulářů, vyhledejte ho a vyberte ze seznamu **všechny prostředky** na portálu. Pak vyberte kartu **klíče** a zobrazte klíče předplatného. Kterákoli z těchto klíčů přiřadí aplikaci přístup k prostředku. Zkopírujte hodnotu **klíče 1**.