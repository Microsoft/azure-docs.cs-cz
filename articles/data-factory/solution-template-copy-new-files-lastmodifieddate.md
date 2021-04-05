---
title: Kopírování nových a změněných souborů pomocí LastModifiedDate
description: Naučte se používat šablonu řešení ke kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 94d09dc96d017dba3b16cc5fe113272a9393741d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362076"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování nových a změněných souborů pouze pomocí LastModifiedDate z úložiště založeného na souborech do cílového úložiště. 

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona nejprve vybere nové a změněné soubory pouze pomocí atributů **LastModifiedDate** a pak zkopíruje tyto vybrané soubory z úložiště zdrojů dat do úložiště cíle dat.

Šablona obsahuje jednu aktivitu:
- **Kopírováním** můžete kopírovat nové a změněné soubory pouze pomocí LastModifiedDate z úložiště souborů do cílového úložiště.

Šablona definuje šest parametrů:
-  *FolderPath_Source* je cesta ke složce, kde můžete číst soubory ze zdrojového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *Directory_Source* je cesta k podsložce, kde můžete číst soubory ze zdrojového úložiště. Výchozí hodnota musí být nahrazena vlastní cestou podsložky.
-  *FolderPath_Destination* je cesta ke složce, kam chcete zkopírovat soubory do cílového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *Directory_Destination* je cesta k podsložce, kam chcete zkopírovat soubory do cílového úložiště. Výchozí hodnota musí být nahrazena vlastní cestou podsložky.
-  *LastModified_From* slouží k výběru souborů, jejichž atribut LastModifiedDate je po nebo roven této hodnotě DateTime.  Pokud chcete vybrat jenom nové soubory, které se nezkopírovaly jako poslední, tato hodnota DateTime může být čas, kdy se kanál aktivoval při posledním spuštění kanálu. Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávaným LastModifiedDate v časovém pásmu UTC. 
-  *LastModified_To* slouží k výběru souborů, jejichž atribut LastModifiedDate je před touto hodnotou DateTime. Aby bylo možné vybrat pouze nové soubory, které nebyly poslední zkopírovány, může být tato hodnota DateTime aktuální čas.  Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávaným LastModifiedDate v časovém pásmu UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Přejít na šablonu **kopírování nových souborů pouze pomocí LastModifiedDate**. Vytvořte **nové** připojení ke zdrojovému úložišti úložiště. Zdrojové úložiště úložiště, ze kterého chcete kopírovat soubory.

    ![Vytvoří nové připojení ke zdroji.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Vytvořte **nové** připojení k cílovému úložišti. Cílovým úložištěm je místo, kam chcete zkopírovat soubory. 

    ![Vytvoří nové připojení k cíli.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Na panelu se zobrazí dostupný kanál, jak je znázorněno v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Vyberte **ladit**, zadejte hodnotu **parametrů** a vyberte **Dokončit**.  Na obrázku níže nastavíme parametry následujícím způsobem.
   - **FolderPath_Source** = SourceFolder
   - **Directory_Source** = podsložka
   - **FolderPath_Destination** = DestinationFolder
   - **Directory_Destination** = podsložka
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Příklad ukazuje, že soubory, které byly naposledy změněny v rámci TimeSpan (**2019-02-01T00:00:00Z** až **2019-03-01T00:00:00Z**), budou zkopírovány ze zdrojové cesty **SourceFolder nebo podsložky** do cílové cesty **DestinationFolder/podsložky**.  Můžete je nahradit vlastními parametry.

    ![Spuštění kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Prohlédněte si výsledek. Zobrazí se pouze soubory poslední změny v nakonfigurovaném časovém rozmezí byly zkopírovány do cílového úložiště.

    ![Kontrola výsledku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nyní můžete přidat bubnovou aktivační událost Windows pro automatizaci tohoto kanálu, aby kanál vždy mohl pravidelně kopírovat nové a změněné soubory pouze pomocí LastModifiedDate.  Vyberte **Přidat aktivační událost** a vyberte **Nová/upravit**.

    ![Snímek obrazovky, který zvýrazní možnost nabídky nová/upravit, která se zobrazí, když vyberete Přidat Trigger.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. V okně **Přidat aktivační události** vyberte **+ Nový**.

9. Vyberte **okno bubnu** pro typ triggeru, nastavte **každých 15 minut** jako opakování (můžete změnit na libovolný časový interval). Zaškrtněte políčko **Ano** pro aktivovanou a potom vyberte **OK**.

    ![Vytvoření triggeru](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Nastavte hodnotu pro **parametry spuštění aktivační události** následujícím způsobem a vyberte **Dokončit**.
    - **FolderPath_Source**  =  **SourceFolder**.  Můžete nahradit složkou ve zdrojovém úložišti dat.
    - **Directory_Source**  =  **podsložky**.  Můžete nahradit podsložku ve zdrojovém úložišti dat.
    - **FolderPath_Destination**  =  **DestinationFolder**.  Můžete nahradit složkou v cílovém úložišti dat.
    - **Directory_Destination**  =  **podsložky**.  Můžete nahradit podsložku v cílovém úložišti dat.
    - **LastModified_From**  =   **\@ Trigger (). Outputs. windowStartTime**.  Jedná se o systémovou proměnnou od triggeru, která určuje čas, kdy se kanál aktivoval při posledním spuštění.
    - **LastModified_To**  =  **\@ Trigger (). Outputs. windowEndTime**.  Jedná se o systémovou proměnnou z triggeru určující čas, kdy se kanál aktivuje.
    
    ![Vstupní parametry](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Vyberte **Publikovat vše**.
    
    ![Publikovat vše](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Vytvořte nové soubory ve zdrojové složce úložiště zdrojů dat.  Nyní čekáte na automatické spuštění kanálu a do cílového úložiště se zkopírují jenom nové soubory.

13. V levém navigačním panelu vyberte kartu **monitorování** a počkejte asi 15 minut, pokud se opakování triggeru nastavilo na každých 15 minut. 

14. Prohlédněte si výsledek. Váš kanál se zobrazí automaticky každých 15 minut a do cílového úložiště v každém spuštění kanálu se zkopírují jenom nové nebo změněné soubory ze zdrojového úložiště.

    ![Snímek obrazovky, který zobrazuje výsledky, které se vrátí při aktivaci kanálu.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Data Factory](introduction.md)
