---
title: 'Úvodní příručka: Testování znalostní báze s dávkovými otázkami'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3bc095d8949f177ccb6c4cc111ba4b272027904e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756712"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Úvodní příručka: Testování znalostní báze s dávkovými otázkami a očekávanými odpověďmi

Pomocí dávkového testovacího nástroje QnA Maker otestujte znalostní báze v prostředku QnA Makeru pro očekávané odpovědi, skóre spolehlivosti a výzvy k více otočení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Buď [vytvořte službu QnA Maker,](create-publish-knowledge-base.md) nebo použijte existující službu, která používá anglický jazyk.
* Stažení [ukázkového souboru s více otočeními `.docx` ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Stáhněte si [nástroj pro dávkové](https://aka.ms/qnamakerbatchtestingtool)testování `.zip` , extrahujte spustitelný soubor ze souboru.

## <a name="sign-into-qna-maker-portal"></a>Přihlášení k portálu QnA Maker

[Přihlaste](https://www.qnamaker.ai/) se k portálu QnA Maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Vytvoření nové znalostní báze ze souboru sample.docx s více otočeními

1. Vyberte **Vytvořit znalostní bázi** z panelu nástrojů.
1. Přeskočte **krok 1,** protože byste již měli mít prostředek QnA Maker, přejděte ke **kroku 2** a vyberte existující informace o prostředcích:
    * Azure Active Directory ID
    * Název předplatného Azure
    * Název služby Azure QnA
    * Jazyk - anglický jazyk
1. Zadejte `Multi-turn batch test quickstart` název jako název znalostní báze.

1. V **kroku 4**nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolte vícenásobné extrakce z adres URL, souborů PDF nebo .docx.**|Zaškrtnuté|
    |**Výchozí text odpovědi**| `Batch test - default answer not found.`|
    |**+ Přidat soubor**|V požadavcích `.docx` vyberte výpis staženého souboru.|
    |**Chit-chat**|Vybrat **profesionální**|

1. V **kroku 5**vyberte vytvořit **kb**.

    Po dokončení procesu vytváření se na portálu zobrazí upravitelná znalostní báze.

## <a name="save-train-and-publish-knowledge-base"></a>Uložení, trénování a publikování znalostní báze

1. Chcete-li **ukládat** znalostní báze, vyberte uložit a trénovat z panelu nástrojů.
1. Vyberte **Publikovat** z panelu nástrojů a pak znovu vyberte **Publikovat** a publikujte znalostní bázi. Publikování zpřístupní znalostní bázi pro dotazy z veřejného koncového bodu adresy URL. Po dokončení publikování uložte adresu URL hostitele a klíčové informace koncového bodu zobrazené na stránce **Publikovat.**

    |Požadovaná data| Příklad|
    |--|--|
    |Publikovaný hostitel|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Publikovaný klíč|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 znaků řetězec `Endpoint` zobrazenpo )|
    |ID aplikace|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(Řetězec 36 znaků zobrazený jako součást `POST`) |

## <a name="create-batch-test-file-with-question-ids"></a>Vytvoření dávkového testovacího souboru s ID otázek

Chcete-li použít dávkový testovací nástroj, `batch-test-data-1.tsv` vytvořte soubor pojmenovaný textovým editorem. Soubor musí mít následující sloupce oddělené kartou.

|Pole vstupního souboru TSV|Poznámky|Příklad|
|--|--|--|
|ID znalostní báze|ID znalostní báze najdete na stránce Publikovat. Otestujte několik znalostních bází ve stejné službě najednou v jednom souboru pomocí různých ID znalostní báze v jednom souboru.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(Řetězec 36 znaků zobrazený jako součást `POST`) |
|Otázka|Text otázky, který by uživatel zadá. 1 000 znaků max.|`How do I sign out?`|
|Značky metadat|optional|`topic:power`používá _klíč:formát hodnoty_|
|Horní parametr|optional|`25`|
|ID očekávané odpovědi|optional|`13`|

Pro tuto znalostní bázi přidejte do souboru 3 řádky pouze 2 požadovaných sloupců. První sloupec je ID znalostní báze a druhý sloupec by měl být následující seznam otázek:

|Sloupec 2 - otázky|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Tyto otázky jsou přesné znění z znalostní báze a měl by vrátit 100 jako skóre spolehlivosti.

Dále přidejte několik otázek, podobně jako tyto otázky, ale ne přesně stejné na dalších 3 řádcích, pomocí stejného ID znalostní báze:

|Sloupec 2 - otázky|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Ujistěte se, že každý sloupec je oddělen pouze oddělovačem karet. Úvodní nebo koncové mezery jsou přidány do dat sloupce a způsobí, že program vyvolat výjimky, pokud je typ nebo velikost je nesprávná.

Dávkový testovací soubor při otevření v aplikaci Excel vypadá jako následující obrázek. ID znalostní báze `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` bylo z důvodu zabezpečení nahrazeno. U vlastního dávkového testu se ujistěte, že sloupec zobrazuje ID znalostní báze.

> [!div class="mx-imgBorder"]
> ![Zadání první verze souboru TSV z dávkového testu](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testování dávkového souboru

Spusťte program dávkového testování pomocí následujícího formátu příkazového řádku na příkazovém řádku.

Nahraďte `YOUR-RESOURCE-NAME` a `ENDPOINT-KEY` s vlastní mi se hodnotami pro název služby a klíč koncového bodu. Tyto hodnoty najdete na stránce **Nastavení** na portálu QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Test dokončí a vygeneruje `out.tsv` soubor:

> [!div class="mx-imgBorder"]
> ![Výstup první verze souboru TSV z dávkového testu](../media/batch-test/batch-test-1-output.png)

ID znalostní báze `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` bylo z důvodu zabezpečení nahrazeno. Pro vlastní dávkový test se ve sloupci zobrazí ID znalostní báze.

Testovací výstup skóre spolehlivosti ve čtvrtém sloupci zobrazuje 3 nejvyšší otázky, které vrátily skóre 100 podle očekávání, protože každá otázka je přesně stejná, jako je zobrazena ve znalostní bázi. Poslední 3 otázky, s novým zněním otázky, nevrátí 100 jako skóre důvěry. Chcete-li zvýšit skóre jak pro test, tak pro uživatele, je třeba přidat další alternativní otázky do znalostní báze.

## <a name="testing-with-the-optional-fields"></a>Testování s volitelnými poli

Jakmile pochopíte formát a proces, můžete vygenerovat testovací soubor, který můžete spustit proti vaší znalostní bázi ze zdroje dat, například z protokolů chatů.

Vzhledem k tomu, že zdroj dat a proces jsou automatizované, testovací soubor lze spustit mnohokrát s různými nastaveními k určení správných hodnot.

Pokud například máte protokol chatu a chcete určit, který text protokolu chatu se vztahuje na která pole metadat, vytvořte testovací soubor a nastavte pole metadat pro každý řádek. Spusťte test a zkontrolujte řádky, které odpovídají metadatům. Obecně by měly být shody pozitivní, ale měli byste zkontrolovat výsledky falešných poplachů. Falešně pozitivní je řádek, který odpovídá metadatům, ale na základě textu by se neměl shodovat.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Použití volitelných polí ve vstupním dávkovém testovacím souboru

Pomocí následujícího grafu zjistíte, jak najít hodnoty polí pro volitelná data.

|Číslo sloupce|Volitelný sloupec|Umístění dat|
|--|--|--|
|3|zprostředkovatele identity|Exportujte existující znalostní bázi pro existující dvojice _klíč:hodnota._|
|4|top|Doporučuje se `25` výchozí hodnota.|
|5|ID sady otázek a odpovědí|Exportujte existující znalostní bázi pro hodnoty ID. Všimněte si také, že ID byly vráceny ve výstupním souboru.|

## <a name="add-metadata-to-the-knowledge-base"></a>Přidání metadat do znalostní báze

1. Na portálu QnA přidejte na stránce `topic:power` **Úpravy** metadata k následujícím otázkám:

    |Dotazy|
    |--|
    |Nabíjení zařízení Surface Pro 4|
    |Zkontrolujte stav baterie|

    Dva dvojice QnA mají nastavena metadata.

    > [!TIP]
    > Chcete-li zobrazit metadata a ID QnA každé sady, exportujte znalostní bázi. Vyberte stránku **Nastavení** a `.xls` pak vyberte **Exportovat** jako soubor. Najděte tento stažený soubor a otevřete v excelové recenzování metadat a ID.

1. Vyberte **Uložit a trénovat**, pak vyberte stránku **Publikovat** a pak vyberte tlačítko **Publikovat.** Tyto akce zpřístupnit změny dávkového testu. Stáhněte si znalostní báze ze stránky **Nastavení.**

    Stažený soubor má správný formát pro metadata a správné id sady otázek a odpovědí. Tato pole se použijí v další části.

    > [!div class="mx-imgBorder"]
    > ![Exportovaná znalostní báze s metadaty](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Vytvoření druhého dávkového testu

Existují dva hlavní scénáře pro dávkové testování:
* **Proces chat u souborů protokolu** - Určete nejvyšší odpověď na dříve neviditelné otázky - nejběžnější situace je, když je třeba zpracovat jsou soubor protokolu dotazů, jako jsou otázky uživatele chatu bot. Vytvořte test dávkového souboru pouze s požadovanými sloupci. Test vrátí nejvyšší odpověď pro každou otázku. To neznamená, že nejvyšší odpověď je správná odpověď. Po dokončení tohoto testu přejděte k ověřovacímu testu.
* **Ověřovací test** - Ověření očekávané odpovědi. Tento test vyžaduje, aby byly ověřeny všechny otázky a odpovídající očekávané odpovědi v dávkovém testu. To může vyžadovat určitý ruční proces.

Následující postup předpokládá, že scénář je zpracování protokolů chatu s

1. Vytvořte nový dávkový testovací soubor, který bude obsahovat volitelná data . `batch-test-data-2.tsv` Přidejte 6 řádků z původního vstupního souboru dávkového testu a pro každý řádek přidejte id páru metadat, horní a QnA.

    Chcete-li simulovat automatizovaný proces kontroly nového textu z protokolů chatu ve znalostní `topic:power`bázi, nastavte metadata pro každý sloupec na stejnou hodnotu: .

    > [!div class="mx-imgBorder"]
    > ![Zadání druhé verze souboru TSV z dávkového testu](../media/batch-test/batch-test-2-input.png)

1. Spusťte test znovu a změna názvů vstupních a výstupních souborů označuje, že se jedná o druhý test.

    > [!div class="mx-imgBorder"]
    > ![Výstup druhé verze souboru TSV z dávkového testu](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Výsledky zkoušek a automatizovaný testovací systém

Tento výstupní soubor testu lze analyzovat jako součást automatizovaného průběžného testovacího kanálu.

Tento konkrétní testovací výstup by měl být přečten takto: každý řádek byl filtrován s metadaty a protože každý řádek neodpovídá metadatům ve znalostní bázi, výchozí odpověď pro tyto neodpovídající řádky vrácené ("žádná dobrá shoda nalezena v kb"). Z těchto řádků, které se shodují, QnA ID a skóre byly vráceny.

Všechny řádky vrátil popisek nesprávné, protože žádný řádek uzavřeno ID odpovědi očekává.

Měli byste být schopni vidět s těmito výsledky, které můžete vzít chat log a použít text jako dotaz každého řádku. Aniž byste o datech něco věděli, výsledky vám řeknou hodně o datech, která pak můžete použít v pohybu vpřed:

* meta-data
* QnA ID
* skóre

Bylo filtrování s meta-data dobrý nápad pro test? Ano i ne. Testovací systém by měl vytvořit testovací soubory pro každý pár metadat, stejně jako test bez párů metadat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v testování znalostní báze, odstraňte dávkový soubor ový nástroj a testovací soubory.

Pokud nebudete tuto znalostní bázi nadále používat, odstraňte znalostní báze pomocí následujících kroků:

1. Na portálu QnA Maker vyberte v horní nabídce **moje znalostní báze.**
1. V seznamu znalostních bází vyberte ikonu **Odstranit** na řádku znalostní báze tohoto rychlého startu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
