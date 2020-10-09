---
title: Streamování videosouborů pomocí služby Azure Media Services – .NET | Microsoft Docs
description: Postupujte podle kroků v tomto kurzu a pomocí .NET vytvořte nový účet Azure Media Services, zakódovat soubor a Streamujte ho do Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 530089b9c6b9544201b0f07368cac3d1a695a4f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268685"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto kurzu se dozvíte, jak snadné je zakódovat a spustit streamování videí na nejrůznějších prohlížečích a zařízeních pomocí Azure Media Services. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.
Ukázka v tomto tématu kóduje obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci kurzu budete moct streamovat video.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Ukázka se nachází ve složce [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Ve staženém projektu otevřete [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) . Nahraďte hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md).

Tato ukázka provede následující akce:

1. Vytvoří **transformaci** (nejprve zkontroluje, zda zadaná transformace existuje). 
2. Vytvoří výstupní **Asset** , který se používá jako výstup **úlohy**kódování.
3. Vytvoří vstup **úlohy**, který je založen na adrese URL https.
4. Odešle **úlohu** kódování pomocí vstupu a výstupu, který byl vytvořen dříve.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

Popisy týkající jednotlivých funkcí v ukázce najdete v kódu. Můžete se také podívat na komentáře v [tomto zdrojovém souboru](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Když spustíte aplikaci, zobrazí se adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů. 

1. Stisknutím kombinace kláves Ctrl+F5 spusťte aplikaci *EncodeAndStreamFiles*.
2. Vyberte protokol **HLS** společnosti Apple (končí řetězcem *manifest(format=m3u8-aapl)*) a zkopírujte adresu URL streamování z konzoly.

![Snímek obrazovky s výstupem z aplikace EncodeAndStreamFiles v aplikaci Visual Studio zobrazující tři adresy URL streamování pro použití v Azure Media Player.](./media/stream-files-tutorial-with-api/output.png)

Ve [zdrojovém kódu](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) ukázky můžete vidět, jak je adresa URL tvořená. Pokud ji chcete vytvořit, musíte zřetězit název hostitele koncového bodu hostování a cestu lokátoru streamování.  

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
 
     Můžete vložit adresu URL ve formátu HLS, pomlčka nebo vyhlazení a Azure Media Player přepnout na příslušný protokol pro streamování pro přehrávání na zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně Media Services a účtů úložiště, které jste pro tento kurz vytvořili, odstraňte skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Kontrola kódu

Popisy týkající jednotlivých funkcí v ukázce najdete v kódu. Můžete se také podívat na komentáře v [tomto zdrojovém souboru](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Kurz [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md) vám poskytne složitější příklad streamování s podrobnými vysvětlivkami. 

### <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při práci s vícevláknovou aplikací byste měli pro každé vlákno vygenerovat nový objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md)
