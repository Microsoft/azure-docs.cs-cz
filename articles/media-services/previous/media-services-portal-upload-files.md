---
title: Nahrání souborů do účtu Media Services na webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede postupem nahrání souborů do účtu Media Services na webu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 7730d15b0d3be9c0ffc7c7fbafe40fece713afbd
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976788"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal-legacy"></a>Nahrání souborů do účtu Media Services na webu Azure Portal (starší verze)

> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

Ve službě Azure Media Services nahráváte digitální soubory do prostředku. Prostředek může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech). Po nahrání souborů bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

> [!NOTE]
> Služba Media Services má pro zpracování souborů omezení maximální velikosti souboru. Podrobnosti o omezeních velikosti souboru najdete v tématu [Kvóty a omezení služby Media Services](media-services-quotas-and-limitations.md).
>

## <a name="upload-files"></a>Nahrání souborů
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení** > **Assety**. Pak vyberte tlačítko **Nahrát**.
   
    ![Nahrání souborů](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zobrazí se okno **Nahrát asset videa**.
   
   > [!NOTE]
   > Služba Media Services pro nahrávání videí neomezuje velikost souboru.
 
3. Na svém počítači přejděte k videu, které chcete nahrát. Vyberte video a pak vyberte **OK**.  
   
    Spustí se nahrávání. Průběh můžete sledovat pod názvem souboru.  

Po dokončení nahrávání bude nový prostředek uvedený v podokně **Prostředky**. 

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [kódovat nahrané prostředky](media-services-portal-encode.md).

* Můžete také použít službu Azure Functions k aktivaci úlohy kódování při doručení souboru do nakonfigurovaného kontejneru. Další informace najdete v ukázce kódu na [Media Services: Integrace služby Azure Media Services pomocí Azure Functions a Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).


