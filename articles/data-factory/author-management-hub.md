---
title: Centrum správy
description: Správa připojení, konfigurace správy zdrojového kódu a vlastností globálního vytváření v centru pro správu Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: df5a13b3d7c3fea98cd571a241b9d25e36ffeea6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570632"
---
# <a name="management-hub-in-azure-data-factory"></a>Centrum správy v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Centrum pro správu, ke kterému přistupovala karta *Spravovat* v prostředí Azure Data Factory, je portál, který hostuje globální akce správy pro vaši datovou továrnu. Tady můžete spravovat připojení k úložištím dat a externím výpočtům, konfiguraci správy zdrojového kódu a nastavením aktivačních událostí.

## <a name="manage-connections"></a>Správa připojení

### <a name="linked-services"></a>Propojené služby

Propojené služby definují informace o připojení pro Azure Data Factory pro připojení k externím úložištím dat a výpočetním prostředím. Další informace najdete v tématu [Koncepty propojených služeb](concepts-linked-services.md). Vytváření, úpravy a odstraňování propojených služeb se provádí v centru pro správu.

![Správa propojených služeb](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Prostředí Integration runtime

Prostředí Integration runtime je výpočetní infrastruktura, kterou používá Azure Data Factory k poskytování možností integrace dat napříč různými síťovými prostředími. Další informace získáte v [konceptech modulu runtime integrace](concepts-integration-runtime.md). V centru pro správu můžete vytvořit, odstranit a monitorovat prostředí Integration runtime.

![Spravovat prostředí Integration runtime](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Spravovat správu zdrojového kódu

### <a name="git-configuration"></a>Konfigurace Gitu

Zobrazení a úprava nakonfigurovaných nastavení úložiště Git v centru pro správu. Další informace o [správě zdrojového kódu najdete v Azure Data Factory](source-control.md).

![Spravovat úložiště Git](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Šablona Parametrizace

Chcete-li při publikování z větve spolupráce přepsat vygenerované parametry šablony Správce prostředků, můžete vytvořit nebo upravit soubor vlastního parametru. Další informace získáte [v tématu použití vlastních parametrů v šabloně správce prostředků](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template). Šablona parametrizace je k dispozici pouze při práci v úložišti Git. Pokud *arm-template-parameters-definition.jsv* souboru v pracovní větvi neexistují, bude tato úprava vygenerována úpravou výchozí šablony.

![Správa vlastních parametrů](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Spravovat vytváření obsahu

### <a name="triggers"></a>Aktivační události

Triggery určují, kdy se má spustit spuštění kanálu. Aktuálně triggery můžou být na časovém plánu na zdi, pracují v pravidelných intervalech nebo závisí na události. Další informace najdete v informacích o [spuštění aktivační události](concepts-pipeline-execution-triggers.md#trigger-execution). V centru pro správu můžete vytvořit, upravit, odstranit nebo zobrazit aktuální stav triggeru.

![Snímek obrazovky, který ukazuje, kde vytvořit, upravit, odstranit nebo zobrazit aktuální stav triggeru](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Globální parametry

Globální parametry jsou konstanty v rámci datové továrny, které mohou být spotřebovány kanálem v libovolném výrazu. Další informace najdete v informacích o [globálních parametrech](author-global-parameters.md).

![Vytvoření globálních parametrů](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak [nakonfigurovat úložiště Git](source-control.md) na ADF


