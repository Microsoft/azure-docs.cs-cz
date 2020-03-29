---
title: Zachyťte trasování prohlížeče pro řešení potíží | Dokumenty společnosti Microsoft
description: Zachyťte informace o síti ze sledování prohlížeče, abyste vám pomohli vyřešit problémy s portálem Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310692"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Zachycení trasování prohlížeče pro řešení potíží

Pokud řešíte problém s portálem Azure a potřebujete kontaktovat podporu Microsoftu, doporučujeme nejprve zachytit trasování prohlížeče a některé další informace. Informace, které shromažďujete, mohou poskytnout důležité podrobnosti o portálu v době, kdy k problému dojde. Podle pokynů v tomto článku naleznete vývojářské nástroje v prohlížeči, který používáte: Google Chrome nebo Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) nebo Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome a Microsoft Edge (Chrom)

Google Chrome a Microsoft Edge (Chromium) jsou založeny na [open source projektu Chromium](https://www.chromium.org/Home). Následující kroky ukazují, jak používat vývojářské nástroje, které jsou velmi podobné ve dvou prohlížečích. Další informace naleznete v [tématech Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) a [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Před spuštěním trasování _before_ je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s přihlášením. 

1. Začněte zaznamenávat kroky, které provedete na portálu, pomocí [funkce Záznam kroků](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Na portálu přejděte ke kroku těsně před kde dojde k problému.

1. ![Stiskněte klávesu F12 nebo](media/capture-browser-trace/chromium-icon-settings.png) > vyberte Snímek obrazovky ikony nastavení prohlížeče**Další nástroje** > **Nástroje pro vývojáře**.

1. Ve výchozím nastavení prohlížeč uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč uchovává všechny informace o trasování, i když repro vyžaduje přejít na více než jednu stránku:

    1. Vyberte kartu **Síť** a pak vyberte **Zachovat protokol**.

          ![Snímek obrazovky "Zachovat protokol"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Vyberte kartu **Konzola,** vyberte **nastavení konzoly**a pak vyberte **Zachovat protokol**. Znovu **vyberte nastavení konzoly,** chcete-li podokno nastavení zavřít.

          ![Snímek obrazovky "Zachovat protokol"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Vyberte kartu **Síť** a pak vyberte **Zastavit záznam síťového protokolu** a **Vymazat**.

    ![Snímek obrazovky s možnostmi Zastavení nahrávání síťového protokolu a "Vymazat"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Vyberte **záznam síťového protokolu**a potom problém reprodukovat na portálu.

    ![Snímek obrazovky s "Zahájit profilování relace"](media/capture-browser-trace/chromium-start-session.png)

    Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **Zastavit záznam síťového protokolu**, pak vyberte **Exportovat HAR** a soubor uložte.

    ![Snímek obrazovky "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Zastavte záznam kroků a uložte soubor.

1. V podokně nástrojů pro vývojáře prohlížeče vyberte kartu **Konzola.** Klepněte pravým tlačítkem myši, pak vyberte **Uložit jako...** a uložte výstup konzoly do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/chromium-console-select.png)

1. Zabalte soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu, jako je zip, a sdílejte je s podporou společnosti Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Následující kroky ukazují, jak používat vývojářské nástroje v Microsoft Edge (EdgeHTML). Další informace naleznete v [tématu Microsoft Edge (EdgeHTML) Developer Tools](/microsoft-edge/devtools-guide).

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Před spuštěním trasování _before_ je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s přihlášením. 

1. Začněte zaznamenávat kroky, které provedete na portálu, pomocí [funkce Záznam kroků](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Na portálu přejděte ke kroku těsně před kde dojde k problému.

1. ![Stiskněte klávesu F12 nebo](media/capture-browser-trace/edge-icon-settings.png) > vyberte Snímek obrazovky ikony nastavení prohlížeče**Další nástroje** > **Nástroje pro vývojáře**.

1. Ve výchozím nastavení prohlížeč uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč uchovává všechny informace o trasování, i když repro vyžaduje přejít na více než jednu stránku:

    1. Vyberte kartu **Síť** a zrušte zaškrtnutí políčka **Vymazat položky při navigaci**.

          ![Snímek obrazovky s položkou Vymazat položky při navigaci](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Vyberte kartu **Konzola** a pak **vyberte Zachovat protokol**.

          ![Snímek obrazovky "Zachovat protokol"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Vyberte kartu **Síť** a pak vyberte **Zastavit profilování relace** a Vymazat **relaci**.

    ![Snímek obrazovky s možnostmi Ukončení profilování relace a "Vymazat relaci"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Vyberte **Spustit relaci profilování**a pak problém reprodukovat na portálu.

    ![Snímek obrazovky s "Zahájit profilování relace"](media/capture-browser-trace/edge-start-session.png)

    Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/edge-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **Zastavit profilování relace**, pak vyberte **Exportovat jako HAR** a uložte soubor.

    ![Snímek obrazovky "Export ovat jako HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Zastavte záznam kroků a uložte soubor.

1. V podokně nástrojů pro vývojáře prohlížeče vyberte kartu **Konzola** a rozbalte okno. Umístěte kurzor na začátek výstupu konzoly a pak přetáhněte a vyberte celý obsah výstupu. Klepněte pravým tlačítkem myši, pak vyberte **Kopírovat**a uložte výstup konzoly do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/edge-console-select.png)

1. Zabalte soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu, jako je zip, a sdílejte je s podporou společnosti Microsoft.

## <a name="apple-safari"></a>Apple Safari

Následující kroky ukazují, jak používat vývojářské nástroje v Apple Safari. Další informace naleznete v tématu [Safari Developer Tools overview](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Povolte vývojářské nástroje v Apple Safari:

    1. Vyberte **Safari**a pak vyberte **Předvolby**.

        ![Snímek obrazovky s předvolbami Safari](media/capture-browser-trace/safari-preferences.png)

    1. Vyberte kartu **Upřesnit** a **v řádku nabídek**vyberte Zobrazit vývoj .

        ![Snímek obrazovky s pokročilými předvolbami Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Před spuštěním trasování _before_ je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s přihlášením. 

1. Začněte zaznamenávat kroky, které provedete na portálu. Další informace najdete [v tématu Jak nahrávat obrazovku na Macu](https://support.apple.com/HT208721).

1. Na portálu přejděte ke kroku těsně před kde dojde k problému.

1. Vyberte **Vyvíjet**a pak vyberte **Zobrazit webový inspektor**.

    ![Snímek obrazovky s pořadem Zobrazit webového inspektora](media/capture-browser-trace/safari-show-web-inspector.png)

1. Ve výchozím nastavení prohlížeč uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč uchovává všechny informace o trasování, i když repro vyžaduje přejít na více než jednu stránku:

    1. Vyberte kartu **Síť** a pak vyberte **Zachovat protokol**.

          ![Snímek obrazovky "Zachovat protokol"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Vyberte kartu **Konzola** a pak **vyberte Zachovat protokol**.

          ![Snímek obrazovky "Zachovat protokol"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Vyberte kartu **Síť** a pak **vyberte Vymazat položky sítě**.

    ![Snímek obrazovky s položkou Vymazat síťové položky](media/capture-browser-trace/safari-clear-session.png)

1. Reprodukovat problém na portálu. Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/safari-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **Exportovat** a uložit soubor.

    ![Snímek obrazovky s "Exportem"](media/capture-browser-trace/safari-network-export-har.png)

1. Zastavte rekordér obrazovky a uložte soubor.

1. V podokně nástrojů pro vývojáře prohlížeče vyberte kartu **Konzola** a rozbalte okno. Umístěte kurzor na začátek výstupu konzoly a pak přetáhněte a vyberte celý obsah výstupu. Pomocí příkazu C zkopírujte výstup a uložte jej do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/safari-console-select.png)

1. Zabalte soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu, jako je zip, a sdílejte je s podporou společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

[Přehled webu Azure Portal](azure-portal-overview.md)