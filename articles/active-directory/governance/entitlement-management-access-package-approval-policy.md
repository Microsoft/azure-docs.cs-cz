---
title: Změna nastavení schválení balíčku pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak změnit nastavení informací o schválení a informace o žadateli pro balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591292"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Nastavení informací o schválení a žadateli o změnu (Preview) pro balíček přístupu v Azure AD – Správa nároků

Jako správce balíčků přístupu můžete kdykoli změnit nastavení informací o schválení a žadateli pro přístup k balíčku, a to úpravou stávající zásady nebo přidáním nové zásady.

Tento článek popisuje, jak změnit nastavení informací o schválení a žadateli pro existující balíček přístupu.

## <a name="approval"></a>Schválení

V části schvalování určíte, jestli se vyžaduje schválení, když si uživatelé vyžádají tento přístupový balíček. Nastavení schválení fungují následujícím způsobem:

- Pouze jeden z vybraných schvalovatelů nebo záložní schvalovatelé musí schválit žádost o schválení na základě jedné fáze. 
- Pouze jeden z vybraných schvalovatelů z každé fáze musí schválit žádost o schválení ve dvou fázích.
- Schvalovatelem může být manažer, interní sponzor nebo externí sponzor v závislosti na tom, kdo zásady řídí přístup.
- Schválení od každého vybraného schvalovatele není vyžadováno pro schválení typu Single nebo 2 fáze.
- Rozhodnutí o schválení vychází z toho, kdo žádost nejprve zkontroluje.

Ukázku, jak přidat schvalovatele do zásady žádosti, najdete v následujícím videu:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Ukázku toho, jak do zásad žádosti přidat schválení ve více fázích, najdete v následujícím videu:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>Změnit nastavení schválení stávajícího balíčku pro přístup

Pomocí těchto kroků určete nastavení schválení pro žádosti pro balíček pro přístup:

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Buď vyberte zásadu, kterou chcete upravit, nebo přidejte novou zásadu do balíčku pro přístup.
    1. Klikněte na **zásady** a pak **přidejte zásadu** , pokud chcete vytvořit novou zásadu.
    1. Klikněte na zásadu, kterou chcete upravit, a pak klikněte na **Upravit**.

1. Přejít na kartu **požadavek** .

1. Pokud chcete pro žádosti od vybraných uživatelů vyžadovat schválení, nastavte u přepínače **vyžadovat schválení** **hodnotu Ano**. Nebo pokud chcete, aby se požadavky automaticky schválily, nastavte přepínač na **ne**.

1. Pokud chcete, aby uživatelé zadali odůvodnění pro přístup k balíčku pro přístup, nastavte u přepínače **vyžadovat změnu zarovnání** na **hodnotu Ano**.
    
1. Teď určíte, jestli žádosti budou vyžadovat schválení jednou nebo 2 fázemi. Nastavte, **kolik fází** se má přepnout na **1** pro schválení v rámci jedné fáze nebo nastavte přepínač na **2** pro schválení ve dvou fázích.

    ![Přístup k balíčku – nastavení schválení](./media/entitlement-management-access-package-approval-policy/approval.png)


Pomocí následujících kroků přidejte schvalovatele po výběru počtu fází, které požadujete: 

### <a name="single-stage-approval"></a>Schválení jednou fází

1. Přidejte **prvního schvalovatele**:
    
    Pokud je zásada nastavená tak, aby se řídil přístup pro uživatele ve vašem adresáři, můžete **jako schvalovatele vybrat správce**. Případně můžete přidat konkrétního uživatele kliknutím na **Přidat schvalovatele** po výběru možnosti zvolit konkrétní schvalovatele z rozevírací nabídky.
    
    ![Přístup k balíčku – požadavky – pro uživatele v adresáři – první schvalovatel](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Pokud je tato zásada nastavená tak, aby se řídil přístup pro uživatele, kteří nejsou ve vašem adresáři, můžete vybrat **externí sponzora** nebo **interní sponzor**. Případně můžete přidat konkrétního uživatele kliknutím na **Přidat schvalovatele** nebo skupiny v části zvolit konkrétní schvalovatele.
    
    ![Přístup k balíčku – požadavky – pro uživatele mimo adresář – první schvalovatel](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Pokud jste jako první schvalovatel vybrali **správce** , klikněte na **Přidat zálohu** a vyberte jednoho nebo více uživatelů nebo skupin ve vašem adresáři jako záložního schvalovatele. Záložní schvalovatelé obdrží žádost, pokud Správa nároků nemůže najít správce pro uživatele, který žádá o přístup.

    Správce je nalezen pomocí oprávnění Správa pomocí atributu **Manager** . Atribut je v profilu uživatele ve službě Azure AD. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Pokud jste vybrali **možnost zvolit konkrétní schvalovatele**, klikněte na **Přidat schvalovatele** a vyberte jednoho nebo více uživatelů nebo skupin v adresáři, které mají být schvalovatelé.

1. V poli v části **rozhodnutí se musí** zadat počet dní, po které má schvalovatel zkontrolovat požadavek na tento přístupový balíček.

    Pokud žádost není v rámci tohoto časového období schválena, bude automaticky odepřena. Uživatel bude muset odeslat další žádost o přístup k balíčku.

1. Pokud chcete, aby schvalovatelé mohli poskytnout odůvodnění pro své rozhodnutí, nastavte vyžadovat odůvodnění schvalovatele na **Ano**.

    Odůvodnění se zobrazí ostatním schvalovatelům a žadateli.

### <a name="2-stage-approval"></a>schválení 2 fáze

Pokud jste vybrali schválení ve dvou fázích, budete muset přidat druhého schvalovatele.

1. Přidejte **druhého schvalovatele**: 
    
    Pokud se uživatelé nacházejí v adresáři, přidejte konkrétního uživatele jako druhého schvalovatele kliknutím na **Přidat schvalovatele** v části zvolit konkrétní schvalovatele.

    ![Přístup k balíčku – požadavky – pro uživatele v adresářovém druhém schvalovateli](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Pokud uživatelé nejsou ve vašem adresáři, jako druhý schvalovatel vyberte **interní sponzor** nebo **externí sponzor** . Po výběru schvalovatele přidejte záložní schvalovatele.

    ![Přístup k balíčku – požadavky – pro uživatele mimo adresář – druhý schvalovatel](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Zadejte počet dní, po které má druhý schvalovatel schválit žádost v poli v části **rozhodnutí se musí udělat v tom, kolik dní?**. 

1. Nastavte přepínač požadovat zarovnání schvalovatele na **Ano** nebo **ne**.

### <a name="alternate-approvers"></a>Alternativní schvalovatelé

Můžete zadat alternativní schvalovatele, podobně jako určení prvních a druhých schvalovatelů, kteří mohou schvalovat žádosti. U alternativních schvalovatelů pomůže zajistit, že žádosti byly schváleny nebo zamítnuty, než vyprší (časový limit). Můžete vypsat alternativní schvalovatele prvním schvalovatelem a druhým schvalovatelem pro schválení ve dvou fázích. 

Zadáním alternativních schvalovatelů v případě, že první nebo druhé schvalovatelé nemohly žádost schválit nebo zamítnout, se čeká na vyřízení žádosti na alternativní schvalovatele podle plánu předávání, který jste zadali během nastavení zásad. Obdrží e-mail ke schválení nebo zamítnutí žádosti, která čeká na vyřízení.

Po přeposlání žádosti na alternativní schvalovatele může první nebo druhé schvalovatele tuto žádost schválit nebo zamítnout. Alternativní schvalovatelé používají stejný web pro přístup ke schválení nebo zamítnutí žádosti, která čeká na vyřízení.

Můžeme vypsat osoby nebo skupiny uživatelů, kteří mají být schvalovatelé a alternativní schvalovatelé. Ujistěte se, že jste si vyřadíte seznam různých skupin uživatelů, které mají být první, druhá a alternativní schvalovatelé.
Například pokud jste jako první schvalovateli v seznamu Alice a Bob, vypíšete jako alternativní schvalovatele seznam Karolínu a Dave. Pomocí následujících kroků přidejte do balíčku pro přístup alternativní schvalovatele:

1. V části první schvalovatel, druhý schvalovatel nebo obojí klikněte na **Zobrazit upřesňující nastavení žádostí**.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Přístup k balíčku – zásady-Zobrazit upřesňující nastavení žádostí":::

1. Nastavit **, jestli se žádná akce neuskutečnila, předají se k alternativním schvalovatelům?** přepnout na **Ano**.

1. Klikněte na **Přidat alternativní schvalovatele** a ze seznamu vyberte alternativní schvalovatele.

    ![Přístup k balíčku – zásady – přidání alternativních schvalovatelů](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Pokud jako schvalovatele vyberete pro prvního schvalovatele možnost manažer, budete mít další možnost **jako alternativního schvalovatele**, která je dostupná pro výběr v poli alternativní schvalovatel. Vyberete-li tuto možnost, je nutné přidat záložního schvalovatele, kterému bude žádost předána, v případě, že systém nemůže najít správce druhé úrovně.

1. V poli **předávat k alternativním schvalovatelům po počtu dní** uveďte počet dní, po které schvalovatelé schválí nebo zamítnou požadavek. Pokud žádný schvalovatel neschválil nebo zamítl požadavek před trváním žádosti, vyprší platnost žádosti (časový limit) a uživatel bude muset odeslat další žádost o přístup k balíčku. 

    Žádosti mohou být předávány alternativním schvalovatelům denně po uplynutí doby trvání žádosti a rozhodnutí hlavních schvalovatelů se musí po dobu nejméně 4 dnů vysílat. Pokud je časový limit požadavku menší nebo roven 3, není dostatek času na přeposlání žádosti na alternativní schvalovatele. V tomto příkladu je doba trvání žádosti 14 dní. To znamená, že doba trvání žádosti dosáhne poloviny životnosti 7 dní. Proto se žádost nedá přeposláním starší než 8 dní. Žádosti se taky nedají přeslat za poslední den trvání žádosti. Takže v tomto příkladu může být nejnovější žádost předána dne 13.

## <a name="enable-requests"></a>Povolit žádosti

1. Pokud chcete, aby byl balíček přístupu hned dostupný pro uživatele v zásadách žádosti, přesuňte přepínač Povolit na **Ano**.

    Po dokončení vytváření balíčku pro přístup ho můžete v budoucnu kdykoli povolit.

    Pokud jste vybrali **možnost žádné (pouze oprávnění správce)** a nastavíte možnost povolit na hodnotu **ne**, správci nebudou moci přiřadit tento balíček přístupu přímo.

    ![Přístup k balíčku – nastavení zásad – Povolit zásadu](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klikněte na **Next** (Další).

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Shromažďování dalších informací o žadatelích ke schválení (Preview)

Chcete-li zajistit, aby uživatelé měli přístup ke správným balíčkům pro přístup, můžete požadovat, aby žadatelé odpověděli na vlastní textové pole nebo na několik otázek volby v době podání žádosti. Pro každou zásadu je povolený limit 20 otázek a limit 25 odpovědí na více otázek výběru. Otázky se pak zobrazí schvalovatelům, aby jim pomohli udělat rozhodnutí.

1. Přejděte na kartu **informace o žadateli** a klikněte na podřízenou kartu **otázky** .
 
1. Zadejte, co chcete požádat o žadatele, označovaný také jako řetězec zobrazení, pro otázku v poli pro **otázky** .

    ![Přístup k balíčku – nastavení zásad – Povolit informace o žadateli](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Pokud komunita uživatelů, kteří budou potřebovat přístup k balíčku přístupu, nemá společný preferovaný jazyk, můžete vylepšit prostředí pro uživatele požadující přístup k myaccess.microsoft.com. Pro zlepšení prostředí můžete zadat alternativní zobrazované řetězce pro různé jazyky. Například pokud je webový prohlížeč uživatele nastavený na španělštinu a máte nakonfigurované řetězce zobrazení španělštiny, zobrazí se tyto řetězce pro žádajícího uživatele. Chcete-li konfigurovat lokalizaci pro požadavky, klikněte na možnost **Přidat lokalizaci**.
    1. V podokně **Přidat lokalizace pro otázku** vyberte **kód jazyka** pro jazyk, ve kterém chcete otázku lokalizovat.
    1. V jazyce, který jste nakonfigurovali, zadejte otázku do **lokalizovaného textového** pole.
    1. Po přidání všech potřebných lokalizací klikněte na **Uložit**.

    ![Přístup k balíčku – zásady – konfigurace lokalizovaného textu](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Vyberte **Formát odpovědi** , ve kterém chcete, aby žadatelé odpověděli. Formáty odpovědí zahrnují: *krátký text*, *Vícenásobný výběr* a *dlouhý text*.
 
    ![Přístup k balíčku – zásady – výběr zobrazení a úprava formátu odpovědi s více volbami](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Pokud vyberete více možností, klikněte na tlačítko **Zobrazit a upravit** a nakonfigurujte možnosti odpovědi.
    1. Po výběru možnosti zobrazit a upravit podokno **otázek Zobrazit/upravit** se otevře.
    1. Zadejte možnosti odpovědi, které chcete žadateli poskytnout při zodpovězení otázky v polích **hodnoty odpovědi** .
    1. Zadejte tolik odpovědí, kolik potřebujete, a pak klikněte na **Uložit**.
    
    ![Přístup k balíčku – zásady – zadejte možnosti vícenásobné volby.](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Pokud požadujete, aby žadateli odpověděli na tuto otázku při žádosti o přístup k balíčku přístupu, zaškrtněte políčko v části **požadováno**.

1. Vyplňte zbývající karty (například životní cyklus) podle vašich potřeb.

Až nakonfigurujete informace o žadateli v zásadách přístupového balíčku, můžou si prohlédnout odpovědi žadatele na otázky. Pokyny k zobrazení informací o žadateli najdete v tématu [odpovědi žadatele na otázky (Preview)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Další kroky
- [Změnit nastavení životního cyklu pro balíček pro přístup](entitlement-management-access-package-lifecycle-policy.md)
- [Zobrazit žádosti pro balíček pro přístup](entitlement-management-access-package-requests.md)
