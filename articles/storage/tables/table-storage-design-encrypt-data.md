---
title: Šifrování dat tabulky Azure storage | Dokumentace Microsoftu
description: Další informace o šifrování dat tabulky ve službě Azure storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8a7b61705f6efc5a76212fdd1345a022f58e0686
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526462"
---
# <a name="encrypt-table-data"></a>Šifrování dat tabulky
Klientská knihovna .NET Azure Storage podporuje šifrování vlastností entity řetězce pro vložení a nahrazovat operace. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a jsou převedeny na řetězce zpět po dešifrování.    

Pro tabulky, vedle zásady šifrování musí uživatelé zadat vlastnosti, které mají být šifrována. To lze provést zadáním buď atribut [EncryptProperty] \(pro entity objektů POCO, které jsou odvozeny od TableEntity) nebo šifrování překladač v žádosti o možnostech. Překladač šifrování je delegát, který má klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která určuje, jestli by měl být šifrovaná tuto vlastnost. Během šifrování se knihovna klienta používá tyto informace se rozhodnout, jestli chcete šifrovat vlastnost při zápisu lince. Delegát také poskytuje možnost logiky po tom, jak jsou zašifrované vlastnosti. (Například, pokud X, pak šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Není nutné poskytnout tyto informace při čtení nebo dotazování entit.

## <a name="merge-support"></a>Sloučit podpory

Sloučení se momentálně nepodporuje. Protože podmnožinu vlastností mohou byla zašifrována pomocí dříve za jiný klíč, jednoduše slučování nové vlastnosti a metadata aktualizace za následek ztrátu dat. Slučují se buď vyžaduje, aby volání další služby do existující entity načíst ze služby nebo pomocí nového klíče pro jednu vlastnost, které nejsou vhodné z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v tématu [šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)