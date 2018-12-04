---
title: Import a export dat s projekty s poznámkovými bloky Azure | Dokumentace Microsoftu
description: Jak přenést data do projektu aplikace poznámkových bloků Azure z externích zdrojů a jak exportovat data z projektu.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 84b238ee287d6b47421788d14235abceb03fafa7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855747"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Práce s datovými soubory v projektech poznámkového bloku v Azure

Data jsou lifeblood mnoho poznámkové bloky Jupyter, zejména poznámkových bloků se používá pro datové vědy. Pomocí poznámkových bloků Azure můžete snadno importovat z mnoha zdrojů do projektu a pak použijte tato data z poznámkových bloků. Je také možné poznámkových bloků, které generují data, která je uložena v projektu, kterou si pak můžete stáhnout pro použití na jiném místě.

**Data** nabídky v rámci poznámkového bloku spuštěné také poskytuje **nahrát** a **Stáhnout** příkazy, které pracují se soubory v projektu, jakož i dočasné soubory pro aktuální relace poznámkového bloku.

Kód v rámci poznámkového bloku můžete také použít pro přístup k řadě zdrojů dat. přímo, včetně souborů v rámci projektu. Můžete také přistupovat libovolná data pomocí příkazů do buňky kódu. Protože tato data jsou uložena v proměnné v rámci relace Poznámkový blok, nebude být uložena v projektu, pokud nechcete použít kód a konkrétně Vygenerujte soubory projektu.

Práce s kódem v datech je nejpohodlnější v rámci spuštěné Poznámkový blok, samotný: pro tento účel, najdete [získávání ke svým datům v poznámkovém bloku ukázkové poznámkové bloky Azure](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Zbývající část tohoto článku poskytuje podrobné informace o operací se soubory projektu.

## <a name="import-data"></a>Import dat

Soubory můžete zahrnout do projektu z řídicího panelu Projekt, nebo v rámci spuštění poznámkového bloku pomocí **Data** nabídky nebo příkazy jako `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Import souborů z řídicího panelu Projekt

1. V projektu přejděte do složky, ve které chcete importovat soubory.

1. Vyberte **nahrát** příkaz a pak buď **z adresy URL** nebo **z počítače** a nezbytné podrobnosti o, pro kterou chcete importovat data projektu:

    - **Z adresy URL**: Zadejte adresu zdroje **adresa URL souboru** pole a název souboru přiřadit do poznámkového bloku v projektu v **název_souboru** pole. Potom vyberte **+ přidat soubor** přidejte adresu URL do seznamu nahrávání. Postup opakujte pro všechny další adresy URL a pak vyberte **provádí**.

    ![Nahrát z adresy URL automaticky otevíraného okna](media/quickstarts/upload-from-url-popup.png)

    - **Z počítače**: přetáhněte soubory do automaticky otevíraného okna nebo vyberte **vybrat soubory**, vyhledejte a vyberte datové soubory, které chcete importovat. Můžete vyřadit nebo zvolit libovolný počet souborů, z libovolného typu a formátu, protože je na kódu v poznámkovém bloku otevřete soubor a analyzovat svá data.

    ![Nahrát z počítače automaticky otevíraného okna](media/quickstarts/upload-from-computer-popup.png)

1. Po importu, soubory se zobrazí na řídicím panelu Projekt a je přístupná v rámci poznámkového bloku kódu pomocí relativní cesty ke složce obsahující.

### <a name="import-files-from-the-data-menu-in-a-notebook"></a>Import souborů z dat nabídky v poznámkovém bloku

1. V rámci spuštěné Poznámkový blok, vyberte **souboru** > **nahrát** příkaz:

    ![Soubor odeslání příkazu nabídky v rámci poznámkového bloku](media/file-menu-upload.png)

1. V dialogovém okně, které se otevře přejděte a vyberte soubory, které chcete nahrát. Můžete vybrat libovolný počet souborů všech typů. Vyberte **otevřít** až budete hotovi.

1. V **stav nahrávání** automaticky otevírané okno, které se zobrazí, vyberte **cílovou složku** z rozevíracího seznamu:

    - Relace složky (*~/* ): nahrání souborů do aktuální relace Poznámkový blok, ale nevytváří soubory v projektu. Složka relace je partnerské zařízení do složky projektu, ale nebude zachována po ukončení relace. Pro přístup k souborům relace v kódu, předpona názvy souborů s relativní cestou *... /*.

        Pomocí složky relace je užitečné pro experimentování ve službě a nebudou zbytečně zabírat projektu se soubory může nebo nemusí potřebovat dlouhodobě se vyhnete. Mohou také nahrávat soubory do složky relace, která mají stejné názvy souborů v projektu, aniž by vznikly konflikty a bez nutnosti přejmenujte soubory. Například Řekněme, že máme jednu verzi *data.csv* v projektu již, ale chcete experimentovat s jinou verzi *data.csv*. Tím, že nahrajete soubor do složky relace, můžete spustit Poznámkový blok, který používá data v nahraného souboru (na ni následně odkazuje v kódu pomocí *... /data.csv*) namísto data v souboru projektu.

    - Složky projektu (*/project*): nahrání souborů do projektu, kde je lze přistupovat pomocí relativní cesty v kódu. Po nahrání souboru do této složky je stejný jako nahrání souboru na řídicí panel Projekt. Soubor je uložen s projektem a je k dispozici v novějším relace.

        Nahrávání se nezdaří při pokusu nahrát soubor se stejným názvem jako ten, který již v projektu existuje. K přepsání souboru, nahrajte nový soubor z řídicího panelu Projekt místo toho, která nabízí možnost přepsat.

1. Vyberte **spustit nahrávání** proces dokončete.

### <a name="create-or-import-files-using-commands"></a>Vytvoření nebo import souborů pomocí příkazů

Příkazy, v terminálu nebo v rámci buňky kódu Python slouží k vytvoření souborů v rámci složky projektu a relace. Například příkazy jako `curl` a `wget` přímo stahovat soubory z Internetu.

Ke stažení souborů, v terminálu, vyberte **terminálu** příkazu na řídicím panelu Projekt, a pak zadejte vhodný příkazy:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Při použití buňky v poznámkovém bloku kódu Python, předpona příkazy `!`.

Složka projektu je výchozí složce, takže zadáním názvu cílového souboru jako *oil_price.csv* vytvoří soubor v projektu. Chcete-li vytvořit soubor relace, zadejte před název *... /* stejně jako v *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Vytvoření souborů v kódu

Při použití kódu, který vytvoří soubor, jako je například pandas `write_csv` funkce, cesty jsou vždy relativní ke složce projektu. Pomocí *... /* vytvoří relaci soubor, který při zastavení a zavření poznámkového bloku.

## <a name="export-files"></a>Exportovat soubory

Data můžete exportovat z řídicího panelu Projekt nebo z poznámkového bloku.

## <a name="export-files-from-the-project-dashboard"></a>Exportovat soubory z řídicího panelu Projekt

Na řídicím panelu projektu klikněte pravým tlačítkem na soubor a vyberte **Stáhnout**:

![Stáhněte si příkazu v kontextové nabídce položky projektu](media/download-command.png)

Můžete také vybrat soubor a použít **Stáhnout** příkazu (Klávesová zkratka: d) na řídicím panelu:

![Stáhněte si příkazu panelu nástrojů na řídicím panelu Projekt](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportovat soubory v nabídce Data v poznámkovém bloku

1. Vyberte **souboru** > **Stáhnout** příkazu nabídky:

    ![Příkaz nabídky stahování dat v rámci poznámkového bloku](media/file-menu-download.png)

1. Automaticky otevíraného okna se zobrazí, který zobrazuje složky v relaci. *projektu* složka obsahuje soubory projektu:

    ![Data ke stažení příkaz místní nabídky ve kterém můžete vybrat soubory a složky](media/file-menu-download-popup.png)

1. Zaškrtněte políčka nalevo souborů a složek, kterou chcete stáhnout, potom vyberte **stáhnout vybrané**.

1. Poznámkový blok připraví jeden *ZIP* soubor, který obsahuje vybrané soubory, které pak uložíte jako budete obvykle provádět z prohlížeče. Vytvoří poznámkový blok *ZIP* soubor i v případě, že stahujete jediný soubor.

## <a name="next-steps"></a>Další postup

- [Přístup k datům cloudu v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
