---
title: Streamování videosouborů pomocí služby Azure Media Services – .NET | Microsoft Docs
description: Pomocí kroků v tomto rychlém startu můžete vytvořit nový účet služby Azure Media Services, zakódovat soubor a streamovat ho do Azure Media Playeru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: d50bb6ca67d7d09525013b65d0635e8e16b4cb4e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417274"
---
# <a name="quickstart-stream-video-files---net"></a>Rychlý start: Streamování videosouborů – .NET

Toto rychlé zprovoznění předvádí, jak je jednoduché kódovat a začít se streamováním videí pomocí služby Azure Media Services v široké škále prohlížečů a zařízení. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.
Ukázka v tomto tématu kóduje obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci rychlého startu budete schopni streamovat video.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvoření účtu Media Services](create-account-cli-how-to.md).<br/>Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.
- Postupujte podle kroků v [rozhraní API k přístupu k Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) a uložte přihlašovací údaje. Je potřeba použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Ukázka se nachází ve složce [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Otevřít [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) stažený projekt. Nahraďte hodnoty s přihlašovacími údaji, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md).

Tato ukázka provede následující akce:

1. Vytvoří **transformace** (nejprve, zkontroluje, jestli existuje Zadaná transformace). 
2. Vytvoří výstup **Asset** , který se používá jako kódování **úlohy**výstup.
3. Vytvoří **úlohy**vstup, který je založen na adresu URL HTTPS.
4. Odešle kódování **úlohy** pomocí vstup a výstup, který jste vytvořili dříve.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

Popisy týkající jednotlivých funkcí v ukázce najdete v kódu. Můžete se také podívat na komentáře v [tomto zdrojovém souboru](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Když spustíte aplikaci, zobrazí se adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů. 

1. Stisknutím kombinace kláves Ctrl+F5 spusťte aplikaci *EncodeAndStreamFiles*.
2. Vyberte protokol **HLS** společnosti Apple (končí řetězcem *manifest(format=m3u8-aapl)*) a zkopírujte adresu URL streamování z konzoly.

![Výstup](./media/stream-files-tutorial-with-api/output.png)

Ve [zdrojovém kódu](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukázky můžete vidět, jak je adresa URL tvořená. Pokud ji chcete vytvořit, musíte zřetězit název hostitele koncového bodu hostování a cestu lokátoru streamování.  

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
 
     Můžete vložit adresu URL do HLS, Dash, nebo technologie Smooth formátu a Azure Media Player se přepne na příslušný protokol streamování pro přehrávání na vašem zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtů úložiště, které jste vytvořili v rámci tohoto rychlého startu, odstraňte tuto skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Kontrola kódu

Popisy týkající jednotlivých funkcí v ukázce najdete v kódu. Můžete se také podívat na komentáře v [tomto zdrojovém souboru](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Kurz [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md) vám poskytne složitější příklad streamování s podrobnými vysvětlivkami. 

### <a name="job-error-codes"></a>Kódy chyb úlohy

Zobrazit [kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při práci s vícevláknovou aplikací byste měli pro každé vlákno vygenerovat nový objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md)
