---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 1bbc549a9ba457d5f515df25d6fdce0c2ede6f2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582478"
---
## <a name="for-users-in-your-directory"></a>Pro uživatele ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby uživatelé ve vašem adresáři mohli požadovat tento balíček přístupu. Při definování zásad žádosti můžete zadat jednotlivé uživatele nebo skupiny uživatelů, kteří jsou často. Vaše organizace například už může mít skupinu, jako je například **Všichni zaměstnanci**.  Pokud se tato skupina přidá v zásadách pro uživatele, kteří můžou požádat o přístup, pak může každý člen této skupiny požádat o přístup.

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na **uživatele v adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti, které vám pomůžou lépe upřesnit, kdo v adresáři může tento přístupový balíček požádat.

    ![Přístup k balíčku – požadavky – pro uživatele ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  | Description |
    | --- | --- |
    | **Konkrétní uživatelé a skupiny** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat jenom pro uživatele a skupiny v adresáři, který jste zadali. |
    | **Všichni členové (kromě hostů)** | Tuto možnost vyberte, pokud chcete, aby všichni členové členských uživatelů v adresáři mohli požádat o tento balíček přístupu. Tato možnost neobsahuje žádné uživatele typu Host, které jste mohli pozvat do svého adresáře. |
    | **Všichni uživatelé (včetně hostů)** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat všichni členové a uživatelé typu Host v adresáři. |

    Uživatelé typu Host odkazují na externí uživatele, kteří byli pozváni do vašeho adresáře pomocí [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md). Další informace o rozdílech mezi členskými uživateli a uživateli typu Host najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Pokud jste vybrali **konkrétní uživatele a skupiny**, klikněte na **Přidat uživatele a skupiny**.

1. V podokně vybrat uživatele a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístup k balíčku – požadavky – výběr uživatelů a skupin](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klikněte na **Vybrat** a přidejte uživatele a skupiny.

1. Přeskočte dolů k části [schválení](#approval) .

## <a name="for-users-not-in-your-directory"></a>Pro uživatele, kteří nejsou ve vašem adresáři

 **Uživatelé, kteří nejsou v adresáři** , odkazují na uživatele, kteří jsou v jiném adresáři nebo adresáři služby Azure AD. Tito uživatelé možná nebyli přizváni do vašeho adresáře. Je potřeba nakonfigurovat adresáře Azure AD tak, aby umožňovaly pozvánky v **omezeních spolupráce**. Další informace najdete v tématu [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](../articles/active-directory/external-identities/delegate-invitations.md).

> [!NOTE]
> Uživatelský účet hosta se vytvoří pro uživatele, který ještě není ve vašem adresáři, jehož žádost je schválená nebo automaticky schválená. Host bude pozván, ale neobdrží e-mail s pozvánkou. Místo toho obdrží e-mail po doručení jejich přiřazení balíčku přístupu. Ve výchozím nastavení platí, že pokud už uživatel typu host již nemá žádná přiřazení balíčku přístupu, protože jejich poslední přiřazení vypršela nebo byla zrušena, bude tento uživatelský účet hosta zablokován a následně odstraněn. Pokud chcete, aby uživatelé typu Host zůstali ve vašem adresáři po neomezenou dobu, a to i v případě, že nemají žádná přiřazení balíčků přístupu, můžete změnit nastavení pro konfiguraci správy nároků. Další informace o objektu uživatele hosta najdete v tématu [vlastnosti Azure Active Directoryho uživatele spolupráce B2B](../articles/active-directory/external-identities/user-properties.md).

Postupujte podle těchto kroků, pokud chcete uživatelům, kteří nejsou ve vašem adresáři, vyžadovat tento balíček přístupu:

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na uživatele, kteří **nejsou ve vašem adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti.

    ![Přístup k balíčku – požadavky – pro uživatele, kteří nejsou ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  | Description |
    | --- | --- |
    | **Konkrétní propojené organizace** | Tuto možnost vyberte, pokud chcete vybrat ze seznamu organizací, které jste přidali jako správce. Přístup k tomuto balíčku můžou vyžádat všichni uživatelé z vybraných organizací. |
    | **Všechny připojené organizace** | Tuto možnost vyberte, pokud všichni uživatelé ze všech připojených organizací můžou tento balíček přístupu požádat. |
    | **Všichni uživatelé (všechny připojené organizace a všichni noví externí uživatelé)** | Tuto možnost vyberte, pokud všichni uživatelé ze všech připojených organizací můžou požádat o tento balíček přístupu a že nastavení seznam povolených a zakázaných B2B by mělo mít přednost pro všechny nové externí uživatele. |

    Připojená organizace je externí adresář nebo doména služby Azure AD, ke kterým máte relaci.

1. Pokud jste vybrali **konkrétní připojené organizace**, klikněte na **Přidat adresáře** a vyberte si ze seznamu připojených organizací, které jste přidali jako správce.

1. Zadejte název nebo název domény, ve kterém chcete hledat dříve připojenou organizaci.

    ![Přístup k balíčku – požadavky – výběr adresářů](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Pokud organizace, se kterou chcete spolupracovat, není v seznamu, můžete požádat správce, aby ho přidal jako připojenou organizaci. Další informace najdete v tématu [Přidání připojené organizace](../articles/active-directory/governance/entitlement-management-organization.md).

1. Po výběru všech připojených organizací klikněte na **Vybrat**.

    > [!NOTE]
    > Všichni uživatelé z vybraných připojených organizací budou moct požádat o tento balíček přístupu. To zahrnuje uživatele v Azure AD ze všech subdomén přidružených k organizaci, pokud tyto domény nejsou blokované seznamem povolených a zakázaných aplikací Azure B2B. Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](../articles/active-directory/external-identities/allow-deny-list.md).

1. Přeskočte dolů k části [schválení](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Žádné (pouze přímé přiřazení správce)

Postupujte podle těchto kroků, pokud chcete vynechat žádosti o přístup a umožníte správcům, aby k tomuto balíčku přístupu přiřadili přímo konkrétní uživatele. Uživatelé nebudou muset vyžadovat balíček přístupu. Pořád můžete nastavit nastavení životního cyklu, ale neexistují žádná nastavení žádostí.

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na možnost **žádný (pouze přímá přiřazení správců**).

    ![Přístup k balíčku – požadavky – pouze přímá přiřazení správců](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po vytvoření balíčku pro přístup můžete k balíčku pro přístup přímo přiřadit konkrétní interní a externí uživatele. Pokud zadáte externího uživatele, vytvoří se ve vašem adresáři uživatelský účet hosta. Informace o tom, jak přímo přiřazovat uživatele, najdete v tématu [zobrazení, přidání a odebrání přiřazení balíčku pro přístup](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Přeskočte dolů do oddílu [Povolit žádosti](#enable-requests) .

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

Pomocí těchto kroků určete nastavení schválení pro žádosti pro balíček pro přístup:

1. Pokud chcete pro žádosti od vybraných uživatelů vyžadovat schválení, nastavte u přepínače **vyžadovat schválení** **hodnotu Ano**. Nebo pokud chcete, aby se požadavky automaticky schválily, nastavte přepínač na **ne**.

1. Pokud chcete, aby uživatelé zadali odůvodnění pro přístup k balíčku pro přístup, nastavte u přepínače **vyžadovat změnu zarovnání** na **hodnotu Ano**.
    
1. Teď určíte, jestli žádosti budou vyžadovat schválení jednou nebo 2 fázemi. Nastavte, **kolik fází** se má přepnout na **1** pro schválení v rámci jedné fáze nebo nastavte přepínač na **2** pro schválení ve dvou fázích.

    ![Přístup k balíčku – nastavení schválení](./media/active-directory-entitlement-management-request-policy/approval.png)

Pomocí následujících kroků přidejte schvalovatele po výběru počtu fází, které požadujete: 

### <a name="single-stage-approval"></a>Schválení jednou fází

1. Přidejte **prvního schvalovatele**:
    
    Pokud je zásada nastavená tak, aby se řídil přístup pro uživatele ve vašem adresáři, můžete **jako schvalovatele vybrat správce**. Případně můžete přidat konkrétního uživatele kliknutím na **Přidat schvalovatele** po výběru možnosti zvolit konkrétní schvalovatele z rozevírací nabídky.
    
    ![Přístup k balíčku – požadavky – pro uživatele v adresáři – první schvalovatel](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Pokud je tato zásada nastavená tak, aby se řídil přístup pro uživatele, kteří nejsou ve vašem adresáři, můžete vybrat **externí sponzora** nebo **interní sponzor**. Případně můžete přidat konkrétního uživatele kliknutím na **Přidat schvalovatele** nebo skupiny v části zvolit konkrétní schvalovatele.
    
    ![Přístup k balíčku – požadavky – pro uživatele mimo adresář – první schvalovatel](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Pokud jste jako první schvalovatel vybrali **správce** , klikněte na **Přidat zálohu** a vyberte jednoho nebo více uživatelů nebo skupin ve vašem adresáři jako záložního schvalovatele. Záložní schvalovatelé obdrží žádost, pokud Správa nároků nemůže najít správce pro uživatele, který žádá o přístup.

    Správce je nalezen pomocí oprávnění Správa pomocí atributu **Manager** . Atribut je v profilu uživatele ve službě Azure AD. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Pokud jste vybrali **možnost zvolit konkrétní schvalovatele**, klikněte na **Přidat schvalovatele** a vyberte jednoho nebo více uživatelů nebo skupin v adresáři, které mají být schvalovatelé.

1. V poli v části **rozhodnutí se musí** zadat počet dní, po které má schvalovatel zkontrolovat požadavek na tento přístupový balíček.

    Pokud žádost není v rámci tohoto časového období schválena, bude automaticky odepřena. Uživatel bude muset odeslat další žádost o přístup k balíčku.

1. Pokud chcete, aby schvalovatelé mohli poskytnout odůvodnění pro své rozhodnutí, nastavte vyžadovat odůvodnění schvalovatele na **Ano**.

    Odůvodnění se zobrazí ostatním schvalovatelům a žadateli.

### <a name="2-stage-approval"></a>schválení 2 fáze

Pokud jste vybrali schválení ve dvou fázích, budete muset přidat druhého schvalovatele.

1. Přidejte **druhého schvalovatele**: 
    
    Pokud se uživatelé nacházejí v adresáři, přidejte konkrétního uživatele jako druhého schvalovatele kliknutím na **Přidat schvalovatele** v části zvolit konkrétní schvalovatele.

    ![Přístup k balíčku – požadavky – pro uživatele v adresářovém druhém schvalovateli](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Pokud uživatelé nejsou ve vašem adresáři, jako druhý schvalovatel vyberte **interní sponzor** nebo **externí sponzor** . Po výběru schvalovatele přidejte záložní schvalovatele.

    ![Přístup k balíčku – požadavky – pro uživatele mimo adresář – druhý schvalovatel](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Zadejte počet dní, po které má druhý schvalovatel schválit žádost v poli v části **rozhodnutí se musí udělat v tom, kolik dní?**. 

1. Nastavte přepínač požadovat zarovnání schvalovatele na **Ano** nebo **ne**.

### <a name="alternate-approvers"></a>Alternativní schvalovatelé

Můžete zadat alternativní schvalovatele, podobně jako určení prvních a druhých schvalovatelů, kteří mohou schvalovat žádosti. U alternativních schvalovatelů pomůže zajistit, že žádosti byly schváleny nebo zamítnuty, než vyprší (časový limit). Můžete vypsat alternativní schvalovatele prvním schvalovatelem a druhým schvalovatelem pro schválení ve dvou fázích. 

Zadáním alternativních schvalovatelů v případě, že první nebo druhé schvalovatelé nemohly žádost schválit nebo zamítnout, se čeká na vyřízení žádosti na alternativní schvalovatele podle plánu předávání, který jste zadali během nastavení zásad. Obdrží e-mail ke schválení nebo zamítnutí žádosti, která čeká na vyřízení.

Po přeposlání žádosti na alternativní schvalovatele může první nebo druhé schvalovatele tuto žádost schválit nebo zamítnout. Alternativní schvalovatelé používají stejný web pro přístup ke schválení nebo zamítnutí žádosti, která čeká na vyřízení.

Můžeme vypsat osoby nebo skupiny uživatelů, kteří mají být schvalovatelé a alternativní schvalovatelé. Ujistěte se, že jste si vyřadíte seznam různých skupin uživatelů, které mají být první, druhá a alternativní schvalovatelé.
Například pokud jste jako první schvalovateli v seznamu Alice a Bob, vypíšete jako alternativní schvalovatele seznam Karolínu a Dave. Pomocí následujících kroků přidejte do balíčku pro přístup alternativní schvalovatele:

1. V části první schvalovatel, druhý schvalovatel nebo obojí klikněte na **Zobrazit upřesňující nastavení žádostí**.

    ![Přístup k balíčku – zásady-Zobrazit upřesňující nastavení žádostí](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Nastavit **, jestli se žádná akce neuskutečnila, předají se k alternativním schvalovatelům?** přepnout na **Ano**.

1. Klikněte na **Přidat alternativní schvalovatele** a ze seznamu vyberte alternativní schvalovatele.

    ![Přístup k balíčku – zásady – přidání alternativních schvalovatelů](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Pokud jako schvalovatele vyberete pro prvního schvalovatele možnost manažer, budete mít další možnost **jako alternativního schvalovatele**, která je dostupná pro výběr v poli alternativní schvalovatel. Vyberete-li tuto možnost, je nutné přidat záložního schvalovatele, kterému bude žádost předána, v případě, že systém nemůže najít správce druhé úrovně.

1. V poli **předávat k alternativním schvalovatelům po počtu dní** uveďte počet dní, po které schvalovatelé schválí nebo zamítnou požadavek. Pokud žádný schvalovatel neschválil nebo zamítl požadavek před trváním žádosti, vyprší platnost žádosti (časový limit) a uživatel bude muset odeslat další žádost o přístup k balíčku. 

    Žádosti mohou být předávány alternativním schvalovatelům denně po uplynutí doby trvání žádosti a rozhodnutí hlavních schvalovatelů se musí po dobu nejméně 4 dnů vysílat. Pokud je časový limit požadavku menší nebo roven 3, není dostatek času na přeposlání žádosti na alternativní schvalovatele. V tomto příkladu je doba trvání žádosti 14 dní. To znamená, že doba trvání žádosti dosáhne poloviny životnosti 7 dní. Proto se žádost nedá přeposláním starší než 8 dní. Žádosti se taky nedají přeslat za poslední den trvání žádosti. Takže v tomto příkladu může být nejnovější žádost předána dne 13.

## <a name="enable-requests"></a>Povolit žádosti

1. Pokud chcete, aby byl balíček přístupu hned dostupný pro uživatele v zásadách žádosti, přesuňte přepínač Povolit na **Ano**.

    Po dokončení vytváření balíčku pro přístup ho můžete v budoucnu kdykoli povolit.

    Pokud jste vybrali **možnost žádné (pouze oprávnění správce)** a nastavíte možnost povolit na hodnotu **ne**, správci nebudou moci přiřadit tento balíček přístupu přímo.

    ![Snímek obrazovky, který ukazuje možnost povolit nové žádosti a přiřazení.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klikněte na **Next** (Další).

## <a name="add-requestor-information-preview-to-an-access-package"></a>Přidání informací o žadateli (Preview) do balíčku pro přístup

1. Přejděte na kartu **informace o žadateli** a klikněte na podřízenou kartu **otázky** .
 
1. Zadejte, co chcete požádat o žadatele, označovaný také jako řetězec zobrazení, pro otázku v poli pro **otázky** .

    ![Přístup k balíčku – nastavení zásad – Povolit informace o žadateli](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Pokud chcete přidat vlastní možnosti lokalizace, klikněte na **Přidat lokalizaci**.
    1. V podokně **Přidat lokalizace pro otázku** vyberte **kód jazyka** pro jazyk, ve kterém chcete otázku lokalizovat.
    1. V jazyce, který jste nakonfigurovali, zadejte otázku do **lokalizovaného textového** pole.
    1. Po přidání všech potřebných lokalizací klikněte na **Uložit**.

    ![Přístup k balíčku – zásady – konfigurace lokalizovaného textu](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Vyberte **Formát odpovědi** , ve kterém chcete, aby žadatelé odpověděli. Formáty odpovědí zahrnují: *krátký text*, *Vícenásobný výběr* a *dlouhý text*.
 
    ![Přístup k balíčku – zásady – výběr zobrazení a úprava formátu odpovědi s více volbami](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Pokud vyberete více možností, klikněte na tlačítko **Zobrazit a upravit** a nakonfigurujte možnosti odpovědi.
    1. Po výběru možnosti zobrazit a upravit podokno **otázek Zobrazit/upravit** se otevře.
    1. Zadejte možnosti odpovědi, které chcete žadateli poskytnout při zodpovězení otázky v polích **hodnoty odpovědi** .
    1. Zadejte tolik odpovědí, kolik potřebujete, a pak klikněte na **Uložit**.
    
    ![Přístup k balíčku – zásady – zadejte možnosti vícenásobné volby.](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Pokud požadujete, aby žadateli odpověděli na tuto otázku při žádosti o přístup k balíčku přístupu, zaškrtněte políčko v části **požadováno**.

1. Kliknutí na Další