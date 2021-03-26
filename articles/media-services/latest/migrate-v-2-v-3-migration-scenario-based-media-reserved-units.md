---
title: Doprovodné materiály k migraci rezervovaných jednotek médií (MRUs)
description: Tento článek obsahuje pokyny pro scénáře, které vám pomůžou při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563068"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Doprovodné materiály k migraci na základě scénářů médií (MRUs)

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek obsahuje pokyny pro scénáře, které vám pomůžou při migraci z Azure Media Services V2 na v3.

- Pro nové účty V3 vytvořené v Azure Portal nebo s verzí 2020-05-01 rozhraní API V3 už nebudete muset nastavit rezervované jednotky médií (MRUs). Systém bude nyní automaticky škálovat nahoru a dolů na základě zatížení.
- Pokud máte účet V3 nebo v2, který byl vytvořen před verzí 2020-05-01 rozhraní API, můžete stále řídit souběžnost a výkon úloh pomocí rezervovaných jednotek médií. Další informace najdete v článku o škálování zpracování médií. MRUs můžete spravovat pomocí CLI 2,0 pro Media Services V3 nebo pomocí Azure Portal.  
- Pokud nevidíte možnost spravovat MRUs v Azure Portal, máte spuštěný účet, který byl vytvořen s rozhraním API 2020-05-01 nebo novějším.
- Pokud jste obeznámeni s nastavením typu MRU na S3, výkon se zvýší nebo zůstane stejný.
- Pokud jste stávající zákazník verze 2, budete muset vytvořit nový účet v2 pro podporu vaší stávající aplikace před dokončením migrace. 
- Indexer v1 nebo jiné procesory médií, které nejsou zcela zastaralé, možná budete muset znovu povolit. 

Další informace o MRUs najdete v tématech [rezervované jednotky médií](concept-media-reserved-units.md) a [postup škálování rezervovaných jednotek médií](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Seznam naposledy použitých konceptů, výukových kurzů a návody

### <a name="concepts"></a>Koncepty

[Rezervované jednotky médií](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Průvodce postupy

[Postup škálování rezervovaných jednotek médií](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).
