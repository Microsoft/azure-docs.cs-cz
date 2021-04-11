---
title: Povolit Azure Active Directory Samoobslužné resetování hesla
description: V tomto kurzu se naučíte povolit Azure Active Directory Samoobslužné resetování hesla pro skupinu uživatelů a otestovat proces resetování hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075162"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Kurz: povolení odemknutí účtu nebo resetování hesla uživateli pomocí samoobslužného resetování hesla Azure Active Directory

Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD) umožňuje uživatelům změnit nebo resetovat svoje heslo bez zásahu správce ani helpdesku. Pokud Azure AD uzamkne uživatelský účet nebo zapomene heslo, může postupovat podle výzev ke zrušení odblokování a návratu do práce. Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci. Doporučujeme toto video o [tom, jak povolit a nakonfigurovat SSPR ve službě Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). K dispozici je také video pro správce IT, které vám pomůžou [vyřešit šest nejběžnějších chybových zpráv koncových uživatelů pomocí SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> V tomto kurzu se dozvíte správce, jak povolit samoobslužné resetování hesla. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, vraťte se na stránku [Microsoft Online resetování hesla](https://passwordreset.microsoftonline.com/) .
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Nastavení metod ověřování a možností registrace
> * Testování procesu SSPR jako uživatel

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD, který má povolenou aspoň bezplatnou nebo zkušební licenci Azure AD. Na úrovni Free SSPR funguje jenom pro cloudové uživatele v Azure AD.
    * Pro pozdější kurzy v této sérii budete potřebovat Azure AD Premium P1 nebo zkušební licenci pro místní zpětný zápis hesla.
    * V případě potřeby si [můžete zdarma vytvořit účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce* .
* Uživatel bez oprávnění správce s heslem, které znáte, jako například *testuser*. Pomocí tohoto účtu v tomto kurzu otestujete prostředí SSPR pro koncové uživatele.
    * Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Skupina, které uživatel bez oprávnění správce je členem, například *SSPR-test-Group*. V tomto kurzu povolíte SSPR pro tuto skupinu.
    * Pokud potřebujete vytvořit skupinu, přečtěte si téma [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Azure AD umožňuje povolit SSPR pro *žádné*, *vybrané* nebo *všechny* uživatele. Tato podrobná možnost umožňuje vybrat podmnožinu uživatelů k otestování procesu registrace a pracovního postupu SSPR. Až budete s tímto procesem spokojeni a čas je správný pro sdělování požadavků s širší skupinou uživatelů, můžete vybrat skupinu uživatelů, které chcete povolit pro SSPR. Nebo můžete povolit SSPR pro všechny uživatele v tenantovi Azure AD.

> [!NOTE]
> V současné době můžete povolit jenom jednu skupinu Azure AD pro SSPR pomocí Azure Portal. V rámci širšího nasazení SSPR podporuje Azure AD vnořené skupiny. Ujistěte se, že uživatelé ve skupinách, které jste zvolili, mají přiřazené příslušné licence. V tuto chvíli neexistují žádné ověřovací procesy těchto licenčních požadavků.

V tomto kurzu nastavíte SSPR pro skupinu uživatelů ve skupině testů. Použijte *SSPR-test-Group* a poskytněte svou vlastní skupinu Azure AD podle potřeby:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce* .
1. Vyhledejte a vyberte **Azure Active Directory** a potom v nabídce na levé straně vyberte **resetování hesla** .
1. Na stránce **vlastnosti** v části možnost *Samoobslužné resetování hesla povoleno* vyberte **Vybrat skupinu** .
1. Vyhledejte a vyberte svou skupinu Azure AD, jako je *SSPR-test-Group*, a pak zvolte *Vybrat*.

    [![Vyberte skupinu v Azure Portal pro povolení samoobslužného resetování hesla.](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Pokud chcete povolit SSPR pro vybrat uživatele, vyberte **Uložit**.

## <a name="select-authentication-methods-and-registration-options"></a>Výběr metod ověřování a možností registrace

Když uživatelé potřebují odemknout svůj účet nebo resetovat heslo, zobrazí se jim výzva k zadání jiné metody potvrzení. Tento dodatečný ověřovací faktor zajistí, že služba Azure AD dokončila pouze schválené události SSPR. Můžete zvolit, které metody ověřování mají být povoleny, na základě registračních informací, které uživatel poskytne.

1. V nabídce na levé straně stránky **metody ověřování** nastavte **počet metod požadovaných k resetování** na *1*.

    Pro zvýšení zabezpečení můžete zvýšit počet metod ověřování požadovaných pro SSPR.

1. Vyberte **metody dostupné pro uživatele** , které chce vaše organizace dovolit. Pro tento kurz zaškrtněte políčka pro povolení následujících metod:

    * *Oznámení mobilní aplikace*
    * *Kód mobilní aplikace*
    * *E-mail*
    * *Mobilní telefon*

    Podle potřeby můžete povolit další metody ověřování, jako je třeba *telefon do kanceláře* nebo *bezpečnostní otázky*.

1. Chcete-li použít metody ověřování, vyberte možnost **Uložit**.

Aby mohli uživatelé odemknout svůj účet nebo resetovat heslo, musí si zaregistrovat svoje kontaktní údaje. Azure AD používá tyto kontaktní informace pro různé metody ověřování nastavené v předchozích krocích.

Správce může tyto kontaktní údaje zadat ručně, nebo může přejít na registrační portál a poskytnout tak informace sami. V tomto kurzu nastavíte Azure AD tak, aby při příštím přihlášení vyzvat uživatele k registraci.

1. V nabídce na levé straně **registrační** stránky vyberte *Ano* , **Pokud chcete, aby se uživatelé zaregistrovali při přihlašování**.
1. Nastavte **Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací** na *180*.

    Je důležité udržovat kontaktní údaje v aktuálním stavu. Pokud při spuštění události SSPR existují zastaralé kontaktní informace, uživatel nemusí být schopný odemknout svůj účet nebo resetovat heslo.

1. Chcete-li použít nastavení registrace, vyberte možnost **Uložit**.

## <a name="set-up-notifications-and-customizations"></a>Nastavení oznámení a přizpůsobení

Pokud chcete, aby uživatelé měli informace o aktivitě účtu, můžete nastavit Azure AD tak, aby odesílala e-mailová oznámení, když dojde k události SSPR. Tato oznámení můžou pokrývat jak běžné uživatelské účty, tak účty správců. Pro účty správců poskytuje toto oznámení další úroveň povědomí, když se resetuje heslo privilegovaného účtu správce pomocí SSPR. Pokud někdo používá SSPR v účtu správce, Azure AD bude informovat všechny globální správce.

1. V nabídce na levé straně stránky **oznámení** nastavte tyto možnosti:

   * Možnost **Upozornit uživatele na resetování hesla** nastavte na *Ano*.
   * Možnost **Upozornit všechny správce na resetování hesla jiného správce** nastavte na *Ano*.

1. Chcete-li použít předvolby oznámení, vyberte možnost **Uložit**.

Pokud uživatelé potřebují k procesu SSPR Další informace, můžete přizpůsobit odkaz "kontaktujte správce". Uživatel může tento odkaz vybrat v procesu registrace SSPR a při odemčení účtu nebo resetování hesla. Abyste se ujistili, že uživatelé potřebují podporu, důrazně doporučujeme zadat e-mail nebo adresu URL vlastního helpdesku.

1. V nabídce na levé straně stránky **přizpůsobení** nastavte **odkaz přizpůsobit Helpdesk** na *Ano*.
1. V poli **e-mail nebo adresa URL vlastního helpdesku** zadejte e-mailovou adresu nebo adresu URL webové stránky, kde můžou vaši uživatelé získat další pomoc z vaší organizace, jako je například *https: \/ /support.contoso.com/.*
1. Chcete-li použít vlastní odkaz, vyberte možnost **Uložit**.

## <a name="test-self-service-password-reset"></a>Testování samoobslužného resetování hesla

S povoleným a nastaveným SSPR otestujte proces SSPR s uživatelem, který je součástí skupiny, kterou jste vybrali v předchozí části, jako je *test-SSPR-Group*. Následující příklad používá účet *testuser* . Zadejte vlastní uživatelský účet. Je součástí skupiny, kterou jste povolili pro SSPR, v první části tohoto kurzu.

> [!NOTE]
> Při testování samoobslužného resetování hesla použijte účet bez oprávnění správce. Služba Azure AD ve výchozím nastavení umožňuje Samoobslužné resetování hesla pro správce. Jsou nutné k resetování hesla pomocí dvou metod ověřování. Další informace najdete v tématu [rozdíly v zásadách resetování správců](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Chcete-li zobrazit proces Ruční registrace, otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte na adresu *https: \/ /aka.MS/ssprsetup*. Služba Azure AD bude při příštím přihlášení nasměrovat uživatele na tento registrační portál.
1. Přihlaste se pomocí testovacího uživatele bez oprávnění správce, jako je například *testuser*, a zaregistrujte kontaktní informace metod ověřování.
1. Po dokončení vyberte tlačítko s označením **vypadá dobře** a zavřete okno prohlížeče.
1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním a přejděte do *protokolu https: \/ /aka.MS/SSPR*.
1. Zadejte informace o účtu testovacích uživatelů bez oprávnění správce, jako je například *testuser*, znaky z CAPTCHA a pak vyberte **Další**.

    ![Zadání informací o uživatelském účtu pro resetování hesla](media/tutorial-enable-sspr/password-reset-page.png)

1. Postupujte podle pokynů k resetování hesla. Po dokončení obdržíte e-mailové oznámení o resetování hesla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V pozdějším kurzu této série nastavíte zpětný zápis hesla. Tato funkce zapisuje změny hesla z Azure AD SSPR zpátky do místního prostředí služby AD. Pokud chcete pokračovat v této sérii kurzů pro nastavení zpětného zápisu hesla, zakažte SSPR nyní.

Pokud už nechcete používat funkci SSPR, kterou jste nastavili v rámci tohoto kurzu, nastavte stav SSPR na **none** pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte **Azure Active Directory** a potom v nabídce na levé straně vyberte **resetování hesla** .
1. Na stránce **vlastnosti** v části možnost *Samoobslužné resetování hesla povoleno* vyberte možnost **žádná**.
1. Pokud chcete použít změnu SSPR, vyberte **Uložit**.

## <a name="faqs"></a>Nejčastější dotazy

V této části jsou vysvětleny běžné otázky od správců a koncových uživatelů, kteří se pokoušejí SSPR:

- Proč můžou federované uživatelé počkat až 2 minuty, než se vaše heslo před tím, než bude moct použít hesla synchronizovaná z místního **prostředí, vynulovat** ?

  Pro federované uživatele, jejichž hesla se synchronizují, je zdroj autority pro hesla místní. V důsledku toho SSPR aktualizuje pouze místní hesla. Synchronizace hodnot hash hesel zpět do služby Azure AD je naplánována každé 2 minuty.

- Když nově vytvořený uživatel, který je předem vyplněný pomocí SSPR dat, jako je telefon a e-mail, navštíví stránku registrace SSPR, **neztratí přístup k vašemu účtu.** Zobrazuje se jako název stránky. Proč se zpráva nelíbí ostatním uživatelům, kteří mají předem vyplněné SSPR data?

  Uživatel, který vidí ke **svému účtu neztratí přístup** je členem SSPR/kombinované registrační skupiny, které jsou nakonfigurovány pro tenanta. Uživatelé, kteří nevidíte, **neztratí přístup k vašemu účtu!** nebyly součástí SSPR/kombinovaných registračních skupin.

- Když někteří uživatelé procházejí SSPR procesem a obnovili své heslo, proč nevidí indikátor síly hesla?

  Uživatelé, kteří nevidí slabý nebo silný sílu hesla, mají povolený synchronizovaný zpětný zápis hesla. Vzhledem k tomu, že SSPR nemůže určit zásady hesel místního prostředí zákazníka, nemůže ověřit sílu nebo slabinu hesla. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili Samoobslužné resetování hesla služby Azure AD pro vybranou skupinu uživatelů. Naučili jste se:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Nastavení metod ověřování a možností registrace
> * Testování procesu SSPR jako uživatel

> [!div class="nextstepaction"]
> [Povolení vícefaktorového ověřování Azure AD](./tutorial-enable-azure-mfa.md)
