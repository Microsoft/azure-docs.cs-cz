---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital – konfigurace ukázkového projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff9d1f0ff75a6df6c115ecfe2b8c5f71b12049e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036129"
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

