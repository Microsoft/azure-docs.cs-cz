---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174758"
---
**1. jak budou zákazníci upozorňováni na vyřazení sady SDK?**

Microsoft bude na konci podpory pro vyřazení sady SDK poskytovat oznámení na 12 měsíců, aby bylo možné snadněji přejít na podporovanou sadu SDK. Zákazníci budou dál upozorňováni prostřednictvím různých komunikačních kanálů – Portál pro správu Azure, centra pro vývojáře, příspěvků na blogu a přímé komunikace s přiřazeným správcům služeb.

**2. zákazníci můžou v průběhu 12 měsíců vytvářet aplikace s využitím "k vyřazení Azure Cosmos DB SDK".** 

Ano, zákazníci budou mít úplný přístup k autorům, nasazování a úpravám aplikací pomocí sady SDK "k dispozici" Azure Cosmos DB v období odkladu po dobu 12 měsíců. V období odkladu 12 měsíců doporučujeme zákazníkům migrovat na novější podporovanou verzi Azure Cosmos DB SDK podle potřeby.

**3. zákazníci můžou po uplynutí 12 měsíců oznámení vytvářet a upravovat aplikace s využitím vyřazení Azure Cosmos DB SDK?**

Po uplynutí 12 měsíců bude sada SDK vyřazena. Žádný přístup k Azure Cosmos DB aplikací s využitím vyřazené sady SDK nebude povolený Azure Cosmos DB platformou. Kromě toho Microsoft neposkytne zákaznickou podporu na vyřazenou sadu SDK.

**4. co se stane s aplikacemi, které běží na zákaznících, které používají nepodporovanou Azure Cosmos DB verzi SDK?**

Všechny pokusy o připojení ke službě Azure Cosmos DB s vyřízenou verzí sady SDK budou odmítnuty. 

**5. budou se nové funkce a funkce aplikovat na všechny nevyřazené sady SDK?**

Nové funkce a funkce budou přidány pouze do nových verzí. Pokud používáte starou nevyřazenou verzi sady SDK, vaše požadavky na Azure Cosmos DB budou pořád fungovat jako předchozí, ale nebudete mít přístup k žádným novým funkcím.  

**6. co mám dělat, když nemůžu aktualizovat aplikaci před vyjmutím z nějakého data?**

Doporučujeme, abyste provedli upgrade na nejnovější sadu SDK co nejdříve. Po označení sady SDK k vyřazení budete mít 12 měsíců, abyste aplikaci aktualizovali. Pokud z jakéhokoli důvodu nemůžete dokončit aktualizaci vaší aplikace v tomto časovém období, obraťte se prosím na [tým Cosmos DB](mailto:askcosmosdb@microsoft.com) a požádejte ho o pomoc před datem přerušení.

