---
title: Šifrování dat v tabulce úložiště Azure | Microsoft Docs
description: Další informace o tabulce šifrování dat v úložišti Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660753"
---
# <a name="encrypt-table-data"></a>Šifrování dat v tabulce
Klientská knihovna pro úložiště Azure .NET podporuje šifrování vlastnosti entity řetězce pro vložení a nahrazovat operace. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a převedené zpět do řetězce po dešifrování.    

Pro tabulky, kromě zásady šifrování musí uživatelé zadat vlastnosti k zašifrování. To lze provést zadáním buď atribut [EncryptProperty] \(pro entity objektů POCO, které jsou odvozeny od TableEntity) nebo šifrování překladač v žádosti o možnostech. Překladač šifrování je delegáta, který přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která určuje, jestli by se šifrovat tuto vlastnost. Během šifrování se Klientská knihovna používá tyto informace se rozhodnout, jestli se má zašifrovat vlastnost při zápisu do sítě. Delegát taky poskytuje možnost logiku kolem jak jsou zašifrované vlastnosti. (Například pokud X, potom šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Není potřeba zadat tyto informace při čtení nebo dotazování entity.

## <a name="merge-support"></a>Merge – podpora

Sloučení se aktuálně nepodporuje. Protože podmnožinu vlastností může být šifrována dříve pomocí jiného klíče, jednoduše slučování nové vlastnosti a aktualizace metadat výsledků dojít ke ztrátě dat. Slučování buď vyžaduje volání další služby ke čtení existující entity ze služby, nebo pomocí nového klíče na vlastnosti, které nejsou vhodné z důvodů výkonu.     

Informace o šifrování dat v tabulce najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Modelování vztahů](table-storage-design-modeling.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)