---
title: Aktualizace existující nabídky virtuálních počítačů v Azure Marketplace
description: Vysvětluje, jak aktualizovat existující nabídku virtuálních počítačů na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: evansma
ms.openlocfilehash: 1ba2abb3fbeb1d08ed780669fb94a2ef83cbfb1b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934250"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizace existující nabídky virtuálních počítačů na Azure Marketplace

Tento článek vás provede různými aspekty aktualizace nabídky virtuálního počítače (VM) v [portál partnerů cloudu](https://cloudpartner.azure.com/) a opětovném publikování této nabídky. 

K dispozici je řada maloobchodechch důvodů, jak aktualizovat nabídku, včetně:

-  Přidat novou verzi image virtuálního počítače do stávajících SKU
-  Změna oblastí, které jsou k dispozici SKU
-  Přidat nové SKU
-  Aktualizace metadat Marketplace pro nabídku nebo jednotlivé SKU
-  Aktualizace cen pro nabídky s průběžnými platbami

Pro pomoc s těmito úpravami nabízí portál funkce **porovnání** a **Historie** .  

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](../../cloud-solution-providers.md) .

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Nepovolené změny nabídky nebo SKU virtuálních počítačů

Existují některé atributy nabídky nebo SKU virtuálních počítačů, které nelze změnit, jakmile je nabídka v Azure Marketplace živá, zejména:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID SKU** existujících SKU
-  Počet stávajících SKU dat pro datový disk
-  Fakturační a licenční model se mění ve stávajících SKU
-  Zvýšení ceny na publikovanou SKU


## <a name="common-update-operations"></a>Běžné operace aktualizace

I když existuje celá řada vlastností, které můžete v nabídce virtuálních počítačů změnit, jsou běžné následující operace.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualizace verze image virtuálního počítače pro SKU

Je běžné, že bitová kopie virtuálního počítače se pravidelně aktualizuje pomocí oprav zabezpečení, dalších funkcí atd.  V takových scénářích chcete aktualizovat bitovou kopii virtuálního počítače, kterou vaše SKU odkazuje, pomocí následujících kroků:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**Najděte nabídku, která se má aktualizovat.

3.  Na kartě **SKU** klikněte na skladovou jednotku přidruženou k imagi virtuálního počítače a aktualizujte ji.

4.  V části **verze disku**klikněte na **+ Nová disková verze** a přidejte novou image virtuálního počítače.

5.  Zadejte novou **verzi disku**imagí virtuálních počítačů. Verze disku musí následovat po formátu [sémantické verze](https://semver.org/) . Verze by měly být ve formátu X. Y. Z, kde X, Y a Z jsou celá čísla. Ověřte, že nová verze, kterou zadáte, je větší než všechny předchozí verze. v opačném případě se nová verze po opětovném publikování nezobrazí na portálu nebo v Azure Marketplace.

6.  Pro **adresu URL virtuálního pevného disku operačního**systému zadejte [identifikátor URI sdíleného přístupového podpisu (SAS)](./cpp-get-sas-uri.md) , který jste vytvořili pro virtuální pevný disk s operačním systémem. 

    > [!WARNING] 
    > Počet datových disků se nesmí měnit mezi různými verzemi SKU. Pokud měly předchozí verze nakonfigurované datové disky, musí mít tato nová verze taky stejný počet datových disků.

7.  Kliknutím na **publikovat** spustíte pracovní postup a publikujete novou verzi virtuálního počítače do Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Změna dostupnosti oblasti SKU

V průběhu času možná budete chtít, aby vaše nabídka/SKU byla dostupná ve více oblastech.  Alternativně můžete chtít zastavit podporu nabídky/SKU v dané oblasti.
Chcete-li upravit dostupnost, použijte následující postup:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najdete nabídku, kterou chcete aktualizovat.

3.  Na kartě **SKU** klikněte na skladovou položku, kterou chcete změnit její dostupnost.

4.  Klikněte na tlačítko **Vybrat země** v poli **dostupnost země/oblasti** .

5.  V místní nabídce dostupnost oblasti přidejte nebo odeberte oblasti této SKU.

6.  Kliknutím na **publikovat** spustíte pracovní postup publikování a aktualizujete dostupnost oblasti SKU.

Pokud je SKU dostupná v nové oblasti, budete mít možnost zadat ceny pro konkrétní oblast prostřednictvím funkce **exportovat cenové údaje** . Pokud přidáváte oblast zpátky, která byla předtím k dispozici, nebudete moct ceny aktualizovat, protože změny cen nejsou povolené.


### <a name="add-a-new-sku"></a>Přidat novou SKLADOVOU položku

K dispozici je nová SKU pro stávající nabídku pomocí následujících kroků: 

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najdete nabídku, kterou chcete aktualizovat.

3.  Na kartě **SKU** klikněte na **Přidat novou SKU** a v automaticky otevíraném okně zadejte **ID SKU** .

4.  Publikujte virtuální počítač znovu, jak je popsáno v článku [publikování virtuálního počítače pro Azure Marketplace](./cpp-publish-offer.md).

5.  Kliknutím na **publikovat** spustíte pracovní postup a publikujete novou skladovou položku.


### <a name="update-offer-marketplace-metadata"></a>Aktualizace metadat webu nabídky

Pomocí následujících kroků aktualizujte metadata Marketplace – název společnosti, loga atd. – přidružené k vaší nabídce: 

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky** najdete nabídku, kterou chcete aktualizovat.

3.  Přejděte na kartu **Marketplace** a pak postupujte podle pokynů v článku [publikování virtuálního počítače pro Azure Marketplace](./cpp-publish-offer.md) a proveďte změny metadat.

4.  Kliknutím na **publikovat** spustíte pracovní postup, aby se změny publikovaly.


### <a name="update-pricing-on-published-offers"></a>Aktualizace cen u publikovaných nabídek

Po publikování nabídky s průběžnými platbami se vám ceny za SKU nedají přímo zvýšit.  (Můžete ale vytvořit novou SKLADOVOU položku v rámci stejné nabídky, odstranit starou SKLADOVOU položku a pak znovu publikovat vaši nabídku pro nové zákazníky.)  Naproti tomu můžete snížit cenu publikované nabídky pomocí následujících kroků:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**Najděte nabídku, která se má aktualizovat.

3.  Klikněte na SKLADOVOU položku, pro kterou chcete snížit ceny.

4.  Pokud jste nastavili ceny v grafickém uživatelském rozhraní 1x1, můžete změnit cenu přímo v uživatelském rozhraní. Pokud jste nastavili ceny pomocí tabulky import/export, můžete snížit ceny jenom pomocí funkce Import/export.

3.  Klikněte na možnost **Uložit**.

4.  Kliknutím na **publikovat** spustíte pracovní postup, aby se změny publikovaly.

Nově snížené ceny budou pro nové zákazníky viditelné, jakmile budou na webu aktivní.  Tato nová cena bude mít vliv na vaše zákazníky následujícími způsoby:

- Novým zákazníkům se bude účtovat Tato nová sazba. 
- U stávajících zákazníků se snížení ceny projeví zpětně na začátek fakturačního cyklu, během kterého se snížení ceny projeví.
Pokud již byly za cyklus, během kterých došlo k poklesu cen, účtovány, obdrží během příštího fakturačního cyklu refundaci, aby se pokryla snížená cena.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Ceny pro zjednodušenou měnu

Od září 1 2018 se na portál přidá nový oddíl s názvem **zjednodušená měna** . Microsoft zjednodušuje Azure Marketplace podnikání tím, že umožňuje více předvídatelných cen a kolekcí od zákazníků po celém světě. Toto zjednodušení bude zahrnovat omezení počtu měn, ve kterých vaše zákazníky fakturují.

Nová sekce bude v těchto nových měnách brát v úvahu ceny. Po migraci všech zákazníků do těchto nových měn vyrovnání se původní část ceny vyřadí a zachová se jenom část s cenami za zjednodušenou měnu.

K nastavení nové ceny pro oblasti, ve kterých se Měna vyrovnání mění, dostanete do 1. listopadu 2018. Nebudete moct zvýšit cenu za oblasti, ve kterých se Měna vyrovnání nemění.

> [!NOTE] 
> Pokud k publikování vaší nabídky používáte rozhraní API, můžete se ve formátu JSON nabídky podívat na novou část. V závislosti na typu nabídky se to dá opatřit jako `virtualMachinePricingV2` nebo `monthlyPricingV2`. 

Pokud máte v souvislosti s touto změnou nějaké dotazy, obraťte se na [podporu Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Compare – funkce

Když provedete změny v již publikované nabídce, můžete využít funkci **porovnání** k auditu provedených změn. Chcete-li použít tuto funkci:

1.  V jakémkoli okamžiku v procesu úprav klikněte na tlačítko **Porovnat** pro vaši nabídku.

    ![Tlačítko porovnat funkci](./media/publishvm_037.png)


2.  Podívejte se na souběžné verze marketingových assetů a metadat.


## <a name="history-of-publishing-actions"></a>Historie akcí publikování

Chcete-li zobrazit všechny historické aktivity publikování, klikněte na položku **Historie** v levém navigačním panelu portál partnerů cloudu. Tady budete moct zobrazit časové razítko, které jste provedli během životnosti vašich Azure Marketplace nabídek.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

