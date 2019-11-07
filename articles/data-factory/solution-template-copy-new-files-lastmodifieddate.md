---
title: Kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory
description: Naučte se používat šablonu řešení ke kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: aaa7114113d5f0330d2dc7d656b0d91963931512
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684231"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování nových a změněných souborů pouze pomocí LastModifiedDate z úložiště založeného na souborech do cílového úložiště. 

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona nejprve vybere nové a změněné soubory pouze pomocí atributů **LastModifiedDate**a pak zkopíruje tyto vybrané soubory z úložiště zdrojů dat do úložiště cíle dat.

Šablona obsahuje jednu aktivitu:
- **Kopírováním** můžete kopírovat nové a změněné soubory pouze pomocí LastModifiedDate z úložiště souborů do cílového úložiště.

Šablona definuje čtyři parametry:
-  *FolderPath_Source* je cesta ke složce, kde můžete číst soubory ze zdrojového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *FolderPath_Destination* je cesta ke složce, kam chcete zkopírovat soubory do cílového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *LastModified_From* se používá k výběru souborů, jejichž atribut LastModifiedDate je po nebo roven této hodnotě DateTime.  Pokud chcete vybrat jenom nové soubory, které se nezkopírovaly jako poslední, tato hodnota DateTime může být čas, kdy se kanál aktivoval při posledním spuštění kanálu. Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávaným LastModifiedDate v časovém pásmu UTC. 
-  *LastModified_To* se používá k výběru souborů, jejichž atribut LastModifiedDate je před touto hodnotou DateTime. Aby bylo možné vybrat pouze nové soubory, které nebyly poslední zkopírovány, může být tato hodnota DateTime aktuální čas.  Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávaným LastModifiedDate v časovém pásmu UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Přejít na šablonu **kopírování nových souborů pouze pomocí LastModifiedDate**. Vytvořte **nové** připojení ke zdrojovému úložišti úložiště. Zdrojové úložiště úložiště, ze kterého chcete kopírovat soubory.

    ![Vytvoří nové připojení ke zdroji.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Nejdřív vyberte **typ**úložiště. Poté zadejte **název účtu** úložiště a **klíč účtu**. Nakonec vyberte **Dokončit**.

    ![Zadání připojovacího řetězce](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Vytvořte **nové** připojení k cílovému úložišti. Cílovým úložištěm je místo, kam chcete zkopírovat soubory. Je také potřeba zadat informace o připojení úložiště cíle dat podobně jako v kroku 2.

    ![Vytvoří nové připojení k cíli.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Vyberte **použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Na panelu se zobrazí dostupný kanál, jak je znázorněno v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Vyberte **ladit**, zadejte hodnotu **parametrů** a vyberte **Dokončit**.  Na obrázku níže nastavíme parametry následujícím způsobem.
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/Destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     Příklad ukazuje soubory, které byly naposledy změněny v rámci TimeSpan mezi *2019-02-01T00:00:00Z* a *2019-03-01T00:00:00Z* budou zkopírovány ze složky */source/* do složky */Destination/* .  Můžete je nahradit vlastními parametry.
    
     ![Spuštění kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Zkontrolujte výsledek. Zobrazí se pouze soubory poslední změny v nakonfigurovaném časovém rozmezí byly zkopírovány do cílového úložiště.

    ![Kontrola výsledku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Nyní můžete přidat bubnovou aktivační událost Windows pro automatizaci tohoto kanálu, aby kanál vždy mohl pravidelně kopírovat nové a změněné soubory pouze pomocí LastModifiedDate.  Vyberte **Přidat aktivační událost**a vyberte **Nová/upravit**.

    ![Kontrola výsledku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. V okně **Přidat aktivační události** vyberte **+ Nový**.

    ![Kontrola výsledku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Vyberte **okno bubnu** pro typ triggeru, nastavte **každých 15 minut** jako opakování (můžete změnit na libovolný čas intervalu) a pak vybrat **Další**.

    ![Vytvořit aktivační událost](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Zadejte hodnotu pro **parametry spuštění aktivační události** následujícím způsobem a vyberte **Dokončit**.
    - **FolderPath_Source** =  **/source/** .  Můžete nahradit složkou ve zdrojovém úložišti dat.
    - **FolderPath_Destination** =  **/Destination/** .  Můžete nahradit složkou v cílovém úložišti dat.
    - **LastModified_From** =   **\@Trigger (). Outputs. windowStartTime**.  Jedná se o systémovou proměnnou od triggeru, která určuje čas, kdy se kanál aktivoval při posledním spuštění.
    - **LastModified_To** =  **\@Trigger (). Outputs. windowEndTime**.  Jedná se o systémovou proměnnou z triggeru určující čas, kdy se kanál aktivuje.
    
    ![Vstupní parametry](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Vyberte **Publikovat vše**.
    
    ![Publikovat vše](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Vytvořte nové soubory ve zdrojové složce úložiště zdrojů dat.  Nyní čekáte na automatické spuštění kanálu a do cílového úložiště se zkopírují jenom nové soubory.

14. V levém navigačním panelu vyberte kartu **monitorování** a počkejte asi 15 minut, pokud se opakování triggeru nastavilo na každých 15 minut. 

    ![Vybrat monitorování](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Zkontrolujte výsledek. Váš kanál se zobrazí automaticky každých 15 minut a do cílového úložiště v každém spuštění kanálu se zkopírují jenom nové nebo změněné soubory ze zdrojového úložiště.

    ![Kontrola výsledku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
