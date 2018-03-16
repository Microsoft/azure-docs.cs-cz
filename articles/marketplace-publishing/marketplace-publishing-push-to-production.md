---
title: "Nasadit vaši nabídku do Azure Marketplace | Microsoft Docs"
description: "Další informace o a provede pokyny k nasazení vaši nabídku – bitovou kopii virtuálního počítače, služby pro vývojáře, data služby atd. – v Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Nasadit vaši nabídku do Azure Marketplace
Až budete spokojeni s vaši nabídku (tedy testování zákazníka scénáře marketingové obsah, atd.) a budete chtít spustit, žádosti o **Push do produkčního prostředí** na **publikovat** kartě.  

1. Čtyři kroky v části průvodce stránku publikování portálu by měl být dokončena a zelená. Pro virtuální počítač nabízí zkontrolujte, jestli jsou postupovali podle následujících pokynů.
   
    ![Kreslení][img-pubportal-walkthru-checked]
2. Vyberte **publikovat** kartě ze seznamu na levé straně.
   
    ![Kreslení][img-pubportal-menu-publish]
3. Klikněte na tlačítko **požádat o schválení tak, aby nabízel do produkčního prostředí**. Po zadání požadavku, týmem schválení provede kontrolu konečné a pak vaši nabídku bude k dispozici v Azure Marketplace.
   
    ![Kreslení][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> V případě virtuálních počítačů když kliknete na tlačítko žádost o schválení tak, aby nabízel do produkčního prostředí, jsou tyto kroky provést za scény. Bude moct zobrazovat průběh každého kroku na kartě Publikovat v publikační portálu. Je nutné zkontrolovat tuto stránku v pravidelných intervalech (než je ve stavu "Uvedené") pro všechny informace o selhání, které musí oprava z vaší elementu end.
> 
> * Na první žádost produkční přejde na certifikační týmu, který ověření virtuální pevný disk. Ale pokud aktualizujete již uvedené nabídku a požadavek má potom pouze marketingové změnit, pak certifikační krok se přeskočí.
> * V dalším kroku žádost se do týmu ověření obsahu, který ověřte marketing obsah nabídku.
> * Pokud jsou výše uvedené kroky úspěšné, je schválen nabídku v produkčním prostředí. V tomto okamžiku stav stane "uvedených" publikování portálu. Tento stav "Uvedené" však neznamená, že je proces dokončen. Následující kroky musí být kompletní před nabídka je k dispozici v Azure Marketplace.
> * Po schválení nabídku v produkčním prostředí v předchozích krocích replikace nabídku start přes Azure datových center. Obvykle trvá 24 48hours na dokončení replikace, ale může trvat až do týdne v závislosti na velikosti virtuálního pevného disku. Pokud aktualizujete již uvedené nabídku a má tu, pouze marketingové změnit, pak replikace je ale rychlejší.
> * Po dokončení replikace poté nabídku bude k dispozici v Azure Marketplace.
> 
> Nabídky můžete odstranit vždy když se **koncept** stav (tj, nikdy **nabízená pracovní** nebo **Push do produkčního prostředí**). Na **historie** , klikněte na **zahodit koncept** tlačítko v dolní části stránky a odstraňte koncept.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Produkční kontrolní seznam pro všechny nabídky virtuálního počítače
* Zajistěte, aby vám partnera Microsoft Azure Certified
* Na kartě SKU možnost "Skrýt tuto SKU z Marketplace vzhledem k tomu, že by měl koupili vždy prostřednictvím šablonu řešení" by měl být označen jako Ano pouze v případě, že verze SKU je součástí šablonu řešení. Ve všech ostatních případech by měl tato možnost vždycky označen jako číslo.
* Mějte na paměti: Byste neměli měnit, SKU viditelnost nastavení po je uvedena verze SKU. Tato funkce nepodporujeme.
* Ujistěte se, že loga splňovat níže uvedené pokyny logo Azure Marketplace.
* Nabídky a SKU popis nesmí být stejné.
* Název společnosti SKU a nabízejí dlouhý Souhrn nesmí být stejné.
* Název SKU a nabízejí Souhrn nesmí být stejné.
* Názvy SKU by neměl být identické nabídku více skladových položek.

**Azure Marketplace logu**

* Azure návrh obsahuje paletu barev jednoduché. Zachovat počet primární a sekundární barvy na vaše logo nízkou.
* Barev motivů na portálu Azure jsou bílé a černé. Proto Vyhněte se používání těchto barvy jako barvu pozadí vašeho loga. Pomocí některé barvu, která by zkontrolujte, že vaše loga viditelného na portálu Azure. Doporučujeme, abyste jednoduché primární barvy. Pokud používáte průhledné pozadí, ujistěte se, že logo/text není bílé nebo blokovaných.
* Nepoužívejte logo barevného přechodu pozadí.
* Vyhněte se umístění na logo text, i vaše společnost nebo název značky.
* Vzhled a chování vaše logo musí být 'ploché' a byste neměli přechody.
* Logo nesmí dojít k roztažení.

**Další pokyny pro nejdůležitější loga:**

* Logo nejdůležitější je volitelný. Vydavatele můžete vypnout nahrávat logo nejdůležitější. **Ale po nahrané ikonu hrdina nelze odstranit z publikování portálu. V ten moment se musí partnerovi splňovat pokyny Azure Marketplace pro nejdůležitější ikony else nebude možné ji schválit nabídku do produkčního prostředí.**
* Zobrazovaný název vydavatele, název SKU a nabídku dlouhé shrnutí se zobrazují v barva bílé písma. Proto byste neměli udržuje všechny světla barva na pozadí na ikonu nejdůležitější. Černé, bílé a průhledné pozadí není povolena pro nejdůležitější ikony.
* Vydavatele zobrazí název, název SKU, dlouhé shrnutí nabídku a tlačítko pro vytvoření jsou vkládat prostřednictvím kódu programu uvnitř logo hrdina po nabídku přejde uvedené. Při návrhu logo nejdůležitější, proto by neměl zadat libovolný text. Nechte prázdné místo na pravé straně, protože textu (tj. zobrazovaný název vydavatele, název SKU, dlouhé shrnutí nabídka) budou zahrnuty prostřednictvím kódu programu tím, abychom přes zde. Prázdné místo pro text by mělo být 415 × 100 na pravé straně (a posunut 370px od levého okraje).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Nabízí další produkční kontrolní seznam pro již uvedené virtuálního počítače
* Zkontrolujte, jestli už nabídku se stejným názvem nabídka z vaší společnosti. Pokud ano, měli byste v existující nabídka místo vytvoření nové duplicitní nabídka přidat nové verze verze SKU.
* Datový disk neměli měnit mezi dvěma verzemi stejné verze SKU.
* Azure Marketplace nepodporuje cenovou změnu uvedené SKU, jak se má dopad na fakturaci stávající zákazníky. Ujistěte se, že neměnit ceny uvedené SKU v oblastech, kde je k dispozici verze SKU. Můžete ale přidat nové SKU nebo přidání nových oblastí do existující SKU.

## <a name="next-steps"></a>Další postup
Jakmile nabídku přejde za provozu, testovací scénáře zákazníka k ověření všechny smlouvy a funkce fungovat správně v provozním prostředí jako otestovat a ověřit v testovacím prostředí.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
