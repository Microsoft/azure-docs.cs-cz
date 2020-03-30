---
title: Import a export dat s projekty pomocí náhledu poznámkových bloků Azure
description: Zjistěte, jak přenést data do projektu Azure Notebooks Preview z externích zdrojů a jak exportovat data z projektu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646972"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Práce s datovými soubory v projektech Náhled poznámkových bloků Azure

Data jsou mízou mnoha notebooků Jupyter, zejména notebooků používaných pro datovou vědu. Pomocí poznámkových bloků Azure můžete snadno importovat z různých zdrojů do projektu a pak použít tato data z poznámkových bloků. Můžete také nechat poznámkové bloky generovat data uložená v projektu, která si pak můžete stáhnout pro použití jinde.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Nabídka **Data** v běžícím poznámkovém bloku také obsahuje příkazy **Pro odesílání** a **stahování,** které fungují se soubory v projektu, a také dočasné soubory pro aktuální relaci poznámkového bloku.

Kód v poznámkovém bloku můžete také použít k přímému přístupu k různým zdrojům dat, včetně souborů v rámci projektu. Můžete také přistupovat k libovolným datům pomocí příkazů v buňce kódu. Vzhledem k tomu, že tato data jsou uložena v proměnných v relaci poznámkového bloku, nebudou uložena v projektu, pokud nepoužijete kód pro specificky generování souborů projektu.

Práce s kódem v datech je nejlepší v rámci samotného běžícího poznámkového bloku: pro tento účel se podívejte [na informace o získání dat v ukázkovém poznámkovém bloku Azure](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Zbývající část tohoto článku obsahuje podrobnosti o operacích souborů na úrovni projektu.

## <a name="import-data"></a>Import dat

Soubory můžete přenést do projektu z řídicího panelu projektu nebo v rámci spuštěného poznámkového bloku pomocí nabídky **Data** nebo příkazu, například `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Import souborů z řídicího panelu projektu

1. V projektu přejděte do složky, do které chcete soubory importovat.

1. Vyberte příkaz **Nahrát,** pak buď **z adresy URL** nebo z **počítače** a promítejte potřebné podrobnosti pro data, která chcete importovat:

   - **Z adresy URL**: Do pole **Adresa URL souboru** zadejte zdrojovou adresu a název souboru, který chcete přiřadit poznámkovému bloku v projektu, do pole **Název souboru.** Pak vyberte **+ Přidat soubor** a přidejte adresu URL do seznamu pro nahrávání. Postup opakujte pro všechny další adresy URL a pak vyberte **Hotovo**.

     ![Nahrát z místního vyskakovacího místa url](media/quickstarts/upload-from-url-popup.png)

   - **Z počítače**: Přetáhněte soubory do místního vyskakovacího místa nebo vyberte **Zvolit soubory**, pak vyhledejte a vyberte datové soubory, které chcete importovat. Můžete přetáhnout nebo zvolit libovolný počet souborů libovolného typu a formátu, protože je na kódu v poznámkovém bloku otevřít soubor a analyzovat jeho data.

     ![Nahrát z místního blokování počítače](media/quickstarts/upload-from-computer-popup.png)

1. Po importu se soubory zobrazí na řídicím panelu projektu a lze k nim přistupovat v kódu poznámkového bloku pomocí relativních názvů cest k obsahující složce.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Import souborů z nabídky Soubor v poznámkovém bloku

1. V běžícím poznámkovém bloku vyberte příkaz**Nahrání** **souboru:** > 

    ![Příkaz nabídky Nahrání souboru v poznámkovém bloku](media/file-menu-upload.png)

1. V zobrazeném dialogovém okně přejděte na soubory, které chcete nahrát, a vyberte je. Můžete vybrat libovolný počet souborů libovolného typu. Po dokončení vyberte **Otevřít.**

1. V rozevíracím okně **Stav nahrávání** vyberte **cílovou složku** z rozevíracího seznamu:

    - Složka relace*~/* ( ): Nahraje soubory do aktuální relace poznámkového bloku, ale nevytvoří soubory v projektu. Složka relace je peer do složky projektu, ale nepřetrvává po ukončení relace. Chcete-li získat přístup k souborům relací v kódu, předponu názvy souborů relativní cestou *.. /*.

        Použití složky relace je užitečné pro experimentování a zabraňuje zahlcení projektu se soubory, které můžete nebo nemusíte potřebovat dlouhodobě. Můžete také nahrát soubory do složky relace, které mají stejné názvy k souborům v projektu, aniž by došlo ke konfliktům a aniž by bylo třeba soubory přejmenovat. Řekněme například, že již máte v projektu jednu verzi *souboru data.csv,* ale chcete experimentovat s jinou verzí *souboru data.csv*. Nahráním souboru do složky relace můžete poznámkový blok spustit pomocí dat v nahraném souboru (odkazující na něj v kódu pomocí *.. /data.csv)* spíše než data v souboru projektu.

    - Složka projektu (*/project*): nahraje soubory do projektu, kde k nim lze přistupovat pomocí relativních názvů cest v kódu. Nahrání souboru do této složky je stejné jako nahrání souboru na řídicí panel projektu. Soubor je uložen spolu s projektem a je k dispozici v pozdějších relacích.

        Nahrávání se nezdaří, pokud se pokusíte nahrát soubor se stejným názvem jako ten, který již v projektu existuje. Chcete-li přepsat soubor, nahrajte nový soubor z řídicího panelu projektu, který vám dává možnost přepsat.

1. Vyberte **Spustit nahrávání,** chcete-li proces dokončit.

### <a name="create-or-import-files-using-commands"></a>Vytváření nebo import souborů pomocí příkazů

Příkazy v terminálu nebo v rámci buňky kódu Pythonu můžete použít k vytvoření souborů v rámci složky projektu i relace. Například příkazy `curl` jako `wget` A stahování souborů z Internetu přímo.

Chcete-li stahovat soubory v terminálu, vyberte příkaz **Terminál** na řídicím panelu projektu a zadejte vhodné příkazy:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Při použití kódové buňky Pythonu v poznámkovém bloku předponujete příkazy pomocí `!`aplikace .

Složka projektu je výchozí složka, takže zadáním cílového názvu souboru, jako *je oil_price.csv,* se soubor vytvoří v projektu. Chcete-li vytvořit soubor relace, předponu název s *.. /* jako v *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Vytváření souborů v kódu

Při použití kódu, který vytvoří soubor, `write_csv` jako je například pandas funkce, názvy cest jsou vždy relativní ke složce projektu. Pomocí *.. /* vytvoří soubor relace, který je zahozen, když je poznámkový blok zastaven a uzavřen.

## <a name="export-files"></a>Exportovat soubory

Data můžete exportovat z řídicího panelu projektu nebo z poznámkového bloku.

## <a name="export-files-from-the-project-dashboard"></a>Export souborů z řídicího panelu projektu

Na řídicím panelu projektu klikněte pravým tlačítkem myši na soubor a vyberte **Stáhnout**:

![Příkaz Stáhnout v kontextové nabídce položky projektu](media/download-command.png)

Můžete také vybrat soubor a použít příkaz **Stáhnout** (klávesová zkratka: d) na řídicím panelu:

![Příkaz Stáhnout panel nástrojů na řídicím panelu projektu](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Export souborů z nabídky Data v poznámkovém bloku

1. Vyberte příkaz nabídky**Stažení** **souboru:** > 

    ![Příkaz nabídky Stahování dat v poznámkovém bloku](media/file-menu-download.png)

1. Zobrazí se vyskakovací okno, které zobrazuje složky v relaci; složka *projektu* obsahuje soubory projektu:

    ![Vyskakovací okno příkazu Stahování dat, ve kterém vybíráte soubory a složky](media/file-menu-download-popup.png)

1. Zaškrtněte políčka nalevo od souborů a složek, které chcete stáhnout, a pak vyberte **Stáhnout vybrané**.

1. Poznámkový blok připraví jeden soubor *ZIP* obsahující vybrané soubory, který pak uložíte jako obvykle z prohlížeče. Poznámkový blok vytvoří soubor *ZIP,* i když stáhnete jeden soubor.

## <a name="next-steps"></a>Další kroky

- [Přístup ke cloudovým datům v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
