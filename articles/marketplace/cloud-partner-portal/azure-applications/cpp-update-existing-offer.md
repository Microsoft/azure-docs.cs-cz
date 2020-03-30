---
title: Aktualizace existující nabídky aplikace Azure | Azure Marketplace
description: Jak aktualizovat existující nabídku aplikací Azure na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275947"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizace existující nabídky aplikace Azure

Existují různé druhy aktualizací, které budete chtít udělat, aby vaše nabídka poté, co byla zveřejněna a je žít. Každá změna, kterou provedete v nové verzi nabídky, by měla být uložena a znovu publikována, aby se mohla odrážet na Marketplace. Tento článek prochází různé aspekty aktualizace nabídky spravované aplikace na [portálu cloudových partnerů](https://cloudpartner.azure.com/).

Nabídku můžete aktualizovat několika důvody, například:

- Přidání nové verze bitové kopie do existujících sku.
- Přidání nových skum.
- Aktualizace metadat marketplace pro nabídku nebo jednotlivé skum.

Portál poskytuje funkce **Porovnání** a **Historie,** které vám pomohou s těmito úpravami.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Nepovolené změny nabídky aplikace Azure nebo skladové položky

Existují atributy nabídky kontejneru nebo skladové položky, které nelze změnit po nabídce je aktivní na Azure Marketplace. Nelze změnit následující nastavení:

- ID nabídky a ID vydavatele nabídky
- ID skladové položky existujících skladových částek
- Značky verzí, například:`1.0.1`
- Změny fakturačního/licenčního modelu stávajících skutých.

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-image-version-for-a-sku"></a>Aktualizace verze bitové kopie pro skladovou položku

Je běžné, že bitová kopie je pravidelně aktualizována bezpečnostními záplatami, dalšími funkcemi a tak dále. V tomto scénáři chcete aktualizovat bitovou kopii, na kterou vaše skladová položka odkazuje, pomocí následujících kroků:

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **Skladové položky** vyberte skladovou položku přidruženou k bitové kopii, kterou chcete aktualizovat.
4. Výběrem **možnosti + Nová verze obrázku** přidáte nový obrázek.
5. Zadejte nové verze obrázků. Verze bitové kopie musí dodržovat stejná pravidla pro značky jako předchozí verze. Značky verze by měly být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla. Ověřte, zda je nová verze, kterou zadáte, větší než všechny předchozí verze.
6. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali novou verzi image kontejneru na Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidání nové skladové položky

Pomocí následujících kroků můžete zpřístupnit novou skladovou položku pro vaši nabídku:

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **Skladové položky** vyberte **Přidat novou skladovou položku** a ve vyskakovacím okně zadejte **ID skladové** položky.
4. Znovu publikovat nabídku pomocí kroků popsaných v [nabídce aplikace Publikovat Azure](./cpp-publish-offer.md).
5. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali novou skladovou položku.

### <a name="update-offer-marketplace-metadata"></a>Aktualizovat metadata tržiště

Pomocí následujících kroků aktualizujte metadata webu marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najdete nabídku, kterou chcete aktualizovat.
3. Přejděte na kartu **Marketplace.** Pomocí pokynů v [nabídce aplikace Publish Azure](./cpp-publish-offer.md) můžete provádět změny metadat.
4. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali změny.
 
>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](../../cloud-solution-providers.md)

## <a name="deleting-an-existing-offer"></a>Odstranění existující nabídky

Můžete se rozhodnout svou nabídku z webu Marketplace odebrat. Odstranění nabídky nemá vliv na aktuální nákupy této nabídky. Tyto nákupy zákazníků budou i nadále fungovat jako dříve. Nabídka však nebude po dokončení odstranění k dispozici pro žádné nové nákupy.

Ukončení nabídky je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky.
Pokyny a zásady týkající se odebrání a ukončení nabídky se řídí smlouvou vydavatele na webu Microsoft Marketplace (viz oddíl 7) a zásadami účasti (viz oddíl 6.2).

Další informace najdete [v tématu Odstranění a nabídka/Sklad skladových položk z Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Funkce Porovnat

Když provedete změny publikované nabídky, můžete pomocí funkce Porovnat auditovat provedené změny.

Použití funkce Porovnat:

1. Kdykoli v procesu úprav vyberte porovnat pro vaši nabídku.
2. Podívejte se na souběžné verze marketingových prostředků a metadat.

## <a name="history-of-publishing-actions"></a>Historie publikování akcí

Chcete-li zobrazit historickou publikační aktivitu, vyberte kartu **Historie** na levém panelu nabídek cloudového partnerského portálu. Můžete vidět akce s časovým razítkem provedené během životnosti nabídek Azure Marketplace.

## <a name="next-steps"></a>Další kroky

[Nabídka aplikací Azure](./cpp-azure-app-offer.md)
