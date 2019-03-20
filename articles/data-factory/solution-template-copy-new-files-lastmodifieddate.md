---
title: Kopírovat nové a změněné soubory podle LastModifiedDate s Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat soubory nové a změněné podle LastModifiedDate s Azure Data Factory pomocí šablony řešení.
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
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111935"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopírovat nové a změněné soubory podle LastModifiedDate s Azure Data Factory

Tento článek popisuje, šablona řešení, který vám pomůže zkopírovat nové a změněné soubory pouze podle LastModifiedDate z úložiště založené na souborech do cílového úložiště. 

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona nových a změněných souborů nejprve vybere pouze podle jejich atributy **LastModifiedDate**a pak zkopíruje tyto vybrané soubory ze zdrojového úložiště dat do cílového úložiště dat.

Šablona obsahuje jednu aktivitu:
- **Kopírování** ke zkopírování nové a změněné soubory pouze podle LastModifiedDate z úložiště souborů do cílového úložiště.

Šablona definuje čtyři parametry:
-  *FolderPath_Source* je cesta ke složce, kde lze číst soubory ze zdrojového úložiště. Je třeba nahradit výchozí hodnotu s vlastním cesta ke složce.
-  *FolderPath_Destination* je cesta ke složce, ve které chcete kopírovat soubory do cílového úložiště. Je třeba nahradit výchozí hodnotu s vlastním cesta ke složce.
-  *LastModified_From* se používá a vyberte soubory, jejichž atribut LastModifiedDate je po nebo rovna této hodnotě data a času.  Abyste mohli vybrat nové soubory, které nebyl zkopírován čas poslední, může být tato hodnota datetime kanál byl při aktivaci čas poslední čas. Můžete nahradit výchozí hodnotu "2019-02-01T00:00:00Z' na vaše očekávané LastModifiedDate v časovém pásmu UTC. 
-  *LastModified_To* slouží k výběru souborů, jejichž atribut LastModifiedDate je před tuto hodnotu data a času. Abyste mohli vybrat nové soubory, které nebyl zkopírován čas posledního, tato hodnota data a času může být aktuální čas.  Můžete nahradit výchozí hodnotu "2019-02-01T00:00:00Z' na vaše očekávané LastModifiedDate v časovém pásmu UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Přejděte do šablony **kopírovat nové soubory pouze pomocí LastModifiedDate**. Vytvoření **nový** připojení k úložišti zdrojového úložiště. Zdrojové úložiště úložiště je, ve které chcete kopírovat soubory z.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Nejprve vyberte úložiště **typ**. Po vstupu, který úložiště **název účtu** a **klíč účtu**. Nakonec vyberte **Dokončit**.

    ![Zadejte připojovací řetězec](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Vytvoření **nový** připojení do cílového úložiště. Cílové úložiště je, kde chcete zkopírovat soubory. Je také nutné zadat informace o připojení z cílového úložiště dat, podobně jako protože jste v kroku 2.

    ![Vytvořit nové připojení k cíli](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Vyberte **pomocí této šablony**.

    ![Použít tuto šablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. K dispozici v panelu, kanál se zobrazí, jak je znázorněno v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Vyberte **ladění**, zapsat hodnotu pro **parametry** a vyberte **Dokončit**.  Na obrázku nastavíme jako následující parametry.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     V příkladu je určující soubory, které byly naposledy změněny v rámci časový interval mezi *2019-02-01T00:00:00Z* a *2019-03-01T00:00:00Z* se zkopírují ze složky */source/*  do složky */destination/*.  Můžete nahradit tyto vlastními parametry.
    
     ![Spuštění kanálu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Zkontrolujte výsledky. Zobrazí se jenom soubory naposledy upraveno v rámci nakonfigurované časový interval byla zkopírována do cílového úložiště.

    ![Zkontrolujte výsledky](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Přeskakující windows pro automatizaci tohoto kanálu, teď můžete přidat tak, aby kanál můžete vždy kopírovat nové a změněné soubory pouze podle LastModifiedDate pravidelně.  Vyberte **aktivační událost přidat**a vyberte **nová/upravit**.

    ![Zkontrolujte výsledky](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. V **přidat aktivační události** okně **+ nová**.

    ![Zkontrolujte výsledky](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Vyberte **Přeskakujícího okna** u typu triggeru nastavit **každých 15 minut** jako opakování (můžete změnit na libovolné časový interval) a pak vyberte **Další**.

    ![Vytvoření aktivační události](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Hodnota pro zápis **parametry spuštění aktivační události** následující a vyberte **Dokončit**.
    - **FolderPath_Source** = **/source/**.  Můžete nahradit složky v úložišti datového zdroje.
    - **FolderPath_Destination** = **/destination/**.  Můžete nahradit složky v cílovém úložišti.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  Je systémová proměnná z triggeru určení času kanálu byl při aktivaci naposledy.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  Je systémová proměnná z triggeru určení času kanálu při aktivaci této doby.
    
    ![Vstupní parametry](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Vyberte **Publikovat vše**.
    
    ![Publikovat vše](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Vytváření nových souborů ve složce zdrojové úložiště zdroje dat.  Jsou nyní čekání na kanál tak, aby se automaticky spustí a pouze nové soubory budou zkopírovány do cílového úložiště.

14. Vyberte **monitorování** kartu v levém navigačním panelu a počkejte asi 15 minut, pokud byla nastavena opakování triggeru každých 15 minut. 

    ![Vyberte monitorování](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Zkontrolujte výsledky. Zobrazí se váš kanál se budou aktivovat automaticky každých 15 minut a ze zdrojového úložiště jenom nové nebo změněné soubory budou zkopírovány do cílového úložiště v každé spuštění kanálu.

    ![Zkontrolujte výsledky](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)