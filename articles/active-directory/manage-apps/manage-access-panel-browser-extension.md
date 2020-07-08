---
title: Řešení potíží s rozšířením přístupového panelu Azure pro IE | Microsoft Docs
description: Použití zásad skupiny k nasazení doplňku Internet Exploreru pro portál moje aplikace
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763257"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Řešení potíží s rozšířením přístupového panelu pro Internet Explorer

Tento článek vám pomůže vyřešit následující problémy:

* Pomocí aplikace Internet Explorer nemůžete k vašim aplikacím přistupovat prostřednictvím portálu moje aplikace.
* Zobrazí se zpráva o instalaci softwaru, i když jste už software nainstalovali.

Pokud jste správce, přečtěte si téma [nasazení rozšíření přístupového panelu pro Internet Explorer pomocí Zásady skupiny](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Spuštění diagnostického nástroje

Problémy s instalací můžete diagnostikovat pomocí rozšíření přístupového panelu stažením a spuštěním diagnostického nástroje přístupového panelu. 

Stažení a instalace diagnostického nástroje:

1. [Pro stažení diagnostického nástroje vyberte tento odkaz.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Otevřete soubor a extrahujte obsah do počítače.
1. Chcete-li spustit nástroj, klikněte pravým tlačítkem myši na soubor s názvem *AccessPanelExtensionDiagnosticTool.js* a vyberte možnost **otevřít v rámci**programu  >  **Script Host založený na systému Microsoft Windows**.

    ![Otevřít pomocí > hostitele skriptu založeného na systému Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Zkontrolujte výsledky diagnostiky, které se zobrazí, a pokud chcete problémy vyřešit, vyberte **Ano** . Zobrazí se dialogové okno **výsledky kontroly** s informacemi o tom, co dělat, pokud rozšíření nefunguje.  
1. Přečtěte si zprávu a vyberte **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Ověřte, že je povolené rozšíření přístupového panelu.

Chcete-li ověřit, zda jste povolili rozšíření přístupového panelu v aplikaci Internet Explorer:

1. V Internet Exploreru vyberte **ikonu ozubeného kolečka** v pravém horním rohu okna a vyberte **Možnosti Internetu**.
1. Přejít na kartu **programy** a vyberte **Spravovat doplňky**.
1. V části **Microsoft Corporation** vyberte **rozšíření přístupového panelu** a vyberte **Povolit**.
1. Chcete-li uložit změny, zavřete všechna okna prohlížeče Internet Explorer, která jste otevřeli. Změna se projeví při příštím spuštění aplikace Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Povolit rozšíření pro procházení InPrivate

Povolení rozšíření pro procházení InPrivate:

1. V Internet Exploreru vyberte **ikonu ozubeného kolečka** v pravém horním rohu okna a vyberte **Možnosti Internetu**.
1. Přejděte na kartu **soukromí** a ověřte, že zaškrtávací políčko **Zakázat panely nástrojů a rozšíření, když se spustí procházení InPrivate** .
1. Chcete-li uložit změny, zavřete všechna okna prohlížeče Internet Explorer, která jste otevřeli. Změna se projeví při příštím spuštění aplikace Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstalace rozšíření přístupového panelu

Odinstalace rozšíření přístupového panelu z počítače:

1. V Ovládacích panelech vyhledejte *odinstalaci*.
1. Ve výsledcích hledání vyberte **Odinstalovat program**.

    ![V Ovládacích panelech vyberte možnost odinstalovat program.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. V seznamu vyberte možnost **rozšíření přístupového panelu** a vyberte možnost **odinstalovat**.

    ![Odinstalace rozšíření přístupového panelu](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Pak se můžete pokusit nainstalovat rozšíření znovu, abyste viděli, zda byl problém vyřešen.

Pokud narazíte na problémy s odinstalací rozšíření, můžete ho taky odebrat pomocí nástroje [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Související články

* [Přístup k aplikaci a jednotné přihlašování pomocí Azure Active Directory](what-is-single-sign-on.md)
* [Nasazení rozšíření přístupového panelu pro Internet Explorer pomocí Zásady skupiny](deploy-access-panel-browser-extension.md)
