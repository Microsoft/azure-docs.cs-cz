---
title: Plánování kapacity pro aplikace Service Fabric
description: Popisuje, jak identifikovat počet výpočetních uzlů vyžadovaných pro aplikaci Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75377204"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Plánování kapacity pro aplikace Service Fabric
V tomto dokumentu se dozvíte, jak odhadnout množství prostředků (CPU, paměť RAM, diskové úložiště), které potřebujete ke spuštění aplikací Service Fabric Azure. Je běžné, že vaše požadavky na prostředky se v průběhu času mění. Obvykle při vývoji nebo testování služby budete potřebovat pár prostředků a pak při přechodu do produkčního prostředí vyžadovat další prostředky a vaše aplikace se bude zvětšovat v oblíbenosti. Při návrhu aplikace je třeba vzít v úvahu dlouhodobé požadavky a vybrat možnosti, které umožňují vaší službě škálování na vysokou zákaznickou poptávku.

 Když vytváříte cluster Service Fabric, rozhodujete, jaké druhy virtuálních počítačů tvoří cluster. Každý virtuální počítač obsahuje omezené množství prostředků ve formě procesorů (jader a rychlosti), šířky pásma sítě, paměti RAM a diskového úložiště. Vzhledem k tomu, že se vaše služba v průběhu času rozroste, můžete upgradovat na virtuální počítače, které nabízí větší prostředky a/nebo přidat do clusteru další virtuální počítače. Abyste to mohli udělat, musíte nejdřív architektovat službu, aby mohla využívat nové virtuální počítače, které se dynamicky přidávají do clusteru.

Některé služby nejenom na samotných virtuálních počítačích spravují žádné údaje. Proto by se plánování kapacity pro tyto služby mělo zaměřit hlavně na výkon, což znamená výběr vhodných procesorů (jader a rychlosti) virtuálních počítačů. Kromě toho byste měli uvažovat o šířce pásma sítě, včetně četnosti síťových přenosů a přenosu dat. Pokud vaše služba potřebuje výkon a využití služby, můžete do clusteru přidat další virtuální počítače a vyrovnávat zatížení síťových požadavků napříč všemi virtuálními počítači.

Pro služby, které spravují velké objemy dat na virtuálních počítačích, se plánování kapacity musí zaměřit hlavně na velikost. Proto byste měli pečlivě zvážit kapacitu paměti RAM virtuálního počítače a úložiště disku. Systém správy virtuální paměti ve Windows vydává místo na disku paměť, která bude vypadat jako RAM pro kód aplikace. Kromě toho modul runtime Service Fabric poskytuje inteligentní stránkování, které udržuje pouze aktivní data v paměti a přesun studených dat na disk. Aplikace mohou proto využívat více paměti, než je fyzicky k dispozici na virtuálním počítači. Větší množství paměti RAM zvyšuje jenom výkon, protože virtuální počítač může uchovávat víc diskových úložišť v paměti RAM. Vybraný virtuální počítač by měl mít dostatečně velký disk pro ukládání dat, která chcete na virtuálním počítači. Podobně by měl mít virtuální počítač dostatek paměti RAM, aby vám poskytl výkon, který si přejete. Pokud se data vaší služby během času rozrůstá, můžete do clusteru přidat další virtuální počítače a rozdělit data na všechny virtuální počítače.

## <a name="determine-how-many-nodes-you-need"></a>Určete, kolik uzlů potřebujete.
Rozdělení služby na oddíly vám umožní škálovat data vaší služby. Další informace o dělení najdete v tématu [dělení Service Fabric](service-fabric-concepts-partitioning.md). Každý oddíl se musí nacházet v jednom virtuálním počítači, ale několik (malých) oddílů může být umístěno na jednom virtuálním počítači. Takže s více malými oddíly získáte větší flexibilitu než u několika větších oddílů. Pokud se rozhodnete pro obchod mimo provoz, zvýší se Service Fabric režie a v různých oddílech nemůžete provádět operace s podporou transakcí. Existuje také více potenciálních síťových přenosů, pokud kód služby často potřebuje přístup k datům, která jsou v různých oddílech živá. Při navrhování služby byste měli pečlivě zvážit tyto specialisty a nevýhody, abyste dorazili do efektivní strategie dělení.

Předpokládejme, že vaše aplikace má jednu stavovou službu, která má velikost úložiště, kterou očekáváte zvětšit DB_Size GB za rok. Jste ochotni přidat další aplikace (a oddíly), když budete mít růst nad rámec tohoto roku.  Faktor replikace (RF), který určuje počet replik pro vaši službu, má dopad na celkový DB_Size. Celkový DB_Size mezi všemi replikami je faktor replikace vynásobený DB_Size.  Node_Size představuje místo na disku/paměť RAM na uzel, který chcete použít pro vaši službu. Nejlepšího výkonu dosáhnete, DB_Size by se měl vejít do paměti napříč clusterem a měla by se zvolit Node_Size, která je kolem paměti RAM virtuálního počítače. Přidělením Node_Size, která je větší než kapacita paměti RAM, se spoléháte na stránkování poskytované modulem runtime Service Fabric. Proto váš výkon nemusí být optimální, pokud jsou celá data považována za horká (od okamžiku, kdy jsou data na stránce/ven). Ale u mnoha služeb, kde jsou jenom zlomková data aktivní, je cenově výhodnější.

Počet uzlů vyžadovaných pro maximální výkon lze vypočítat následujícím způsobem:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Účet pro růst
Můžete chtít vypočítat počet uzlů na základě DB_Size, na které očekáváte, že se vaše služba bude zvětšovat, kromě DB_Size, kterou jste začali s. Pak Rozšiřte počet uzlů v průběhu služby, abyste se převzali mezi zřizováním počtu uzlů. Počet oddílů by ale měl být založený na počtu uzlů, které jsou potřeba při spouštění vaší služby při maximálním nárůstu.

Je vhodné mít kdykoli k dispozici několik dalších počítačů, aby bylo možné zpracovat jakékoli neočekávané špičky nebo selhání (například v případě, že několik virtuálních počítačů vychází dolů).  I když by se kapacita navíc měla určit pomocí očekávaných Špičk, je výchozím bodem vyhradit několik dalších virtuálních počítačů (5-10% Extra).

Předchozí předpokládá jednu stavovou službu. Pokud máte více než jednu stavovou službu, musíte do rovnice přidat DB_Size přidružené k ostatním službám. Alternativně můžete vypočítat počet uzlů samostatně pro každou stavovou službu.  Vaše služba může mít nevyvážené repliky nebo oddíly. Mějte na paměti, že oddíly mohou mít také více dat než jiné. Další informace o dělení najdete v článku věnovaném [vytváření oddílů na základě doporučených postupů](service-fabric-concepts-partitioning.md). Předchozí rovnice je však nezávislá oddílu a repliky, protože Service Fabric zajišťuje, že jsou repliky rozloženy mezi uzly optimalizovaným způsobem.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Použití tabulky pro výpočet nákladů
Teď do vzorce vložte nějaká skutečná čísla. [Příklad tabulky](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) ukazuje, jak naplánovat kapacitu pro aplikaci, která obsahuje tři typy datových objektů. Pro každý objekt je Přibližná velikost a počet objektů, které očekáváte. Také si vybereme, kolik replik chceme u každého typu objektu. Tabulka vypočítá celkovou velikost paměti, která má být uložena v clusteru.

Pak zadáme velikost virtuálního počítače a měsíční náklady. V závislosti na velikosti virtuálního počítače vám v tabulce zjistíte minimální počet oddílů, které musíte použít k rozdělení dat na uzly, aby se fyzicky vešly. Můžete si vyhovět většímu počtu oddílů, aby bylo možné přizpůsobit konkrétní výpočetní a síťové zatížení vaší aplikace. Tabulka zobrazuje počet oddílů, které spravují objekty profilu uživatele, se zvýšily z jednoho na šest.

V závislosti na všech těchto informacích se v tabulce zobrazí, že byste mohli fyzicky získat všechna data s požadovanými oddíly a replikami v clusteru se 26 uzly. Nicméně tento cluster by byl zhuštěný zabalený, takže možná budete chtít, aby některé další uzly vyhovovaly selháním a upgradům uzlů. Tabulka také ukazuje, že s více než 57 uzly neposkytuje žádnou další hodnotu, protože by měly být prázdné uzly. Znovu můžete chtít přejít nad rámec 57 uzlů a vyhovět tak selháním a upgradům uzlů. Můžete upravit tabulku tak, aby odpovídala konkrétním potřebám vaší aplikace.   

![Tabulka pro výpočet nákladů][Image1]

## <a name="next-steps"></a>Další kroky
Další informace o dělení služby najdete v [oddílu Service Fabric služby dělení na oddíly][10] .

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
