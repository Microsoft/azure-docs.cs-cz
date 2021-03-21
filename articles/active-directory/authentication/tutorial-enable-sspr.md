---
title: Povolit Azure Active Directory Samoobslužné resetování hesla
description: V tomto kurzu se naučíte povolit Azure Active Directory Samoobslužné resetování hesla pro skupinu uživatelů a otestovat proces resetování hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd1a68b06814d13c386b873ed715f3b03a7b827
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198485"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Kurz: povolení odemknutí účtu nebo resetování hesla uživateli pomocí samoobslužného resetování hesla Azure Active Directory

Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD) umožňuje uživatelům změnit nebo resetovat svoje heslo bez zásahu správce ani helpdesku. Pokud je účet uživatele uzamčený nebo zapomněl heslo, může postupovat podle výzev ke zrušení odblokování a opětovnému získání práce. Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci. Tady je video o [tom, jak nakonfigurovat a povolit samoobslužné resetování hesla ve vašem tenantovi](https://www.youtube.com/watch?v=rA8TvhNcCvQ) (**doporučeno**). K dispozici je také video pro správce IT, které vám pomůžou [vyřešit šest nejběžnějších chybových zpráv koncových uživatelů pomocí SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> V tomto kurzu se dozvíte správce, jak povolit samoobslužné resetování hesla. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, pokračujte na https://aka.ms/sspr .
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Konfigurace metod ověřování a možností registrace
> * Testování procesu SSPR jako uživatel

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD s povolenou aspoň Azure AD Free nebo zkušební licencí. Na úrovni Free SSPR funguje jenom pro cloudové uživatele v Azure AD.
    * Pro pozdější kurzy v této sérii se pro zpětný zápis hesla vyžaduje Azure AD Premium P1 nebo zkušební licence.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce* .
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. Pomocí tohoto účtu v tomto kurzu otestujete prostředí SSPR pro koncové uživatele.
    * Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Skupina, pro kterou je uživatel bez oprávnění správce členem, například *SSPR-test-Group*. V tomto kurzu povolíte SSPR pro tuto skupinu.
    * Pokud potřebujete vytvořit skupinu, přečtěte si téma Postup [Vytvoření skupiny a přidání členů v Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Azure AD umožňuje povolit SSPR pro *žádné*, *vybrané* nebo *všechny* uživatele. Tato podrobná možnost umožňuje vybrat podmnožinu uživatelů k otestování procesu registrace a pracovního postupu SSPR. Až budete s tímto procesem spokojeni a můžete tyto požadavky sdělit širší skupině uživatelů, můžete vybrat skupinu uživatelů, které chcete povolit pro SSPR. Nebo můžete povolit SSPR pro všechny uživatele v tenantovi Azure AD.

> [!NOTE]
>
> Pro SSPR se v tuto chvíli dá povolit jenom jedna skupina Azure AD pomocí Azure Portal. V rámci širšího nasazení SSPR se podporují vnořené skupiny. Ujistěte se, že uživatelé ve skupinách, které jste zvolili, mají přiřazené příslušné licence. V tuto chvíli neexistují žádné ověřovací procesy těchto licenčních požadavků.

V tomto kurzu nakonfigurujte SSPR pro skupinu uživatelů ve skupině testů. V následujícím příkladu se používá skupina *SSPR-test-Group* . Podle potřeby zadejte svou vlastní skupinu Azure AD:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce* .
1. Vyhledejte a vyberte **Azure Active Directory** a potom v nabídce na levé straně zvolte **resetování hesla** .
1. Na stránce **vlastnosti** v části možnost *Samoobslužné resetování hesla povoleno* vyberte **Vybrat skupinu** .
1. Vyhledejte a vyberte skupinu Azure AD, například *SSPR-test-Group*, a pak zvolte *Vybrat*.

    [![Vyberte skupinu v Azure Portal pro povolení samoobslužného resetování ](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) hesla.](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Pokud chcete povolit SSPR pro vybrat uživatele, vyberte **Uložit**.

## <a name="select-authentication-methods-and-registration-options"></a>Výběr metod ověřování a možností registrace

Když uživatelé potřebují odemknout svůj účet nebo resetovat heslo, zobrazí se jim výzva k další metodě potvrzení. Tento přídavný faktor ověřování zajišťuje, že jsou dokončeny pouze schválené události SSPR. Můžete zvolit, které metody ověřování mají být povoleny, na základě registračních informací, které uživatel poskytne.

1. Na stránce **metody ověřování** v nabídce na levé straně nastavte **počet metod požadovaných k resetování** na *1*.

    Pro zvýšení zabezpečení můžete zvýšit počet metod ověřování požadovaných pro SSPR.

1. Vyberte **metody dostupné pro uživatele** , které chce vaše organizace dovolit. Pro tento kurz zaškrtněte políčka pro povolení následujících metod:

    * *Oznámení mobilní aplikace*
    * *Kód mobilní aplikace*
    * *E-mail*
    * *Mobilní telefon*

    Další metody ověřování, jako je třeba *telefon do kanceláře* nebo *bezpečnostní otázky*, můžete podle potřeby povolit, aby vyhovovaly vašim obchodním požadavkům.

1. Chcete-li použít metody ověřování, vyberte možnost **Uložit**.

Aby mohli uživatelé odemknout svůj účet nebo resetovat heslo, musí si zaregistrovat svoje kontaktní údaje. Tyto kontaktní informace se používají pro různé metody ověřování nakonfigurované v předchozích krocích.

Správce může tyto kontaktní údaje zadat ručně, nebo může přejít na registrační portál a poskytnout tak informace sami. V tomto kurzu nakonfigurujete uživatelům, aby se při příštím přihlášení vyzváni k registraci.

1. Na **registrační** stránce v nabídce na levé straně vyberte *Ano* , **Pokud chcete, aby se uživatelé zaregistrovali při přihlašování**.
1. Je důležité, aby byly kontaktní údaje udržovány v aktuálním stavu. Pokud jsou kontaktní informace zastaralá, když se spustí událost SSPR, uživatel nemusí být schopný odemknout svůj účet nebo resetovat heslo.

    Nastavte **Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací** na *180*.
1. Chcete-li použít nastavení registrace, vyberte možnost **Uložit**.

## <a name="configure-notifications-and-customizations"></a>Konfigurace oznámení a přizpůsobení

Pokud chcete, aby uživatelé měli informace o aktivitě účtu, můžete nakonfigurovat e-mailová oznámení, která se budou posílat, když dojde k události SSPR. Tato oznámení můžou pokrývat jak běžné uživatelské účty, tak účty správců. V případě účtů správců poskytuje toto oznámení další úroveň povědomí, když se resetuje heslo privilegovaného účtu správce pomocí SSPR. Pokud se SSPR použije v účtu správce, budou všichni globální správci upozorněni.

1. Na stránce **oznámení** v nabídce na levé straně nakonfigurujte následující možnosti:

   * Možnost **Upozornit uživatele na resetování hesla** nastavte na *Ano*.
   * Možnost **Upozornit všechny správce na resetování hesla jiného správce** nastavte na *Ano*.

1. Chcete-li použít předvolby oznámení, vyberte možnost **Uložit**.

Pokud uživatelé potřebují další nápovědu k procesu SSPR, můžete upravit odkaz na "kontaktujte správce". Tento odkaz se používá v procesu registrace SSPR a uživatel odemkne svůj účet nebo obnoví heslo. Abyste se ujistili, že uživatelé potřebují potřebnou podporu, důrazně doporučujeme zadat e-mail nebo adresu URL vlastního helpdesku.

1. Na stránce **vlastní nastavení** v nabídce na levé straně nastavte *odkaz přizpůsobit Helpdesk* na **Ano**.
1. V poli **e-mail nebo adresa URL vlastního helpdesku** zadejte e-mailovou adresu nebo adresu URL webové stránky, kde mohou uživatelé získat další pomoc z vaší organizace, například *`https://support.contoso.com/`*
1. Chcete-li použít vlastní odkaz, vyberte možnost **Uložit**.

## <a name="test-self-service-password-reset"></a>Testování samoobslužného resetování hesla

S povoleným a nakonfigurovaným SSPR otestujte proces SSPR s uživatelem, který je součástí skupiny, kterou jste vybrali v předchozí části, například *test-SSPR-Group*. V následujícím příkladu je použit účet *testuser* . V první části tohoto kurzu zadejte vlastní uživatelský účet, který je součástí skupiny, kterou jste povolili pro SSPR.

> [!NOTE]
> Při testování samoobslužného resetování hesla použijte účet bez oprávnění správce. Ve výchozím nastavení jsou správcům povoleny Samoobslužné resetování hesla a jsou vyžadovány k resetování hesla pomocí dvou metod ověřování. Další informace najdete v tématu [rozdíly v zásadách resetování správců](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Chcete-li zobrazit proces Ruční registrace, otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte na adresu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Uživatelé by měli být přesměrováni na tento registrační portál při příštím přihlášení.
1. Přihlaste se pomocí testovacího uživatele bez oprávnění správce, jako je například *testuser*, a zaregistrujte kontaktní informace metod ověřování.
1. Po dokončení vyberte tlačítko s označením **vypadá dobře** a zavřete okno prohlížeče.
1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte na [https://aka.ms/sspr](https://aka.ms/sspr) .
1. Zadejte informace o účtu nesprávce testovacích uživatelů, jako je například *testuser*, znaky z CAPTCHA a pak vyberte **Další**.

    ![Zadání informací o uživatelském účtu pro resetování hesla](media/tutorial-enable-sspr/password-reset-page.png)

1. Postupujte podle pokynů k resetování hesla. Po dokončení byste měli obdržet e-mailové oznámení o resetování hesla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V následujícím kurzu tohoto seriálu nakonfigurujete zpětný zápis hesla. Tato funkce zapisuje změny hesla z Azure AD SSPR zpátky do místního prostředí služby AD. Pokud chcete pokračovat v této sérii kurzů a nakonfigurovat zpětný zápis hesla, zakažte SSPR nyní.

Pokud už nechcete používat funkci SSPR, kterou jste nakonfigurovali v rámci tohoto kurzu, nastavte stav SSPR na **none** pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte **Azure Active Directory** a potom v nabídce na levé straně zvolte **resetování hesla** .
1. Na stránce **vlastnosti** v části možnost *Samoobslužné resetování hesla povoleno* vyberte možnost **žádná**.
1. Pokud chcete použít změnu SSPR, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili Samoobslužné resetování hesla služby Azure AD pro vybranou skupinu uživatelů. Naučili jste se:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Konfigurace metod ověřování a možností registrace
> * Testování procesu SSPR jako uživatel

> [!div class="nextstepaction"]
> [Povolení vícefaktorového ověřování Azure AD](./tutorial-enable-azure-mfa.md)
