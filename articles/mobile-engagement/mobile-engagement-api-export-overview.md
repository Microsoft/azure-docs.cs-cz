---
title: Přehled rozhraní API Export Mobile Engagementu
description: Získejte základní informace o exportu nezpracovaných dat generovaná zařízeními uživatelů můžete využít ve vlastní nástroje pro správu
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: ''
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: c1dc8a34abf84be3d7f59d41c36d90c2a67e5d32
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="mobile-engagement-export-api-overview"></a>Přehled rozhraní API Export Mobile Engagementu
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

## <a name="introduction"></a>Úvod
V tomto dokumentu se dozvíte základní informace o exportu nezpracovaných dat generovaná zařízeními uživatelů můžete využít ve vlastní nástroje pro správu.

## <a name="pre-requisites"></a>Požadavky
Export nezpracovaná data z Mobile Engagement vyžaduje:

* Nastavení ověřování rozhraní API, abyste mohli použít rozhraní API (viz [ruční instalaci ověřování](mobile-engagement-api-authentication-manual.md)),
* Použití rozhraní REST API nebo [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Účet služby Azure Storage.

> [!NOTE]
> Také doporučujeme jako vynikající [Microsoft Azure Storage Explorer](http://storageexplorer.com/), alespoň v průběhu fáze vývoje jak poskytuje snadno použitelný uživatelského rozhraní pro interakci s Azure Storage.
> 
> 

## <a name="what-can-be-exported"></a>Co je možné exportovat?
Mobile Engagement umožňuje svým uživatelům ke shromažďování mnoho typů dat a proto má několik typů export vhodnější tyto různé datové typy.
Existují 2 základní typy exportu:

* Snímek: používá obvykle export dat, která představuje stavu a pro které Mobile Engagement nemá historie. To zahrnuje třeba značky (app-info), tokeny nebo nabízené kampaně názory. V důsledku těchto typů export nesouvisí se datum.
* historické: Tento typ exportu se používá pro data, která shromažďuje časem například události nebo aktivity, například.

Následující tabulka popisuje podrobně všechny možné exporty:

| Typ exportu | Typ dat | Popis |
| --- | --- | --- |
| Snímek |Nabízená oznámení |Generuje o export nabízené kampaně názory na základě za deviceid/ID uživatele |
| Snímek |Značka |Exportu značky (app-info) přidružený ke každé zařízení generuje |
| Snímek |Zařízení |Generuje o export většina dat o zařízení, jako jsou technicals (modelu, národní prostředí, časové pásmo,...), značky, zaznamenané poprvé... |
| Snímek |Podpisový |Generuje o export platné tokeny |
| Historie |Aktivita |Generuje o export všechny aktivity pro každé zařízení v daném časovém období |
| Historie |Událost |Generuje o export všechny aktivity pro každé zařízení v daném časovém období |
| Historie |Úloha |Generuje o export všechny úlohy pro každé zařízení v daném časovém období |
| Historie |Chyba |Generuje o export všech chyb pro každé zařízení v daném časovém období |

## <a name="how-does-it-work"></a>Jak to funguje?
Exportuje jsou dlouho spuštěných úloh, který může vytvořit velké datové soubory. Z tohoto důvodu se nedá vyvolat vrátit okamžitě soubor ke stažení.
Aby bylo možné exportovat data z Mobile Engagement, budete muset vytvořit **úlohy exportu** prostřednictvím rozhraní API, kde můžete určit obecně:

* Typ exportu (snímek nebo historické),
* Datový typ
* **Kontejneru úložiště Azure** (včetně platný SAS s přístup pro zápis) zápis výsledek exportu.
* například parametr URL kontejneru příkladu by https://[StorageAccountName].blob.core.windows.net/[ContainerName]? [SASWritePermissionsToken]  

Tady je příklad skutečném světě. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Upozorňujeme, že může trvat několik minut pro úlohu spustit, a pak může spustit za několik sekund pro malá velikost aplikace několik hodin pro aplikace s velkým množstvím uživatelů nebo aktivity.

Po vytvoření úlohy, je možné zkontrolovat její stav chcete zobrazit, pokud je stále spuštěna, nebo pokud byla dokončena.

Po úlohu není úspěšné, bude výsledný soubor dat je k dispozici v kontejneru zadané úložiště.

