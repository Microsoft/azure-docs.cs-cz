---
title: Správa více klientů pomocí Video Indexer – Azure
description: Tento článek navrhuje různé možnosti integrace pro správu více klientů s Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990500"
---
# <a name="manage-multiple-tenants"></a>Správa několika klientů

Tento článek popisuje různé možnosti pro správu více klientů s Video Indexer. Zvolte metodu, která je nejvhodnější pro váš scénář:

* Účet video indexeru na klienta
* Účet jednoho video indexeru pro všechny klienty
* Předplatné Azure na klienta

## <a name="video-indexer-account-per-tenant"></a>Účet video indexeru na klienta

Při použití této architektury je vytvořen účet Video Indexer pro každého klienta. Klienti mají úplnou izolaci v trvalé a výpočetní vrstvy.  

![Účet video indexeru na klienta](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Požadavky

* Zákazníci nesdílejí účty úložiště (pokud není ručně nakonfigurován zákazníkem).
* Zákazníci nesdílejí výpočetní prostředky (rezervované jednotky) a nemají vliv na pracovní doby zpracování navzájem.
* Klienta můžete snadno odebrat ze systému odstraněním účtu Video Indexer.
* Neexistuje žádná možnost sdílet vlastní modely mezi tenanty.

    Ujistěte se, že neexistuje žádný obchodní požadavek na sdílení vlastních modelů.
* Těžší spravovat z důvodu více účtů Video Indexer (a přidružené media services) na klienta.

> [!TIP]
> Vytvořte uživatele správce pro váš systém na [portálu pro vývojáře videoindexeru](https://api-portal.videoindexer.ai/) a pomocí autorizačního rozhraní API poskytněte svým klientům příslušný [přístupový token účtu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Účet jednotného video indexeru pro všechny uživatele

Při použití této architektury je zákazník zodpovědný za izolaci klientů. Všichni klienti musí používat jeden účet Video Indexer s jedním účtem Azure Media Service. Při nahrávání, vyhledávání nebo odstranění obsahu bude zákazník muset filtrovat správné výsledky pro tohoto klienta.

![Účet jednotného video indexeru pro všechny uživatele](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Pomocí této možnosti lze modely přizpůsobení (osoba, jazyk a značky) sdílet nebo izolovat mezi klienty filtrováním modelů podle tenanta.

Při [nahrávání videí](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)můžete zadat jiný atribut oddílu na klienta. To umožní izolaci ve [vyhledávacím rozhraní API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Zadáním atributu oddílu v rozhraní API pro vyhledávání získáte pouze výsledky zadaného oddílu. 

### <a name="considerations"></a>Požadavky

* Možnost sdílení obsahu a modelů přizpůsobení mezi klienty.
* Jeden klient ovlivňuje výkon ostatních klientů.
* Zákazník potřebuje vytvořit komplexní vrstvu správy nad video indexerem.

> [!TIP]
> Atribut [priority](upload-index-videos.md) můžete použít k určení priority úloh nájemníků.

## <a name="azure-subscription-per-tenant"></a>Předplatné Azure na klienta 

Při použití této architektury bude mít každý klient své vlastní předplatné Azure. Pro každého uživatele vytvoříte nový účet Video Indexer v předplatném klienta.

![Předplatné Azure na klienta](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Požadavky

* Toto je jediná možnost, která umožňuje oddělení fakturace.
* Tato integrace má větší režii na správu než účet Video Indexer na klienta. Pokud fakturace není požadavek, je doporučeno použít jednu z dalších možností popsaných v tomto článku.

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
