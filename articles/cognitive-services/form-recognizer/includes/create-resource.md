---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205813"
---
Přejít na Azure Portal a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="vytvořit nový prostředek pro rozpoznávání formulářů" target="_blank">vytvořit nový</a>prostředků <span class="docon docon-navigate-external x-hidden-focus"></span> pro rozpoznávání formulářů. V podokně **vytvořit** zadejte následující informace:

|    |    |
|--|--|
| **Název** | Popisný název prostředku. Doporučujeme použít popisný název, například *MyNameFormRecognizer*. |
| **Předplatné** | Vyberte předplatné Azure, kterému bylo uděleno oprávnění k přístupu. |
| **Umístění** | Umístění instance služby vyvnímání. Různá umístění můžou způsobit latenci, ale nemají žádný vliv na dostupnost vašeho prostředku za běhu. |
| **Cenová úroveň** | Náklady na váš prostředek závisí na cenové úrovni, kterou si zvolíte, a na způsobu využití. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/)rozhraní API.
| **Skupina prostředků** | [Skupina prostředků Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek. Můžete vytvořit novou skupinu nebo ji přidat do již existující skupiny. |

> [!IMPORTANT]
> Když v Azure Portal vytvoříte prostředek služby pro rozpoznávání, máte možnost vytvořit klíč předplatného s více službami (používá se u více služeb rozpoznávání) nebo klíč předplatného s jednou službou (používá se jenom pro konkrétní službu rozpoznávání). Vzhledem k tomu, že nástroj pro rozpoznávání formulářů je verze Preview, není zahrnutý v předplatném s více službami a předplatné s jednou službou nemůžete vytvořit, pokud nepoužijete odkaz v uvítacím e-mailu.

Když se dokončí nasazení prostředku pro rozpoznávání formulářů, vyhledejte ho a vyberte ze seznamu **všechny prostředky** na portálu. Pak vyberte kartu **rychlý Start** , abyste zobrazili data vašeho předplatného. Uloží hodnoty **klíč1** a **Endpoint** do dočasného umístění. Budete je používat v následujících krocích.
