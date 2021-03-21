---
title: Průvodce kopírováním Data Factory Azure
description: Přečtěte si, jak pomocí Průvodce kopírováním Azure Data Factory zkopírovat data z podporovaných zdrojů dat do jímky.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393373"
---
# <a name="azure-data-factory-copy-wizard"></a>Průvodce kopírováním Azure Data Factory

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Průvodce kopírováním Azure Data Factory usnadňuje proces ingestování dat, což je obvykle první krok v uceleném scénáři integrace dat. Když projdete průvodcem kopírováním Azure Data Factory, nemusíte pochopit žádné definice JSON pro propojené služby, datové sady a kanály. Průvodce automaticky vytvoří kanál pro kopírování dat z vybraného zdroje dat do vybraného cíle. Kromě toho vám Průvodce kopírováním pomůže ověřit data, která se ingestují v době vytváření. Tím ušetříte čas, zejména při první ingestování dat ze zdroje dat. Průvodce kopírováním spustíte tak, že kliknete na dlaždici **Kopírovat data** na domovské stránce vaší datové továrny.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Navrženo pro velké objemy dat
Tento průvodce vám umožní snadno přesunout data z nejrůznějších zdrojů do cílových míst během několika minut. Po absolvování průvodce se automaticky vytvoří kanál s aktivitou kopírování, spolu se závislými Data Factory entitami (propojené služby a datové sady). K vytvoření kanálu nejsou nutné žádné další kroky.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z objektu blob Azure do tabulky Azure SQL Database najdete v [kurzu Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Průvodce je navržen s velkými objemy dat od začátku, s podporou pro různé typy dat a objektů. Můžete vytvářet Data Factory kanály, které přesouvají stovky složek, souborů nebo tabulek. Průvodce podporuje automatickou náhled dat, zachytávání schématu a mapování a filtrování dat.

## <a name="automatic-data-preview"></a>Náhled automatických dat
Můžete zobrazit náhled části dat z vybraného zdroje dat a ověřit tak, zda jsou data co chcete kopírovat. Kromě toho, pokud jsou zdrojová data v textovém souboru, Průvodce kopírováním analyzuje textový soubor, aby se dozvěděl oddělovače řádků a sloupců a schéma automaticky.

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Zachytávání a mapování schématu
Schéma vstupních dat nemusí v některých případech odpovídat schématu výstupních dat. V tomto scénáři je nutné mapovat sloupce ze zdrojového schématu na sloupce z cílového schématu.

> [!TIP]
> Při kopírování dat z SQL Server nebo Azure SQL Database do služby Azure synapse Analytics, pokud tabulka v cílovém úložišti neexistuje, Data Factory podporovat vytvoření automatické tabulky pomocí schématu zdroje. Přečtěte si další informace z [přesunu dat do a z Azure synapse Analytics pomocí Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Pomocí rozevíracího seznamu vyberte sloupec ze zdrojového schématu, který chcete namapovat na sloupec v cílovém schématu. Průvodce kopírováním se pokusí pochopit vzor pro mapování sloupců. Aplikuje stejný vzor na zbytek sloupců, takže nemusíte jednotlivě vybírat jednotlivé sloupce pro dokončení mapování schématu. Pokud chcete, můžete tato mapování přepsat pomocí rozevíracích seznamů a namapovat sloupce jeden po jedné. Vzor je přesnější při mapování více sloupců. Průvodce kopírováním průběžně aktualizuje vzor a nakonec dosáhne správného vzoru mapování sloupců, které chcete dosáhnout.     

![Mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat jímky. Filtrování omezuje objem dat, která mají být zkopírována do úložiště dat jímky, a tím zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob, jak filtrovat data v relační databázi pomocí dotazovacího jazyka SQL nebo souborů ve složce objektů BLOB v Azure pomocí [Data Factory funkcí a proměnných](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pomocí `Text.Format` funkce a `WindowStart` proměnné.

![Ověřit výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce Azure Blob
Proměnné v cestě ke složce můžete použít ke kopírování dat ze složky, která je určena za běhu na základě [systémových proměnných](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{Day}**, **{Hour}**, **{minute}** a **{Custom}**. Příklad: inputfolder/{Year}/{Month}/{Day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** pro **soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klikněte na **zvolit**. Mělo by se zobrazit `2016/03/01/02` v textovém poli. Nyní nahraďte **2016** **řetězcem** **{year}**, **03** a **{month}**, **01** s **{Day}** a **02** a stiskněte klávesu **TAB** . Měli byste vidět rozevírací seznamy a vybrat formát pro tyto čtyři proměnné:

![Použití systémových proměnných](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete také použít **vlastní** proměnnou a všechny [podporované formátovací řetězce](/dotnet/standard/base-types/custom-date-and-time-format-strings). Chcete-li vybrat složku s touto strukturou, použijte nejprve tlačítko **Procházet** . Potom hodnotu nahraďte hodnotou **{Custom}** a stisknutím klávesy **TAB** zobrazte textové pole, ve kterém můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den atd.). Obě tyto možnosti lze použít pro šířku konektorů napříč prostředími, včetně místních, cloudových a místních kopií.

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a veškerého podporovaného formátu. Naplánované kopírování umožňuje kopírovat data po předepsaném opakování. Ke konfiguraci naplánované kopie můžete použít bohatá nastavení (například opakování, časový limit a upozornění).

![Vlastnosti plánování](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Poradce při potížích

V této části se prozkoumá běžné metody řešení potíží pro Průvodce kopírováním v Azure Data Factory.

> [!NOTE] 
> Tyto tipy k odstraňování potíží se vztahují na Průvodce kopírováním ve verzi 1 Data Factory. Informace o Data Factory v2 najdete v Průvodci odstraňováním potíží při [řešení potíží s Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Kód chyby: nejde ověřit v průvodci kopírováním.

- **Příznaky**: v prvním kroku Průvodce kopírováním zjistíte zprávu s upozorněním "nelze ověřit".
- **Příčiny**: k tomu může dojít, když jsou všechny soubory cookie třetích stran zakázané.
- **Řešení**: 
    - Použijte Internet Explorer nebo prohlížeč Microsoft Edge.
    - Pokud používáte prohlížeč Chrome, postupujte podle pokynů níže a přidejte výjimku soubory cookie pro *microsoftonline.com* a *Windows.NET*.
        1.  Otevřete prohlížeč Chrome.
        2.  Klikněte na klíče nebo tři čáry vpravo (přizpůsobení a řízení Google Chrome).
        3.  Klikněte na **Nastavení**.
        4.  Prohledejte **soubory cookie** nebo v části Upřesnit nastavení otevřete **ochranu osobních údajů** .
        5.  Vyberte **Nastavení obsahu**.    
        6.  Soubory cookie by měly být nastavené tak, aby **umožňovaly nastavení místních dat (doporučeno)**.
        7.  Klikněte na **Spravovat výjimky**. V části **vzor názvu hostitele** zadejte následující **a ujistěte se, že je nastavení** chování nastaveno.
            - login.microsoftonline.com
            - login.windows.net
        8.  Zavřete prohlížeč a znovu ho spusťte.
    - Pokud používáte prohlížeč Firefox, přidejte výjimku soubory cookie podle pokynů níže.
        1. V nabídce Firefox přejděte na   >  **Možnosti** nástroje.
        2. V části Historie **ochrany osobních údajů** se  >  může zobrazit, že aktuální nastavení **používá pro historii vlastní nastavení**.
        3. V části **přijmout soubory cookie třetích stran** nemusí být vaše aktuální nastavení **nikdy**, potom klikněte na **výjimky** na pravé straně pro přidání následujících webů.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Zavřete prohlížeč a znovu ho spusťte. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Kód chyby: nejde otevřít přihlašovací stránku a zadat heslo.

- **Příznaky**: Průvodce kopírováním vás přesměruje na přihlašovací stránku, ale přihlašovací stránka se nezobrazuje úspěšně.
- **Příčiny**: k tomuto problému může dojít, pokud jste změnili síťové prostředí ze sítě Office do domácí sítě. V prohlížečích jsou několik mezipamětí. 
- **Řešení**: 
    1.  Zavřete prohlížeč a zkuste to znovu. Pokud problém ještě existuje, pokračujte na další krok.   
    2.  Pokud používáte prohlížeč Internet Explorer, zkuste ho otevřít v privátním režimu (stiskněte klávesu CTRL + SHIFT "+" P "). Pokud používáte prohlížeč Chrome, zkuste ho otevřít v režimu anonymním (stiskněte kombinaci kláves CTRL + SHIFT + "N"). Pokud problém ještě existuje, pokračujte na další krok. 
    3.  Zkuste použít jiný prohlížeč. 


## <a name="next-steps"></a>Další kroky
Rychlý návod, jak pomocí Průvodce kopírováním Data Factory vytvořit kanál s aktivitou kopírování, najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).