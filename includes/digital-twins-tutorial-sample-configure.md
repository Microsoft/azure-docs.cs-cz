---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital – konfigurace ukázkového projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613534"
---
## <a name="configure-the-sample-project"></a>Konfigurace ukázkového projektu

Dále nastavte ukázkovou klientskou aplikaci, která bude pracovat s vaší instancí digitálních vláken Azure. Pokud jste ještě nestáhli ukázkový projekt, Získejte ho hned [stažením ukázek úložiště ukázek Azure s digitálními dvojitými kopiemi jako soubor zip](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Přejděte na stažený soubor na svém počítači a rozbalte ho.

Jednou do složky getzip přejděte na _Digital-zdvojené-Samples-Master/AdtSampleApp/_. Otevřete _**AdtE2ESample. sln**_ v aplikaci Visual Studio 2019. 

V aplikaci Visual Studio použijte *Průzkumník řešení* podokno k vytvoření kopie souboru _ **SERVICECONFIG. JSON. template** _ (můžete použít nabídky, které se dají kopírovat a vkládat) do > SampleClientApp. Přejmenujte kopii *serviceConfig. JSON*. Tato akce bude sloužit jako předem nastavený soubor JSON s nezbytnými konfiguračními proměnnými pro spuštění projektu.

Vyberte nový soubor a otevřete ho v okně pro úpravy. Změňte `tenantId` *ID vašeho adresáře*na ID vaší `clientId` *aplikace*a `instanceUrl` adresu URL *hostitele* instance digitálního vlákna Azure (s *https://* před ním, jak je uvedeno níže).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Uložte soubor a zavřete ho. 

Dále nakonfigurujte soubor *serviceConfig. JSON* , který se má zkopírovat do výstupního adresáře při vytváření *SampleClientApp*. Provedete to tak, že kliknete pravým tlačítkem na soubor *serviceConfig. JSON* a zvolíte *Vlastnosti.* V inspektoru *vlastností* změňte hodnotu vlastnosti *Kopírovat do výstupního adresáře* na *Kopírovat, pokud je novější*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Výňatek z okna sady Visual Studio, ve kterém se zvýrazní podokno Průzkumník řešení se zvýrazněným serviceConfig. JSON a podokno vlastností s vlastností kopírovat do výstupního adresáře nastaveno na kopírovat, pokud je novější" border="false":::

Nechejte projekt _**AdtE2ESample**_ otevřený v aplikaci Visual Studio, abyste ho mohli dál používat v tomto kurzu.

