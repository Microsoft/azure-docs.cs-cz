---
title: "Vytvoření a registrace účtu vydavatele | Microsoft Docs"
description: "Pokyny pro vytvoření účtu Microsoft Developer, takže po schválení můžete prodeje různých nabízejí typy na webu Azure Marketplace."
services: Azure Marketplace
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: mbaldwin
ms.openlocfilehash: da805bead3f6af4ebd0b4b94b30a51ff2abc62dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-microsoft-developer-account"></a>Vytvoření účtu Microsoft Developer
Tento článek vás provede procesem vytvoření nezbytné účtu a procesu registrace se schválené Microsoft Developer pro Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Vytvořit účet Microsoft
Chcete-li zahájit proces publikování, musíte vytvořit účet Microsoft. Tento účet se použije k registraci v zároveň **Microsoft Developer Center** a **publikování portálu Azure**. Měli byste mít pouze jeden účet Microsoft pro vaše nabídky Azure Marketplace. Nesmí být specifické pro služeb nebo nabídky.

Adresa, která tvoří uživatelské jméno musí být ve vaší doméně a řídí váš IT tým. Publikování souvisejících činností musí být prováděno pomocí tohoto účtu.

> [!WARNING]
> Slova, například **"Azure"** a **"Microsoft"** nejsou podporovány pro registraci účtu Microsoft. Nepoužívejte tato slova a dokončete vytváření účtů a proces registrace.
>
>

### <a name="guidelines-for-company-accounts"></a>Pokyny pro firemní účty
Při vytváření účtu společnosti, postupujte podle následujících pokynů, pokud víc než jedna osoba potřebovat přístup k účtu přihlášením pomocí účtu Microsoft, který otevírá účet.

> [!Important]
> Důležité k více uživatelům povolit přístup k účtu Dev Center, vám doporučujeme používat Azure Active Directory přiřadit role pro jednotlivé uživatele, kteří mohou mít přístup k účtu po přihlášení pomocí svých osobních přihlašovací údaje Azure AD. Další informace najdete v tématu [spravovat uživatelé s účtem](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Vytvoření účtu Microsoft pomocí e-mailovou adresu, která patří do domény vaší společnosti, ale nechcete jeden uživatel – třeba windowsapps@fabrikam.com.
* Omezte přístup k tomuto účtu Microsoft na nejmenší možný počet vývojáři.
* Nastavte distribučního seznamu podnikovému e-mailu, který zahrnuje všechny uživatele, kteří potřebují přístup k vývojářský účet a přidat tuto e-mailovou adresu ke svým bezpečnostním údajům. To umožňuje všichni zaměstnanci v seznamu, můžete získat zabezpečovací kódy, v případě potřeby a spravovat bezpečnostní údaje účtu Microsoft. Pokud nastavení distribučního seznamu není vhodná, vlastník jednotlivé e-mailový účet se bude muset být k dispozici pro přístup k a sdílet bezpečnostní kód po zobrazení výzvy (například při přidání nových bezpečnostních údajů k účtu nebo když musí být přístup z nového zařízení).
* Přidejte telefonní číslo společnosti, který nevyžaduje rozšíření a je přístupný pro členy týmu klíče.
* Obecně platí mají vývojáři použít důvěryhodné zařízení k přihlášení k účtu vývojáře vaší společnosti. Všichni členové týmu klíče mají mít přístup k těchto důvěryhodných zařízení. Tím se sníží potřebu zabezpečovací kódy při přístupu k účtu odeslat.
* Pokud potřebujete povolit přístup k účtu z počítače se nedůvěryhodné, omezte této přístup k maximálně pět vývojáři. V ideálním případě tyto vývojáři měli přístup k účtu z počítače, které sdílejí stejné zeměpisné a síťové umístění.
* Často zkontrolovat bezpečnostní údaje vaší společnosti na [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) k zkontrolujte, zda je všechny aktuální.

Vývojářského účtu by měly být dostupné především z důvěryhodných počítačů. To je zásadní, protože existuje omezení počtu kódů generovaných každý účet za týden. Umožňuje také bezproblémové přihlašování uživatelů.

Další informace o zabezpečení a další vývojáře účet pokyny, klikněte na tlačítko [zde](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Pokyny
1. Otevřete na nové Chrome Incognito nebo Internet Explorer InPrivate procházení relace a ujistěte se, že nejste přihlášení k existujícímu účtu.
2. Zaregistrovat e-mailu (na výše uvedené pokyny například windowsapp@fabrikam.com) jako účet Microsoft pomocí odkazu [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Postupujte podle pokynů níže.

   1. Při registraci účtu jako účet Microsoft, je třeba zadat platné telefonní číslo pro systém Odeslat ověřovací kód účtu jako textovou zprávu nebo automatické volání.
   2. Při registraci účtu jako účet Microsoft, je třeba zadat platnou e-mailovou id pro příjem automatizovaných e-mailu pro ověření účtu.
3. Ověřit e-mailovou adresu příjemce DL.
4. Nyní jste připraveni používat nový účet Microsoft na webu Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registrace účtu Microsoft Developer Center
Microsoft Developer Center slouží k registraci jednou informace společnosti. Osob žádajících o registraci musí být platný zástupce společnosti a musíte zadat jejich osobní údaje, jako způsob, jak ověřit svou identitu. Osoba, která registrace musíte použít účet Microsoft, jež jsou sdílena pro společnosti, **a musí se použít stejný účet na portálu Azure publikování.** Byste měli zkontrolovat a ujistěte se, že vaše společnost ještě nemá účet Microsoft Developer Center před dalším pokusem o vytvořit. Během procesu jsme bude shromažďovat informace o adrese společnosti, informace o účtu bank a daňové informace. Ty se obvykle dají získat od finančních nebo podnikových kontaktů.

> [!IMPORTANT]
> Chcete-li průběhu procházení v různých fázích nabídka vytvoření a nasazení, je třeba provést následující součásti profil Developer.
>
>

| Profil pro vývojáře | Chcete-li spustit konceptu | Pracovní | Publikování bezplatné a šablona řešení | Publikování komerční |
| --- | --- | --- | --- | --- |
| Registraci společnosti |Musí mít |Musí mít |Musí mít |Musí mít |
| Daňové číslo profilu |Nepovinné |Nepovinné |Nepovinné |Musí mít |
| Bankovní účet |Nepovinné |Nepovinné |Nepovinné |Musí mít |

> [!NOTE]
> Přineste si vlastní licenci (BYOL) je podporována pouze pro virtuální počítače a je považován za **volné** nabídky.
>
>

### <a name="register-your-company-account"></a>Registrace účtu vaší společnosti
1. Otevřete na nové Internet Explorer InPrivate nebo Incognito Chrome procházení relace a ujistěte se, že nejste přihlášení do osobního účtu.
2. Přejděte na [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) sami zaregistrovat jako prodejce na webu Dev Center. Než budete pokračovat, přečtěte si následující důležité upozornění.

   > [!IMPORTANT]
   > Zajistěte, aby byla na e-mailu id nebo distribuční seznam (distribuční seznam se doporučuje k odebrání závislostí jednotlivce), který budete používat pro registraci na webu Dev Center na první registrován jako účet Microsoft. Pokud ne, pak zaregistrujte pomocí této [odkaz](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Navíc **všechny e-mailu id pod doménu společnosti Microsoft tj @microsoft.com nelze použít** pro registraci Dev Center.
   >
   >

    ![Kreslení][img-signin]
3. Dokončete průvodce "Pomozte nám chránit váš účet", který bude ověřit svoji identitu prostřednictvím telefonní číslo nebo e-mailovou adresu.

    ![Kreslení][img-verify]
4. V části "Informace o registraci účtu" Vyberte vaše **účet země nebo oblast** z rozevírací nabídky.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Zákazník – od" zemích:** k vašim službám v Azure Marketplace prodeje, musí být z jednoho z schválené "zákazník – od" zemí výše vaší registrované entity. Toto omezení je výběr a zdanění z důvodů. Aktivně chceme v blízké budoucnosti rozbalte tento seznam zemí, tak si Nenechte ujít. Další informace najdete v tématu [Marketplace zapojení zásady](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Vyberte typ účtu"" jako **společnosti** a klikněte **Další** tlačítko.

   > [!IMPORTANT]
   > Abyste lépe pochopili typy účtů a která je nejvhodnější pro můžete zvolit, zobrazte stránku [účet poplatků, typy a umístění](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Zadejte **zobrazovaný název vydavatele**, obvykle název vaší společnosti.

   > [!TIP]
   > Zadaný vydavatel zobrazovaný název na webu Dev Center se nezobrazí v Azure Marketplace, jakmile vaši nabídku přejde uvedené. Ale to musí být vyplněna dokončete proces registrace.
   >
   >
7. Zadejte **kontaktní údaje** pro ověření účtu.

   > [!IMPORTANT]
   > Vzhledem k tomu, použije se v našem proces ověření pro vaši společnost schválení v Centru pro vývojáře, je nutné zadat přesné kontaktní informace.
   >
   >
8. Zadejte kontaktní informace na **společnosti schvalovatel**. Schvalovatel společnosti je osoba, která můžete ověřit, že máte oprávnění k vytvoření účtu na webu Dev Center jménem vaší organizace. Klikněte na **Další** přesunout do **"Platebních část"** Jakmile budete hotovi.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Zadejte své údaje platby a zaplatit svůj účet. Pokud máte propagační kód, který obsahuje náklady na registrace, můžete, zadejte sem. Jinak hodnota poskytnete informace o platební kartě (nebo PayPal podporované trzích). Až budete hotovi, klikněte na tlačítko **Další** přesunout na **"Zkontrolujte obrazovky"**.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Informace o účtu zkontrolujte a potvrďte, že je vše v pořádku. Potom, přečtěte si a přijměte podmínky a ujednání tohoto [smlouva Microsoft Azure Marketplace vydavatele](http://go.microsoft.com/fwlink/?LinkID=699560). Zaškrtněte políčko označující máte přečíst a přijmout tyto podmínky.
11. Klikněte na tlačítko **Dokončit** k potvrzení registrace. Potvrzovací zpráva pošleme na vaši e-mailovou adresu.
12. Pokud máte v úmyslu publikovat pouze volné nabídky, klikněte na tlačítko **přejděte na portál Azure Marketplace publikování** a můžete přejít do části 3 tohoto dokumentu [zaregistrovat svůj účet na portálu publikování](#3-register-your-account-in-the-publishing-portal).

Pokud máte v úmyslu publikovat komerční nabízí (např. virtuální počítač nabízí s hodinové fakturační model), klikněte na tlačítko **aktualizace informací o vašem účtu** kde je nutné vyplnit daň a bankovních údajů ve vašem účtu Centrum pro vývojáře.

Pokud chcete aktualizovat informace daň a bank později, pak můžete přesunout k další části tj část 3 v tomto dokumentu [zaregistrovat svůj účet na portálu publikování](#3-register-your-account-in-the-publishing-portal)a potom se později pomocí odkazů v Azure Publishing Portál.

> [!IMPORTANT]
> V případě komerční nabídky nebudete moci push vaší nabídky do produkčního prostředí bez dokončení informace o daň a bank účtu.
>
>

Pokud dáváte přednost aktualizovat informace daň a bank později, můžete přejít do části 3, [zaregistrovat svůj účet na portálu publikování](#3-register-your-account-in-the-publishing-portal)a potom se později pomocí odkazů na portálu Azure publikování.

### <a name="add-tax-and-banking-information"></a>Přidat daň a bankovnictví informace
 Pokud chcete publikovat obchodní nabídky k nákupu, musíte taky přidat výběr a daňové informace a odešlete ji pro ověření v Centru pro vývojáře. Pokud budete publikovat pouze volné nabídky (nebo nabízí BYOL), není potřeba přidat tyto informace. Můžete přidat později, ale ověřit informace o daň nějakou dobu trvá. Pokud jste si jisti, že bude nabízet komerční nabídky k nákupu, doporučujeme jej přidat co nejdříve.

**Informace o bank**

1. Přihlaste se k [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) s vaším účtem Microsoft.
2. Klikněte na tlačítko **výběr účtu** v levé nabídce v části **zvolte způsob platby** klikněte na tlačítko **bankovní účet** nebo **PayPal**.

   > [!IMPORTANT]
   > Pokud máte komerční nabídky, které zákazníci zakoupit na webu Marketplace, jedná se o účet, kterou budete dostávat výběr těchto nákupů.
   >
   >
3. Zadejte informace o platebních a klikněte na tlačítko **Uložit** až budete spokojeni.

   > [!IMPORTANT]
   > Pokud potřebujete aktualizovat nebo změnit váš výběr účtu, postupujte podle stejných kroků výše, nahraďte aktuální informace o nové informace. Změna účtu výběr můžete zpozdit vaše platby až jeden cyklus platba. Toto zpoždění dochází, musíme ověřit změny účtu stejně, jako jsme to udělali při prvním nastavení účtu výběr. Budete stále platby za celou částku po váš účet byl ověřen; žádné platby kvůli aktuální platby cyklus přidá dalšímu.
   >
   >
4. Klikněte na **Další**.

**Informace o daně**

1. Přihlaste se k [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) pomocí účtu Microsoft (v případě potřeby).
2. Klikněte na tlačítko **daně profil** v levé nabídce.
3. Na **nastavení svého formuláře daň** vyberte zemi nebo oblast, ve které máte trvalé sídla a potom vyberte zemi nebo oblast, kde uložení primární přístupem. Klikněte na **Další**.
4. Zadejte podrobnosti o vaší daň a pak klikněte na tlačítko **Další**.

> [!WARNING]
> Nebudete moci push do produkčního prostředí vaší komerční nabízí bez dokončení daň a účet bank informace ve vašem účtu Microsoft Developer Center.
>
>

Pokud máte problémy s registrací středisku pro vývojáře, jak je uvedeno níže prosím protokolu lístek podpory

1. Přejděte na odkaz na podporu [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. V části **kontaktujte nás** části, klikněte na tlačítko **odeslání incidentu** (jak je znázorněno na tomto snímku obrazovky)

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Vyberte "Help s Dev Center" jako **typ problému** a "publikování a Správa aplikací" jako **kategorie**. Potom klikněte na tlačítko "E-mailu Start".

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Bude poskytována s přihlašovací stránkou. Používejte všechny přihlašovací účet Microsoft. Pokud nemáte účet Microsoft, pak vytvořte jednu pomocí této [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Zadejte podrobnosti o problému a subit the-ticket kliknutím na **odeslání** tlačítko.

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registrace účtu na portálu publikování
[Publikování portál](http://publish.windowsazure.com) se používá k publikování a správa tyto nabídky.

1. Otevřete na nové Chrome Incognito nebo Internet Explorer InPrivate procházení relace a ujistěte se, že nejste přihlášení do osobního účtu.
2. Přejděte do [ (Nastavení)http://publish.windowsazure.com](http://publish.windowsazure.com) (Integrace a služby).
3. Pokud jste nového uživatele a přihlášení k publikování portálu poprvé, pak musíte se přihlásit s stejným id e-mailu, se kterým je registrován účtu Dev Center. Tímto způsobem se vzájemně propojit účtu Dev Center a publikování portálu účtu. Ostatním členům společnosti, kteří pracují na aplikaci, můžete později přidat jako spolusprávce v publikační portálu podle následujících kroků.

Pokud jsou přidány jako spolusprávce v publikační portálu, pak se můžete přihlásit pomocí účtu spolusprávce.

> [!TIP]
> Účast zásady jsou popsané na [webu Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Postup pro přidání spolusprávce v publikační portálu
**Za předpokladu, že jste správcem,** níže uvedené kroky přidejte ko-správce.

> [!NOTE]
> **Pro nové uživatele** předtím, než přidáte spolusprávce v publikační portál, ujistěte se, že jste vytvořili aspoň jednu aplikaci v publikační portálu. To je potřeba jako **VYDAVATELŮ** kartě se zobrazují pouze po vytvoření alespoň jedna aplikace v publikační portálu.
>
>

1. Zajistěte, aby byl identifikátor e-mailu spolusprávce account(MSA) společnosti Microsoft. Pokud ne, zaregistrujte ji jako účet, pomocí této [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Zkontrolujte, zda je alespoň jedna aplikace pod účtem správce, než se pokusíte přidat ko-správce.
3. Poté, co se dokončí výše uvedené kroky, přihlášení k publikování portálu s id spolusprávce e-mailu a pak při odhlášení.
4. Teď přihlášení k publikování portálu s id e-mailu správce.
5. Přejděte do vydavatele -> vyberte -> váš účet správce -> Přidat spolusprávce (snímek vypsáni níže)

   ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Postup odstranění spolusprávce v publikační portálu
**Za předpokladu, že jste správcem,** níže uvedené kroky odstranit co správce.

1. Přihlášení k publikování portálu s id e-mailu správce.
2. Přejděte na **vydavatelů** -> vyberte -> váš účet **správci** -> **Spolusprávci**.
3. Klikněte na **X** tlačítko vedle spolusprávce chcete odstranit tot (snímek vypsáni níže).

    ![Kreslení](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Další kroky
Teď, když váš účet je vytvořená a zaregistrovaná, zajistěte splnění nebo splňovat všechny požadavky netechnické publikování vaši nabídku kontrolou [netechnické předpoklady](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)

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
