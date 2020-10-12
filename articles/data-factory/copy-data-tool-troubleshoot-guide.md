---
title: Řešení potíží s nástrojem Kopírování dat v Azure Data Factory
description: Naučte se řešit problémy s Kopírování dat nástroji v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388372"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Řešení potíží s nástrojem Kopírování dat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro nástroj Kopírování dat v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Kód chyby: nejde ověřit v nástroji Kopírování dat.

- **Příznaky**: v prvním kroku nástroje kopírování dat Tool se zobrazí zpráva s upozorněním "nelze ověřit".
- **Příčiny**: k tomu může dojít, když jsou všechny soubory cookie třetích stran zakázané.
- **Řešení**: 
    - Použijte Internet Explorer nebo prohlížeč Microsoft Edge.
    - Pokud používáte prohlížeč Chrome, postupujte podle pokynů níže a přidejte výjimku soubory cookie pro *microsoftonline.com* a *Windows.NET*.
        1.  Otevřete prohlížeč Chrome.
        2.  Klikněte na klíče nebo tři čáry vpravo (přizpůsobení a řízení Google Chrome).
        3.  Klikněte na **Nastavení**.
        4.  Prohledejte **soubory cookie** nebo v části Upřesnit nastavení otevřete **ochranu osobních údajů** .
        5.  Vyberte **Nastavení obsahu**.    
        6.  Soubory cookie by měly být nastavené tak, aby **umožňovaly nastavení místních dat (doporučeno)**.
        7.  Klikněte na **Spravovat výjimky**. V části **vzor názvu hostitele** zadejte následující **a ujistěte se, že je nastavení** chování nastaveno.
            - login.microsoftonline.com
            - login.windows.net
        8.  Zavřete prohlížeč a znovu ho spusťte.
    - Pokud používáte prohlížeč Firefox, přidejte výjimku soubory cookie podle pokynů níže.
        1. V nabídce Firefox přejděte na **Tools**  >  **Možnosti**nástroje.
        2. V části Historie **ochrany osobních údajů**se  >  **History**může zobrazit, že aktuální nastavení **používá pro historii vlastní nastavení**.
        3. V části **přijmout soubory cookie třetích stran**nemusí být vaše aktuální nastavení **nikdy**, potom klikněte na **výjimky** na pravé straně pro přidání následujících webů.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Zavřete prohlížeč a znovu ho spusťte. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Kód chyby: nejde otevřít přihlašovací stránku a zadat heslo.

- **Příznaky**: kopírování dat Tool vás přesměruje na přihlašovací stránku, ale přihlašovací stránka se nezobrazí úspěšně.
- **Příčiny**: k tomuto problému může dojít, pokud jste změnili síťové prostředí ze sítě Office do domácí sítě. V prohlížečích jsou několik mezipamětí. 
- **Řešení**: 
    1.  Zavřete prohlížeč a zkuste to znovu. Pokud problém ještě existuje, pokračujte na další krok.   
    2.  Pokud používáte prohlížeč Internet Explorer, zkuste ho otevřít v privátním režimu (stiskněte klávesu CTRL + SHIFT "+" P "). Pokud používáte prohlížeč Chrome, zkuste ho otevřít v režimu anonymním (stiskněte kombinaci kláves CTRL + SHIFT + "N"). Pokud problém ještě existuje, pokračujte na další krok. 
    3.  Zkuste použít jiný prohlížeč. 


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce výkonem datových toků mapování ADF](concepts-data-flow-performance.md)
