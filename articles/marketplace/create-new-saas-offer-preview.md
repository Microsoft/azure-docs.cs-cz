---
title: Jak přidat cílovou skupinu Preview pro vaši nabídku SaaS na komerčním webu Microsoft Marketplace
description: Jak přidat cílovou skupinu Preview pro nabídku software as a Service (SaaS) v partnerském centru Microsoftu.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380781"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Jak přidat cílovou skupinu Preview pro nabídku SaaS

Při vytváření nabídky software jako služba (SaaS) v partnerském centru musíte definovat cílovou skupinu verze Preview, která může zkontrolovat seznam nabídek ještě předtím, než bude aktivní. Tento článek vysvětluje, jak nakonfigurovat cílovou skupinu verze Preview.

> [!NOTE]
> Pokud se rozhodnete zpracovávat transakce nezávisle, tato možnost se nezobrazí. Místo toho přejděte k tomu, [jak si SaaS nabídku na trh](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Definovat cílovou skupinu Preview

Na kartě **cílová skupina verze Preview** můžete definovat omezené publikum, který může zkontrolovat vaši nabídku SaaS ještě předtím, než ji publikujete na širší cílovou skupinu na webu Marketplace. Pozvánky můžete odesílat do e-mailových adres účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD). Přidejte minimálně jednu a až 10 e-mailových adres ručně nebo importujte až 20 souborů. csv. Seznam cílových skupin verze Preview můžete kdykoli aktualizovat.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílové skupině Preview je povolen přístup k vaší nabídce ještě předtím, než se publikuje živě v online obchodech. Můžete se také rozhodnout vytvořit plán a zpřístupnit ho pouze pro privátní cílovou skupinu. Soukromé plány jsou vysvětleny v tématu [jak vytvořit plány pro nabídku SaaS](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Ruční přidání e-mailových adres

1. Na stránce **Preview cílovou skupinu** přidejte do uvedených polí jednu e-mailovou adresu Azure AD nebo MSA a případně i její popis.
1. Pokud chcete přidat další e-mailovou adresu, vyberte odkaz **Přidat další e-mailový** odkaz.
1. Před pokračováním na další kartu vyberte **Uložit koncept** : **Technická konfigurace**.
1. Pokračujte [postupem, jak přidat technické podrobnosti pro SaaS nabídku](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Přidání e-mailových adres pomocí souboru CSV

1. Na stránce **Preview cílovou skupinu** vyberte odkaz **exportovat cílovou skupinu (CSV)** .
1. Otevřete. Soubor CSV v aplikaci, jako je například Microsoft Excel.
1. V. Soubor CSV ve sloupci **ID** zadejte e-mailové adresy, které chcete přidat do cílové skupiny Preview.
1. Ve sloupci **Popis** můžete volitelně přidat popis pro každou e-mailovou adresu.
1. Do sloupce **typ** přidejte **MixedAadMsaId** do každého řádku, který má e-mailovou adresu.
1. Uložte soubor jako. Soubor CSV.
1. Na stránce **Preview cílovou skupinu** vyberte odkaz **importovat cílovou skupinu (CSV)** .
1. V dialogovém okně **Potvrdit** vyberte **Ano**.
1. Vyberte. Soubor CSV a pak vyberte **otevřít**.
1. Před pokračováním na další kartu vyberte **Uložit koncept** : **Technická konfigurace**.

## <a name="next-steps"></a>Další kroky

- [Postup přidání technických podrobností pro nabídku SaaS](create-new-saas-offer-technical.md)
