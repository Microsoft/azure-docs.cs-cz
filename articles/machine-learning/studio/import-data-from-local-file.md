---
Název: Importovat data ze souboru titleSuffix: Azure Machine Learning Studio Popis: Zjistěte, jak nahrát soubor trénovací data z pevného disku do Azure Machine Learning Studio. Tím se vytvoří datová sada modulu v pracovním prostoru.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 03/20/2017
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Import trénovacích dat ze souboru na pevném disku do Azure Machine Learning Studio

Zjistěte, jak nahrát soubor dat z vašeho pevného disku, který se použije jako trénovacích dat v Azure Machine Learning Studio. Importováním souboru dat máte datovou sadu modul, který je připravený k použití ve vašem pracovním prostoru.

## <a name="steps-to-import-data-from-a-local-file"></a>Postup při importu dat z místního souboru
Import dat z místního pevného disku, postupujte takto:

1. Klikněte na tlačítko **+ nová** v dolní části okna Machine Learning Studio.
2. Vyberte **datovou sadu** a **z místního souboru**.
3. V **nahrát novou datovou sadu** dialogové okno, přejděte na soubor, který chcete nahrát
4. Zadejte název, identifikace typu dat a volitelně zadejte popis. Popis se doporučuje – umožňuje zaznamenat všechny charakteristiky data, která chcete mějte na paměti při používání dat v budoucnu.
5. Zaškrtávací políčko **jde o novou verzi existující datovou sadu** umožňuje aktualizovat existující datovou sadu s novými daty. Klikněte na toto zaškrtávací políčko a potom zadejte název existující datovou sadu.

![Nahrát novou datovou sadu](./media/import-data-from-local-file/upload-dataset.png)

Při nahrávání zobrazí se vám zpráva, že váš soubor se nahrává. Nahrát doba závisí na velikosti dat a rychlosti vašeho připojení ke službě. Pokud víte, že soubor bude trvat dlouhou dobu, během čekáte můžete provádět další akce v nástroji Machine Learning Studio. Však zavření prohlížeče způsobí, že se data odeslala selhání.

## <a name="dataset-module-is-ready-for-use"></a>Modul datová sada je připravená k použití
Po nahrání dat je uložený v modulu datová sada a je k dispozici žádné experimentu do pracovního prostoru.

Při úpravě experiment, můžete najít datových sad, které jste vytvořili v **Moje datové sady** seznamu v části **uložení datové sady** seznamu paletě modulů. Můžete přetáhnout a vyřadit datovou sadu na plátno experimentu. Pokud chcete používat datovou sadu pro další analýzy s využitím a strojové učení.
