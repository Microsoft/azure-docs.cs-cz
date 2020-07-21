---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544054"
---
## <a name="prerequisites"></a>Předpoklady

Jediným předpokladem je předplatné služby Azure Speech. Pokud ho ještě nemáte, přečtěte si [příručku](../get-started.md#new-resource) k vytvoření nového předplatného.

## <a name="download-and-install"></a>Stažení a instalace

#### <a name="windows-install"></a>[Instalace Windows](#tab/windowsinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči ve Windows:

1. Nainstalujte buď [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) nebo [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat podadresář, který potřebujete ( `spx-net471` pro .NET Framework 4,7 nebo `spx-netcore-win-x64` pro .NET Core 3,0 na CPU x64).

Na příkazovém řádku změňte adresář na toto umístění a potom zadejte `spx` nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

> [!NOTE]
> V systému Windows může rozhraní příkazového řádku pro rozpoznávání řeči Zobrazit pouze písma dostupná pro příkazový řádek v místním počítači.
> Služba [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) podporuje všechna písemně vytvořená písma pomocí funkce Speech CLI.
> Pokud výstup do souboru, textový editor, jako je například Poznámkový blok nebo webový prohlížeč, jako je například Microsoft Edge, může zobrazit také všechna písma.

> [!NOTE]
> PowerShell při hledání příkazu nekontroluje místní adresář. V prostředí PowerShell změňte adresář na umístění `spx` a zavolejte nástroj zadáním `.\spx` .
> Pokud přidáte tento adresář do vaší cesty, PowerShell a příkazový řádek Windows se najde `spx` z libovolného adresáře bez zahrnutí `.\` předpony.

#### <a name="linux-install"></a>[Instalace pro Linux](#tab/linuxinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči v systému Linux na procesor x64:

1. Nainstalujte [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat `spx-netcore-30-linux-x64` do nového `~/spx` adresáře.
4. V terminálu zadejte tyto příkazy:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Zadáním `spx` zobrazíte nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

***

## <a name="create-subscription-config"></a>Vytvořit konfiguraci předplatného

Pokud chcete začít používat rozhraní příkazového řádku, musíte nejdřív zadat klíč předplatného pro rozpoznávání řeči a informace o oblasti. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Jakmile budete mít svůj klíč předplatného a identifikátor oblasti (např. `eastus`, `westus` ) spusťte následující příkazy.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Ověřování předplatného je nyní uloženo pro budoucí požadavky SPX. Pokud potřebujete některou z těchto uložených hodnot odebrat, spusťte `spx config @region --clear` nebo `spx config @key --clear` .
