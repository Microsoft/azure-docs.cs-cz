---
title: Jak přidat cílovou skupinu Preview pro nabídku aplikací Azure
description: Naučte se, jak přidat cílovou skupinu Preview pro nabídku aplikací Azure v partnerském centru.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370085"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Jak přidat cílovou skupinu Preview pro nabídku aplikací Azure

Tento článek popisuje, jak nakonfigurovat cílovou skupinu verze Preview pro nabídku aplikací Azure na komerčním webu Marketplace. Musíte definovat cílovou skupinu verze Preview, která může zkontrolovat seznam nabídek ještě před tím, než bude aktivní.

## <a name="define-a-preview-audience"></a>Definovat cílovou skupinu Preview

Na stránce **verze Preview** můžete definovat omezené cílové skupiny, které vám umožní zkontrolovat nabídku aplikací Azure ještě předtím, než ji publikujete na širší cílovou skupinu Marketplace. Nadefinujete cílovou skupinu verze Preview pomocí ID předplatného Azure spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět. ID vašeho předplatného Azure můžete najít na stránce **předplatná** v Azure Portal.

Přidejte minimálně jedno a až 10 ID předplatného Azure, buď jednotlivě (až 10), nebo nahrajte soubor CSV (až 100) a definujte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Je-li již vaše nabídka živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování nabídky změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílové skupině Preview je povolen přístup k vaší nabídce ještě předtím, než se publikuje živě v online obchodech. Můžou zobrazit a ověřit všechny plány, včetně těch, které budou dostupné jenom pro soukromou cílovou skupinu po úplném publikování vaší nabídky na webu Marketplace. Plán můžete vytvořit pouze pro privátní cílovou skupinu. Privátní skupina (definovaná na kartě **dostupnost** plánu) má výhradní přístup k určitému plánu.

### <a name="add-email-addresses-manually"></a>Ruční přidání e-mailových adres

1. Na stránce **Preview cílovou skupinu** přidejte do uvedených polí jedno ID předplatného Azure a volitelný popis.
1. Pokud chcete přidat další e-mailovou adresu, vyberte odkaz **Přidat ID (max. 10)** .
1. Před pokračováním na další kartu vyberte **Uložit koncept** : technická konfigurace.
1. Přejít na [Další kroky](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>Přidání e-mailových adres pomocí souboru CSV

1. Na stránce **Preview cílovou skupinu** vyberte odkaz **exportovat cílovou skupinu (CSV)** .
1. Otevřete. Soubor CSV v aplikaci, jako je například Microsoft Excel.
1. V. Soubor CSV ve sloupci **ID** zadejte ID předplatného Azure, která chcete přidat k cílové skupině Preview.
1. Ve sloupci **Popis** můžete volitelně přidat popis pro každou e-mailovou adresu.
1. Do sloupce **typ** přidejte **SubscriptionId** do každého řádku, který má e-mailovou adresu.
1. Uložte soubor jako. Soubor CSV.
1. Na stránce **Preview cílovou skupinu** vyberte odkaz **importovat cílovou skupinu (CSV)** .
1. V dialogovém okně **Potvrdit** vyberte **Ano**.
1. Vyberte. Soubor CSV a pak vyberte **otevřít**.
1. Před pokračováním na další kartu vyberte **Uložit koncept** : technická konfigurace.

## <a name="next-steps"></a>Další kroky

- [Jak přidat technické podrobnosti pro nabídku aplikací Azure](create-new-azure-apps-offer-technical.md)
