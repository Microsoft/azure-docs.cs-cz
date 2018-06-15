---
title: Plánování kapacity pro aplikace Service Fabric | Microsoft Docs
description: Popisuje, jak identifikovat počet výpočetních uzlů, které jsou potřebné pro aplikace Service Fabric
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ac8abbdbbe9125ea036d837c08e1089aa6d1e55d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212853"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Plánování kapacity pro aplikace Service Fabric
Tento dokument se naučíte, jak odhadovat objem prostředků (procesory, paměť RAM, disk v úložišti), budete muset spustit aplikace Azure Service Fabric. Je běžné pro požadavky na prostředky pro časem změnit. Několik prostředků se obvykle vyžadují, jako je vývoj/testování vaší služby a pak vyžadují více prostředků, jak můžete přejít do produkčního prostředí a aplikace zvětšování popularita. Při návrhu vaší aplikace, pečlivě promyslete požadavky na dlouhodobé a rozhodování, umožňujících služby škálování potřeby vysoké zákazníka.

 Když vytvoříte cluster Service Fabric, je rozhodnout, jaké druhy virtuální počítače (VM) se skládá clusteru. Každý virtuální počítač se dodává s omezené množství prostředků ve formě procesorů (jader a rychlost), šířku pásma sítě, paměť RAM a diskových úložišť. S růstem vaší služby v čase, můžete upgradovat na virtuální počítače, které nabízejí větší prostředky nebo přidejte další virtuální počítače do clusteru. Pokud chcete provést k tomu, musí vaše služba architektury původně, ho můžete využít výhod nové virtuální počítače, které se dynamicky přidají do clusteru.

Některé služby spravovat málo na žádná data na vlastních virtuálních počítačů. Plánování kapacity pro tyto služby proto měli zaměřit především na výkon, což znamená, výběrem příslušné procesorů (jader a rychlost) virtuálních počítačů. Kromě toho byste měli zvážit šířky pásma sítě, včetně jak často dochází k síťové přenosy a kolik dat je přenosu. Pokud vaše služba potřebuje provést a taky zvýší využití služby, můžete přidat další virtuální počítače v clusteru a zatížení vyrovnávání síťové požadavky napříč všech virtuálních počítačích.

Pro služby, které spravují velké objemy dat na virtuálních počítačích měli primárně na velikosti zaměřit plánování kapacity. Proto měli pečlivě zvážit kapacitu paměti RAM Virtuálního počítače a diskových úložišť. Systém správy virtuální paměti v systému Windows umožňuje místa na disku vypadat RAM do kódu aplikace. Kromě toho poskytuje modulu runtime Service Fabric inteligentní stránkování zachovat data pouze aktivní v paměti a přesunutí pomaleji přístupná data na disk. Aplikace můžou proto používat více paměti, než je fyzicky k dispozici ve virtuálním počítači. S víc paměti RAM zvyšuje výkon, jednoduše, vzhledem k tomu, že virtuální počítač můžete ponechat další diskového úložiště v paměti RAM. Virtuální počítač, který vyberete by měl mít disk dostatečně velký pro ukládání dat, která chcete na virtuálním počítači. Podobně virtuální počítač by měl mít dostatek paměti RAM, kde přinášejí výkonu, které očekáváte. Pokud časem naroste data vaší služby, můžete přidat další virtuální počítače do clusteru a oddíl data mezi všechny virtuální počítače.

## <a name="determine-how-many-nodes-you-need"></a>Určit, kolik uzly, budete potřebovat
Vytváření oddílů služby umožňuje škálovat data vaší služby. Další informace o vytváření oddílů, najdete v části [dělení Service Fabric](service-fabric-concepts-partitioning.md). Každý oddíl se musí vejít do jednoho virtuálního počítače, ale více oddílů (malé) mohou být umístěny na jeden virtuální počítač. Ano s více oddíly malé vám dává větší flexibilitu, než má několik větší oddíly. O kompromisu je, že spousta oddíly zvyšuje režii Service Fabric a napříč oddíly nelze provést zpracovaných operací. Je zde také další potenciální síťový provoz Pokud kódu služby často potřebuje přístup k kusy data, která za provozu v různých oddílů. Při navrhování vaší služby, pečlivě zvažte tyto výhody a nevýhody přijaty ve efektivní strategie dělení.

Předpokládejme, že aplikace má jeden stavové služby, která má velikost úložiště, který chcete dosáhnout DB_Size GB v roce. Chcete-li přidat další aplikace (a oddíly) jako prostředí růstu nad rámec tohoto roku.  Replikace faktor (RF), která určuje počet replik služby má dopad na celkový DB_Size. Celkový počet DB_Size napříč všechny repliky je násobí hodnotou DB_Size faktor replikace.  Node_Size představuje místa nebo RAM disku na jeden uzel, který chcete použít pro vaši službu. Pro nejlepší výkon DB_Size by měl začlenit do paměti v clusteru a je třeba zvolit Node_Size, který je kolem paměť RAM virtuálního počítače. Přidělí Node_Size, která je větší než kapacita paměti RAM, se spoléhat na stránkování poskytované modulu runtime Service Fabric. Proto nemusí být optimální, pokud se považuje za aktivního celého datového výkon (od té doby dat je stránkovaného vstup/výstup). Pro mnoho služeb, kde je aktivní pouze část dat, je však cenově výhodnější.

Počet uzlů, které jsou potřebné pro maximální výkon můžete vypočítat následujícím způsobem:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Účet pro růst
Můžete vypočítat počet uzlů podle DB_Size, které očekáváte, že služby růst, kromě DB_Size, které začne s. Potom růst počet uzlů s růstem vaší služby, aby nejsou předimenzování počet uzlů. Ale počet oddílů by měl být podle počtu uzlů, které jsou potřebné, pokud používáte služby v maximální růstu.

Je vhodné mít některé další počítače, které jsou k dispozici kdykoli tak, aby bylo možné zpracovat všechny neočekávané špičky nebo selhání, (například pokud několika virtuálními počítači přejděte).  Při kapacitu navíc by měl být určen pomocí vaší očekávané špičky, výchozí bod je můžete vyhradit několik navíc za virtuální počítače (5-10 procent navíc).

Podle předchozích předpokládá jednu stavové služby. Pokud máte více než jeden stavové služby, budete muset přidat DB_Size spojené s jinými službami do rovnice. Alternativně můžete vypočítat počet uzlů samostatně pro každou stavové služby.  Vaše služba může mít repliky nebo oddíly, které nejsou spárovány. Uvědomte si, že oddíly mohou mít i více dat než jiné. Další informace o vytváření oddílů, najdete v části [dělení článek o osvědčených postupech](service-fabric-concepts-partitioning.md). Ale předchozí vztah je oddíl a repliky lhostejné, protože Service Fabric zajistí, že repliky jsou rozprostřeny mezi uzly optimalizovaným způsobem.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Používání tabulky pro výpočet nákladů
Nyní ve vzorci přidejme některé reálná čísla. [Příklad tabulky](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) ukazuje, jak plánování kapacity pro aplikaci, která obsahuje tři typy datových objektů. Pro každý objekt jsme Přibližná jeho velikost a kolik objekty že Očekáváme, že má. Můžeme také vybrat kolik repliky chceme každého typu objektu. Tabulku vypočítá celkové množství paměti k uložení do clusteru.

Potom jsme zadejte velikost virtuálního počítače a měsíční náklady. Podle velikosti virtuálního počítače, tabulce zjistíte minimální počet oddílů, které je nutné použít k rozdělení dat, aby se fyzicky vejít na uzly. Očekáváte větší počet oddílů pro přizpůsobení výpočetní konkrétní aplikace a je třeba síťový provoz. Tabulku zobrazuje počet oddílů, které budete spravovat objekty uživatelského profilu zvýšilo 1 až šest.

Nyní založená na všechny tyto informace, tabulku zobrazuje, může získat všechna data s požadovanou oddíly a repliky fyzicky na 26 uzly clusteru. Však tento cluster by hustě balí, takže můžete chtít, aby dokázala pojmout selhání uzlů a upgrady některé další uzly. Tabulku také ukazuje, že s více než 57 uzly poskytuje žádná další hodnota, protože bude mít prázdný uzly. Chcete znovu přejděte výše 57 uzly přesto zohlednit selhání uzlů a upgrady. Tabulku tak, aby odpovídala konkrétním potřebám vaší aplikace, můžete upravit.   

![Tabulky pro výpočet nákladů][Image1]

## <a name="next-steps"></a>Další postup
Podívejte se na [dělení Service Fabric služby] [ 10] Další informace o vytváření oddílů služby.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
