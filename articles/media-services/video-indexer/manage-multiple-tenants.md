---
title: Spravujete víc klientů s Video Indexer – Azure
description: Tento článek navrhuje různých integrace možností pro správu více tenantů s Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: ikbarmen
ms.openlocfilehash: 800cd04f8f4acf7a9768a8ccdce5a4d9783d3a4d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985173"
---
# <a name="manage-multiple-tenants"></a>Spravujete víc klientů

Tento článek popisuje různé možnosti pro správu více tenantů s Video Indexer. Zvolte metodu, která bude nejvíc vyhovovat vašemu scénáři:

* Účet služby video Indexer na tenanta
* Jeden účet služby Video Indexer pro všechny klienty
* Předplatné Azure na tenanta

## <a name="video-indexer-account-per-tenant"></a>Účet služby video Indexer na tenanta

Při použití této architektury, účet služby Video Indexer se vytvoří pro každého tenanta. Klienti máte úplnou izolaci trvalé a výpočetní vrstvy.  

![Účet služby video Indexer na tenanta](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Požadavky

* Zákazníci nesmí sdílet účty úložiště (Pokud zákazník ručně nakonfigurovali).
* Zákazníci, nesdílejí výpočetní prostředky (rezervovaných jednotek) a nebudou mít vliv na dobu zpracování úloh navzájem.
* Klienta můžete snadno odebrat ze systému odstraněním účet služby Video Indexer.
* Neexistuje žádná možnost sdílet vlastní modely mezi tenanty.

    Ujistěte se, že neexistuje žádný požadavek obchodní ke sdílení vlastních modelů.
* Obtížnější spravovat účty pro každého klienta z důvodu více Video Indexer (a související služby Media Services).

> [!TIP]
> Vytvoření uživateli s právy pro váš systém v [portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/) a pomocí rozhraní API pro povolení klientům poskytovat příslušné [přístupový token účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Jeden účet služby Video Indexer pro všechny uživatele

Při použití této architektury, zákazník zodpovídá za izolaci tenantů. Za všechny tenanty nutné použít jeden účet služby Video Indexer pomocí jednoho účtu Azure Media Service. Při nahrávání, vyhledávání, nebo odstraníte obsah, zákazník muset filtrovat správné výsledky pro tohoto tenanta.

![Jeden účet služby Video Indexer pro všechny uživatele](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Pomocí této možnosti můžete sdílené nebo samostatný mezi tenanty filtrováním modely tenantem přizpůsobení modely (osoba, jazyk a značky).

Když [nahrávání videí](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), můžete určit atribut jiný oddíl každého tenanta. To vám umožní izolace v [hledání rozhraní API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Zadáním atributu oddílu v rozhraní API pro vyhledávání získáte jenom výsledky zadaný oddíl. 

 ### <a name="considerations"></a>Požadavky

* Možnost sdílení obsahu a přizpůsobení modelů mezi tenanty.
* Jeden tenant má vliv na výkon jiných klientů.
* Zákazník musí vytvářet komplexní správu vrstvu nad Video Indexer.

> [!TIP]
> Můžete použít [priority](upload-index-videos.md) atribut k určení priority úlohy tenantů.

## <a name="azure-subscription-per-tenant"></a>Předplatné Azure na tenanta 

Při použití této architektury, bude mít každý klient své předplatné Azure. Pro každého uživatele vytvoříte v rámci předplatného tenanta nový účet služby Video Indexer.

![Předplatné Azure na tenanta](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Požadavky

* Toto je jediná možnost, která umožňuje fakturační oddělení.
* Tato integrace je k dispozici další režie na správu než účet služby Video Indexer na tenanta. Pokud fakturace není povinné, se doporučuje používat jednu z dalších možností popsané v tomto článku.

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
