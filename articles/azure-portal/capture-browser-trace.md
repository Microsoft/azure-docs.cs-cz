---
title: Zaznamenat trasování prohlížeče pro řešení potíží | Microsoft Docs
description: Zachyťte informace o síti z trasování v prohlížeči, abyste pomohli řešit problémy s Azure Portal.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: dba321d055e64d62ca91f95461c3299bee5f90d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714215"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Zachycení trasování prohlížeče pro řešení potíží

Pokud řešíte problém s Azure Portal a potřebujete kontaktovat podporu Microsoftu, doporučujeme nejprve zachytit trasování prohlížeče a další informace. Informace, které shromažďujete, mohou poskytnout důležité informace o portálu v době výskytu problému. Postupujte podle kroků v tomto článku pro vývojářské nástroje v prohlížeči, který používáte: Google Chrome nebo Microsoft Edge (chrom), Microsoft Edge (EdgeHTML), Apple Safari nebo Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome a Microsoft Edge (chrom)

Google Chrome a Microsoft Edge (chrom) jsou založené na [projektu chrom Open Source](https://www.chromium.org/Home). Následující kroky ukazují, jak používat vývojářské nástroje, které jsou ve dvou prohlížečích velmi podobné. Další informace najdete v tématu [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) a [Microsoft Edge (chrom) vývojářské nástroje](/microsoft-edge/devtools-guide-chromium).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). _Před_ zahájením trasování je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s vaším přihlášením. 

1. Začněte nahrávat kroky, které provedete na portálu, pomocí [záznamu postupu](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Na portálu přejděte k tomuto kroku těsně před tím, kde k problému dochází.

1. Stiskněte klávesu F12 nebo vyberte ![ snímek obrazovky nastavení prohlížeče ikony ](media/capture-browser-trace/chromium-icon-settings.png)  >  **Další nástroje**  >  **Developer Tools**.

1. Prohlížeč standardně uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč zachovává všechny informace o trasování, a to i v případě, že váš reprodukci vyžaduje, aby přechází na více než jednu stránku:

    1. Vyberte kartu **síť** a pak vyberte **zachovat protokol**.

          ![Snímek obrazovky "zachovat protokol"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Vyberte kartu **Konzola** , vyberte **nastavení konzoly**a pak vyberte **zachovat protokol**. Opětovným výběrem **Možnosti nastavení konzoly** zavřete podokno nastavení.

          ![Snímek obrazovky "zachovat protokol"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Vyberte kartu **síť** a pak vyberte **Zastavit záznam síťového protokolu** a **Vymazat**.

    ![Snímek obrazovky "Zastavit záznam síťového protokolu" a "Vymazat"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Vyberte **zaznamenat protokol sítě**a potom problém reprodukován na portálu.

    ![Snímek obrazovky "spuštění relace profilace"](media/capture-browser-trace/chromium-start-session.png)

    Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **Zastavit záznam síťového protokolu**a pak vyberte **exportovat Har** a soubor uložte.

    ![Snímek obrazovky "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Zastavte záznam kroků a záznam uložte.

1. Zpátky v podokně nástroje pro vývojáře v prohlížeči vyberte kartu **Konzola** . Klikněte pravým tlačítkem myši na jednu ze zpráv, vyberte **Uložit jako...** a uložte výstup konzoly do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/chromium-console-select.png)

1. Zabalit soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu jako. zip a sdílet s podporou Microsoftu.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Následující kroky ukazují, jak používat vývojářské nástroje v Microsoft Edge (EdgeHTML). Další informace najdete v tématu [Microsoft Edge (EdgeHTML) vývojářské nástroje](/microsoft-edge/devtools-guide).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). _Před_ zahájením trasování je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s vaším přihlášením. 

1. Začněte nahrávat kroky, které provedete na portálu, pomocí [záznamu postupu](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Na portálu přejděte k tomuto kroku těsně před tím, kde k problému dochází.

1. Stiskněte klávesu F12 nebo vyberte ![ snímek obrazovky nastavení prohlížeče ikony ](media/capture-browser-trace/edge-icon-settings.png)  >  **Další nástroje**  >  **Developer Tools**.

1. Prohlížeč standardně uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč zachovává všechny informace o trasování, a to i v případě, že váš reprodukci vyžaduje, aby přechází na více než jednu stránku:

    1. Vyberte kartu **síť** a potom zrušte zaškrtnutí políčka **vymazat položky při navigaci**.

          ![Snímek obrazovky "vymazání položek při navigaci"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Vyberte kartu **Konzola** a pak vyberte **zachovat protokol**.

          ![Snímek obrazovky "zachovat protokol"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Vyberte kartu **síť** a pak vyberte **zastavit relaci profilace** a **Vymazat relaci**.

    ![Snímek obrazovky "zastavení relace profilování" a "vymazání relace"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Vyberte **spustit relaci profilace**a pak problém reprodukován na portálu.

    ![Snímek obrazovky "spuštění relace profilace"](media/capture-browser-trace/edge-start-session.png)

    Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/edge-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **zastavit relaci profilace**a pak vyberte **exportovat jako Har** a soubor uložte.

    ![Snímek obrazovky "exportovat jako HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Zastavte záznam kroků a záznam uložte.

1. Zpátky v podokně nástroje pro vývojáře v prohlížeči vyberte kartu **Konzola** a rozbalte okno. Umístěte kurzor na začátek výstupu konzoly a pak přetáhněte a vyberte celý obsah výstupu. Klikněte pravým tlačítkem myši, vyberte možnost **Kopírovat**a uložte výstup konzoly do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/edge-console-select.png)

1. Zabalit soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu jako. zip a sdílet s podporou Microsoftu.

## <a name="apple-safari"></a>Apple Safari

Následující kroky ukazují, jak používat vývojářské nástroje v Apple Safari. Další informace najdete v tématu [přehled vývojářské nástroje Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Povolení vývojářských nástrojů v Apple Safari:

    1. Vyberte **Safari**a pak vyberte **Předvolby**.

        ![Snímek obrazovky s preferencemi prohlížeče Safari](media/capture-browser-trace/safari-preferences.png)

    1. Vyberte kartu **Upřesnit** a pak **na panelu nabídek vyberte možnost Zobrazit nabídku vývoje**.

        ![Snímek obrazovky s pokročilými preferencemi Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). _Před_ zahájením trasování je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s vaším přihlášením. 

1. Začněte nahrávat kroky, které provedete na portálu. Další informace najdete v tématu [Jak nahrávat obrazovku na Macu](https://support.apple.com/HT208721).

1. Na portálu přejděte k tomuto kroku těsně před tím, kde k problému dochází.

1. Vyberte **vývoj**a pak vyberte **Zobrazit web Inspector**.

    ![Snímek obrazovky "zobrazení webového inspektoru"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Prohlížeč standardně uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč zachovává všechny informace o trasování, a to i v případě, že váš reprodukci vyžaduje, aby přechází na více než jednu stránku:

    1. Vyberte kartu **síť** a pak vyberte **zachovat protokol**.

          ![Snímek obrazovky "zachovat protokol"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Vyberte kartu **Konzola** a pak vyberte **zachovat protokol**.

          ![Snímek obrazovky "zachovat protokol"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Vyberte kartu **síť** a pak vyberte **Vymazat síťové položky**.

    ![Snímek obrazovky "vymazání síťových položek"](media/capture-browser-trace/safari-clear-session.png)

1. Reprodukování problému na portálu. Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/safari-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **exportovat** a uložte soubor.

    ![Snímek obrazovky "Export"](media/capture-browser-trace/safari-network-export-har.png)

1. Zastavte záznam obrazovky a záznam uložte.

1. Zpátky v podokně nástroje pro vývojáře v prohlížeči vyberte kartu **Konzola** a rozbalte okno. Umístěte kurzor na začátek výstupu konzoly a pak přetáhněte a vyberte celý obsah výstupu. Pomocí příkazu-C zkopírujte výstup a uložte ho do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/safari-console-select.png)

1. Zabalit soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu jako. zip a sdílet s podporou Microsoftu.

## <a name="firefox"></a>Firefox

Následující kroky ukazují, jak používat vývojářské nástroje v prohlížeči Firefox. Další informace najdete v tématu [Firefox vývojářské nástroje](https://developer.mozilla.org/docs/Tools).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). _Před_ zahájením trasování je důležité se přihlásit, aby trasování neobsahovalo citlivé informace související s vaším přihlášením. 

1. Začněte nahrávat kroky, které provedete na portálu. Použijte nástroj pro [zaznamenávání kroků](https://support.microsoft.com/help/22878/windows-10-record-steps) ve Windows nebo si přečtěte, [jak si můžete na Mac nahrát obrazovku](https://support.apple.com/HT208721).

1. Na portálu přejděte k tomuto kroku těsně před tím, kde k problému dochází.

1. Stiskněte klávesu F12 nebo vyberte ![ snímek obrazovky ikona nastavení prohlížeče ](media/capture-browser-trace/firefox-icon-settings.png)  >  **webové vývojáře**  >  **Přepnout nástroje**.

1. Prohlížeč standardně uchovává informace o trasování pouze pro aktuálně načtenou stránku. Nastavte následující možnosti, aby prohlížeč zachovává všechny informace o trasování, a to i v případě, že váš reprodukci vyžaduje, aby přechází na více než jednu stránku:

    1. Vyberte kartu **síť** a pak vyberte **zachovat protokoly**.

          ![Snímek obrazovky "trvalé protokoly"](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Vyberte kartu **Konzola** , vyberte **nastavení konzoly**a pak vyberte **zachovat protokoly**.

          ![Snímek obrazovky "trvalé protokoly"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Vyberte kartu **síť** a pak vyberte **Vymazat**.

    ![Snímek obrazovky "Clear"](media/capture-browser-trace/firefox-clear-session.png)

1. Reprodukování problému na portálu. Zobrazí se výstup relace podobný následujícímu obrázku.

    ![Snímek obrazovky s výsledky trasování prohlížeče](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Po reprodukování neočekávaného chování portálu vyberte **Har export/import** a pak **vše uložte jako Har**.

    ![Snímek obrazovky "Export HAR"](media/capture-browser-trace/firefox-network-export-har.png)

1. Zastavte záznam kroků ve Windows nebo na obrazovce na Macu a záznam uložte.

1. Zpátky v podokně nástroje pro vývojáře v prohlížeči vyberte kartu **Konzola** . Klikněte pravým tlačítkem na jednu ze zpráv, vyberte **exportovat viditelnou zprávu do**a uložte výstup konzoly do textového souboru.

    ![Snímek obrazovky s výstupem konzoly](media/capture-browser-trace/firefox-console-select.png)

1. Zabalit soubor HAR, výstup konzoly a záznam obrazovky v komprimovaném formátu jako. zip a sdílet s podporou Microsoftu.

## <a name="next-steps"></a>Další kroky

[Přehled webu Azure Portal](azure-portal-overview.md)