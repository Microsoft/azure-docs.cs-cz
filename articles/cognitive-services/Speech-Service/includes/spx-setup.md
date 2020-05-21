---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6a38590d2511afaac6bd9213f53a8c69d0264eb4
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714652"
---
## <a name="prerequisites"></a>Požadavky

Jediným předpokladem je předplatné služby Azure Speech. Pokud ho ještě nemáte, přečtěte si [příručku](../get-started.md#new-resource) k vytvoření nového předplatného.

## <a name="download-and-install"></a>Stažení a instalace

#### <a name="windows-install"></a>[Instalace Windows](#tab/windowsinstall)

Pomocí těchto kroků nainstalujete nástroj SPX ve Windows:

1. Nainstalujte buď [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) nebo [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro nástroj SPX a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat podadresář, který potřebujete ( `spx-net471` pro .NET Framework 4,7 nebo `spx-netcore-win-x64` pro .NET Core 3,0 na CPU x64).

Na příkazovém řádku změňte adresář na toto umístění a potom zadejte `spx` nápovědu pro nástroj SPX.

#### <a name="linux-install"></a>[Instalace pro Linux](#tab/linuxinstall)

Pomocí těchto kroků nainstalujete nástroj SPX na platformě Linux na procesor x64:

1. Nainstalujte [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro nástroj SPX a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat `spx-netcore-30-linux-x64` do nového `~/spx` adresáře.
4. V terminálu zadejte tyto příkazy:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Zadáním `spx` zobrazíte nápovědu pro nástroj SPX.

***

## <a name="create-subscription-config"></a>Vytvořit konfiguraci předplatného

Pokud chcete začít používat protokol SPX, musíte nejdřív zadat klíč předplatného pro rozpoznávání řeči a informace o oblasti. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Jakmile budete mít svůj klíč předplatného a identifikátor oblasti (např. `eastus`, `westus` ) spusťte následující příkazy.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Ověřování předplatného je nyní uloženo pro budoucí požadavky SPX. Pokud potřebujete některou z těchto uložených hodnot odebrat, spusťte `spx config @region --clear` nebo `spx config @key --clear` .
