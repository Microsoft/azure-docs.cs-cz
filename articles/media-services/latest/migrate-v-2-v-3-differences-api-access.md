---
title: Přístup k rozhraní API pro Media Services V2 vs V3
description: Tento článek popisuje rozdíly v přístupu k rozhraní API mezi Azure Media Services v2 až v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567963"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Rozdíly v přístupu k rozhraní API mezi Azure Media Services v2 až V3 API

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-2.svg)

Tento článek popisuje rozdíly v přístupu k rozhraní API mezi Azure Media Services v2 až v3.

## <a name="api-access"></a>Přístup přes rozhraní API

Všechny účty Media Services budou mít přístup k rozhraní V3 API. Důrazně však doporučujeme vývoj migrace na začátku nového účtu ještě před tím, než použijete aktualizovaný kód na existující účet v2. Důvodem je to, že entity V3 nejsou zpětně kompatibilní s v2. Některé entity v2, jako jsou prostředky, jsou dopředně kompatibilní se systémem v3.
Existující účty můžete nadále používat, pokud nekombinují rozhraní API v2 a v3 a pak se pokusíte přejít zpět na v2, ale to se nedoporučuje.

Přístup k rozhraní v2 API bude k dispozici, dokud nebude vyřazený v 2024.

## <a name="create-a-v3-account"></a>Vytvoření účtu V3

Při migraci můžete vytvořit účet v3, který má stále přístup k v2.  Vytvoření účtu se dá udělat takto:

- REST API a starší verze
- Vyberte zaškrtávací políčko na portálu.

> [!div class="mx-imgBorder"]
> [![vytváření účtů na portálu ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Všechny platformy .NET, CLI a jiné sady SDK budou zaměřené na nejnovější verzi rozhraní 2020-05-01 API, takže můžete najít nebo nakonfigurovat starší verze rozhraní API.

> [!NOTE]
> Nové účty vytvořené s rozhraním API 2020-05-01 nemůžou používat rozhraní API v2.
