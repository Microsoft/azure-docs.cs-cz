---
title: Řešení potíží s příponou Panel přístupu Azure pro aplikaci Internet Explorer | Microsoft Docs
description: Postup nasazení aplikace Internet Explorer rozšíření pro portál Moje aplikace pomocí zásad skupiny.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4f1538cf598da8b5b9aa19def2d5f86ceaca0a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Řešení potíží s příponou Panel přístupu pro Internet Explorer
Tento článek vám pomůže vyřešit tyto problémy:

* Nelze získat přístup k vaší aplikace prostřednictvím portálu Moje aplikace při používání aplikace Internet Explorer.
* Zobrazí se zpráva "Instalace softwaru", i když jste již nainstalovali software.

Pokud jste správce, viz také: [nasazení rozšíření Panel přístupu pro Internet Explorer pomocí zásad skupiny](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Spustit diagnostické nástroje
Instalace problémy s příponou Panel přístupu lze diagnostikovat pomocí stažení a spuštění diagnostické nástroje přístupového panelu:

1. [Chcete-li stáhnout nástroj pro diagnostiku, klikněte sem.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Otevřete soubor a stiskněte klávesu **extrahujte všechny** tlačítko.
   
    ![Stiskněte klávesu extrahujte všechny](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Stiskněte **extrahovat** tlačítko Pokračovat.
   
    ![Stiskněte klávesu extrakce](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Chcete-li spustit nástroj, klikněte pravým tlačítkem na soubor s názvem **AccessPanelExtensionDiagnosticTool**, pak vyberte **Otevřít protokolem > Microsoft Windows na základě Script Host**.
   
    ![Otevřít v programu > základě Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Zobrazí se následující okno diagnostiky, které popisuje, co mohou být další potíže s instalací.
   
    ![Ukázka okno diagnostiky](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Klikněte na tlačítko "**Ano**" umožníte programu opravit problémy, které byly nalezeny.
7. Pokud chcete tyto změny uložit, zavřít okno každé aplikace Internet Explorer a pak znovu otevřete Internet Explorer.<br />Pokud pořád nemáte přístup k vaší aplikace, zkuste následující postup.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Zkontrolujte, že je povolené rozšíření přístup k panelu
Chcete-li ověřit, že je v Internet Exploreru povolená rozšíření přístup k panelu:

1. V aplikaci Internet Explorer, klikněte **ozubené kolečko ikonu** v pravém horním rohu okna. Potom vyberte **Možnosti Internetu**.<br />(V starší verze aplikace Internet Explorer tento nástroj naleznete v části **nástroje > Možnosti Internetu**.
   
    ![Přejděte na Nástroje > Možnosti Internetu](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Klikněte na tlačítko **programy** a potom klikněte **spravovat doplňky** tlačítko.
   
    ![Kliknutím na Spravovat doplňky](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. V tomto dialogovém okně vyberte **rozšíření přístup k panelu** a klikněte **povolit** tlačítko.
   
    ![Klikněte na položku Povolit](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Pokud chcete tyto změny uložit, zavřít okno každé aplikace Internet Explorer a pak znovu otevřete Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Povolit rozšíření pro procházení InPrivate
Pokud používáte v režimu procházení InPrivate:

1. V aplikaci Internet Explorer, klikněte **ozubené kolečko ikonu** v pravém horním rohu okna. Potom vyberte **Možnosti Internetu**.<br />(V starší verze aplikace Internet Explorer tento nástroj naleznete v části **nástroje > Možnosti Internetu**.
   
    ![Ukázka okno diagnostiky](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Přejděte na **o ochraně osobních údajů** kartě pak **zrušte zaškrtnutí políčka** zaškrtávací políčko s názvem bez přípony **zakažte panely nástrojů a rozšíření při procházení se službou InPrivate spuštění**</p>
   
    ![Zrušte zaškrtnutí zakažte panely nástrojů a rozšíření při spuštění procházení InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Pokud chcete tyto změny uložit, zavřít okno každé aplikace Internet Explorer a pak znovu otevřete Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstalovat rozšíření Panel přístupu
Chcete-li odinstalovat rozšíření přístupový Panel z vašeho počítače:

1. Na klávesnici, stiskněte **Windows klíč** k otevření nabídky Start. V otevřeném v nabídce, můžete zadat nic, aby hledání. Zadejte "Ovládací panely" a pak otevřete **ovládací panely** při zobrazí ve výsledcích hledání.
   
    ![Vyhledejte ovládací panely](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. V pravém horním rohu ovládacího panelu změnit **zobrazit** možnost k **ikony. velké ikony**. Poté vyhledejte a vyberte **programy a funkce** tlačítko.
   
    ![Zobrazení změn zobrazíte ikony. velké ikony](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. V seznamu vyberte **rozšíření přístup k panelu**a potom **odinstalovat** tlačítko.
   
    ![Klikněte na tlačítko Odinstalovat](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Potom můžete zkusit nainstalovat rozšíření zjistíte, pokud byl problém vyřešen.

Pokud narazíte na problémy odinstalace rozšíření, můžete také odebrat pomocí [Microsoft opravte ji](https://go.microsoft.com/?linkid=9779673) nástroj.

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Postup nasazení rozšíření Panel přístupu pro Internet Explorer pomocí zásad skupiny](active-directory-saas-ie-group-policy.md)

