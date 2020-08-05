---
title: Řešení potíží s Azure Data Factory UX
description: Naučte se řešit potíže s Azure Data Factorym UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567903"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Řešení potíží s Azure Data Factorym UX
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>Nenačítá se uživatelské prostředí ADF

> [!NOTE]
> Azure Data Factory UX oficiálně podporuje Microsoft Edge a Google Chrome. Používání jiných webových prohlížečů může vést k neočekávanému nebo nedokumentovanýmu chování.

### <a name="third-party-cookies-blocked"></a>Blokované soubory cookie třetích stran

UX v uživatelském rozhraní ADF používá soubory cookie prohlížeče k zachování uživatelské relace a umožňuje interaktivní vývojové a monitorovací prostředí. Je možné, že prohlížeč blokuje soubory cookie třetích stran, protože používáte relaci anonymním nebo máte povolený blokování služby Active Directory. Blokování souborů cookie třetích stran může při načítání portálu způsobit problémy, jako je například přesměrování na prázdnou stránku, https://adf.azure.com/accesstoken.html nebo získání varovné zprávy s informací o tom, že soubory cookie třetích stran jsou zablokované. Pokud chcete tento problém vyřešit, povolte v prohlížeči možnosti souborů cookie třetích stran pomocí následujících kroků:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Povolení všech souborů cookie

1. Navštivte **Chrome://Settings/cookies** v prohlížeči.
1. Vyberte možnost **povolení všech souborů cookie** ![ Chrome-Allow-all-cookies.](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Umožněte použití souborů cookie jenom v uživatelském prostředí ADF.
Pokud nechcete povolit všechny soubory cookie, můžete volitelně povolit jenom uživatelské prostředí ADF:
1. Navštivte **Chrome://Settings/cookies**.
1. Klikněte na **Přidat** v části **weby, které můžou vždycky používat soubory cookie** . možnost ![ Přidat uživatelské prostředí ADF do povolených webů](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Přidejte web **ADF.Azure.com** , vyhledejte možnost **všechny soubory cookie** a uložte. ![Povolení všech souborů cookie z webu ADF UX](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Navštivte **Edge://Settings/Content/cookies** v prohlížeči.
1. Ujistěte se, že je povolená možnost **Povolit webům ukládat a číst data souborů cookie** a že je zakázaná možnost **Blokovat soubory cookie třetích stran** ![ Povolit všechny soubory cookie na Edge.](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Umožněte použití souborů cookie jenom v uživatelském prostředí ADF.

Pokud nechcete povolit všechny soubory cookie, můžete volitelně povolit jenom uživatelské prostředí ADF:

1. Navštivte **Edge://Settings/Content/cookies**.
1. V části **povolený** klikněte na **Přidat** a přidejte lokalitu **ADF.Azure.com** . ![Přidání uživatelského prostředí ADF do povolených webů](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
* [Videa k Azure](https://azure.microsoft.com/resources/videos/index/)
* [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
