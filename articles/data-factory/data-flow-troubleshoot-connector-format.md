---
title: Řešení potíží s konektorem a formátováním v mapování toků dat
description: Naučte se řešit problémy toku dat související s konektorem a formátem v Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 7596bf3182f59758eeee66e248404e98c18da2e7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715431"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Řešení potíží s konektorem a formátováním při mapování toků dat v Azure Data Factory


Tento článek popisuje metody řešení potíží související s konektorem a formátem pro mapování toků dat v Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>JSON Cosmos DB &

### <a name="support-customized-schemas-in-the-source"></a>Podpora vlastních schémat ve zdroji

#### <a name="symptoms"></a>Příznaky
Pokud chcete tok dat ADF použít k přesunu nebo přenosu dat z Cosmos DB/JSON do jiných úložišť dat, může dojít k vynechání některých sloupců zdrojových dat. 

#### <a name="cause"></a>Příčina 
U bezplatných konektorů schématu (číslo sloupce, název sloupce a sloupec datový typ každého řádku se může při porovnávání s ostatními) lišit. ve výchozím nastavení používá ADF vzorové řádky (například data prvních 100 nebo 1000 řádků) k odvození schématu a odvozený výsledek bude použit jako schéma pro čtení dat. Takže pokud vaše úložiště dat obsahují nadbytečné sloupce, které se nezobrazují v ukázkových řádcích, data těchto sloupců navíc nebudou čtena, přesunuta ani převedena do úložišť dat jímky.

#### <a name="recommendation"></a>Doporučení
Chcete-li přepsat výchozí chování a přinášet do dalších polí, ADF poskytuje možnosti pro přizpůsobení schématu zdroje. Můžete zadat další/chybějící sloupce, které by mohly chybět ve výsledku v důsledku projekce zdroje toku dat pro čtení dat, a můžete použít jednu z následujících možností pro nastavení přizpůsobeného schématu. **Možnost-1** je obvykle vhodnější.

- **Možnost-1**: porovnání s původními zdrojovými daty, která můžou být jedním z velkých souborů, tabulek nebo kontejnerů, které obsahují miliony řádků se složitými schématy, můžete vytvořit dočasnou tabulku nebo kontejner s několika řádky, které obsahují všechny sloupce, které chcete číst, a pak přejít k následující operaci: 

    1. K získání kompletního schématu použijte **nastavení ladění** zdroje toku dat pro **Import projekce** s ukázkovými soubory nebo tabulkami. Můžete postupovat podle kroků na následujícím obrázku:<br/>

        ![Snímek obrazovky, který ukazuje možnost 1-1 doporučení](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. V plátně toku dat vyberte **nastavení ladění** .
         1. V místním podokně vyberte na kartě **CosmosSource** **ukázkovou tabulku** . 
         1. Zadejte název tabulky v bloku **tabulky** .
         1. Vyberte **Uložit** a uložte nastavení.
         1. Vyberte **Import projekce**.<br/>  
    
    1. Změňte **nastavení ladění** zpět na použít zdrojovou datovou sadu pro zbývající přesun a transformaci dat. Můžete přejít na kroky na následujícím obrázku:<br/>

        ![Snímek obrazovky, který ukazuje možnost 1-2 doporučení](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. V plátně toku dat vyberte **nastavení ladění** .
         1. V místním podokně vyberte **zdrojovou datovou sadu** na kartě **cosmosSource** .
         1. Vyberte **Uložit** a uložte nastavení.<br/>
    
    Následně bude modul runtime toku dat ADF a pomocí přizpůsobeného schématu číst data z původního úložiště dat. <br/>

- **Možnost-2**: Pokud jste obeznámeni se schématem a jazykem DSL zdrojových dat, můžete ručně aktualizovat skript zdroje dat a přidat další/zmeškané sloupce pro čtení dat. Příklad je znázorněn na následujícím obrázku: 

    ![Snímek obrazovky, který ukazuje možnost 2 doporučení](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Další kroky
Další pomoc při řešení potíží najdete v těchto materiálech:

*  [Řešení potíží s mapováním toků dat v Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)