---
title: Nasazení modulu ABAP funkce pro extrakci metadat pro skupinu SAP R3 pro mosty v Azure dosah
description: Tento článek popisuje postup nasazení modulu funkcí ABAP na serveru SAP.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 1d9f1c5beafb7b54c5fd0189dd738ff8e346a3e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695947"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Nasazení modulu ABAP funkce extrakce metadat pro skupinu SAP R3 pro mosty 
Tento článek popisuje postup nasazení modulu funkcí ABAP na serveru SAP.
## <a name="overview"></a>Přehled 

Most SAP Business Suite 4 HANA (S/4HANA), ECC a R/3 ERP se dá použít k extrakci metadat ze serveru SAP. Toho dosáhnete tak, že umístíte modul funkce ABAP na Server SAP. Tento modul Function je vzdáleně přístupný pro daný most k dotazování a ke stažení (jako textový soubor) metadata obsahující Server SAP.
Po spuštění mostu pak provede jednu z těchto akcí:

1.  Importuje metadata z existujícího souboru již staženého místně z předchozího spuštění přemostění.

2.  Vyvolá rozhraní API modulu ABAP, počká na stažení a pak Importuje metadata z tohoto souboru.

Tento dokument podrobně popisuje kroky potřebné k nasazení tohoto modulu.

> [!Note] 
>Následující pokyny byly zkompilovány na základě grafického uživatelského rozhraní SAP v. 7.2

## <a name="deployment-of-the-module"></a>Nasazení modulu 

### <a name="create-a-package"></a>Vytvoření balíčku 

Tento krok je nepovinný a můžete použít existující balíček.

1.  Přihlaste se k SAP S/4HANA nebo SAP ECC serveru a otevřete \" navigátor objektů \" (SE80 transakce).

2.  \"V seznamu vyberte možnost balíček možností \" a zadejte název nového balíčku (například Z \_ MITI) a potom stiskněte tlačítko Zobrazit.

3.  V okně vytvořit balíček stiskněte tlačítko Ano. V důsledku toho se \" otevře okno Tvůrce balíčků oken: vytvořit balíček \" . Zadejte hodnotu do pole krátký popis a stiskněte \" \" ikonu pokračovat.

4.  V okně výzvy pro místní žádost aplikace Workbench klikněte na vlastní požadavky. Vyberte žádost o vývoj.

### <a name="create-a-function-group"></a>Vytvoření skupiny funkcí 

V části navigátor objektů v \" seznamu vyberte skupinu funkcí \" a zadejte její název do vstupního pole níže (například Z \_ MITI \_ FGROUP). Stiskněte ikonu zobrazit.

1.  V \" okně vytvořit objekt \" stisknutím tlačítka Ano vytvořte novou skupinu funkcí.

2.  Zadejte odpovídající popis v \" poli krátký text \" a stiskněte tlačítko \" Uložit \" .

3.  Vyberte balíček, který jste připravili v předchozím kroku, \" vytvořte balíček \" a klikněte na ikonu \" Uložit \" .

4.  Potvrďte požadavek stisknutím ikony \" pokračovat \" .

5.  Aktivujte tuto skupinu funkcí.

### <a name="create-the-abap-function-module"></a>Vytvoření modulu funkce ABAP 

Po vytvoření a výběru skupiny funkcí klikněte pravým tlačítkem na jeho název v prohlížeči úložiště a vyberte \" vytvořit = \> modul funkce \" .

\" \_ \_ \" Do pole modul funkce zadejte Z MITI stáhnout \" \" a vyplňte \" krátký text \" zadáním správného popisu.

Po vytvoření modulu zadejte následující informace:

1.  Přejděte na \" kartu atributy \" .

2.  Vyberte typ zpracování = Remote-Enabled modul funkce.

    :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Možnosti registrace zdrojů" border="true":::

3.  Přejděte na \" kartu zdrojový kód \" . Existují dva způsoby, jak nasadit kód pro funkci:

    a.  Z hlavní nabídky nahrajte Z \_ MITI \_DOWNLOAD.txt soubor tak, že vyberete pomůcky = \> Další nástroje = \> nahrát/stáhnout = \> nahrát.

    b.  Případně otevřete soubor, zkopírujte jeho obsah a vložte ho do \" oblasti zdrojového kódu \" .

4.  Přejděte na \" kartu Import \" a vytvořte následující parametry:

    a.  \_Typ oblasti P DD02L-TABNAME (volitelné = true)

    b.  *P \_ \_Řetězec typu místní cesty* (volitelné = true)

    c.  *\_Typ jazyka P L001TAB – výchozí hodnota \' E dat E\'*

    d.  *TYP ROWSKIPS a \_ Výchozí hodnota int 0*

    e.  *TYP ROWCOUNT, takže \_ Výchozí hodnota int 0*

    > [!Note]
    > Zvolit \" předání hodnoty \" pro všechny

    :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Možnosti registrace zdrojů" border="true":::

5.  Přejděte na kartu Tables (tabulky) a definujte následující:

    *EXPORTOVAT \_ tabulku jako TAB512*

    :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Možnosti registrace zdrojů" border="true":::

6.  Přejděte na \" kartu výjimky \" a definujte tuto výjimku:

    *\_ \_ DOWNLOADFAILED grafického uživatelského rozhraní pro E exp \_*

    :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Možnosti registrace zdrojů" border="true":::

7.  Uložte funkci (stiskněte CTRL + S nebo zvolte funkce Module = \> Uložit v hlavní nabídce).

8.  Klikněte \" na \" ikonu aktivovat na panelu nástrojů (CTRL + F3) a \" v dialogovém okně klikněte na \" tlačítko pokračovat. Pokud se zobrazí výzva, měli byste vybrat vygenerované zahrnutí, která se mají aktivovat, spolu s hlavním modulem funkcí.

### <a name="testing-the-function"></a>Testování funkce 

Po dokončení všech předchozích kroků můžete funkci otestovat pomocí následujících kroků:

1.  Otevřete \_ \_ modul funkce stažení Z MITI.

2.  \"V hlavní nabídce zvolte možnost modul Module = \> Test = \> modul test Function \" (nebo stiskněte klávesu F8).

3.  Zadejte cestu ke složce v místním systému souborů do parametru P \_ Local \_ path a stiskněte \" \" ikonu spustit na panelu nástrojů (nebo stiskněte klávesu F8).

4.  \_Pokud je nutné stáhnout nebo aktualizovat soubor s metadaty, uveďte název oblasti zájmu do pole oblast P. Když funkce dokončí práci, složka, která byla uvedena v \_ parametru místní cesty P, \_ musí obsahovat několik souborů s metadaty v. Názvy souborů napodobují oblasti, které lze zadat v \_ poli oblasti P.

Funkce dokončí provádění a metadata budou stažena mnohem rychleji v případě jejího spuštění na počítači, který má vysokorychlostní síťové připojení k SAP S/4HANA nebo ECC serveru.

## <a name="next-steps"></a>Další kroky

- [Registrace a kontrola zdroje SAP ECC](register-scan-sapecc-source.md)
- [Registrace a kontrola zdroje SAP S/4HANA](register-scan-saps4hana-source.md)