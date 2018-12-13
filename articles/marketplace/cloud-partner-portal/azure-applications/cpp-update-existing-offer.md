---
title: Aktualizace stávající nabídky aplikace Azure | Dokumentace Microsoftu
description: Jak aktualizovat stávající nabídky Azure aplikace na Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 3fbbf688f6de7c3fceb6695a6b085f917dbec242
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196663"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizace stávající nabídky aplikace Azure

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku poté, co byla publikována a je v provozu. Všechny změny provedené v nové verzi nabídky by měla uložit a publikovat jej odrážejí na webu Marketplace. Tento článek popisuje postup prostřednictvím různých aspektů aktualizuje vaše nabídka spravované aplikace v [portál partnerů cloudu](https://cloudpartner.azure.com/).

Tady je několik důvodů, proč můžete chtít aktualizovat vaši nabídku, jako například:

- Přidává se nová verze image do existujících skladových položek.
- Přidání nové SKU.
- Aktualizace metadat marketplace pro jednotlivé SKU a nabídky.

Které vám pomohou tyto úpravy, portál poskytuje **porovnání** a **historie** funkce.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Nedovolené změny do nabídky aplikace Azure nebo SKU

Existují atributy nabídky kontejner nebo skladovou jednotku, kterou nelze změnit poté, co tato nabídka je za provozu na webu Azure Marketplace. Nelze změnit následující nastavení:

- ID nabídky a ID vydavatele, nabídky
- ID SKU existující skladové položky
- Verze značky, například: `1.0.1`
- Model fakturace nebo licencí se změní na existující skladové položky

## <a name="common-update-operations"></a>Běžné operace update

Následující operace update jsou společná.

### <a name="update-image-version-for-a-sku"></a>Aktualizace verze image pro SKU

Je běžné, obrázek, který se pravidelně aktualizují opravy zabezpečení, další funkce a tak dále. V tomto scénáři kterou chcete aktualizovat obrázek, který odkazuje na skladovou jednotku s použitím následujících kroků:

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. V **SKU** kartu, vyberte skladovou Položku přidruženou bitovou kopii k aktualizaci.
4. Vyberte **+ nová verze Image** přidáte novou bitovou kopii.
5. Zadejte nové verze image. Verze image musí postupujte podle pokynů na stejné značky jako předchozí verze. Označení verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Ověřte, zda je větší než všechny předchozí verze nové verze, kterou zadáte.
6. Vyberte **publikovat** spuštění pracovního postupu pro publikování novou verzi image kontejneru na webu Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou skladovou Položku

Chcete-li novou skladovou Položku pro danou nabídku dostupná, postupujte následovně:

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. V části **SKU** kartu, vyberte možnost **přidat novou skladovou Položku** a zadejte **SKU ID** v místním okně.
4. Publikovat nabídku, pomocí kroků popsaných v [nabídka aplikaci Azure publikovat](./cpp-publish-offer.md).
5. Vyberte **publikovat** spuštění pracovního postupu pro publikování nové skladové jednotky.

### <a name="update-offer-marketplace-metadata"></a>Aktualizace nabídky marketplace metadat

Použijte následující postup k aktualizaci marketplace metadata související s vaší nabídkou. (Příklad: název společnosti, loga, atd.)

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. Přejděte **Marketplace** kartu. Postupujte podle pokynů v [nabídka aplikaci Azure publikovat](./cpp-publish-offer.md) provést změny metadat.
4. Vyberte **publikovat** ke spuštění pracovního postupu publikovat provedené změny.

## <a name="deleting-an-existing-offer"></a>Odstraňuje se stávající nabídky

Můžete rozhodnout odebrat svou nabídku z webu Marketplace. Odstranění nabídky nemá vliv na aktuální nákupy na základě této nabídky. Tyto nákupy zákazníků budou nadále fungovat jako předtím. Tuto nabídku však nebudou k dispozici pro všechny nové nákupy po odstranění je dokončeno.

Ukončení nabídky je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky.
Pokyny a zásady týkající se odebrání a ukončení nabídky se řídí smlouvy vydavatele Microsoft Marketplace (viz sekce 7) a zásadách pro účast (viz část 6.2).

Další informace najdete v tématu [Delete a/SKU nabídky z Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Porovnání funkcí

Když provedete změny v publikované nabídky, můžete použít funkci porovnání auditovat změny, které jste udělali.

Jak používat funkci porovnání:

1. V každém okamžiku procesu úprav vyberte porovnání pro vaši nabídku.
2. Podívejte se na verzích vedle sebe marketingové prostředky a metadata.

## <a name="history-of-publishing-actions"></a>Historie publikování akce

Pokud chcete zobrazit Historický publikační aktivity, vyberte **historie** karty na levé navigační nabídky panelu z cloudu portál pro partnery. Zobrazí se tedy akce prováděné po celou dobu životnosti vaší nabídky na webu Azure Marketplace.

## <a name="next-steps"></a>Další postup

[Nabídka aplikace Azure](./cpp-azure-app-offer.md)