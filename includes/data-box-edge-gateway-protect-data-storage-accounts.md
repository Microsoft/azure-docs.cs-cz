---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684417"
---
Vaše zařízení je přidružené k účtu úložiště, který se používá jako cíl pro vaše data v Azure. Přístup k účtu úložiště řídí předplatné a úložiště dva 512bitové přístupové klíče přidružené k tomuto účtu úložiště.

Jeden z klíčů se používá k ověřování, když Data Box hraniční zařízení získá přístup k účtu úložiště. Další klíč se nachází ve fondu, abyste mohli klíče pravidelně otočit.

Z bezpečnostních důvodů se vyžadují mnoho datových centrech obměny klíče. Doporučujeme, abyste postupovali podle těchto osvědčených postupů pro obměna klíčů:

- Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Pečlivě Chraňte klíč účtu. Není distribuovat ostatním uživatelům heslo, intenzivně kód nebo ji uložit kdekoli ve formátu prostého textu, který je přístupný ostatním uživatelům.
- [Znovu vygenerovat klíč účtu](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) pomocí webu Azure portal, pokud si myslíte, že je možná ohrožené.
- Správce Azure by měl pravidelně, změnit nebo znovu vygenerovat primární nebo sekundární klíč pomocí části úložiště na webu Azure portal pro přímý přístup k účtu úložiště.