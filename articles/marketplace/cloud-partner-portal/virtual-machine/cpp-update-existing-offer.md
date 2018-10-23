---
title: Aktualizace stávajícího virtuálního počítače nabízet na webu Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak aktualizovat stávající nabídky virtuálních počítačů na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: b48910b74d90072a360bad504e2b826402dceea5
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639608"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizace stávající nabídky virtuálních počítačů na webu Azure Marketplace

Tento článek vás provede různé aspekty nabídky virtuálních počítačů (VM) v aktualizaci [portál partnerů cloudu](https://cloudpartner.azure.com/) a pak znovu publikovat tuto nabídku. 

Existuje mnoho běžné důvody pro aktualizaci nabídky, včetně:

-  Přidat novou verzi image virtuálního počítače do existující skladové položky
-  Změna oblasti SKU je k dispozici
-  Přidat nové SKU
-  Aktualizujte metadata marketplace pro jednotlivé SKU a nabídky
-  Aktualizovat ceny u nabídek

Které vám pomohou tyto úpravy, tento portál nabízí **porovnání** a **historie** funkce.  


## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Nedovolené změny nabídky virtuálních počítačů nebo SKU

Existují některé atributy nabídky virtuálních počítačů nebo skladovou jednotku, kterou nelze změnit, jakmile tato nabídka je za provozu na webu Azure Marketplace, hlavně:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **SKU ID** existujících skladových položek
-  Datový disk počet existujících skladových položek
-  Model fakturace nebo licencí se změní na existující skladové položky
-  Cena zvýší na publikované skladové položky


## <a name="common-update-operations"></a>Běžné operace update

I když existují širokou škálu vlastnostmi, které můžete změnit na nabídky virtuálních počítačů, jsou společné následující operace.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualizovat verzi image virtuálního počítače pro SKU

Je běžné, že image virtuálního počítače pravidelně aktualizovat opravy zabezpečení, další funkce a tak dále.  V takových případech budete chtít aktualizovat image virtuálního počítače, který odkazuje na skladovou jednotku s použitím následujících kroků:

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**, najít nabídku na aktualizaci.

3.  V **SKU** klikněte na tlačítko na skladovou Položku přidruženou image virtuálního počítače k aktualizaci.

4.  V části **disková verze**, klikněte na **+ nová verze disku** přidat nové image virtuálního počítače.

5.  Zadejte nové Image virtuálních počítačů **disková verze**. Verze disku musí podstoupit [sémantickou verzi](http://semver.org/) formátu. Verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Ověřte, zda je větší než všechny předchozí verze; nové verze, které zadáte v opačném případě po publikování na novou verzi nezobrazí na portálu nebo na webu Azure Marketplace.

6.  Pro **URL virtuálního pevného disku operačního systému**, zadejte [sdílený přístupový podpis (SAS) URI](./cpp-get-sas-uri.md) pro operační systém virtuálního pevného disku. 

    > [!WARNING] 
    > Mezi různými verzemi SKU nejde změnit počet datových disků. Pokud předchozí verze umožňovaly datové disky, které jsou nakonfigurované, musí tuto novou verzi také mít stejný počet datových disků.

7.  Klikněte na **publikovat** spuštění pracovního postupu pro publikování novou verzi virtuálního počítače na webu Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Změnit dostupnost v oblastech SKU

V průběhu času můžete zpřístupnit vaše nabídka/SKU v dalších oblastech.  Alternativně můžete chtít zastavit podporu nabídka/SKU v dané oblasti.
Pokud chcete upravit dostupnosti, postupujte následovně:

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.

3.  V **SKU** klikněte na tlačítko jednotky SKU, kterou chcete upravit její dostupnost.

4.  Klikněte na **vybrat země** tlačítko **země/oblast dostupnosti** pole.

5.  Ve skupině dostupnosti oblast rozbalovací přidat nebo odebrat oblasti pro tato skladová položka.

6.  Klikněte na **publikovat** ke spuštění pracovního postupu publikovat k aktualizaci vašeho dostupnosti oblast skladové položky.

Pokud SKU je k dispozici v nové oblasti, budete mít možnost určit ceny pro tuto konkrétní oblast prostřednictvím **exportovat Data ceny** funkce. Pokud přidáváte zpět oblasti, který byl dříve k dispozici před, nebude možné aktualizovat jeho ceny, protože o změně v cenách nejsou povolené.


### <a name="add-a-new-sku"></a>Přidat novou skladovou Položku

Následujícím postupem zpřístupnit novou skladovou Položku pro váš stávající nabídky: 

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.

3.  V části **skladové položky** klikněte na tlačítko na **přidat novou skladovou Položku** a zadejte **SKU ID** v místní nabídce.

4.  Opakované publikování virtuálního počítače podle popisu v článku [publikování virtuálního počítače na webu Azure Marketplace](./cpp-publish-offer.md).

5.  Klikněte na **publikovat** spuštění pracovního postupu pro publikování nové skladové jednotky.


### <a name="update-offer-marketplace-metadata"></a>Aktualizace nabídky marketplace metadat

Pomocí následujících kroků pro aktualizaci metadat marketplace – název společnosti, loga, atd. – spojené s nabídkou na: 

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najít nabídky, které chcete aktualizovat.

3.  Přejít **Marketplace** kartu a potom podle pokynů v článku [publikování virtuálního počítače na webu Azure Marketplace](./cpp-publish-offer.md) provést změny metadat.

4.  Klikněte na **publikovat** ke spuštění pracovního postupu publikovat provedené změny.


### <a name="update-pricing-on-published-offers"></a>Aktualizace cen pro publikování nabídek

Po publikování vaší nabídky s průběžnými platbami nelze zvýšit přímo ceny SKU.  (Ale můžete vytvořit novou skladovou Položku v rámci stejné nabídky, odstranit staré skladové položky a pak znovu publikovat vaši nabídku pro nové zákazníky.)  Naopak můžete snížit ceny publikované nabídky pomocí následujících kroků:

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**, najít nabídku na aktualizaci.

3.  Klikněte na SKU, pro kterou chcete snížit ceny.

4.  Pokud jste nastavili informace o cenách v 1 × 1 grafické uživatelské rozhraní, můžete změnit ceny přímo v uživatelském rozhraní. Pokud nastavíte ceny prostřednictvím importu/exportu tabulky, může pouze snížit ceny prostřednictvím funkce importu a exportu.

3.  Klikněte na **Uložit**.

4.  Klikněte na **publikovat** ke spuštění pracovního postupu publikovat provedené změny.

Nové nižší ceny budou viditelné pro nové zákazníky, jakmile je v provozu na webu.  Tato nová cena ovlivní vaše zákazníky následujícími způsoby:

- Tato nová míra bude účtovat nové zákazníky. 
- Stávající zákazníci snížení cen se projeví zpětně na začátku fakturačního cyklu, během které snížení cen začal platit.
Pokud mají byl již účtuje cyklu, během které došlo k cenu, dostanou při jejich příštím účtovacím období pro snížení ceny refundaci.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Ceny zjednodušené měny

Od 1. září 2018 se nazývá obsahují nový oddíl **zjednodušená ceny měny** přidá se k portálu. Microsoft je zjednodušení obchodních Azure Marketplace povolením více předvídatelné ceny a kolekce od svých zákazníků po celém světě. Toto zjednodušení bude obsahovat snížení počtu měny, ve kterých jsme vašim zákazníkům faktury.

Nový oddíl bude trvat ceny v nových měn. Jakmile všichni zákazníci úspěšně migrováni na nových měn vyrovnání, původní části s cenami se vyřadí z provozu a bude i nadále pouze část zjednodušená ceny měny.

Budete mít až do 1. listopadu 2018 nastavit nové ceny pro oblasti, ve které měně vyrovnání se mění. Nebudete tak zároveň rozšiřujete ceny pro oblasti, ve které není změněn vyrovnání měny.

> [!NOTE] 
> Pokud používáte rozhraní API pro publikování vaší nabídky, může se zobrazit nová část v rámci nabídky JSON. To by být označena jako `virtualMachinePricingV2` nebo `monthlyPricingV2`, v závislosti na typu nabídky. 

Pokud máte nějaké dotazy o této změně, obraťte se na [podpory Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Porovnání funkcí

Pokud provedete změny u již publikované nabídky, můžete využít **porovnání** funkce Auditovat změny, které byly provedeny. Chcete používat tuto funkci:

1.  V každém okamžiku procesu úprav, klikněte na tlačítko **porovnání** tlačítko pro vaši nabídku.

    ![Porovnejte funkce tlačítko](./media/publishvm_037.png)


2.  Zobrazit vedle sebe verze marketingové prostředky a metadata.


## <a name="history-of-publishing-actions"></a>Historie publikování akce

Chcete-li zobrazit všechny historické publikační aktivity, klikněte na **historie** položky v levém navigačním řádku nabídek portál partnerů cloudu. Tady budou moci zobrazit časovým razítkem akce, která byla přijata po celou dobu životnosti vaší nabídky na webu Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

