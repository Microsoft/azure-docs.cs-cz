---
title: Nasazení rozšíření přístupového panelu Azure pro ie pomocí objektů skupiny | Dokumenty společnosti Microsoft
description: Jak použít zásady skupiny k nasazení doplňku aplikace Internet Explorer pro portál Moje aplikace.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807685"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Postup: Nasazení rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny

Tento kurz ukazuje, jak pomocí zásad skupiny vzdáleně nainstalovat rozšíření přístupového panelu pro aplikaci Internet Explorer do počítačů uživatelů. Toto rozšíření je vyžadováno pro uživatele aplikace Internet Explorer, kteří se potřebují přihlásit k aplikacím, které jsou nakonfigurovány pomocí [jednotného přihlašování založeného na heslech](what-is-single-sign-on.md#password-based-sso).

Doporučujese, aby správci automatizovali nasazení tohoto rozšíření. V opačném případě musí uživatelé stáhnout a nainstalovat rozšíření sami, což je náchylné k chybě uživatele a vyžaduje oprávnění správce. Tento kurz popisuje jednu metodu automatizace nasazení softwaru pomocí zásad skupiny. [Přečtěte si další informace o zásadách skupiny.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozšíření přístupového panelu je k dispozici také pro [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), z nichž ani jedno nevyžaduje oprávnění správce k instalaci.

## <a name="prerequisites"></a>Požadavky

* Nastavili jste [službu Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k doméně.
* Chcete-li upravit objekt zásad skupiny (GPO), musíte mít oprávnění Upravit nastavení. Ve výchozím nastavení mají členové následujících skupin zabezpečení toto oprávnění: Správci domény, Podnikoví správci a Vlastníci tvůrců zásad skupiny. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Vytvoření distribučního bodu

Nejprve je nutné umístit instalační balíček do síťového umístění, ke kterému mají přístup počítače, na které chcete rozšíření vzdáleně nainstalovat. Postupujte přitom takto:

1. Přihlaste se k serveru jako správce.
1. V okně **Správce serveru** přejděte na **Soubory a služby úložiště**.

    ![Otevřít soubory a služby úložiště](./media/deploy-access-panel-browser-extension/files-services.png)

1. Přejděte na kartu **Sdílené** položky. Pak klikněte na **Úkoly** > **nová sdílená...**

    ![Snímek obrazovky ukazuje, kde najít novou sdílenou položku na obrazovce Úkoly](./media/deploy-access-panel-browser-extension/shares.png)

1. Dokončete **Průvodce novou sdílenou položkou** a nastavte oprávnění, abyste zajistili, že k němu budete mít přístup z počítačů uživatelů. [Přečtěte si další informace o sdílených sazbě.](https://technet.microsoft.com/library/cc753175.aspx)
1. Stáhněte si následující balíček Instalační služby systému Microsoft Windows (soubor MSI): [Rozšíření přístupového panelu.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Zkopírujte instalační balíček do požadovaného umístění ve sdílené složce.

    ![Zkopírování souboru MSI do sdílené složky](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ověřte, zda jsou klientské počítače schopny získat přístup k balíčku instalačního programu ze sdílené položky.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Vytvoření objektu zásad skupiny

1. Přihlaste se k serveru, který je hostitelem instalace služby AD DS (Active Directory Domain Services).
1. Ve Správci serveru přejděte na **nástrojovou** > **správu zásad skupiny**.

    ![Přejít na Nástroje > správa zásad skupiny](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. V levém podokně okna **Správa zásad skupiny** zobrazte hierarchii organizační jednotky (OU) a určete, ve kterém oboru chcete zásady skupiny použít. Můžete se například rozhodnout vybrat malou organizační výhonek, kterou chcete nasadit několika uživatelům k testování, nebo můžete vybrat organizační výučnou aktivitu nejvyšší úrovně, kterou chcete nasadit v celé organizaci.

   > [!NOTE]
   > Chcete-li vytvořit nebo upravit organizační jednotky, přepněte zpět do Správce serveru a přejděte na **nástroj nástroje** > **AD Uživatelé a počítače služby Active Directory**.

1. Jakmile vyberete ou, klikněte na ni pravým tlačítkem myši a v této doméně vyberte **Vytvořit objekt skupiny a propojte ji zde...**

    ![Snímek obrazovky zobrazuje možnost Vytvořit nový gpo](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Do výzvy **Nový objekt zásad skupiny** zadejte název nového objektu zásad skupiny.
1. Klikněte pravým tlačítkem myši na objekt zásad skupiny, který jste vytvořili, a vyberte **upravit**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Přiřazení instalačního balíčku

1. Určete, zda chcete rozšíření nasadit na základě **konfigurace počítače** nebo **konfigurace uživatele**. Při použití [konfigurace počítače](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)je rozšíření nainstalováno v počítači bez ohledu na to, kteří uživatelé se k němu přihlašují. Při [konfiguraci uživatele](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)mají uživatelé pro ně nainstalováno rozšíření bez ohledu na to, ke kterým počítačům se přihlašují.
1. V levém podokně okna **Editor správy zásad skupiny** přejděte na jednu z následujících cest složek podle toho, jaký typ konfigurace jste zvolili:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Klepněte pravým tlačítkem myši na **instalaci softwaru**a vyberte **možnost Nový** > **balíček...**
1. Přejděte do sdílené složky obsahující balíček instalačního programu z [kroku 1: Vytvořte distribuční bod](#step-1-create-the-distribution-point), vyberte soubor MSI a klepněte na tlačítko **Otevřít**.

   > [!IMPORTANT]
   > Pokud je sdílená složka umístěna na stejném serveru, ověřte, zda přistupujete k msi prostřednictvím cesty k síťovému souboru, nikoli k místní cestě k souboru.

    ![Výběr instalačního balíčku ze sdílené složky](./media/deploy-access-panel-browser-extension/select-package.png)

1. V **poruce Nasazení softwaru** vyberte **Přiřazeno** pro metodu nasazení. Pak klikněte na **OK**.

Rozšíření je nyní nasazeno do hlavní výužky, kterou jste vybrali. [Přečtěte si další informace o instalaci softwaru zásad skupiny.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Automatické povolení rozšíření pro Internet Explorer

Kromě spuštění instalačního programu musí být před použitím explicitně povolena všechna rozšíření pro aplikaci Internet Explorer. Chcete-li rozšíření přístupového panelu povolit pomocí zásad skupiny, postupujte podle následujících kroků:

1. V okně **Editor správy zásad skupiny** přejděte na jednu z následujících cest v závislosti na typu konfigurace, kterou jste zvolili v [kroku 3: Přiřazení instalačního balíčku](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Klepněte pravým tlačítkem myši na **seznam doplňků**a vyberte příkaz **Upravit**.

    ![Klikněte pravým tlačítkem na "Seznam doplňků" a vyberte "Upravit"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. V okně **Seznam doplňků** vyberte **Možnost Povoleno**. Potom v části **Možnosti** klikněte na **Zobrazit...**.

    ![Klikněte na Povolit a potom na Zobrazit...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. V okně **Zobrazit obsah** proveďte následující kroky:

   1. Pro první sloupec (pole **Název hodnoty)** zkopírujte a vložte následující ID třídy:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Pro druhý sloupec (pole **Hodnota)** zadejte následující hodnotu:`1`
   1. Klepnutím na **tlačítko OK** zavřete okno **Zobrazit obsah.**

      ![Vyplnění hodnot určených v předchozím kroku](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klepnutím na **tlačítko OK** aplikujte změny a zavřete okno **Seznam doplňků.**

Rozšíření by nyní mělo být povoleno pro počítače ve vybrané hlavní výměře. [Přečtěte si další informace o povolení nebo zakázání doplňků aplikace Internet Explorer pomocí zásad skupiny.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (nepovinné): Zakázat výzvu "Zapamatovat heslo"

Když se uživatelé přihlašují k webům pomocí rozšíření přístupového panelu, může aplikace Internet Explorer zobrazit následující výzvu s dotazem" Chcete uložit heslo?"

![Zobrazuje "Chcete uložit heslo..." Výzva](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Chcete-li uživatelům zabránit v zobrazení této výzvy, postupujte podle následujících kroků a zapamatujte te si hesla:

1. V okně **Editor správy zásad skupiny** přejděte na níže uvedenou cestu. Toto nastavení konfigurace je k dispozici pouze v části **Konfigurace uživatele**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Najít nastavení s názvem **Zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**.

   > [!NOTE]
   > Předchozí verze služby Active Directory mohou toto nastavení uvést pod názvem **Nepovolit automatické dokončování ukládání hesel**. Konfigurace pro toto nastavení se liší od nastavení popsaného v tomto kurzu.

    ![Nezapomeňte se podívat na to v nastavení uživatele](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Klepněte pravým tlačítkem myši na výše uvedené nastavení a vyberte **příkaz Upravit**.
1. V okně s názvem **Zapnout funkci automatického dokončování uživatelských jmen a hesel ve formulářích**vyberte **Možnost Zakázáno**.

    ![Vyberte možnost "Zakázáno" pro funkci automatického dokončování zapnutí](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klepnutím na **tlačítko OK** tyto změny aplikujte a zavřete okno.

Uživatelé již nebudou moci ukládat svá pověření ani používat automatické dokončování pro přístup k dříve uloženým přihlašovacím údajům. Tato zásada však umožňuje uživatelům nadále používat automatické dokončování pro jiné typy polí formuláře, jako jsou například vyhledávací pole.

> [!WARNING]
> Pokud je tato zásada povolena poté, co se *not* uživatelé rozhodli uložit některá pověření, tato zásada nevymaže pověření, která již byla uložena.

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testování nasazení

Podle následujících kroků ověřte, jestli bylo nasazení rozšíření úspěšné:

1. Pokud jste nasadili pomocí **konfigurace počítače**, přihlaste se ke klientském počítači, který patří k hlavní pracovní pouzdravé aktivitě vybrané v [kroku 2: Vytvoření objektu zásad skupiny](#step-2-create-the-group-policy-object). Pokud jste nasadili pomocí **konfigurace uživatele**, ujistěte se, že se přihlásit jako uživatel, který patří do této ou.
1. Může trvat několik přihlášení pro změny zásad skupiny plně aktualizovat s tímto počítačem. Chcete-li aktualizaci vynutit, otevřete okno **příkazového řádku** a spusťte následující příkaz:`gpupdate /force`
1. Chcete-li provést instalaci, je nutné restartovat počítač. Bootup může trvat podstatně déle než obvykle při instalaci rozšíření.
1. Po restartování otevřete **aplikaci Internet Explorer**. V pravém horním rohu okna klikněte na **Nástroje** (ikona ozubeného kola) a potom vyberte **Spravovat doplňky**.
1. V okně **Spravovat doplňky** ověřte, zda bylo **nainstalováno rozšíření přístupového panelu** a zda bylo jeho **stav** nastaven na **hodnotu Povoleno**.

   ![Ověření, zda je nainstalováno a povoleno rozšíření přístupového panelu](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Další informace

* [Přístup k aplikacím a jednotné přihlašování pomocí služby Azure Active Directory](what-is-single-sign-on.md)
* [Poradce při potížích s rozšířením přístupového panelu pro aplikaci Internet Explorer](manage-access-panel-browser-extension.md)
