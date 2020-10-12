---
title: Import a export dat s projekty pomocí Azure Notebooks Preview
description: Naučte se, jak přenést data do projektu aplikace Azure Notebooks Preview z externích zdrojů a jak exportovat data z projektu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834110"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Práce s datovými soubory v Azure Notebooks projektech ve verzi Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Data jsou Lifeblood mnoha poznámkových bloků Jupyter, zejména poznámkových blocích používaných pro datové vědy. Pomocí Azure Notebooks můžete snadno importovat z různých zdrojů do projektu a pak tato data použít z poznámkových bloků. Poznámkové bloky můžete také vygenerovat data uložená v projektu, které pak můžete stáhnout pro použití jinde.

Nabídka **data** v běžícím poznámkovém bloku taky nabízí příkazy pro **nahrávání** a **stahování** , které pracují se soubory v projektu, i dočasné soubory pro aktuální relaci poznámkového bloku.

Můžete také použít kód v rámci poznámkového bloku pro přístup k nejrůznějším zdrojům dat přímo, včetně souborů v rámci projektu. K libovolným datům můžete také přistupovat pomocí příkazů v buňce kódu. Vzhledem k tomu, že tato data jsou uložena v proměnných v rámci relace poznámkového bloku, nebudou uložena do projektu, pokud nepoužijete kód pro specifickou generaci souborů projektu.

Práce s kódem v datech se nejlépe používá v samotném běžícím poznámkovém bloku: pro účely tohoto účelu si přečtěte [téma Jak získat data v Azure Notebooks ukázkovém poznámkovém bloku](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Zbývající část tohoto článku poskytuje podrobné informace o operacích se soubory na úrovni projektu.

## <a name="import-data"></a>Import dat

Soubory můžete přenést do projektu z řídicího panelu projekt nebo do běžícího poznámkového bloku pomocí nabídky **data** nebo příkazu, jako je například `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Importovat soubory z řídicího panelu projekt

1. V projektu přejděte do složky, do které chcete soubory importovat.

1. Vyberte příkaz **Odeslat** a potom buď **z adresy URL** , nebo **z počítače** a projektu potřebné podrobnosti pro data, která chcete importovat:

   - **Z adresy URL**: Zadejte zdrojovou adresu do pole **Adresa URL souboru** a název souboru, který chcete přiřadit k poznámkovému bloku v projektu v poli **název souboru** . Pak vyberte **+ Přidat soubor** a přidejte tak adresu URL do seznamu nahrávání. Opakujte tento postup pro všechny další adresy URL a potom vyberte **Hotovo**.

     ![Místní nabídka nahrát z adresy URL](media/quickstarts/upload-from-url-popup.png)

   - **Z počítače**: Přetáhněte soubory do místní nabídky nebo vyberte **zvolit soubory**, vyhledejte a vyberte datové soubory, které chcete importovat. Můžete vyřadit nebo zvolit libovolný počet souborů, libovolný typ a formát, protože je až do kódu v poznámkovém bloku k otevření souboru a k analýze jeho dat.

     ![Místní nabídka nahrát z počítače](media/quickstarts/upload-from-computer-popup.png)

1. Po importu se soubory zobrazí na řídicím panelu projekt a v rámci kódu poznámkového bloku jsou k dispozici pomocí relativních cest k obsaženým složkám.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Import souborů z nabídky soubor v poznámkovém bloku

1. V běžícím poznámkovém bloku **File**vyberte příkaz pro  >  **nahrání** souboru:

    ![Příkaz nabídky pro nahrání souboru v rámci poznámkového bloku](media/file-menu-upload.png)

1. V dialogovém okně, které se otevře, přejděte na seznam a vyberte soubory, které chcete nahrát. Můžete vybrat libovolný počet souborů libovolného typu. Po dokončení vyberte **otevřít** .

1. V místní nabídce **stav nahrávání** , která se zobrazí, vyberte v rozevíracím seznamu **cílovou složku** :

    - Složka relace ( *~/* ): nahraje soubory do aktuální relace poznámkového bloku, ale v projektu nevytvoří soubory. Složka relace je rovnocenná složce projektu, ale po ukončení relace nebude zachována. Chcete-li získat přístup k souborům relace v kódu, použijte předponu názvů souborů s relativní cestou *.. /*.

        Použití složky Session je užitečné pro experimentování a vyhnout se zbytečnému projektu se soubory, které mohou nebo nemusí být potřeba na dlouhodobé bázi. Soubory můžete také odeslat do složky relace, která má identické názvy souborů v projektu, aniž by docházelo ke konfliktům a bez nutnosti přejmenovat soubory. Řekněme například, že máte v projektu již jednu verzi *data.csv* , ale chcete experimentovat s jinou verzí nástroje *data.csv*. Nahráním souboru do složky relace můžete spustit Poznámkový blok pomocí dat v nahraném souboru (odkazující na něj v kódu pomocí *.. /data.csv*) místo dat v souboru projektu.

    - Složka projektu (*/Project*): nahraje soubory do projektu, kde k nim lze přihlédnout pomocí relativních cest v kódu. Nahrání souboru do této složky je stejné jako nahrávání souboru na řídicím panelu projektu. Soubor se uloží spolu s projektem a je dostupný v pozdějších relacích.

        Nahrávání se nepovede, pokud se pokusíte nahrát soubor se stejným názvem, který už v projektu existuje. Chcete-li přepsat soubor, nahrajte na řídicím panelu projektu nový soubor, který vám nabídne možnost přepsat.

1. Chcete-li dokončit proces, vyberte možnost **Spustit nahrávání** .

### <a name="create-or-import-files-using-commands"></a>Vytvoření nebo import souborů pomocí příkazů

Pomocí příkazů v terminálu nebo v rámci buňky kódu Pythonu můžete vytvářet soubory v rámci složky projektu i relace. Například příkazy jako `curl` a `wget` stahují soubory přímo z Internetu.

Chcete-li stáhnout soubory v terminálu, vyberte příkaz **terminálu** na řídicím panelu projektu a zadejte vhodné příkazy:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Při použití buňky kódu Pythonu v poznámkovém bloku použijte předponu příkazů `!` .

Složka projektu je výchozí složka, takže zadáním cílového názvu souboru, například *oil_price.csv* , vytvoří soubor v projektu. Chcete-li vytvořit soubor relace, předponu názvu nahraďte *.. jako v* *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Vytváření souborů v kódu

Při použití kódu, který vytváří soubor, jako je například `write_csv` funkce PANDAS, jsou cesty vždy relativní ke složce projektu. Pomocí *.. Vytvoří soubor* relace, který se zahodí při zastavení a zavření poznámkového bloku.

## <a name="export-files"></a>Exportovat soubory

Data můžete exportovat z řídicího panelu projektu nebo v rámci poznámkového bloku.

## <a name="export-files-from-the-project-dashboard"></a>Exportovat soubory z řídicího panelu projekt

Na řídicím panelu Projekt klikněte pravým tlačítkem na soubor a vyberte **Stáhnout**:

![Příkaz ke stažení v kontextové nabídce položky projektu](media/download-command.png)

Můžete také vybrat soubor a použít příkaz **ke stažení** (Klávesová zkratka: d) na řídicím panelu:

![Stáhnout příkaz panelu nástrojů na řídicím panelu projekt](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Export souborů z nabídky data v poznámkovém bloku

1. Vyberte **File**  >  příkaz nabídky**Stáhnout** soubor:

    ![Příkaz nabídky pro stažení dat v rámci poznámkového bloku](media/file-menu-download.png)

1. Zobrazí se automaticky otevírané okno, které zobrazuje složky v relaci. Složka *projektu* obsahuje soubory projektu:

    ![Místní nabídka příkazu ke stažení dat, ve kterém vyberete soubory a složky](media/file-menu-download-popup.png)

1. Zaškrtněte políčka nalevo od souborů a složek, které chcete stáhnout, a pak vyberte **Stáhnout vybrané**.

1. Poznámkový blok připraví jeden soubor *. zip* obsahující vybrané soubory, které pak uložíte jako normálně z prohlížeče. Poznámkový blok vytvoří soubor *. zip* i v případě, že stahujete jeden soubor.

## <a name="next-steps"></a>Další kroky

- [Přístup k datům v cloudu v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
