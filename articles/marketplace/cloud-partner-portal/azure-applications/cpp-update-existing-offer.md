---
title: Aktualizace stávající nabídky aplikací Azure | Azure Marketplace
description: Jak aktualizovat existující nabídku aplikace Azure na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826103"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizace stávající nabídky aplikací Azure

Existují různé druhy aktualizací, které můžete chtít provést v rámci své nabídky po jejím publikování a živém provozu. Všechny změny, které provedete v nové verzi vaší nabídky, by se měly uložit a znovu publikovat, aby se projevily na webu Marketplace. Tento článek popisuje různé aspekty aktualizace nabídky spravované aplikace v [portál partnerů cloudu](https://cloudpartner.azure.com/).

K dispozici je několik důvodů, proč byste mohli chtít aktualizovat nabídku, například:

- Přidává se nová verze image do stávajících SKU.
- Přidávání nových SKU.
- Aktualizují se metadata Marketplace pro nabídku nebo jednotlivé SKU.

Pro pomoc s těmito úpravami nabízí portál funkce **porovnání** a **Historie** .

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Nepovolené změny nabídky nebo SKU aplikací Azure

Existují atributy nabídky kontejneru nebo SKU, které nelze změnit poté, co je nabídka v Azure Marketplace živá. Nemůžete změnit následující nastavení:

- ID nabídky a ID vydavatele nabídky
- ID SKU existujících SKU
- Značky verze, například: `1.0.1`
- Fakturační a licenční model se mění ve stávajících SKU

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-image-version-for-a-sku"></a>Verze aktualizace image pro SKU

Je běžné, že se image pravidelně aktualizuje pomocí oprav zabezpečení, dalších funkcí atd. V tomto scénáři chcete aktualizovat bitovou kopii, kterou vaše SKU odkazuje, pomocí následujících kroků:

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **SKU** vyberte skladovou jednotku přidruženou k imagi, kterou chcete aktualizovat.
4. Pokud chcete přidat nový obrázek, vyberte **+ Nová verze image** .
5. Zadejte nové verze imagí. Verze image musí splňovat stejné pokyny pro značky jako v předchozích verzích. Značky verze by měly mít formát X. Y. Z, kde X, Y a Z jsou celá čísla. Ověřte, že nová verze, kterou zadáte, je větší než všechny předchozí verze.
6. Vyberte **publikovat** a spusťte pracovní postup, který publikuje novou verzi image kontejneru do Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou SKLADOVOU položku

K dispozici je nová SKU pro vaši nabídku pomocí následujících kroků:

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **SKU** vyberte **Přidat novou SKU** a v automaticky otevíraném okně zadejte **ID SKU** .
4. Znovu publikujte nabídku pomocí postupu popsaného v tématu [publikování nabídky aplikací Azure](./cpp-publish-offer.md).
5. Vyberte **publikovat** a spusťte pracovní postup pro publikování nové SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizace metadat webu nabídky

Pomocí následujících kroků aktualizujte metadata Marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Přejít na kartu **Marketplace** . při provádění změn metadat použijte pokyny v tématu [publikování nabídky aplikací Azure](./cpp-publish-offer.md) .
4. Vyberte **publikovat** a spusťte pracovní postup pro publikování změn.
 
>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](../../cloud-solution-providers.md) .

## <a name="deleting-an-existing-offer"></a>Odstranění existující nabídky

Můžete se rozhodnout odebrat svou nabídku z webu Marketplace. Odstranění nabídky nemá vliv na aktuální nákupy této nabídky. Tyto nákupy zákazníků budou i nadále fungovat jako dříve. Nabídka však nebude k dispozici pro žádné nové nákupy po dokončení odstranění.

Ukončení nabídky je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky.
Doprovodné materiály a zásady týkající se odebrání a ukončení nabídky se řídí smlouvou Microsoft Marketplace vydavateli (viz oddíl 7) a zásadami pro účast (viz oddíl 6,2).

Další informace najdete v tématu [odstranění a nabídka/SKU z Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Compare – funkce

Když provedete změny v publikované nabídce, můžete pomocí funkce Compare auditovat změny, které jste provedli.

Použití funkce Compare:

1. V jakémkoli okamžiku v procesu úprav vyberte porovnat pro vaši nabídku.
2. Podívejte se na souběžné verze materiálů a metadat marketingu.

## <a name="history-of-publishing-actions"></a>Historie akcí publikování

Chcete-li zobrazit historické aktivity publikování, vyberte kartu **Historie** v levém navigačním panelu portál partnerů cloudu. Můžete zobrazit akce s časovým razítkem provedenými během životnosti Azure Marketplace nabídek.

## <a name="next-steps"></a>Další kroky

[Nabídka aplikace Azure](./cpp-azure-app-offer.md)