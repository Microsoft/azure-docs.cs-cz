---
title: Nasazení rozšíření Panel přístupu Azure pro aplikaci Internet Explorer pomocí objektu zásad skupiny | Microsoft Docs
description: Postup nasazení aplikace Internet Explorer rozšíření pro portál Moje aplikace pomocí zásad skupiny.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a39e454bd0993f07efd1168404df453f3013e0fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341002"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Postup nasazení rozšíření Panel přístupu pro Internet Explorer pomocí zásad skupiny
Tento kurz ukazuje, jak provést vzdálenou instalaci rozšíření Panel přístupu pro Internet Explorer na počítačích uživatelů pomocí zásad skupiny. Toto rozšíření je pro Internet Explorer uživatelů, kteří potřebují pro přihlášení do aplikace, které jsou nakonfigurované pomocí [založené na heslech jednotné přihlašování](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on).

Doporučujeme, aby správci automatizovat nasazení tohoto rozšíření. Uživatelé, jinak hodnota mít ke stažení a instalaci rozšíření sami, který je pravděpodobnost chyby uživatele a musí mít oprávnění správce. Tento kurz se zaměřuje na jednu z metod automatizaci nasazení softwaru pomocí zásad skupiny. [Další informace o zásadách skupiny.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozšíření přístupový Panel je také k dispozici pro [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ani jedno, které vyžadují oprávnění správce pro instalaci.

## <a name="prerequisites"></a>Požadavky
* Jste nastavili [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), a vaši uživatelé počítačů se připojili k vaší doméně.
* Musíte mít oprávnění "Upravit nastavení" k úpravě objektu zásad skupiny (GPO). Ve výchozím nastavení, toto oprávnění mají členy z těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Group Policy Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Vytvoření distribučního bodu
Balíček Instalační služby musíte nejprve umístit na umístění v síti, která je přístupná na počítače, které chcete vzdáleně nainstalovat rozšíření na. Postupujte přitom takto:

1. Přihlaste se k serveru jako správce
2. V **správce serveru** okno, přejděte na **soubory a služby úložiště**.
   
    ![Otevřené soubory a služby úložiště](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Přejděte na **sdílené složky** kartě. Pak klikněte na tlačítko **úlohy** > **Nová sdílená složka...**
   
    ![Otevřené soubory a služby úložiště](./media/active-directory-saas-ie-group-policy/shares.png)
4. Dokončení **Průvodce vytvořením nové sdílené složky** a nastavit oprávnění a ujistěte se, že byla přístupná z vašich uživatelů počítačů. [Další informace o sdílených složek.](https://technet.microsoft.com/library/cc753175.aspx)
5. Stáhněte si následující balíček Instalační služby systému Windows (soubor MSI): [Extension.msi Panel přístupu](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. Zkopírujte instalační balíček do požadovaného umístění na sdílené složky.
   
    ![Zkopírujte soubor .msi ke sdílené složce.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Ověřte, zda jsou klientské počítače moct přistupovat ze sdílené složky je balíček Instalační služby. 

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Vytvoření objektu zásad skupiny
1. Přihlaste se k serveru, který je hostitelem vaší instalace služby Active Directory Domain Services (AD DS).
2. Ve Správci serveru přejděte na **nástroje** > **Správa zásad skupiny**.
   
    ![Přejděte na Nástroje > Managment zásad skupiny](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. V levém podokně **Správa zásad skupiny** okně zobrazení hierarchie organizační jednotky (OU) a zjistit, v oboru, který chcete použít zásady skupiny. Například můžete rozhodnout pro vyberte malé organizační jednotky k nasazení na několik uživatelů pro testování, nebo vyberete nejvyšší úrovně organizační jednotky k nasazení do celé organizace.
   
   > [!NOTE]
   > Pokud chcete vytvořit nebo upravit organizačních jednotkách (OU), přepněte zpět do správce serveru a přejděte k **nástroje** > **Active Directory Users and Computers**.
   > 
   > 
4. Jakmile vyberete organizační jednotku, pravým tlačítkem myši a vyberte **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...**
   
    ![Vytvořit nový objekt zásad skupiny](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. V **nový objekt zásad skupiny** řádku, zadejte název pro nový objekt zásad skupiny.
   
    ![Název nového objektu zásad skupiny](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Klikněte pravým tlačítkem na objekt zásad skupiny, který jste vytvořili a vyberte **upravit**.
   
    ![Upravit nový objekt zásad skupiny](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Přiřaďte instalačního balíčku
1. Určete, jestli chcete nasadit rozšíření na základě **konfigurace počítače** nebo **konfigurace uživatele**. Při použití [konfigurace počítače](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), rozšíření je nainstalován v počítači bez ohledu na to, které je uživatelům přihlášení. S [konfigurace uživatele](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), uživatelé mají rozšíření nainstalovat pro ně bez ohledu na počítače, které se přihlašují.
2. V levém podokně **Editor správy zásad skupiny** okno, přejděte na jednu z následujících cestách složku, v závislosti na tom, jaký typ konfigurace jste zvolili:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Klikněte pravým tlačítkem na **instalace softwaru**, pak vyberte **nový** > **balíčku...**
   
    ![Vytvořit nový balíček pro instalaci softwaru](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Přejděte do sdílené složky, která obsahuje balíček Instalační služby z [krok 1: Vytvoření distribučního bodu](#step-1-create-the-distribution-point), vyberte soubor MSI a klikněte na **otevřete**.
   
   > [!IMPORTANT]
   > Pokud je na tomto serveru stejné sdílené složky, ověřte, že přístupu k souboru MSI prostřednictvím sítě cesta k souboru, nikoli cestu k místnímu souboru.
   > 
   > 
   
    ![Vyberte instalační balíček ze sdílené složky.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. V **nasazení softwaru** výzvy, vyberte **přiřazeno** pro metodu nasazení. Pak klikněte na **OK**.
   
    ![Vyberte přiřazeno a potom klikněte na tlačítko OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Rozšíření je nyní nasadit na organizační jednotku, kterou jste vybrali. [Další informace o instalace softwaru zásad skupiny.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Automatické povolení rozšíření pro Internet Explorer
Kromě spuštění Instalační program, každou příponu pro Internet Explorer musí být explicitně povoleno před použitím. Použijte následující postup k povolení rozšíření Panel přístupu pomocí zásad skupiny:

1. V **Editor správy zásad skupiny** okno, přejděte na jednu z následujících cestách, v závislosti na tom, jaký typ konfigurace, který jste zvolili v [krok 3: přiřaďte instalační balíček](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Klikněte pravým tlačítkem na **seznam doplňků**a vyberte **upravit**.
    ![Seznam rozšíření upravte.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. V **seznam doplňků** vyberte **povoleno**. Potom v části **možnosti** klikněte na tlačítko **zobrazit...** .
   
    ![Klikněte na položku Povolit a pak klikněte na zobrazit...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. V **zobrazit obsah** okna, proveďte následující kroky:
   
   1. První sloupec ( **název hodnoty** pole), zkopírujte a vložte následující ID třídy: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Pro druhý sloupec ( **hodnotu** pole), zadejte následující hodnotu: `1`
   3. Klikněte na tlačítko **OK** zavřete **zobrazit obsah** okna.
      
      ![Vyplňte hodnoty, jak je uvedeno výše.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Klikněte na tlačítko **OK** použít změny a zavřete **seznam doplňků** okno.

Rozšíření by měl nyní povolena pro počítače ve vybrané organizační jednotce. [Další informace o použití zásad skupiny k povolení nebo zakázání doplňky aplikace Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (volitelné): zakázat "Zapamatovat heslo" řádku
Když uživatelé přihlášení k weby s využitím rozšíření přístup k panelu, Internet Explorer může zobrazit následující výzva s dotazem, "Chcete uložit heslo?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Pokud chcete zabránit uživatelům v zobrazení tuto výzvu, postupujte podle následujících kroků a zabránit automatické dokončování z zapamatování hesla:

1. V **Editor správy zásad skupiny** okno, přejděte k cestě uvedené níže. Toto nastavení konfigurace je k dispozici v části pouze **konfigurace uživatele**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Najít nastavení s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**.
   
   > [!NOTE]
   > Předchozí verze služby Active Directory může zobrazit seznam toto nastavení s názvem **zakázat automatické dokončování pro ukládání hesel**. Konfigurace tohoto nastavení se liší od nastavení popsané v tomto kurzu.
   > 
   > 
   
    ![Mějte na paměti, a to Hledat v části Nastavení uživatele.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Klikněte pravým tlačítkem na výše uvedené nastavení a vyberte **upravit**.
4. V okně s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**, vyberte **zakázané**.
   
    ![Vyberte zakázat](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Klikněte na tlačítko **OK** tyto změny a zavřete toto okno.

Uživatelé už nebude moct ukládání jejich přihlašovacích údajů nebo jejich použití automatického dokončování pro přístup k dříve uložené přihlašovací údaje. Ale tyto zásady umožňují uživatelům nadále používat automatické dokončování pro jiné typy polí formuláře, například pole hledání.

> [!WARNING]
> Pokud je tato zásada povolena po uživatelé jste se rozhodli uložit některé přihlašovací údaje, zásady budou *není* vymazat přihlašovací údaje, které již byly uloženy.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testování nasazení
Použijte následující postup ověření, pokud rozšíření nasazení bylo úspěšné:

1. Pokud jste nasadili pomocí **konfigurace počítače**, přihlaste se klientský počítač, který patří do organizační jednotky, kterou jste vybrali v [krok 2: vytvoření objektu zásad skupiny](#step-2-create-the-group-policy-object). Pokud jste nasadili pomocí **konfigurace uživatele**, zajistěte, aby se přihlásit jako uživatel, který patří do dané organizační jednotky.
2. To může trvat několik sign in pro zásady skupiny se změní na plně aktualizovat pomocí tohoto počítače. Chcete-li vynutit aktualizaci, otevřete **příkazového řádku** okno a spusťte následující příkaz: `gpupdate /force`
3. Je nutné restartovat počítač pro instalaci proběhla. Spuštění může trvat výrazně déle než obvykle při rozšíření nainstaluje.
4. Po restartování počítače, otevřete **Internet Explorer**. V pravém horním rohu okna, klikněte na tlačítko **nástroje** (ozubené kolečko ikonu) a potom vyberte **spravovat doplňky**.
   
    ![Přejděte na Nástroje > Správa doplňků](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. V **spravovat doplňky** okno, ověřte, zda **rozšíření přístup k panelu** byla nainstalována a že jeho **stav** byla nastavena na **povoleno**.
   
    ![Ověřte, zda rozšíření přístup k panelu je nainstalován a povolen.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Řešení potíží s příponou Panel přístupu pro Internet Explorer](active-directory-saas-ie-troubleshooting.md)

