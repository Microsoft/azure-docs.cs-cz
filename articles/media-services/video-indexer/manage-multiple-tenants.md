---
title: Správa více tenantů pomocí Video Indexer – Azure
description: Tento článek navrhuje různé možnosti integrace pro správu více tenantů pomocí Video Indexer.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76990500"
---
# <a name="manage-multiple-tenants"></a>Správa několika klientů

Tento článek popisuje různé možnosti správy více tenantů pomocí Video Indexer. Vyberte si metodu, která je pro váš scénář nejvhodnější:

* Jeden účet Video Indexeru na tenanta
* Jeden účet Video Indexeru pro všechny tenanty
* Jedno předplatné Azure na tenanta

## <a name="video-indexer-account-per-tenant"></a>Jeden účet Video Indexeru na tenanta

Při použití této architektury se pro každého tenanta vytvoří účet Video Indexer. Klienti mají úplnou izolaci v trvalé a výpočetní vrstvě.  

![Jeden účet Video Indexeru na tenanta](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Důležité informace

* Zákazníci nesdílejí účty úložiště (Pokud je ručně nenakonfiguroval zákazník).
* Zákazníci nesdílejí výpočetní výkon (rezervované jednotky) a neovlivňují zpracování úloh navzájem.
* Klienta můžete snadno odebrat ze systému odstraněním účtu Video Indexer.
* Není možné sdílet vlastní modely mezi klienty.

    Ujistěte se, že neexistuje žádný podnikový požadavek na sdílení vlastních modelů.
* Těžší spravovat z důvodu několika účtů Video Indexer (a přidružených Media Services) na jednoho klienta.

> [!TIP]
> Vytvořte uživatele s právy pro správu pro váš systém na [portálu video indexer Portal](https://api-portal.videoindexer.ai/) a pomocí AUTORIZAČNÍHO rozhraní API Poskytněte svým klientům příslušný [přístupový token účtu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Jeden Video Indexer účet pro všechny uživatele

Při používání této architektury je zákazník zodpovědný za izolaci klientů. Všichni klienti musí používat jeden Video Indexer účet s jedním účtem Azure Media Service. Při nahrávání, vyhledávání nebo odstraňování obsahu bude zákazník potřebovat vyfiltrovat správné výsledky pro tohoto tenanta.

![Jeden Video Indexer účet pro všechny uživatele](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Díky této možnosti mohou být modely přizpůsobení (osoba, jazyk a značky) sdíleny nebo izolovány od klientů pomocí filtrování modelů.

Při [nahrávání videí](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)můžete pro každého tenanta zadat jiný atribut oddílu. To umožní izolaci v [rozhraní API pro hledání](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Zadáním atributu partition v rozhraní API pro hledání získáte jenom výsledky zadaného oddílu. 

### <a name="considerations"></a>Důležité informace

* Možnost sdílet obsah a přizpůsobit modely mezi klienty.
* Jeden tenant má dopad na výkon jiných tenantů.
* Zákazník musí vytvořit komplexní vrstvu správy nad Video Indexer.

> [!TIP]
> Pomocí atributu [priority](upload-index-videos.md) můžete určit prioritu úloh klientů.

## <a name="azure-subscription-per-tenant"></a>Jedno předplatné Azure na tenanta 

Když použijete tuto architekturu, bude mít každý tenant vlastní předplatné Azure. Pro každého uživatele vytvoříte nový účet Video Indexer v rámci předplatného tenanta.

![Jedno předplatné Azure na tenanta](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Důležité informace

* Toto je jediná možnost, která umožňuje oddělení fakturace.
* Tato integrace má větší nároky na správu než Video Indexer účet na tenanta. Pokud fakturace není požadavkem, doporučuje se použít jednu z dalších možností popsaných v tomto článku.

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
