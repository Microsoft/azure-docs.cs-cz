---
title: Privátní SKU a plány | Dokumentace Microsoftu
description: Jak používat privátní skladové položky ke správě dostupnosti nabídky.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 519f0354f2a19e106ca1072170721b27357d173e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849203"
---
<a name="private-skus-and-plans"></a>Privátní SKU a plány
============

Privátní SKU umožňují omezit dostupnost SKU pro konkrétní zákazníky. Když skladová položka je označena privátní, není k dispozici ve všech veřejných katalogu včetně na [Azure Marketplace](https://azuremarketplace.microsoft.com) a [webu Azure portal](http://portal.azure.com). Na portálu Azure portal jenom zákazníkům s přístupem k SKU je můžou zobrazit. Kromě toho by také být uživatel vyzván, aby měli přístup k soukromé nabídky.

>[!NOTE]
>Privátní SKU musí mít nové jedinečné ID SKU nebo plán, aby rozporu s veřejnou skladové jednotky.

Můžete používat privátní SKU pro zpracování scénářů:

1.  Publikujte software, který má pouze k dispozici pro konkrétní zákazníky a veřejně dostupné.
2.  Publikujte změny veřejné softwaru za cenu přizpůsobené pro konkrétní zákazníky.
3.  Publikujte změny veřejné softwaru vlastní popis a podmínky (prostřednictvím nové nabídky).

Pokud chcete změnit ceny, můžete znovu použít disky z jinou jednotku SKU ve stejné nabídce. Privátní skladových položek není nutné znovu odeslat disky napříč skladové položky.

<a name="mark-a-sku-private"></a>Označit privátní SKU
---------------------

Označit jako soukromé SKU, přepněte možnost s dotazem, pokud je privátní SKU:

![Označit jako soukromé SKU](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Můžete znovu použít disky v jinou jednotku SKU a změnit ceny nebo popis. Chcete-li znovu použít disky, vyberte **Ano** jako odpověď na "Nepodporuje tuto skladovou Položku znovu použít obrázky z veřejného SKU" výzva.

Pokud SKU je označen jako privátní a nabídka má ostatní SKU s kupříkladu disky, je nutné k označení, že SKU opětovně používá disky ze jinou jednotku SKU. Navíc je třeba zadat cílovou skupinou pro SKU privátní.

>[!NOTE]
>Po publikování, veřejné skladovou Položku nelze nastavit jako soukromé.

<a name="select-an-image"></a>Vybrat obrázek
------------------

Můžete zadat nové disky, které pro privátní SKU nebo znovu použít stejné disky už k dispozici v jiném SKU, pouze změna ceny nebo popis. Chcete-li znovu použít disky, vyberte **Ano** jako odpověď na výzvu "Nepodporuje tuto skladovou Položku znovu použít image z veřejné skladová položka".

![Označit image znovu použít.](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Jakmile potvrdíte, že SKU opětovně používá Image z jinou jednotku SKU, identifikovat skladové položky, které je zdrojem obrázků.

Zachycení pokynů na další obrazovce zobrazit, jak identifikovat privátní SKU by znovu použít Image z vybraná skladová položka:

![Vybrat obrázek](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Při publikování nabídky Image z vybraná skladová položka by být k dispozici v rámci privátní ID SKU s vlastní sazby/podmínky. Privátní SKU pouze staly viditelnými pro určenou cílovou skupinu.

Pro aktualizace imagí můžete by pouze muset aktualizovat bitovou kopii pro základní SKU. Na pozadí bitovou kopii pro privátní SKU se také automaticky aktualizovat. Podobně pokud odstranit image z základní SKU image také se odeberou ze soukromé SKU.

<a name="restricting-the-audience"></a>Omezení cílové skupiny
------------------------

Soukromé nabídky můžete najít a nasadit jenom pomocí cílových uživatelů.
Momentálně podporujeme cílení na uživatele, kteří používají ID předplatného.

Tato předplatná můžete zaznamenat přes formulář ruční zadání **pro až 10 předplatná**, nebo tak, že nahrajete soubor CSV, který umožňuje **pro až 20 000 předplatných**.

Ruční zadání cílové skupiny s omezeným přístupem:

![Ručně omezit cílové skupiny](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Nahrát CSV pro cílovou skupinu s omezeným přístupem:

![Pomocí sdíleného svazku clusteru můžete omezit cílové skupiny](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Ukázkový obsah souboru CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Když přepnete z ruční zadání do sdíleného svazku clusteru nahrát zobrazení nebo ze souboru CSV pro ruční zadání není zachováno původní seznam ID předplatných s přístupem k SKU. Zobrazí se upozornění a v seznamu je přepsat jenom při ukládání nabídky.

<a name="sync-private-subscriptions"></a>Synchronizace privátní předplatná
-------------------------

Při přidávání předplatná do publikované nabídky s privátní SKU nebo plán, není potřeba znovu publikovat nabídka přidat informace o cílové skupině. Stačí použijte ID předplatného Azure (plány a skladové položky) nebo ID Tenanta (pouze plány) Chcete-li přidat cílové skupiny.

<a name="previewing-private-offers"></a>Nabízí privátní náhled
-------------------------

Během verze preview/testovacího kroku, jen pro předplatná nabídky úrovně ve verzi preview bude mít přístup k SKU. To je, že testovací fáze, po kterém můžete ověřit, jaké nabídky může vypadat třeba cílové zákazníkům a je standardní pro všechny typy publikování.

Nabízejí úroveň předplatných ve verzi Preview pro přístup k dvoufázové instalace nabídky:

![ID předplatných ve verzi Preview](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po nabídky je v provozu, bude moct zobrazovat a nasadit privátní SKU jenom předplatná cílové skupiny s omezeným přístupem (zadaná prostřednictvím ruční zadání nebo sdílený svazek clusteru). Doporučujeme vám **vždy zahrnout vlastní odběry s omezeným přístupem cílovou skupinu** pro privátní SKU pro účely ověření.

>[!NOTE]
>Pro účely ladění, podporu Microsoftu a technické týmy se také měli tyto soukromé nabídky.
