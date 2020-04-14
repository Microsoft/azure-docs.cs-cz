---
title: Aktualizace existující nabídky virtuálních počítačů na Azure Marketplace
description: Vysvětluje, jak aktualizovat existující nabídku virtuálních počítačů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: a15ccb1de2a9ce0072d032e624ead3b4d730763b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273048"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizace existující nabídky virtuálních počítačů na Azure Marketplace

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky virtuálního počítače Azure](https://aka.ms/CreateAzureVMoffer) ke správě migrovaných nabídek.

Tento článek vás provede různými aspekty aktualizace nabídky virtuálního počítače (VM) na [portálu partnerů cloudu](https://cloudpartner.azure.com/) a pak znovu publikuje nabídku. 

Existuje celá řada běžných důvodů pro aktualizaci nabídky, včetně:

-  Přidání nové verze image virtuálního aplikace pro virtuální movitý chod
-  Změna oblastí, které je k dispozici sku
-  Přidání nových sous
-  Aktualizace metadat tržiště pro nabídku nebo jednotlivé sku
-  Aktualizace cen na nabídkách průběžných plateb

Portál nabízí funkce **Porovnat** a **Historie,** které vám pomohou s těmito úpravami.  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](../../cloud-solution-providers.md)

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Nepovolené změny nabídky virtuálních montovek nebo skladové položky

Existují některé atributy nabídky virtuálního počítače nebo skladové položky, které nelze změnit, jakmile je nabídka aktivní na Azure Marketplace, hlavně:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID skladové položky** existujících skladových částek
-  Počet datových disků existujících sku
-  Změny fakturačního/licenčního modelu stávajících skutých.
-  Zvýšení ceny na publikovanou skladovou položku


## <a name="common-update-operations"></a>Běžné operace aktualizace

I když existuje široká škála charakteristik, které můžete změnit na nabídce virtuálního počítači, následující operace jsou běžné.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualizace verze image virtuálního softwaru pro skladovou položku

Je běžné, že image virtuálního počítače se pravidelně aktualizuje pomocí oprav zabezpečení, dalších funkcí a tak dále.  V takových případech chcete aktualizovat image virtuálního počítače, na kterou vaše skladová položka odkazuje, pomocí následujících kroků:

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky**najdete nabídku k aktualizaci.

3.  Na kartě **Skladové položky** klikněte na skladovou položku přidruženou k bitové kopii virtuálního počítači, kterou chcete aktualizovat.

4.  V části **Verze disku**klikněte na **+Nová verze disku** a přidejte novou bitovou kopii virtuálního počítače.

5.  Zadejte novou **verzi disku**s obrázky virtuálních počítačů . Verze disku musí následovat formát [sémantické verze.](https://semver.org/) Verze by měly být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla. Ověřte, zda je nová verze, kterou zadáte, větší než všechny předchozí verze. v opačném případě po opětovném publikování nové verze se nezobrazí na portálu nebo Azure Marketplace.

6.  Pro **adresu URL virtuálního pevného disku operačního systému**zadejte [identifikátor URI sdílený přístupový podpis (SAS)](./cpp-get-sas-uri.md) vytvořený pro virtuální pevný disk operačního systému. 

    > [!WARNING] 
    > Počet datových disků se nemůže měnit mezi různými verzemi skladové položky. Pokud byly předchozí verze nakonfigurovány na datových discích, musí mít tato nová verze také stejný počet datových disků.

7.  Kliknutím na **Publikovat** spusťte pracovní postup a publikujte novou verzi virtuálního počítače na Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Změna dostupnosti oblasti skladové položky

V průběhu času můžete chtít, aby vaše nabídka nebo skladová položka k dispozici ve více oblastech.  Případně můžete chtít přestat podporovat nabídku nebo skladovou položku v dané oblasti.
Chcete-li změnit dostupnost, postupujte takto:

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky** najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **Skladové položky** klikněte na skladovou položku, kterou chcete změnit její dostupnost.

4.  Klikněte na tlačítko **Vybrat země** v poli Dostupnost země **nebo oblasti.**

5.  V místní dostupnosti oblasti přidejte nebo odeberte oblasti pro tuto skladovou položku.

6.  Kliknutím na **Publikovat** spusťte pracovní postup publikování a aktualizujte dostupnost oblasti skutých.s.

Pokud je skladová položka k dispozici v nové oblasti, budete mít možnost zadat ceny pro tuto konkrétní oblast prostřednictvím funkce **Exportovat data ocenění.** Pokud přidáváte oblast zpět, která byla kdysi k dispozici dříve, nebudete moci aktualizovat své ceny, protože změny cen nejsou povoleny.


### <a name="add-a-new-sku"></a>Přidání nové skladové položky

Pomocí následujících kroků zpřístupněte novou skladovou položku pro stávající nabídku: 

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky** najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **Skladové položky** klikněte na **Přidat novou skladovou položku** a ve automaticky otevírané nabídce zadejte **ID skladové** položky.

4.  Znovu publikujte virtuální počítač podle podrobného článku [Publikování virtuálního počítače na Azure Marketplace](./cpp-publish-offer.md).

5.  Kliknutím na **Publikovat** spusťte pracovní postup a publikujte novou skladovou položku.


### <a name="update-offer-marketplace-metadata"></a>Aktualizovat metadata tržiště

Pomocí následujících kroků aktualizujte metadata tržiště – název společnosti, loga atd.– spojená s vaší nabídkou: 

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky** najděte nabídku, kterou chcete aktualizovat.

3.  Přejděte na kartu **Marketplace** a postupujte podle pokynů v článku [Publikování virtuálního počítače na Azure Marketplace](./cpp-publish-offer.md) a proveďte změny metadat.

4.  Kliknutím na **Publikovat** spusťte pracovní postup a publikujte změny.


### <a name="update-pricing-on-published-offers"></a>Aktualizovat ceny u publikovaných nabídek

Jakmile je vaše průběžná nabídka zveřejněna, nemůžete přímo zvýšit ceny skladové položky.  (Můžete však vytvořit novou skladovou položku v rámci stejné nabídky, odstranit starou skladovou položku a potom znovu publikovat nabídku pro nové zákazníky.)  Naopak cenu publikované nabídky můžete snížit pomocí následujících kroků:

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **Všechny nabídky**najdete nabídku k aktualizaci.

3.  Klikněte na skladovou položku, pro kterou chcete snížit ceny.

4.  Pokud jste nastavili ceny v grafickém uživatelském rozhraní 1x1, můžete cenu změnit přímo v uživatelském rozhraní. Pokud nastavíte ceny prostřednictvím tabulky importu nebo exportu, můžete ceny snížit pouze prostřednictvím funkce importu nebo exportu.

3.  Klikněte na **Uložit**.

4.  Kliknutím na **Publikovat** spusťte pracovní postup a publikujte změny.

Nové snížené ceny budou viditelné pro nové zákazníky, jakmile bude žít na webových stránkách.  Tato nová cena ovlivní vaše zákazníky následujícími způsoby:

- Noví zákazníci budou účtovány tuto novou sazbu. 
- U stávajících zákazníků se snížení ceny odrazí zpětně na začátku fakturačního cyklu, během něhož pokles ceny nabyl účinnosti.
Pokud již byly fakturovány za cyklus, během kterého došlo ke snížení ceny, obdrží refundaci během dalšího fakturačního cyklu, aby pokryli sníženou cenu.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Zjednodušené ceny měn

září 2018 bude na portál přidánnový oddíl s názvem **Zjednodušené oceňování měn.** Microsoft zefektivňuje podnikání na Azure Marketplace tím, že umožňuje předvídatelnější ceny a kolekce od vašich zákazníků po celém světě. Toto zefektivnění bude zahrnovat snížení počtu měn, ve kterých fakturujeme vašim zákazníkům.

Nová sekce bude mít ceny v těchto nových měnách.Po migraci všech zákazníků do těchto nových měn vyrovnání bude původní cenová část vyřazena a zůstane pouze část Zjednodušená měna.

Listopadu 2018 budete mít čas na stanovení nové ceny pro regiony, ve kterých se mění měna vypořádání. Nebudete moci zvýšit cenu pro regiony, kde se měna vypořádání nemění.

> [!NOTE] 
> Pokud používáte api k publikování nabídky, může se zobrazit nová část v nabídce JSON. To by bylo anotováno jako `virtualMachinePricingV2` nebo `monthlyPricingV2`, v závislosti na typu nabídky. 

Máte-li jakékoli dotazy týkající se této změny, obraťte se na [podporu Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Funkce porovnání

Když provedete změny v již publikované nabídce, můžete využít funkci **Porovnání** k auditu provedených změn. Použití této funkce:

1.  V libovolném okamžiku procesu úprav klikněte na tlačítko **Porovnat** pro vaši nabídku.

    ![Tlačítko Porovnat funkci](./media/publishvm_037.png)


2.  Zobrazení verzí marketingových prostředků a metadat vedle sebe.


## <a name="history-of-publishing-actions"></a>Historie publikačních akcí

Chcete-li zobrazit jakoukoli historickou publikační aktivitu, klikněte na položku **Historie** v levém navigačním panelu portálu partnerů cloudu. Zde budete moct zobrazit akce s časovým razítkem, které byly podniknuty během životnosti nabídek Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

