---
title: Šifrování dat tabulky úložiště Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o šifrování dat tabulky v úložišti Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60325999"
---
# <a name="encrypt-table-data"></a>Šifrování dat tabulky
Klientská knihovna úložiště .NET Azure podporuje šifrování vlastností entit řetězce pro operace vložení a nahrazení. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.    

U tabulek musí uživatelé kromě zásad šifrování zadat vlastnosti, které mají být šifrovány. To lze provést zadáním atributu [EncryptProperty] (pro entity POCO, které jsou odvozeny z TableEntity) nebo překládáním šifrování v možnostech požadavku. Překládání šifrování je delegát, který přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Během šifrování klientská knihovna používá tyto informace k rozhodnutí, zda chcete šifrovat vlastnost při zápisu do drátu. Delegát také poskytuje možnost logiky kolem jak jsou zašifrovány vlastnosti. (Například pokud X, pak šifrovat vlastnost A; jinak šifrovat vlastnosti A a B.) Není nutné poskytovat tyto informace při čtení nebo dotazování entit.

## <a name="merge-support"></a>Podpora pro sloučení

Sloučení není aktuálně podporováno. Vzhledem k tomu, že podmnožina vlastností mohla být dříve zašifrována pomocí jiného klíče, může dojít ke sloučení nových vlastností a aktualizaci metadat ke ztrátě dat. Sloučení buď vyžaduje provedení volání další služby ke čtení již existující entity ze služby nebo pomocí nového klíče na vlastnost, z nichž oba nejsou vhodné z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v [tématu Šifrování na straně klienta a Azure Key Vault pro úložiště Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Další kroky

- [Návrhové vzory tabulky](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
