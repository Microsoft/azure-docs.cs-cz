---
title: Nasazení vaší nabídky na webu Azure Marketplace | Dokumentace Microsoftu
description: Další informace o a provede pokyny k nasazení vaši nabídku image virtuálního počítače, služby pro vývojáře, služba dat atd. – na Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714277"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Nasazení vaší nabídky na webu Azure Marketplace
Až budete spokojení s nabídkou (to znamená, testování scénářů zákazníků, marketingový obsah, atd.) a jste připraveni spustit, požádat o **přejít do produkčního prostředí** na **publikovat** kartu.  

1. Čtyři kroky v NÁVODU stránku publikování portálu by měly být dokončené a zelená. Nabídky se virtuální počítač Ujistěte se, že jsou postupovali podle následujících pokynů.
   
    ![Kreslení][img-pubportal-walkthru-checked]
2. Vyberte **publikovat** karty v seznamu na levé straně.
   
    ![Kreslení][img-pubportal-menu-publish]
3. Klikněte na tlačítko **žádost o schválení přejít do produkčního prostředí**. Po zadání požadavku schválení tým provede konečnou kontrolu a nabídky bude k dispozici na webu Azure Marketplace.
   
    ![Kreslení][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> V případě virtuálních počítačů když kliknete na tlačítko žádosti o schválení pro přejít do produkčního prostředí, následující kroky probíhají za scénu. Bude moct zobrazit průběh každého kroku na kartě publikovat publikování portálu. Je třeba zkontrolovat tuto stránku v pravidelných intervalech (dokud je ve stavu "Uvedené") pro všechny informace o selhání, které potřebují opravu z-endu vašeho.
> 
> * Zpočátku vaše žádost o výrobní přejde na certifikaci týmu, který ověřit virtuální pevný disk. Nicméně pokud aktualizujete již uvedené nabídky a požadavek má obor pouze marketingové změnu, pak certifikace přeskočen.
> * V dalším kroku žádosti přijdou do týmu ověření obsahu, který ověřte marketingový obsah nabídky.
> * Pokud výše uvedené kroky jsou úspěšné, je schválena nabídky v produkčním prostředí. V tuto chvíli stav stane "uveden" na portálu publikování. Tento stav "Uvedené" však neznamená dokončení tohoto procesu se. Následující kroky musí být dokončena předtím, než tato nabídka je dostupná na webu Azure Marketplace.
> * Po schválení nabídky v produkčním prostředí v předchozím kroku spuštění replikace nabídky v Azure datových centrech. Obvykle trvá 24-48hours na dokončení replikace, ale může trvat až týden v závislosti na velikosti virtuálního pevného disku. Pokud aktualizujete již uvedené nabídky a má obor pouze marketingové změnu, pak replikace je ale rychlejší.
> * Po dokončení replikace nabídky nebude k dispozici na webu Azure Marketplace.
> 
> Vždy můžete odstranit tuto nabídku v průběhu **koncept** stav (to znamená, nikdy **přejdete k přípravám** nebo **přejít do produkčního prostředí**). Na **historie** klikněte na tlačítko **zahodit koncept** tlačítko v dolní části stránky a odstraňte koncept.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Produkční kontrolní seznam pro všechny nabídky virtuálního počítače
* Ujistěte se, že jste partnerem Microsoft Azure Certified
* Na kartě SKU možnost "Skrýt tuto skladovou Položku z webu Marketplace vzhledem k tomu, že by měl koupil(a) vždy pomocí šablony řešení" musí být označené jako Ano pouze v případě, že SKU není částí šablony řešení. Ve všech ostatních případech by měla tuto možnost vždy označena jako číslo.
* Mějte na paměti: Byste neměli měnit, SKU viditelnost nastavení po SKU je uvedena. Tato funkce nepodporujeme.
* Ujistěte se, že loga pravidlům Azure Marketplace logo uvedena níže.
* Nabídka a skladová položka popis nesmí být stejný.
* Nadpis Skladových položek a nabízejí Long summary nesmí být stejný.
* Název SKU a nabízejí Souhrn nesmí být stejný.
* Názvy SKU by neměly být identické pro nabídky s více jednotek SKU.

**Logo Azure Marketplace – pokyny**

* Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.
* Jsou bílé barvy motivu na webu Azure portal a černé. Vyhněte se proto používání tyto barvy jako barva pozadí vašeho loga. Použijte některé barvu, která by provedla vašeho loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledným pozadím, ujistěte se, že není logo/text bílé nebo černá.
* Nepoužívejte logo barevného přechodu pozadí.
* Předejde text, dokonce i vaše společnost nebo název značky, loga.
* Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.
* Logo by neměl být roztažená.

**Další pokyny pro Hero logo:**

* Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. **Ale jednou nahrané ikonu hero nelze odstranit z publikování portálu. V tu chvíli partner musí dodržovat pokyny Azure Marketplace pro jiný Hero ikony neschválí nabídky do produkčního prostředí.**
* Barva písma bílé zobrazené zobrazovaný název vydavatele, název SKU a nabídky, dlouhé shrnutí. Proto byste se měli vyhnout zachovat všechny světlé barva pozadí ikony Hero. Černou, bílou a průhledné pozadí není povolena pro Hero ikony.
* Vydavatel zobrazované jméno, název SKU, nabídky, dlouhé shrnutí a tlačítka pro vytvoření jsou vložené prostřednictvím kódu programu uvnitř Hero logo po nabídky přejde uvedené. Při návrhu Hero logo, proto by neměl zadat libovolný text. Ponechte prázdné místo na pravé straně, protože text (tj. zobrazovaný název vydavatele, název SKU, nabídky, dlouhé shrnutí) budou zahrnuty programově od nás tam. Prázdný text by měl být 415 x 100 na pravé straně (a je posunut 370px od levého okraje).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Nabízí další produkční kontrolní seznam pro již uvedené virtuální počítač
* Zkontrolujte, jestli už nabídky se stejným názvem nabídky z vaší společnosti. Pokud ano, měli byste do existující nabídky místo vytvoření nové nabídky duplicitní přidat novou verzi SKU.
* Datový disk by neměly měnit mezi dvěma verzemi se stejným SKU.
* Na webu Azure Marketplace při změně cen uvedených SKU nepodporuje, protože má vliv na fakturaci stávající zákazníky. Ujistěte se, že neměnit ceny uvedené SKU v oblastech, kde je k dispozici SKU. Můžete však přidat nové SKU nebo přidat nové oblasti pro existující skladové položky.

## <a name="next-steps"></a>Další postup
Jakmile uvedete nabídky, testování scénářů zákazníků k ověření, že všechny kontrakty a funkce fungovat správně v provozním prostředí jako otestovali a ověřili v přípravném prostředí.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
