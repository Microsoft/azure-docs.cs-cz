---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515285"
---
**1. jak budou zákazníci upozorňováni na vyřazení sady SDK?**

Microsoft bude mít na konci podpory pro vyřazení sady SDK 12měsíční oznámení, aby bylo možné zajistit hladký přechod na podporovanou sadu SDK. Zákazníci budou dál upozorňováni prostřednictvím různých komunikačních kanálů – Azure Portal, aktualizacích Azure a přímé komunikace s přiřazeným správcům služeb.

**2. zákazníci můžou v průběhu 12 měsíců vytvářet aplikace s využitím "k vyřazení Azure Cosmos DB SDK".** 

Ano, zákazníci budou mít úplný přístup k vytváření, nasazování a úpravám aplikací pomocí sady SDK "k dispozici" Azure Cosmos DB v období 12 měsíců. Během 12 měsíců doporučujeme zákazníkům migrovat na novější podporovanou verzi Azure Cosmos DB SDK podle potřeby. 

**3. po datu vyřazení dojde k tomu, co se stane s aplikacemi, které používají nepodporovanou sadu Azure Cosmos DB SDK?** 

Po datu vyřazení již Azure Cosmos DB nebude provádět opravy chyb, přidávat nové funkce a poskytovat podporu pro vyřazené verze sady SDK. Pokud nechcete upgradovat, požadavky odeslané z vyřazených verzí sady SDK budou nadále obsluhovány službou Azure Cosmos DB. 

**4. které verze SDK budou mít nejnovější funkce a aktualizace?**

Nové funkce a aktualizace budou přidány pouze do nejnovější dílčí verze nejnovější podporované hlavní verze sady SDK. Doporučujeme vždy použít nejnovější verzi, abyste mohli využít nové funkce, vylepšení výkonu a opravy chyb. Pokud používáte starou nevyřazenou verzi sady SDK, vaše požadavky na Azure Cosmos DB budou pořád fungovat, ale nebudete mít přístup k žádným novým funkcím.  

**5. co mám dělat, když nemůžu aktualizovat aplikaci před vyjmutím z nějakého data?**

Doporučujeme, abyste provedli upgrade na nejnovější sadu SDK co nejdříve. Po označení sady SDK k vyřazení budete mít 12 měsíců, abyste aplikaci aktualizovali. Pokud se vám nedaří aktualizovat datum vyřazení, požadavky odeslané z vyřazených verzí sady SDK budou nadále obsluhovány Azure Cosmos DB, takže vaše běžící aplikace budou nadále fungovat. Azure Cosmos DB však již nebude provádět opravy chyb, přidávat nové funkce a poskytovat podporu pro vyřazení verze sady SDK. 

Pokud máte plán podpory a potřebujete technickou podporu, [kontaktujte nás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) podáním lístku podpory.
    


