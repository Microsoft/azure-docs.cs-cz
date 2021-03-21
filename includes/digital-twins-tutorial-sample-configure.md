---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital – konfigurace ukázkového projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463745"
---
## <a name="configure-the-sample-project"></a>Konfigurace ukázkového projektu

Dále nastavte ukázkovou klientskou aplikaci, která bude pracovat s vaší instancí digitálních vláken Azure.

Přejděte na počítač do souboru, který jste si stáhli dříve z [*digitálních vláken Azure, z kompletních ukázek*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (a rozbalte ho, pokud jste to ještě neudělali).

Ve složce přejděte do _AdtSampleApp_. Otevřete _**AdtE2ESample. sln**_ v aplikaci Visual Studio 2019. 

V aplikaci Visual Studio vyberte _SampleClientApp > **appsettings.jsv**_ souboru, aby se otevřela v okně pro úpravy. Tato akce bude sloužit jako předem nastavený soubor JSON s nezbytnými konfiguračními proměnnými pro spuštění projektu.

V těle souboru změňte `instanceUrl` *adresu URL hostitele* instance digitálního vlákna Azure (přidáním **_https://_** před *název hostitele*, jak je znázorněno níže).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Uložte soubor a zavřete ho. 

Dále nakonfigurujte *appsettings.js* pro soubor, který se má zkopírovat do výstupního adresáře při sestavování *SampleClientApp*. Provedete to tak, že v souboru kliknete pravým tlačítkem na *appsettings.js* a zvolíte **vlastnosti**. V inspektoru **vlastností** vyhledejte vlastnost *Kopírovat do výstupního adresáře* . Změňte hodnotu na **Kopírovat, pokud není novější** , pokud už není nastavená na.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Výňatek z okna sady Visual Studio zobrazující podokno Průzkumník řešení s appsettings.jsna zvýrazněné a podokno vlastností s vlastností kopírovat do výstupního adresáře nastavenou na kopírovat, pokud je novější" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Nechejte projekt _**AdtE2ESample**_ otevřený v aplikaci Visual Studio, abyste ho mohli dál používat v tomto kurzu.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
