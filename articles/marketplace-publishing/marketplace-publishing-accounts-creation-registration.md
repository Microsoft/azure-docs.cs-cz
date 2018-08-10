---
title: Vytvoření a registrace vydavatelského účtu | Dokumentace Microsoftu
description: Pokyny pro vytvoření účtu Microsoft Developer, takže po schválení se můžete prodávat různé nabídky typy na webu Azure Marketplace.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715654"
---
# <a name="create-a-microsoft-developer-account"></a>Vytvoření účtu Microsoft Developer
Tento článek vás provede vytváření nezbytné účtu a proces registrace schválené Microsoft Developer pro Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Vytvoření účtu Microsoft
Chcete-li zahájit proces publikování, musíte vytvořit účet Microsoft. Tento účet se použije k registraci v obou **Microsoft Developer Center** a **portál publikování Azure**. Pro svoje nabídky na webu Azure Marketplace byste měli mít jenom jeden účet Microsoft. Neměl by být specifické pro služeb nebo nabídky.

Adresa, která tvoří uživatelské jméno by měly být ve vaší doméně a řídit váš IT tým. Pomocí tohoto účtu se má počítat publikování souvisejících aktivit.

> [!WARNING]
> Slova, jako jsou **"Azure"** a **"Microsoft"** nejsou podporovány pro registraci účtu Microsoft. Vyhněte se použití těchto slov a dokončete vytváření účtů a proces registrace.
>
>

### <a name="guidelines-for-company-accounts"></a>Pokyny pro firemní účty
Při vytváření účtu společnosti, postupujte podle následujících pokynů, pokud pro přístup k účtu po přihlášení pomocí účtu Microsoft, které se otevřelo účet bude potřebovat víc než jedna osoba.

> [!Important]
> Důležité, pokud chcete povolit více uživatelů pro přístup k účtu Dev Center, doporučujeme, abyste pomocí Azure Active Directory pro přiřazení rolí pro jednotlivé uživatele, kteří můžou přístup k účtu, když se přihlásíte pomocí svých osobních přihlašovacích údajů Azure AD. Další informace najdete v tématu [spravovat uživatele účtu](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Vytvoření účtu Microsoft pomocí e-mailovou adresu, která patří do domény vaší společnosti, ale ne k někdo – například windowsapps@fabrikam.com.
* Omezit přístup k tomuto účtu Microsoft na nejmenší možný počet vývojářů.
* Nastavení firemního e-mailu distribučního seznamu, který zahrnuje všem uživatelům, kteří potřebují přístup k účtu pro vývojáře a přidat tuto e-mailovou adresu ke svým bezpečnostním údajům. To umožňuje všichni zaměstnanci v seznamu a získat zabezpečovací kódy, pokud nepotřebujete spravovat bezpečnostní údaje účtu Microsoft. Pokud nastavení distribučního seznamu není proveditelné, vlastník jednotlivé e-mailový účet se musí být dostupné pro přístup k a sdílet kód zabezpečení po zobrazení výzvy (například při přidání nové bezpečnostní údaje k účtu nebo když musí být přistupovat z nového zařízení).
* Přidáte telefonní číslo společnosti, který nevyžaduje, aby rozšíření a je přístupné členům týmu klíče.
* Obecně platí mají vývojáři používat důvěryhodná zařízení pro přihlášení k účtu vaší společnosti pro vývojáře. Všichni členové týmu klíče by měl mít přístup k těmto důvěryhodná zařízení. Tím se sníží potřebu zabezpečovací kódy, které má být odeslána při přístupu k účtu.
* Pokud je potřeba povolit přístup k účtu z nedůvěryhodné počítače, omezte, která přístup k maximálně pět vývojářů. V ideálním případě tyto vývojáře by měla přístup k účtu z počítače, které sdílejí stejné zeměpisné a síťové umístění.
* Informace o zabezpečení vaší společnosti na často zkontrolujte [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) k Ujistěte se, že se všechny aktuální.

Váš účet vývojáře by měla přístupná především z důvěryhodných počítačů. To je důležité, protože není omezený počet kódů generovaných každý účet za týden. Taky umožňuje bezproblémové přihlašování.

Další informace o zabezpečení a další vývojářské účet pokyny, klikněte na tlačítko [tady](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Pokyny
1. Otevřete nový Chrome Incognito nebo Internet Explorer se službou InPrivate relaci procházení k zajištění, že nejste přihlášení k existujícímu účtu.
2. Registraci e-mailu (podle pokynů výše například windowsapp@fabrikam.com) jako účet Microsoft pomocí odkazu [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Postupujte podle pokynů níže.

   1. Během registrace svého účtu jako účet Microsoft, je třeba zadat platné telefonní číslo pro systém na poslat ověřovací kód účtu jako textovou zprávu nebo automatizovaného hovoru.
   2. Během registrace svého účtu jako účet Microsoft, budete muset zadat id platné e-mailu pro příjem automatizovaných e-mailu pro ověření účtu.
3. Ověřit e-mailovou adresu odeslání do distribučního seznamu.
4. Teď jste připraveni používat nový účet Microsoft na webu Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Zaregistrujte svůj účet ve službě Microsoft Developer Center
Microsoft Developer Center, se použije k registraci informací o společnosti jednou. Žadateli o registraci musí být platný zástupce společnosti a musíte zadat svoje osobní údaje jako způsob, jak ověřit svoji identitu. Osoba registrace musíte použít účet Microsoft, jež jsou sdílena pro společnosti, **a musí používat stejný účet na portálu publikování Azure.** Měli byste zkontrolovat zajistit, aby že vaše společnost ještě nemá účet Microsoft Developer Center před dalším pokusem ho vytvořit. Během procesu budeme shromažďovat informace o adrese společnosti, informace o bankovním účtu a daňové údaje. Ty se obvykle dají získat od finančních nebo podnikových kontaktů.

> [!IMPORTANT]
> Pokud chcete po absolvování různých fází nabídky vytváření a nasazování, je třeba provést následující součásti profil pro vývojáře.
>
>

| Profil vývojáře | Spuštění nástroje draft | Fázování | Publikování zdarma a šablony řešení | Publikovat obchodní |
| --- | --- | --- | --- | --- |
| Registrace společnosti |Musí mít |Musí mít |Musí mít |Musí mít |
| Daňové identifikační číslo profilu |Nepovinné |Nepovinné |Nepovinné |Musí mít |
| Bankovním účtu |Nepovinné |Nepovinné |Nepovinné |Musí mít |

> [!NOTE]
> Používání vlastní licence (BYOL) se podporuje jenom pro virtuální počítače a je považován za **bezplatné** nabídky.
>
>

### <a name="register-your-company-account"></a>Zaregistrujte svůj účet společnosti
1. Otevřete nový Internet Explorer InPrivate nebo Incognito Chrome relaci procházení k zajištění, že nejste přihlášení k osobního účtu.
2. Přejděte na [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) sami zaregistrovat jako prodejce v Centru pro vývojáře. Přečtěte si prosím následující důležitá Poznámka: než budete pokračovat.

   > [!IMPORTANT]
   > Zajistěte, aby byl na e-mailu id nebo distribuční seznam (distribuční seznam doporučujeme odebrat závislost jednotlivců) který budete používat pro registraci v Centru pro vývojáře v nejprve zaregistrovat jako účet Microsoft. Pokud ne, pak zaregistrujte prosím pomocí tohoto [odkaz](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Navíc **všechny e-mailem id v rámci domény společnosti Microsoft tedy @microsoft.com nelze použít** pro vývojáře pro registraci.
   >
   >

    ![Kreslení][img-signin]
3. Dokončete průvodce "Pomozte nám chránit váš účet", který ověří vaši identitu prostřednictvím telefonního čísla nebo e-mailové adresy.

    ![Kreslení][img-verify]
4. V části "Informace o registraci účtu" vyberte váš **účtu země/oblast** z rozevírací nabídky.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Zákazník z" zemí:** Pokud chcete prodávat vaše služby na Azure Marketplace, musí být z jedné ze schválených "prodej z" zemí výše registrované entity. Toto omezení je platební a daňové z důvodů. Těšíme se aktivně na tento seznam zemí, rozbalte v blízké budoucnosti, tak si Nenechte ujít. Další informace najdete v tématu [zásady účasti na webu Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Vyberte typ"účet" jako **společnosti** a potom klikněte na tlačítko **Další** tlačítko.

   > [!IMPORTANT]
   > Pro lepší pochopení typů účtů a které je pro vás k výběru nejvhodnější, podívejte se prosím stránku [typy, umístění a poplatky za účet](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Zadejte **zobrazovaný název vydavatele**, obvykle název vaší společnosti.

   > [!TIP]
   > Zobrazovaný název vydavatele zadaný v Centru pro vývojáře se nezobrazí na webu Azure Marketplace, jakmile dostane vaši nabídku uvedené. Ale to je nutné zadat pro dokončení procesu registrace.
   >
   >
7. Zadejte **kontaktní informace** pro ověření účtu.

   > [!IMPORTANT]
   > Je nutné zadat přesné kontaktní informace, protože ho se použije v našich proces ověření pro vaši společnost, chcete-li možné ji schválit v Centru pro vývojáře.
   >
   >
8. Zadejte kontaktní informace **společnosti schvalovatel**. Schvalovatel společnosti je osoba, která můžete ověřit, že máte oprávnění k vytvoření účtu služby v Centru pro vývojáře jménem svojí organizace. Klikněte na **Další** přesunete **"Platby oddíl"** Jakmile budete hotovi.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Zadejte své platební údaje platí pro váš účet. Pokud máte propagační kód, které zahrnují náklady na registrace, zadejte, který tady. V opačném případě poskytnete informace o platební kartu (nebo PayPal v podporované trhy). Až budete hotovi, klikněte na tlačítko **Další** přesunout do **"Kontrola obrazovky"**.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Projděte si informace o účtu a potvrďte, že je všechno správně. Potom, přečtěte si a přijměte podmínky a ujednání o [smlouvy vydavatele Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Zaškrtněte políčko označující vám přečetli a přijali tyto podmínky.
11. Klikněte na tlačítko **Dokončit** potvrďte svou registraci. Pošleme vám zprávu potvrzení e-mailovou adresu.
12. Pokud máte v úmyslu publikovat pouze bezplatných nabídek, klikněte na tlačítko **přejděte na portál publikování Azure Marketplace** a můžete přeskočit k části 3 tohoto dokumentu [registraci účtu na portálu publikování](#3-register-your-account-in-the-publishing-portal).

Pokud máte v úmyslu publikovat obchodní nabízí (např. virtuální počítač nabídek s modelem hodinové fakturace), klikněte na tlačítko **aktualizovat informace o vašem účtu** kde je nutné vyplnit daňové a bankovní informace ve svém účtu centra vývojářů.

Pokud budete chtít aktualizovat daňové a bankovní informace později, pak můžete přejít k další části, to znamená, část 3 tohoto dokumentu [registraci účtu na portálu publikování](#3-register-your-account-in-the-publishing-portal)a později pomocí odkazů v publikování Azure Portál.

> [!IMPORTANT]
> V případě obchodní nabídky nebudete moct přejít do produkčního prostředí svoje nabídky bez dokončení daňové a bankovní informace.
>
>

Pokud chcete aktualizovat daňové a bankovní informace později, můžete přejít do části 3, [registraci účtu na portálu publikování](#3-register-your-account-in-the-publishing-portal)a později pomocí odkazů na portálu publikování Azure.

### <a name="add-tax-and-banking-information"></a>Přidat daňové a bankovní informace
 Pokud chcete publikovat obchodní nabídky k nákupu, musíte také přidat platební a daňové údaje a odeslat je k ověření v Centru pro vývojáře. Pokud budete publikovat pouze bezplatných nabídek (nebo nabízí BYOL), není potřeba tyto informace přidat. Můžete přidat později, ale to ověřit informace o daň za nějakou dobu trvá. Pokud víte, že bude nabízet komerční nabídky k nákupu, doporučujeme jej přidat co nejdříve.

**Bankovní informace**

1. Přihlaste se k [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) s vaším účtem Microsoft.
2. Klikněte na tlačítko **výběr účtu** v nabídce vlevo v části **zvolte způsob platby** klikněte na tlačítko **bankovním účtu** nebo **PayPal**.

   > [!IMPORTANT]
   > Pokud budete mít obchodní nabídky, které zákazníci zakoupit na webu Marketplace, jedná se o účet, ve kterém obdržíte výběr pro tyto nákupy.
   >
   >
3. Zadejte informace o platbě a klikněte na tlačítko **Uložit** až budete spokojení.

   > [!IMPORTANT]
   > Pokud je potřeba aktualizovat nebo změnit váš výběr účtu, postupujte podle stejných kroků výše, nahraďte aktuální informace o nové informace o. Změna účtu výběr zpozdit vaší platby podle až jeden cyklus platby. Toto zpoždění dochází, protože budeme potřebovat ověřit změnu účtu stejně jako při prvním nastavování účtu výběr. Budete stále platby za celou částku po váš účet byl ověřen; všechny platby z důvodu pro aktuální platební cyklu přibudou další.
   >
   >
4. Klikněte na **Další**.

**Informace o daních**

1. Přihlaste se k [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) pomocí svého účtu Microsoft (v případě potřeby).
2. Klikněte na tlačítko **daňové profilu** v levé nabídce.
3. Na **nastavit váš formulář** stránky, vyberte zemi nebo oblast, kde máte trvalý rezidenci a pak vyberte zemi nebo oblast, kde podržte primární citizenship. Klikněte na **Další**.
4. Zadejte své daňové údaje a pak klikněte na tlačítko **Další**.

> [!WARNING]
> Nebude moct přejít do produkčního prostředí, vaše komerční nabízí bez dokončení daňové a bankovní informace ve vašem účtu Microsoft Developer Center.
>
>

Pokud máte problémy s registrací Developer Center, přihlaste se prosím na lístku podpory jak je uvedeno níže

1. Přejděte na odkaz na podporu [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. V části **kontaktujte nás** části, klikněte na tlačítko **incident odeslat** (jak je znázorněno na snímku obrazovky níže)

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Zvolte možnost "Pomoct s Dev Center" jako **typ problému** a "publikovat a spravovat aplikace" jako **kategorie**. Potom klikněte na tlačítko "Start e-mailu".

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Budete mít k dispozici na přihlašovací stránce. Použití jakékoli přihlašovací účet Microsoft. Pokud nemáte účet Microsoft, vytvořte ji pomocí to [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Vyplňte podrobnosti o problému a subit-the-ticket kliknutím na **odeslat** tlačítko.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Zaregistrujte svůj účet na portálu publikování
[Portál pro publikování](http://publish.windowsazure.com) slouží k publikování a správě vašich nabídek.

1. Otevřete nový Chrome Incognito nebo Internet Explorer se službou InPrivate relaci procházení k zajištění, že nejste přihlášení k osobního účtu.
2. Přejděte do [ (Nastavení)http://publish.windowsazure.com](http://publish.windowsazure.com) (Integrace a služby).
3. Pokud jste nový uživatel a přihlášení k publikování portal poprvé, pak musíte se přihlásit pomocí stejné id e-mailu, pomocí kterého je registrována účtu Dev Center. Tímto způsobem se mezi sebou propojit účtu Dev Center a portálu účet pro publikování. Později přidáte ostatní členy podíváme na společnost, kteří pracují na aplikace, jako spolupracujícího Správce publikování portálu podle následujících kroků.

Pokud jsou přidány jako spolupracujícího Správce publikování portálu, pak se můžete přihlásit pomocí účtu spolusprávce.

> [!TIP]
> Na jsou popsané zásadách pro účast [web Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Postup přidání spolusprávce v publikování portálu
**Za předpokladu, že jste správce,** níže uvedené kroky pro přidání spolusprávce.

> [!NOTE]
> **Pro nové uživatele** předtím, než přidáte spolusprávce v publikování portál, ujistěte se, že jste vytvořili aspoň jednu aplikaci v publikování portálu. To je potřeba jako **VYDAVATELÉ** kartě se zobrazí pouze po vytvoření alespoň jednu aplikaci v publikování portálu.
>
>

1. Ujistěte se, že je id e-mailu spolusprávce Microsoft account(MSA). Pokud ne, zaregistrujte ho jako MSA použití této funkce [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Ujistěte se, že existuje alespoň jedna aplikace pod účtem správce, než se pokusíte o přidání spolusprávce.
3. Po dokončení výše uvedené kroky, přihlaste se k publikování s id e-mailu spolupracujícího správce a přihlaste se na portálu.
4. Nyní Přihlaste se k publikování portálu s id e-mailu správce.
5. Přejděte na vydavatelé -> vyberte váš účet -> správci -> Přidat spolusprávce (snímek obrazovky níže uvedené)

   ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Kroky pro odstranění spolupracujícího Správce publikování portálu
**Za předpokladu, že jste správce,** uvedena níže jsou kroky pro odstranění spolusprávce.

1. Přihlaste se k publikování portálu s id e-mailu správce.
2. Přejděte do **vydavatelé** -> vyberte váš účet -> **správci** -> **Spolusprávci**.
3. Klikněte na **X** tlačítko vedle spolusprávce chcete odstranit tot (níže uvedeném snímku obrazovky).

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Další postup
Teď, když váš účet je vytvořen a zaregistrován, zajistěte splnění nebo splňovat všechna netechnické požadavků k publikování vaší nabídky kontrolou [netechnické požadavky](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
