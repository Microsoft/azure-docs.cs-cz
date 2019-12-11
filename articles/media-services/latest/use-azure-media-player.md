---
title: Přehrávání pomocí Azure Media Player – Azure | Microsoft Docs
description: Azure Media Player je webový přehrávač, který je založený na přehrávání mediálního obsahu z Microsoft Azure Media Services na nejrůznějších prohlížečích a zařízeních.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: a0b0a4e89242103811f20071b7235c825c9d8bd2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967520"
---
# <a name="azure-media-player-overview"></a>Přehled Azure Media Player

Azure Media Player je webový přehrávač, který je založený na přehrávání mediálního obsahu z Microsoft Azure Media Services na nejrůznějších prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), aby poskytovala obohacené možnosti adaptivního streamování. Pokud tyto standardy nejsou k dispozici v zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologii Flash a Silverlight. Bez ohledu na použitou technologii přehrávání budou mít vývojáři sjednocené rozhraní JavaScript pro přístup k rozhraním API. To umožňuje, aby se obsah sloužil Azure Media Services, aby se hrál v široké škále zařízení a prohlížečů bez dalšího úsilí.

Microsoft Azure Media Services umožňuje, aby se obsah mohl zpracovat pomocí HLS, POMLČKy a Smooth Streamingch formátů streamování pro přehrávání obsahu. Azure Media Player brát v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz na základě schopností platforem a prohlížečů. Media Services taky umožňuje dynamické šifrování assetů pomocí šifrování PlayReady nebo šifrování obálek s kódováním AES-128. Azure Media Player umožňuje dešifrování šifrovaného obsahu PlayReady a AES-128, když je správně nakonfigurovaný. 

> [!NOTE]
> Pro Widevine zašifrovaný obsah se vyžaduje přehrávání HTTPS.

[Vyzkoušejte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Použití ukázkové stránky Azure Media Player

### <a name="start-using"></a>Začít používat

K přehrání ukázek Azure Media Services nebo vlastního datového proudu můžete použít [stránku Azure Media Player demo](https://aka.ms/azuremediaplayer) .  

Pokud chcete přehrát nové video, vložte jinou adresu URL a stiskněte **aktualizovat**.

Pokud chcete nakonfigurovat různé možnosti přehrávání (například tech, Language nebo Encryption), stiskněte **Rozšířené možnosti**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorování diagnostiky streamu videa

Pomocí [stránky Azure Media Player demo](https://aka.ms/azuremediaplayer) můžete monitorovat diagnostiku streamu videa. 

![Diagnostika Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Nastavení Azure Media Player ve formátu HTML

Nastavení Azure Media Player je snadné. Získání základního přehrávání mediálního obsahu z vašeho účtu Media Services trvá jenom chvíli. Podrobnosti o tom, jak nastavit a nakonfigurovat Azure Media Player, najdete v [dokumentaci k Azure Media Player](https://aka.ms/ampdocs) . 

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

- [Dokumentace k Azure Media Playeru](https://aka.ms/ampdocs)
- [Ukázky Azure Media Player](https://aka.ms/ampsamples)
