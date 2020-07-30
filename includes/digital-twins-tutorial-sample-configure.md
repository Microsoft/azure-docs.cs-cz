---
author: baanders
description: kurzy k zahrnutí souborů pro Azure Digital – konfigurace ukázkového projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ad9eb95c9e3a21cd33a367cc864a4d791888afb9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124546"
---
## <a name="configure-the-sample-project"></a>Konfigurace ukázkového projektu

Dále nastavte ukázkovou klientskou aplikaci, která bude pracovat s vaší instancí digitálních vláken Azure. Pokud jste ještě nestáhli ukázkový projekt, Získejte ho hned na úvodní stránce [*ukázek digitálních vláken Azure*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) , a to tak, že v nadpisu vyberete tlačítko *Stáhnout ZIP* .

Přejděte na stažený soubor na svém počítači a rozbalte ho.

Jednou do složky getzip přejděte do _AdtSampleApp/_. Otevřete _**AdtE2ESample. sln**_ v aplikaci Visual Studio 2019. 

V aplikaci Visual Studio použijte podokno *Průzkumník řešení* k vytvoření kopie _> SampleClientApp **serviceConfig.jsna. Soubor šablony** _ (k zkopírování a vložení můžete použít nabídky ze seznamu pravým tlačítkem myši). Přejmenujte *serviceConfig.jskopie na*. Tato akce bude sloužit jako předem nastavený soubor JSON s nezbytnými konfiguračními proměnnými pro spuštění projektu.

Vyberte *serviceConfig.jsv* souboru a otevřete ji v okně pro úpravy. Změňte `tenantId` *ID vašeho adresáře*na ID vaší `clientId` *aplikace*a `instanceUrl` adresu URL *hostitele* instance digitálního vlákna Azure (s *https://* před ním, jak je uvedeno níže).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Uložte soubor a zavřete ho. 

Dále nakonfigurujte *serviceConfig.js* pro soubor, který se má zkopírovat do výstupního adresáře při sestavování *SampleClientApp*. Provedete to tak, že v souboru kliknete pravým tlačítkem na *serviceConfig.js* a zvolíte *Vlastnosti.* V inspektoru *vlastností* změňte hodnotu vlastnosti *Kopírovat do výstupního adresáře* na *Kopírovat, pokud je novější*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Výňatek z okna sady Visual Studio zobrazující podokno Průzkumník řešení s serviceConfig.jsna zvýrazněné a podokno vlastností s vlastností kopírovat do výstupního adresáře nastavenou na kopírovat, pokud je novější" border="false":::

Nechejte projekt _**AdtE2ESample**_ otevřený v aplikaci Visual Studio, abyste ho mohli dál používat v tomto kurzu.

