---
title: Jak přidat cílovou skupinu Preview pro nabídku spravované služby
description: Naučte se, jak přidat cílovou skupinu Preview pro nabídku spravované služby v partnerském centru Microsoftu.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918163"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Jak přidat cílovou skupinu Preview pro nabídku spravované služby

Tento článek popisuje, jak nakonfigurovat cílovou skupinu Preview pro nabídku spravované služby na komerčním webu Marketplace pomocí partnerského centra. Cílová skupina verze Preview může zkontrolovat vaši nabídku ještě předtím, než bude živá.

## <a name="define-a-preview-audience"></a>Definovat cílovou skupinu Preview

Na stránce **verze Preview** můžete definovat omezené publikum, který může zkontrolovat vaši nabídku spravované služby před tím, než ji publikujete na širší cílovou skupinu Marketplace. Nadefinujete cílovou skupinu verze Preview pomocí ID předplatného Azure spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět. ID vašeho předplatného Azure můžete najít na stránce **předplatná** na Azure Portal.

Přidejte aspoň jedno ID předplatného Azure, a to buď jednotlivě (až 10), nebo nahrajte soubor CSV (až 100) a definujte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina *verze Preview* se liší od *soukromé* cílové skupiny. Cílové skupině Preview je povolen přístup k vaší nabídce ještě předtím, než se publikuje živě v online obchodech. Můžou zobrazit a ověřit všechny plány, včetně těch, které budou dostupné jenom pro soukromou cílovou skupinu po úplném publikování vaší nabídky na webu Marketplace. Plán můžete vytvořit pouze pro privátní cílovou skupinu. Privátní skupina (definovaná na kartě Dostupnost plánu) má výhradní přístup k určitému plánu.

## <a name="add-email-addresses-manually"></a>Ruční přidání e-mailových adres

1. Na stránce **Preview cílovou skupinu** přidejte do uvedených polí jedno ID předplatného Azure a volitelný popis.
2. Pokud chcete přidat další e-mailovou adresu, vyberte odkaz **Přidat ID (max. 10)** .
3. Než budete pokračovat na další kartu, vyberte **Uložit koncept** .

## <a name="add-email-addresses-using-a-csv-file"></a>Přidání e-mailových adres pomocí souboru CSV

1. Na **stránce verze Preview cílové** skupiny vyberte odkaz **exportovat cílovou skupinu (CSV)** .
2. Otevřete soubor CSV. Do sloupce **ID** zadejte ID předplatného Azure, které chcete přidat do cílové skupiny Preview.
3. Ve sloupci **Popis** máte možnost přidat popis pro každou položku.
4. Do sloupce **typ** přidejte **SubscriptionId** do každého řádku, který má ID.
5. Uložte soubor jako soubor CSV.
6. Na stránce **Preview cílovou skupinu** vyberte odkaz **importovat cílovou skupinu (CSV)** .
7. V dialogovém okně **Potvrdit** vyberte **Ano** a pak nahrajte soubor CSV.
8. Než budete pokračovat na další kartu, vyberte **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

* [Vytváření plánů](create-managed-service-offer-plans.md)
