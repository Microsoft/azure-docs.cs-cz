---
title: Plánování kapacity pro aplikace Service Fabric
description: Popisuje, jak identifikovat počet výpočetních uzlů požadovaných pro aplikaci Service Fabric
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377204"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Plánování kapacity pro aplikace Service Fabric
Tento dokument vás naučí odhadnout množství prostředků (procesory, paměť RAM, diskové úložiště), které potřebujete ke spuštění aplikací Azure Service Fabric. Je běžné, že vaše požadavky na prostředky měnit v průběhu času. Obvykle vyžadují několik prostředků při vývoji nebo testování služby a pak vyžadují další prostředky, jak jdete do produkčního prostředí a vaše aplikace roste v popularitě. Při navrhování aplikace si promyslete dlouhodobé požadavky a proveďte volby, které umožňují škálování vaší služby tak, aby splňovala vysokou poptávku zákazníků.

 Při vytváření clusteru Service Fabric se rozhodnete, jaké druhy virtuálních počítačů (VM) tvoří cluster. Každý virtuální modul je dodáván s omezeným množstvím prostředků ve formě procesorů (jádra a rychlost), šířky pásma sítě, paměti RAM a úložiště disku. Jak vaše služba roste v průběhu času, můžete upgradovat na virtuální počítače, které nabízejí větší prostředky a/nebo přidat další virtuální počítače do clusteru. Chcete-li provést druhé, musíte architekt vaší služby zpočátku tak, aby mohla využívat nové virtuální počítače, které získat dynamicky přidány do clusteru.

Některé služby spravovat málo nebo žádná data na samotných virtuálních počítačích. Plánování kapacity pro tyto služby by se proto mělo zaměřit především na výkon, což znamená výběr vhodných procesorů (jader a rychlosti) virtuálních počítačů. Kromě toho byste měli zvážit šířku pásma sítě, včetně toho, jak často dochází k síťovým přenosům a kolik dat se přenáší. Pokud vaše služba potřebuje provést dobře a zvyšuje využití služby, můžete přidat další virtuální počítače do clusteru a vyrovnávání zatížení síťové požadavky napříč všemi virtuálními počítači.

U služeb, které spravují velké množství dat na virtuálních počítačích, by se plánování kapacity mělo zaměřit především na velikost. Proto byste měli pečlivě zvážit kapacitu paměti RAM virtuálního počítače a úložiště disků. Systém správy virtuální paměti v systému Windows způsobí, že místo na disku bude vypadat jako paměť RAM pro kód aplikace. Kromě toho service fabric runtime poskytuje inteligentní stránkování vedení pouze horká data v paměti a přesunutí studená data na disk. Aplikace tak můžete použít více paměti, než je fyzicky k dispozici na virtuálním počítači. S více paměti RAM jednoduše zvyšuje výkon, protože virtuální počítače můžete zachovat více diskového úložiště v paměti RAM. Vybraný virtuální počítač by měl mít disk dostatečně velký pro uložení požadovaných dat na virtuálním počítači. Podobně by virtuální virtuální paměť měla mít dostatek paměti RAM, aby vám poskytla výkon, který si přejete. Pokud se data vaší služby v průběhu času zvětšují, můžete do clusteru přidat další virtuální počítače a rozdělit data mezi všechny virtuální počítače.

## <a name="determine-how-many-nodes-you-need"></a>Určení počtu uzlů, které potřebujete
Rozdělení služby umožňuje škálovat data služby. Další informace o dělení naleznete v tématu [Partitioning Service Fabric](service-fabric-concepts-partitioning.md). Každý oddíl se musí vejít do jednoho virtuálního počítače, ale více (malých) oddílů může být umístěno na jeden virtuální počítač. Takže s více malých oddílů vám dává větší flexibilitu než s několik větších oddílů. Kompromis je, že mají velké množství oddílů zvyšuje service fabric režii a nelze provádět transakční operace napříč oddíly. Existuje také další potenciální síťový provoz, pokud váš kód služby často potřebuje přístup k části dat, které žijí v různých oddílech. Při navrhování služby, měli byste pečlivě zvážit tyto výhody a nevýhody, aby se dospělo k efektivní strategii dělení.

Předpokládejme, že vaše aplikace má jednu stavovou službu, která má velikost úložiště, které očekáváte růst na DB_Size GB za rok. Jste ochotni přidat další aplikace (a oddíly), jak dochází k růstu po tomto roce.  Faktor replikace (RF), který určuje počet replik pro vaši službu ovlivňuje celkový DB_Size. Celkový DB_Size ve všech replikách je faktor replikace vynásobený DB_Size.  Node_Size představuje místo na disku/ram na uzel, který chcete použít pro vaši službu. Pro dosažení nejlepšího výkonu by DB_Size měl ypovity do paměti v rámci clusteru a měla by být zvolena Node_Size, která je kolem paměti RAM virtuálního virtuálního zařízení. Přidělením Node_Size, která je větší než kapacita paměti RAM, spoléháte na stránkování poskytované service fabric runtime. Proto váš výkon nemusí být optimální, pokud jsou celá data považována za horká (od té doby jsou data stránkována dovnitř / ven). U mnoha služeb, kde je pouze zlomek dat horký, je však nákladově efektivnější.

Počet uzlů požadovaných pro maximální výkon lze vypočítat takto:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Účet pro růst
Můžete chtít vypočítat počet uzlů na základě DB_Size, které očekáváte, že vaše služba růst, kromě DB_Size, které jste začali s. Potom zvětšit počet uzlů jako vaše služba roste tak, že nejsou nadměrné zřizování počet uzlů. Ale počet oddílů by měla být založena na počtu uzlů, které jsou potřebné při spuštění služby na maximální růst.

Je dobré mít některé další počítače k dispozici kdykoliv, takže můžete zpracovat všechny neočekávané špičky nebo selhání (například pokud několik virtuálních počítačů přejít dolů).  Zatímco další kapacita by měla být určena pomocí očekávané špičky, výchozím bodem je rezervovat několik dalších virtuálních počítačích (5-10 procent navíc).

Předchozí předpokládá jednu stavovou službu. Pokud máte více než jednu stavovou službu, musíte do rovnice přidat DB_Size přidružené k ostatním službám. Případně můžete vypočítat počet uzlů samostatně pro každou stavovou službu.  Vaše služba může mít repliky nebo oddíly, které nejsou vyvážené. Mějte na paměti, že oddíly mohou mít také více dat než ostatní. Další informace o dělení naleznete v [článku o dělení osvědčených postupů](service-fabric-concepts-partitioning.md). Předchozí rovnice je však oddíl a replika agnostik, protože Service Fabric zajišťuje, že repliky jsou rozprostřeny mezi uzly optimalizovaným způsobem.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Použití tabulky pro výpočet nákladů
Teď dáme do vzorce nějaká reálná čísla. [Ukázková tabulka](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) ukazuje, jak naplánovat kapacitu pro aplikaci, která obsahuje tři typy datových objektů. U každého objektu přibližujeme jeho velikost a počet objektů, které očekáváme. Také jsme vybrat, kolik replikchceme každého typu objektu. Tabulka vypočítá celkové množství paměti, která má být uložena v clusteru.

Pak zadáme velikost virtuálního počítače a měsíční náklady. Na základě velikosti virtuálního počítače tabulka informuje o minimálním počtu oddílů, které je nutné použít k rozdělení dat tak, aby se fyzicky vešly do uzlů. Můžete chtít větší počet oddílů pro potřeby konkrétní chod vaší aplikace a síťového provozu. Tabulka zobrazuje počet oddílů, které spravují objekty profilu uživatele, se zvýšil z jedné na šest.

Nyní, na základě všech těchto informací, tabulka ukazuje, že můžete fyzicky získat všechna data s požadovanými oddíly a repliky v clusteru s 26 uzly. Tento cluster by však být hustě zabalené, takže můžete chtít některé další uzly pro umístění selhání uzlů a upgrady. Tabulka také ukazuje, že s více než 57 uzlů poskytuje žádnou další hodnotu, protože by prázdné uzly. Opět můžete chtít přejít nad 57 uzlů stejně ubytovat selhání uzlů a upgrady. Tabulku můžete vyladit tak, aby odpovídala specifickým potřebám vaší aplikace.   

![Tabulka pro výpočet nákladů][Image1]

## <a name="next-steps"></a>Další kroky
Další informace o rozdělení služby najdete v informacích o službě [Partitioning Service Fabric.][10]

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
