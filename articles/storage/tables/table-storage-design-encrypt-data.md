---
title: Šifrovat data tabulky Azure Storage | Microsoft Docs
description: Seznamte se s šifrováním dat v tabulkách v Azure Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "60325999"
---
# <a name="encrypt-table-data"></a>Šifrovat data tabulky
Klientská knihovna Azure Storage .NET podporuje šifrování vlastností entity řetězce pro operace INSERT a nahrazování. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.    

U tabulek (kromě zásad šifrování) musí uživatelé zadat vlastnosti, které mají být zašifrované. To lze provést buď zadáním atributu [EncryptProperty] (pro entity POCO, které jsou odvozeny od TableEntity) nebo překladače šifrování v možnostech žádosti. Překladač šifrování je delegát, který převezme klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, jestli má být tato vlastnost zašifrovaná. Při šifrování používá Klientská knihovna tyto informace k rozhodnutí, jestli se má při psaní do sítě šifrovat vlastnost. Delegát také poskytuje možnost logiky kolem způsobu šifrování vlastností. (Například pokud X, pak Šifrujte vlastnost A; jinak Zašifrujte vlastnosti a a B.) Při čtení nebo dotazování entit není nutné tyto informace zadávat.

## <a name="merge-support"></a>Podpora sloučení

Sloučení se momentálně nepodporuje. Vzhledem k tomu, že podmnožina vlastností mohla být dříve zašifrovaná pomocí jiného klíče, stačí sloučit nové vlastnosti a aktualizace metadat způsobí ztrátu dat. Sloučení vyžaduje, aby dodatečná volání služby přečetla stávající entita ze služby nebo používala nový klíč pro jednu vlastnost, která nejsou vhodná z důvodů výkonu.     

Informace o šifrování dat tabulky najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Další kroky

- [Vzory návrhu tabulek](table-storage-design-patterns.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Modelování relací](table-storage-design-modeling.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
