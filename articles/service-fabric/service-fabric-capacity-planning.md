---
title: Plánování kapacity pro aplikace Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak určit počet výpočetních uzlů požadovaných pro aplikaci Service Fabric
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
ms.openlocfilehash: d7ca566b86ed79aa773d7af2553223c79ed9944a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341998"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Plánování kapacity pro aplikace Service Fabric
Tento dokument vás naučí, jak odhadnout objem prostředků (procesory, paměť RAM, diskové úložiště), budete muset spustit vaše aplikace Azure Service Fabric. Je běžné, že požadavky na prostředky do v průběhu času měnit. Obvykle potřebujete několik prostředků, při vývoji a testování vaší služby, a pak přejdete do produkčního prostředí a vaše aplikace poroste popularita vyžadují více prostředků. Při návrhu aplikace pečlivě promyslete dlouhodobé požadavky a rozhodovat, které umožňují službě škálovat podle potřeby vysoké zákazníka.

 Při vytváření clusteru Service Fabric je rozhodnout, jaké typy virtuálních počítačů (VM) tvoří cluster. Každý virtuální počítač součástí omezené množství prostředků ve formě procesorů (jader a rychlost), šířky pásma sítě, RAM a diskové úložiště. S růstem vaší služby v čase, můžete upgradovat na virtuální počítače, které nabízejí větší prostředků a/nebo přidat další virtuální počítače do clusteru. Na druhém, musí vaše služba navrhovat zpočátku tak ji můžete využít výhod nových virtuálních počítačů, které získat dynamicky přidat do clusteru.

Některé služby spravovat malý na no data na samotných virtuálních počítačích. Plánování kapacity pro tyto služby by proto zaměřují především na výkon, což znamená, že vyberete příslušný procesorů (jader a rychlost) virtuálních počítačů. Kromě toho byste měli zvážit šířky pásma sítě, včetně jak často dochází k síťové přenosy a kolik data přenášejí. Pokud vaše služba potřebuje provést i zvýšení využití služeb, můžete přidat další virtuální počítače clusteru a zatížení vyrovnávání síťové požadavky ve všech virtuálních počítačích.

Pro služby, které spravují velkého objemu dat na virtuálních počítačích plánování kapacity byste se zaměřit primárně na velikosti. To znamená měli byste pečlivě zvážit kapacitu paměti RAM Virtuálního počítače a disku úložiště. Virtuální paměti systému pro správu ve Windows umožňuje místo na disku RAM vypadat, aby kód aplikace. Kromě toho modul runtime Service Fabric poskytuje inteligentní stránkování uchovávání pouze výměně dat v paměti a přechod na studená data na disk. Proto můžou aplikace použít více paměti, než je fyzicky k dispozici na virtuálním počítači. S více paměti RAM zvyšuje výkon, jednoduše, protože virtuální počítač můžete ponechat další diskové úložiště v paměti RAM. Virtuální počítač, který vyberete by měl mít dostatečně velký pro uložení dat, který chcete na virtuálním počítači disk. Podobně tento virtuální počítač by měl mít dostatek paměti RAM, kde přinášejí výkonu, které očekáváte. Pokud vaše služba objemu postupně, můžete přidat další virtuální počítače do clusteru a třídění dat ve všech virtuálních počítačích.

## <a name="determine-how-many-nodes-you-need"></a>Určení počtu uzlů, které potřebujete
Dělení služby můžete horizontálně navýšit kapacitu vaší služby data. Další informace o dělení najdete v tématu [vytváření oddílů Service Fabric](service-fabric-concepts-partitioning.md). Každý oddíl musí vejít do jednoho virtuálního počítače, ale více oddílů (malé) mohou být umístěny na jeden virtuální počítač. Tedy s více malých oddílů umožňuje větší flexibilitu než s několika větší oddíly. Nutný kompromis je, že spousta oddílů zvyšuje režii Service Fabric a nemůže provést přerušené provedené operace napříč oddíly. Je také další potenciální síťový provoz Pokud kódu služby se často potřebuje přístup k byly v různých oddílech. Při navrhování vaší služby, pečlivě zvažte tyto výhody a nevýhody můžete přejít na efektivní strategie dělení.

Předpokládejme, že aplikace obsahuje jeden stavové služby, který má velikost úložiště, který očekáváte, že se jejich DB_Size GB v roce. Chcete-li přidat víc aplikací (a oddíly) jak dochází k růstu za daný rok.  Faktor replikace (RF), která určuje počet replik pro vaši službu má dopad na celkový počet DB_Size. Celkový počet DB_Size přes všechny repliky je faktor replikace vynásobené DB_Size.  Node_Size představuje prostor/RAM disku na jeden uzel, který chcete použít pro vaši službu. Pro zajištění nejlepšího výkonu DB_Size by se měl vejít do paměti napříč clusterem a je třeba zvolit Node_Size, který spočívá v paměti RAM virtuálního počítače. Přidělením Node_Size, který je větší než kapacita paměti RAM, jste závislí na stránkování poskytuje modul runtime Service Fabric. Proto nemusí být optimální, pokud všechna data se považuje za aktivní výkon (od té doby data se stránkováním vstup a výstup). U řady služeb, kde je aktivní jenom část data, je cenově výhodnější.

Počet uzlů, vyžaduje se pro maximální výkon může být vypočítán takto:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Počítat s nárůstem
Můžete vypočítat počet uzlů podle DB_Size, který očekáváte, že vaše služba rozrůstá, kromě DB_Size, který jste začali. Potom růst počtu uzlů vaší služby tak, aby se bylo potřeba zřizovat z počtu uzlů. Ale počet oddílů, by měla vycházet z počtu uzlů, které jsou potřebné, pokud používáte služby na maximální růstu.

Je dobré mít některé další počítače, které jsou dostupné kdykoli, aby (například pokud několik virtuálních počítačů, přejděte) můžete zpracovávat všechny neočekávaným nárůstem nebo selhání.  Když kapacita navíc byste měli určit podle očekávané špičky použití, je výchozí bod rezervovat několik dalších virtuálních počítačů (% 5 až 10 Další).

Předchozí předpokládá jednu stavové služby. Pokud máte více než jeden stavové služby, budete muset přidat DB_Size spojené s jinými službami do rovnice. Alternativně můžete vypočítat počet uzlů samostatně pro každý stavové služby.  Vaše služba může mít repliky nebo oddíly, které nejsou vyváženy. Uvědomte si, že oddíly mohou mít i více dat než jiné. Další informace o dělení najdete v tématu [článek věnovaný tomu, osvědčené postupy vytváření oddílů](service-fabric-concepts-partitioning.md). Předchozí rovnice ale oddílů a replik nezávislá, protože Service Fabric zajišťuje, že tyto repliky jsou rozprostřeny mezi uzly optimalizovaným způsobem.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Pomocí tabulky pro výpočet nákladů
Teď můžeme ve vzorci vložit některé reálná čísla. [Tabulky příklad](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) ukazuje, jak plánování kapacity pro aplikace, která obsahuje tři typy datových objektů. Pro každý objekt budeme blížit jeho velikost a počet objektů, že chceme mít. Můžeme také vybrat počet replik chceme, aby každý typ objektu. Tabulky vypočítá celkové množství paměti se ukládají v clusteru.

Potom jsme zadat velikost virtuálního počítače a měsíční náklady. Na základě velikosti virtuálního počítače tabulky zjistíte, minimální počet oddílů, které je nutné použít k rozdělení data fyzicky vejít na uzlech. Může mít větší počet oddílů, aby odpovídala konkrétním výpočtu vaší aplikace a je třeba síťový provoz. Tabulka ukazuje, že počet oddílů, které spravujete uživatelské objekty profilu zvýšil z nich se má šest.

Nyní na základě této informace tabulky ukazuje, že by mohla fyzicky získá všechna data s požadovanou oddíly a replikami v 26 uzlu clusteru. Ale tento cluster by hustě balí, proto je vhodné některé další uzly selhání uzlů a upgrady. Tabulka také ukazuje, že máte víc než 57 uzly neposkytuje žádnou další hodnotu, protože bude mít prázdné uzly. Chcete znovu, přesto přejít nad 57 uzly selhání uzlů a upgrady. Můžete upravit tabulku tak, aby odpovídala konkrétním potřebám vaší aplikace.   

![Tabulky pro výpočet nákladů][Image1]

## <a name="next-steps"></a>Další postup
Podívejte se na [služeb vytváření oddílů Service Fabric] [ 10] Další informace o dělení vaší služby.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
