---
title: Kopírovat nové a změněné soubory podle LastModifiedDate
description: Zjistěte, jak pomocí šablony řešení kopírovat nové a změněné soubory podle LastModifiedDate s Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942016"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopírování nových a změněných souborů podle LastModifiedDate pomocí Azure Data Factory

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování nových a změněných souborů pouze podle LastModifiedDate z úložiště založeného na souborech do cílového úložiště. 

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona nejprve vybere nové a změněné soubory pouze podle jejich atributů **LastModifiedDate**a potom zkopíruje vybrané soubory z úložiště zdrojů dat do cílového úložiště dat.

Šablona obsahuje jednu aktivitu:
- **Zkopírujte** zkopírovat nové a změněné soubory pouze lastmodifieddate z úložiště souborů do cílového úložiště.

Šablona definuje šest parametrů:
-  *FolderPath_Source* je cesta ke složce, kde můžete číst soubory ze zdrojového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *Directory_Source* je cesta podsložky, kde můžete číst soubory ze zdrojového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou podsložky.
-  *FolderPath_Destination* je cesta ke složce, kam chcete kopírovat soubory do cílového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou ke složce.
-  *Directory_Destination* je cesta podsložky, kde chcete kopírovat soubory do cílového úložiště. Výchozí hodnotu je třeba nahradit vlastní cestou podsložky.
-  *LastModified_From* se používá k výběru souborů, jejichž lastmodifieddate atribut je po nebo rovna této datetime hodnotu.  Chcete-li vybrat pouze nové soubory, které nebyly naposledy zkopírovány, může být tato hodnota datatime časem časem, kdy byl kanál naposledy spuštěn. Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávanou hodnotou LastModifiedDate v časovém pásmu UTC. 
-  *LastModified_To* se používá k výběru souborů, jejichž atribut LastModifiedDate je před touto hodnotou datetime. Chcete-li vybrat pouze nové soubory, které nebyly naposledy zkopírovány, může být tato hodnota datetime aktuálním časem.  Výchozí hodnotu 2019-02-01T00:00:00Z můžete nahradit očekávanou hodnotou LastModifiedDate v časovém pásmu UTC. 

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Přejít na šablonu **Kopírovat nové soubory pouze lastmodifieddate**. Vytvořte **nové** připojení k úložišti zdrojového úložiště. Úložiště zdrojového úložiště je místo, odkud chcete kopírovat soubory.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Vytvořte **nové** připojení k cílovému úložišti. Cílové úložiště je místo, kam chcete kopírovat soubory. 

    ![Vytvoření nového připojení k cíli](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. V panelu uvidíte kanál, který je k dispozici, jak je znázorněno v následujícím příkladu:

    ![Zobrazit potrubí](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Vyberte **Ladit**, napište hodnotu **parametrů** a vyberte **Dokončit**.  Na následujícím obrázku nastavíme následující parametry.
   - **FolderPath_Source** = zdrojová složka
   - **Directory_Source** = podsložka
   - **FolderPath_Destination** = cílová složka
   - **Directory_Destination** = podsložka
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    V příkladu je uvedeno, že soubory, které byly naposledy změněny v rámci časový montovna (**2019-02-01T00:00:00Z** na **2019-03-01T00:00:00Z**) budou zkopírovány ze zdrojové **cesty source sourcefolder/podfolder** do **cílové složky/podsložky**cílové cesty .  Můžete je nahradit vlastními parametry.

    ![Spuštění kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Zkontrolujte výsledek. Zobrazí se pouze soubory naposledy změněné v rámci nakonfigurovaného časovým rozpětí, které bylo zkopírováno do cílového úložiště.

    ![Zkontrolujte výsledek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nyní můžete přidat omílání windows aktivační událost automatizovat tento kanál, tak, aby kanál může vždy kopírovat nové a změněné soubory pouze lastmodifieddate pravidelně.  Vyberte **Přidat aktivační událost**a vyberte **Nový/Upravit**.

    ![Zkontrolujte výsledek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. V okně **Přidat aktivační události** vyberte + **Nový**.

9. Vyberte **omílání okno** pro typ aktivační události, nastavit **každých 15 minut** jako opakování (můžete změnit na libovolný čas intervalu). Vyberte **Ano** pro aktivované pole a pak vyberte **OK**.

    ![Vytvoření triggeru](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Nastavte hodnotu **parametrů spuštění aktivační události** takto a vyberte **možnost Dokončit**.
    - **FolderPath_Source** = **zdrojové složky**.  Složku můžete nahradit v úložišti zdrojových dat.
    - **Directory_Source** = **podsložky**.  V úložišti zdrojových dat můžete nahradit podsložku.
    - **FolderPath_Destination** = **cílovou složku**.  V cílovém úložišti dat můžete nahradit složku.
    - **Directory_Destination** = **podsložka**.  V cílovém úložišti dat můžete nahradit podsložku.
    - **LastModified_From** =  **LastModified_From\@trigger().outputs.windowStartTime**.  Jedná se o systémovou proměnnou z aktivační události určující čas, kdy byl kanál naposledy spuštěn.
    - **LastModified_To** = **LastModified_To\@trigger().outputs.windowEndTime**.  Jedná se o systémovou proměnnou z aktivační události určující čas, kdy je kanál aktivován tentokrát.
    
    ![Vstupní parametry](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Vyberte **Publikovat vše**.
    
    ![Publikovat vše](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Vytvořte nové soubory ve zdrojové složce úložiště zdrojů dat.  Nyní čekáte na automatické spuštění kanálu a do cílového úložiště budou zkopírovány pouze nové soubory.

13. Na levém navigačním panelu vyberte kartu **Sledování** a počkejte přibližně 15 minut, pokud je opakování aktivační události nastaveno na každých 15 minut. 

14. Zkontrolujte výsledek. Zobrazí se, že váš kanál se spustí automaticky každých 15 minut a pouze nové nebo změněné soubory ze zdrojového úložiště budou zkopírovány do cílového úložiště v každém spuštění kanálu.

    ![Zkontrolujte výsledek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
