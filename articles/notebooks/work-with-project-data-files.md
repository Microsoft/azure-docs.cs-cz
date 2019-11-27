---
title: Import a export dat s projekty s poznámkovými bloky Azure
description: Jak přenést data do projektu aplikace poznámkových bloků Azure z externích zdrojů a jak exportovat data z projektu.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277388"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Práce s datovými soubory v projektech poznámkového bloku v Azure

Data jsou lifeblood mnoho poznámkové bloky Jupyter, zejména poznámkových bloků se používá pro datové vědy. Pomocí poznámkových bloků Azure můžete snadno importovat z mnoha zdrojů do projektu a pak použijte tato data z poznámkových bloků. Je také možné poznámkových bloků, které generují data, která je uložena v projektu, kterou si pak můžete stáhnout pro použití na jiném místě.

Nabídka **data** v běžícím poznámkovém bloku taky nabízí příkazy pro **nahrávání** a **stahování** , které pracují se soubory v projektu, i dočasné soubory pro aktuální relaci poznámkového bloku.

Kód v rámci poznámkového bloku můžete také použít pro přístup k řadě zdrojů dat. přímo, včetně souborů v rámci projektu. Můžete také přistupovat libovolná data pomocí příkazů do buňky kódu. Protože tato data jsou uložena v proměnné v rámci relace Poznámkový blok, nebude být uložena v projektu, pokud nechcete použít kód a konkrétně Vygenerujte soubory projektu.

Práce s kódem v datech se nejlépe používá v samotném běžícím poznámkovém bloku: pro účely tohoto účelu si přečtěte [téma Jak získat data v Azure Notebooks ukázkovém poznámkovém bloku](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Zbývající část tohoto článku poskytuje podrobné informace o operací se soubory projektu.

## <a name="import-data"></a>Import dat

Soubory můžete přenést do projektu z řídicího panelu projekt nebo do běžícího poznámkového bloku pomocí nabídky **data** nebo příkazu, jako je například `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Import souborů z řídicího panelu Projekt

1. V projektu přejděte do složky, ve které chcete importovat soubory.

1. Vyberte příkaz **Odeslat** a potom buď **z adresy URL** , nebo **z počítače** a projektu potřebné podrobnosti pro data, která chcete importovat:

   - **Z adresy URL**: Zadejte zdrojovou adresu do pole **Adresa URL souboru** a název souboru, který chcete přiřadit k poznámkovému bloku v projektu v poli **název souboru** . Pak vyberte **+ Přidat soubor** a přidejte tak adresu URL do seznamu nahrávání. Opakujte tento postup pro všechny další adresy URL a potom vyberte **Hotovo**.

     ![Nahrát z adresy URL automaticky otevíraného okna](media/quickstarts/upload-from-url-popup.png)

   - **Z počítače**: Přetáhněte soubory do místní nabídky nebo vyberte **zvolit soubory**, vyhledejte a vyberte datové soubory, které chcete importovat. Můžete vyřadit nebo zvolit libovolný počet souborů, z libovolného typu a formátu, protože je na kódu v poznámkovém bloku otevřete soubor a analyzovat svá data.

     ![Nahrát z počítače automaticky otevíraného okna](media/quickstarts/upload-from-computer-popup.png)

1. Po importu, soubory se zobrazí na řídicím panelu Projekt a je přístupná v rámci poznámkového bloku kódu pomocí relativní cesty ke složce obsahující.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Import souborů z nabídky soubor v poznámkovém bloku

1. V běžícím poznámkovém bloku vyberte **soubor** > **Odeslat** příkaz:

    ![Soubor odeslání příkazu nabídky v rámci poznámkového bloku](media/file-menu-upload.png)

1. V dialogovém okně, které se otevře přejděte a vyberte soubory, které chcete nahrát. Můžete vybrat libovolný počet souborů všech typů. Po dokončení vyberte **otevřít** .

1. V místní nabídce **stav nahrávání** , která se zobrazí, vyberte v rozevíracím seznamu **cílovou složku** :

    - Složka relace ( *~/* ): nahraje soubory do aktuální relace poznámkového bloku, ale v projektu nevytvoří soubory. Složka relace je partnerské zařízení do složky projektu, ale nebude zachována po ukončení relace. Chcete-li získat přístup k souborům relace v kódu, použijte předponu názvů souborů s relativní cestou *.. /* .

        Pomocí složky relace je užitečné pro experimentování ve službě a nebudou zbytečně zabírat projektu se soubory může nebo nemusí potřebovat dlouhodobě se vyhnete. Mohou také nahrávat soubory do složky relace, která mají stejné názvy souborů v projektu, aniž by vznikly konflikty a bez nutnosti přejmenujte soubory. Řekněme například, že máte v projektu jednu verzi souboru *data. csv* , ale chcete experimentovat s jinou verzí souboru *data. csv*. Nahráním souboru do složky relace můžete spustit Poznámkový blok pomocí dat v nahraném souboru (odkazující na něj v kódu pomocí *.. /data.csv*) a ne data v souboru projektu.

    - Složka projektu ( */Project*): nahraje soubory do projektu, kde k nim lze přihlédnout pomocí relativních cest v kódu. Po nahrání souboru do této složky je stejný jako nahrání souboru na řídicí panel Projekt. Soubor je uložen s projektem a je k dispozici v novějším relace.

        Nahrávání se nezdaří při pokusu nahrát soubor se stejným názvem jako ten, který již v projektu existuje. K přepsání souboru, nahrajte nový soubor z řídicího panelu Projekt místo toho, která nabízí možnost přepsat.

1. Chcete-li dokončit proces, vyberte možnost **Spustit nahrávání** .

### <a name="create-or-import-files-using-commands"></a>Vytvoření nebo import souborů pomocí příkazů

Příkazy, v terminálu nebo v rámci buňky kódu Python slouží k vytvoření souborů v rámci složky projektu a relace. Například příkazy jako `curl` a `wget` stahovat soubory přímo z Internetu.

Chcete-li stáhnout soubory v terminálu, vyberte příkaz **terminálu** na řídicím panelu projektu a zadejte vhodné příkazy:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Při použití buňky kódu Pythonu v poznámkovém bloku použijte předponu příkazů `!`.

Složka projektu je výchozí složka, takže zadáním názvu cílového souboru jako *oil_price. csv* se vytvoří soubor v projektu. Chcete-li vytvořit soubor relace, předponu názvu nahraďte *.. jako v* *.. /oil_price. csv*.

### <a name="create-files-in-code"></a>Vytvoření souborů v kódu

Při použití kódu, který vytváří soubor, jako je například funkce PANDAS `write_csv`, jsou cesty vždy relativní ke složce projektu. Pomocí *.. Vytvoří soubor* relace, který se zahodí při zastavení a zavření poznámkového bloku.

## <a name="export-files"></a>Exportovat soubory

Data můžete exportovat z řídicího panelu Projekt nebo z poznámkového bloku.

## <a name="export-files-from-the-project-dashboard"></a>Exportovat soubory z řídicího panelu Projekt

Na řídicím panelu Projekt klikněte pravým tlačítkem na soubor a vyberte **Stáhnout**:

![Stáhněte si příkazu v kontextové nabídce položky projektu](media/download-command.png)

Můžete také vybrat soubor a použít příkaz **ke stažení** (Klávesová zkratka: d) na řídicím panelu:

![Stáhněte si příkazu panelu nástrojů na řídicím panelu Projekt](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportovat soubory v nabídce Data v poznámkovém bloku

1. Vyberte **soubor** > příkaz nabídky **Stáhnout** :

    ![Příkaz nabídky stahování dat v rámci poznámkového bloku](media/file-menu-download.png)

1. Zobrazí se automaticky otevírané okno, které zobrazuje složky v relaci. Složka *projektu* obsahuje soubory projektu:

    ![Data ke stažení příkaz místní nabídky ve kterém můžete vybrat soubory a složky](media/file-menu-download-popup.png)

1. Zaškrtněte políčka nalevo od souborů a složek, které chcete stáhnout, a pak vyberte **Stáhnout vybrané**.

1. Poznámkový blok připraví jeden soubor *. zip* obsahující vybrané soubory, které pak uložíte jako normálně z prohlížeče. Poznámkový blok vytvoří soubor *. zip* i v případě, že stahujete jeden soubor.

## <a name="next-steps"></a>Další kroky

- [Přístup k datům v cloudu v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
