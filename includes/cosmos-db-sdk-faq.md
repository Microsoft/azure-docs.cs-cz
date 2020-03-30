---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174758"
---
**1. Jak budou zákazníci informováni o odchodu do důchodu SDK?**

Společnost Microsoft poskytne 12 měsíční předběžné oznámení na ukončení podpory sady SDK, která odchází do důchodu, aby usnadnila hladký přechod na podporovanou sadu SDK. Zákazníci budou dále upozorňováni prostřednictvím různých komunikačních kanálů – Portálpro správu Azure, Centrum pro vývojáře, blogový příspěvek a přímá komunikace s přiřazenými správci služeb.

**2. Mohou zákazníci vytvářet aplikace pomocí "nabýt" vyřazené sady Azure Cosmos DB SDK během období 12 měsíců?** 

Ano, zákazníci budou mít plný přístup k vytváření, nasazování a úpravám aplikací pomocí "nastávající" vyřazené sady Azure Cosmos DB SDK během 12měsíčního období odkladu. Během 12 měsíční období odkladu se zákazníkům doporučuje migrovat na novější podporovanou verzi sady Azure Cosmos DB SDK podle potřeby.

**3. Můžou zákazníci vytvářet a upravovat aplikace pomocí vyřazené sady Azure Cosmos DB SDK po uplynutí 12měsíční oznamovací lhůty?**

Po uplynutí 12měsíční oznamovací lhůty bude sada SDK vyřazena. Žádný přístup k Azure Cosmos DB aplikacemi pomocí vyřazené sady SDK nebude povoleno platformou Azure Cosmos DB. Společnost Microsoft dále nebude poskytovat zákaznickou podporu v vyřazené sadě SDK.

**4. Co se stane se spuštěných aplikací zákazníka, které používají nepodporovanou verzi Azure Cosmos DB SDK?**

Všechny pokusy o připojení ke službě Azure Cosmos DB s vyřazenou verzí sady SDK budou odmítnuty. 

**5. Budou nové funkce použity pro všechny sady SDK, které nejsou vyřazeny?**

Nové funkce a funkce budou přidány pouze do nových verzí. Pokud používáte starou, nevyřazenou, verze sady SDK vaše požadavky na Azure Cosmos DB bude fungovat jako předchozí, ale nebudete mít přístup k žádné nové funkce.  

**6. Co mám dělat, když nemohu aktualizovat svou žádost před datem uzávěrky?**

Doporučujeme upgradovat na nejnovější sdk co nejdříve. Jakmile je sada SDK označena pro vyřazení, budete mít 12 měsíců na aktualizaci aplikace. Pokud z jakéhokoli důvodu nemůžete dokončit aktualizaci aplikace v tomto časovém rámci, obraťte se na [tým Cosmos DB](mailto:askcosmosdb@microsoft.com) a požádejte o jejich pomoc před datem přerušení.

