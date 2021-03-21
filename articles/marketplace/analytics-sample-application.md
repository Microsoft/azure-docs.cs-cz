---
title: Ukázková aplikace pro přístup k datům analýzy komerčního tržiště
description: Použijte ukázkovou aplikaci k sestavení vlastní aplikace pro analýzu na komerčních webu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583858"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Ukázková aplikace pro přístup k datům analýzy komerčního tržiště

Ukázkové aplikace se vytvářejí v jazycích C# a JAVA a jsou k dispozici na [GitHubu](https://github.com/partneranalytics).

- [Ukázková aplikace v jazyce C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Ukázková aplikace JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Můžete si vybrat, že se inspiraci z ukázkové aplikace a sestavíte svou vlastní aplikaci v jakémkoli jazyce.

Ukázková aplikace dosahuje následujících cílů:

- Vygeneruje token Azure Active Directory (Azure AD).
- Získá dostupné datové sady.
- Vytvoří uživatelsky definované dotazy.
- Získá uživatelsky definované a systémové dotazy.
- Naplánuje sestavu.

Ukázková aplikace nepokrývá metodu volání rozhraní API pro jiné funkce. Proces volání jiných rozhraní API však zůstává stejný, jak je popsáno výše.

## <a name="how-to-run-the-application"></a>Spuštění aplikace

1. Naklonujte úložiště do místního systému pomocí tohoto příkazu:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Další pokyny najdete `ProgrammaticExportSampleAppISV/README.md` v souboru v [úložišti](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)GitHub.

1. Pokud chcete aplikaci rychle spustit, aktualizujte ID klienta a tajný klíč klienta v **appsettings.Development.jsdne** .

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Ukazuje fragment appsettings.Development.jsv souboru.":::

Spuštění aplikace spustí místní webový server a otevře se stránka ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Znázorňuje stránku sestava plánování.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Tato stránka provede volání rozhraní API na server, který běží na místním počítači, a pak provede skutečná volání rozhraní API pro programové přístup.

## <a name="code-snippets"></a>Fragmenty kódu

Základní struktura kódu jazyka C# pro volání rozhraní API pro programové přístup je následující:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Snímek obrazovky s voláními rozhraní API":::

## <a name="next-steps"></a>Další kroky

- [Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace](analytics-available-apis.md)
