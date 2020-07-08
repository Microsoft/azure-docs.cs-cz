---
title: Nasazení rozšíření přístupového panelu Azure pro IE pomocí objektu zásad skupiny | Microsoft Docs
description: Použití zásad skupiny k nasazení doplňku Internet Exploreru pro portál moje aplikace
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c434a2892060acfdd56c496a31e41597c21357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763427"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Postupy: nasazení rozšíření přístupového panelu pro Internet Explorer pomocí zásad skupiny

V tomto kurzu se dozvíte, jak pomocí zásad skupiny vzdáleně nainstalovat rozšíření přístupového panelu pro Internet Explorer na počítačích uživatelů. Toto rozšíření se vyžaduje pro uživatele Internet Exploreru, kteří se potřebují přihlašovat k aplikacím nakonfigurovaným pomocí [jednotného přihlašování založeného na heslech](what-is-single-sign-on.md#password-based-sso).

Doporučuje správcům automatizovat nasazení tohoto rozšíření. V opačném případě musí uživatelé stáhnout a nainstalovat rozšíření samotné, což je náchylné k chybě uživatele a vyžaduje oprávnění správce. V tomto kurzu se zabývá jedna metoda automatizace nasazení softwaru pomocí zásad skupiny. [Přečtěte si další informace o zásadách skupiny.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozšíření přístupového panelu je k dispozici také pro [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ani když není potřeba instalovat oprávnění správce.

## <a name="prerequisites"></a>Požadavky

* Nastavili jste [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k vaší doméně.
* Chcete-li upravit objekt Zásady skupiny (GPO), je nutné mít oprávnění upravit nastavení. Ve výchozím nastavení mají toto oprávnění členové těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Zásady skupiny Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Vytvoření distribučního bodu

Nejdřív je nutné umístit instalační balíček do síťového umístění, ke kterému mají přístup počítače, na kterých chcete vzdáleně nainstalovat rozšíření. Postupujte přitom takto:

1. Přihlaste se k serveru jako správce.
1. V okně **Správce serveru** přejdete do části **soubory a služby úložiště**.

    ![Otevřené soubory a služby úložiště](./media/deploy-access-panel-browser-extension/files-services.png)

1. Přejít na kartu **sdílené složky** . Pak klikněte na **úlohy**  >  **Nová sdílená složka...**

    ![Snímek obrazovky s informacemi o tom, kde najít novou sdílenou složku z obrazovky úlohy](./media/deploy-access-panel-browser-extension/shares.png)

1. Dokončete **Průvodce vytvořením sdílené složky** a nastavte oprávnění, aby bylo zajištěno, že bude možné k němu přicházet z počítačů uživatelů. [Přečtěte si další informace o sdílených složkách.](https://technet.microsoft.com/library/cc753175.aspx)
1. Stáhněte si následující balíček Microsoft Instalační služba systému Windows (soubor. msi): [přístupový Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Zkopírujte instalační balíček do požadovaného umístění ve sdílené složce.

    ![Zkopírujte soubor. msi do sdílené složky.](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ověřte, že klientské počítače mají přístup k instalačnímu balíčku ze sdílené složky.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: vytvoření objektu zásad skupiny

1. Přihlaste se k serveru, který je hostitelem instalace aplikace Active Directory Domain Services (služba AD DS).
1. V správce serveru přejdete na **nástroje**  >  **Zásady skupiny Správa**.

    ![Přejít na nástroje > Zásady skupiny Management](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. V levém podokně okna **správy Zásady skupiny** zobrazte hierarchii organizační jednotky (OU) a určete, ve kterém oboru byste chtěli zásady skupiny použít. Například se můžete rozhodnout vybrat malou organizační jednotku, která se má pro testování nasadit na několik uživatelů, nebo můžete vybrat organizační jednotku nejvyšší úrovně pro nasazení do celé organizace.

   > [!NOTE]
   > Pokud chcete vytvořit nebo upravit organizační jednotky (OU), přepněte zpět na správce serveru a přejděte na **nástroje**  >  **Uživatelé a počítače služby Active Directory**.

1. Po výběru organizační jednotky klikněte na ni pravým tlačítkem myši a vyberte **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...**

    ![Snímek obrazovky se zobrazením možnosti vytvořit nový objekt zásad skupiny](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Do příkazového řádku **Nový objekt zásad skupiny** zadejte název nového objektu Zásady skupiny.
1. Klikněte pravým tlačítkem na objekt Zásady skupiny, který jste vytvořili, a vyberte **Upravit**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3: přiřazení instalačního balíčku

1. Určete, zda chcete rozšíření nasadit na základě **Konfigurace počítače** nebo **Konfigurace uživatele**. Při použití [Konfigurace počítače](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)se rozšíření nainstaluje na počítač bez ohledu na to, kteří uživatelé se k němu přihlásí. V případě [Konfigurace uživatele](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)mají uživatelé nainstalované rozšíření pro ně bez ohledu na to, k jakým počítačům se přihlašuje.
1. V levém podokně okna **Editor pro správu zásad skupiny** v závislosti na zvoleném typu konfigurace přejít na jednu z následujících cest ke složkám:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Klikněte pravým tlačítkem na **Instalace softwaru**a pak vyberte **Nový**  >  **balíček...**
1. Přejděte do sdílené složky, která obsahuje instalační balíček z [kroku 1: vytvořte distribuční bod](#step-1-create-the-distribution-point), vyberte soubor. msi a klikněte na tlačítko **otevřít**.

   > [!IMPORTANT]
   > Pokud se sdílená složka nachází na stejném serveru, ověřte, že k souboru. msi přistupuje přes cestu k síťovému souboru, a ne k místní cestě k souboru.

    ![Vyberte instalační balíček ze sdílené složky.](./media/deploy-access-panel-browser-extension/select-package.png)

1. V příkazovém řádku **nasadit software** vyberte **přiřazeno** pro metodu nasazení. Pak klikněte na **OK**.

Rozšíření je nyní nasazeno do organizační jednotky, kterou jste vybrali. [Přečtěte si další informace o Zásady skupiny pro instalaci softwaru.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: automatické povolení rozšíření pro Internet Explorer

Kromě spuštění instalačního programu musí být každé rozšíření pro Internet Explorer explicitně povoleno, aby bylo možné ho použít. Pomocí následujících kroků Povolte rozšíření přístupového panelu pomocí zásad skupiny:

1. V okně **Editor pro správu zásad skupiny** v závislosti na typu konfigurace, který jste zvolili v kroku 3, přejít na kteroukoli z následujících cest: [přiřazení instalačního balíčku](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Klikněte pravým tlačítkem na **seznam doplňků**a vyberte **Upravit**.

    ![Klikněte pravým tlačítkem na seznam doplňků a vyberte Upravit.](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. V okně se **seznamem doplňků** vyberte **povoleno**. Pak v části **Možnosti** klikněte na **Zobrazit...**.

    ![Klikněte na povolit a pak na zobrazit...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. V okně **Zobrazit obsah** proveďte následující kroky:

   1. Do prvního sloupce (pole **název hodnoty** ) zkopírujte a vložte následující ID třídy:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Pro druhý sloupec (pole **hodnota** ) zadejte následující hodnotu:`1`
   1. Kliknutím na tlačítko **OK** zavřete okno **Zobrazit obsah** .

      ![Vyplňte hodnoty uvedené v předchozím kroku.](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kliknutím na tlačítko **OK** změny aplikujte a zavřete okno **seznam doplňků** .

Rozšíření by teď mělo být povolené pro počítače ve vybrané organizační jednotce. [Další informace o použití zásad skupiny k povolení nebo zakázání doplňků v aplikaci Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (volitelné): vypnutí výzvy zapamatování hesla

Když se uživatelé přihlásí k webům pomocí rozšíření přístupového panelu, může aplikace Internet Explorer zobrazit následující výzvu s dotazem "Přejete si uložit heslo?"

![Ukazuje, jak chcete uložit heslo... výzv](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Pokud chcete uživatelům zabránit v zobrazení této výzvy, postupujte podle následujících kroků, abyste zabránili automatickému dokončení hesla při zapamatování hesla:

1. V okně **Editor pro správu zásad skupiny** přejít na níže uvedenou cestu. Toto nastavení konfigurace je dostupné pouze v části **Konfigurace uživatele**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Vyhledejte nastavení s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**.

   > [!NOTE]
   > Předchozí verze služby Active Directory můžou vypsat toto nastavení s názvem **Nepovolit automatické dokončování pro ukládání hesel**. Konfigurace tohoto nastavení se liší od nastavení popsaných v tomto kurzu.

    ![Nezapomeňte to najít v části Uživatelská nastavení.](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Klikněte pravým tlačítkem na výše uvedené nastavení a vyberte **Upravit**.
1. V okně s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**vyberte **zakázáno**.

    ![Vyberte možnost disabled pro funkci zapnout automatické dokončování.](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Kliknutím na tlačítko **OK** aplikujte tyto změny a zavřete okno.

Uživatelé už nebudou moct ukládat svoje přihlašovací údaje ani používat automatické dokončování pro přístup k dříve uloženým přihlašovacím údajům. Tato zásada ale uživatelům umožní i nadále používat automatické dokončování pro jiné typy polí formuláře, například vyhledávací pole.

> [!WARNING]
> Pokud je tato zásada povolená, když se uživatelé budou chtít ukládat nějaké přihlašovací údaje, tato zásada *nevymaže přihlašovací* údaje, které už jsou uložené.

## <a name="step-6-testing-the-deployment"></a>Krok 6: testování nasazení

Postupujte podle následujících kroků a ověřte, zda nasazení rozšíření bylo úspěšné:

1. Pokud jste nasadili pomocí **Konfigurace počítače**, přihlaste se ke klientskému počítači, který patří do organizační jednotky, kterou jste vybrali v [kroku 2: vytvoření objektu Zásady skupiny](#step-2-create-the-group-policy-object). Pokud jste nasadili pomocí **Konfigurace uživatele**, nezapomeňte se přihlásit jako uživatel, který patří do této organizační jednotky.
1. Může trvat několik přihlášení, aby se změny zásad skupiny plně aktualizovaly s tímto počítačem. Pokud chcete tuto aktualizaci vynutit, otevřete okno **příkazového řádku** a spusťte následující příkaz:`gpupdate /force`
1. Je nutné restartovat počítač, aby se instalace mohla uskutečnit. Spouštění může trvat delší dobu než obvykle při instalaci rozšíření.
1. Po restartování spusťte **aplikaci Internet Explorer**. V pravém horním rohu okna klikněte na **nástroje** (ikona ozubeného kolečka) a pak vyberte **Spravovat doplňky**.
1. V okně **Spravovat doplňky** ověřte, že je nainstalované **rozšíření přístupového panelu** a že jeho **stav** je nastavené na **povoleno**.

   ![Ověřte, že je nainstalované a povolené rozšíření přístupového panelu.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Další informace

* [Přístup k aplikaci a jednotné přihlašování pomocí Azure Active Directory](what-is-single-sign-on.md)
* [Řešení potíží s rozšířením přístupového panelu pro Internet Explorer](manage-access-panel-browser-extension.md)
