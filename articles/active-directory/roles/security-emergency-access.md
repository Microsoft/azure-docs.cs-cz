---
title: Správa účtů pro správu pro nouzový přístup – Azure AD | Microsoft Docs
description: Tento článek popisuje, jak používat účty pro nouzový přístup k tomu, aby se zabránilo neúmyslnému uzamčení vaší organizace Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 966d264cc338487dd1a8c04f2efd0825dfccdef0
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378750"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Správa účtů pro nouzový přístup v Azure AD

Je důležité, abyste zabránili nechtěnému uzamčení vaší organizace Azure Active Directory (Azure AD), protože se nemůžete přihlásit nebo aktivovat účet jiného uživatele jako správce. Vytvořením dvou nebo více *účtů pro nouzový přístup* ve vaší organizaci můžete zmírnit dopad náhodného nedostatku oprávnění správce.

Účty pro nouzový přístup jsou vysoce privilegované a nepřiřazují se konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu. Doporučujeme, abyste zachovali cíl omezení používání nouzového účtu jenom na časy, kdy je nezbytně nutné.

Tento článek poskytuje pokyny pro správu účtů pro nouzový přístup ve službě Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Proč používat účet pro nouzový přístup

Organizace může potřebovat účet pro nouzový přístup v následujících situacích:

- Uživatelské účty jsou federované a federace není aktuálně k dispozici z důvodu přerušení v síťové síti nebo při výpadku poskytovatele identity. Pokud například hostitel poskytovatele identity ve vašem prostředí zmizí, uživatelé se nemusí přihlašovat, když Azure AD přesměruje na svého zprostředkovatele identity.
- Správci jsou zaregistrovaní prostřednictvím Azure Multi-Factor Authentication a všechna jejich individuální zařízení nejsou k dispozici nebo služba není k dispozici. Uživatelé možná nebudou moci dokončit Multi-Factor Authentication k aktivaci role. Například výpadek sítě mezi buňkami brání v zodpovězení telefonních hovorů nebo přijímání textových zpráv, ale pouze dvou mechanismů ověřování registrovaných pro své zařízení.
- Uživatel, který má přístup k nejnovějšímu globálnímu správci, opustil organizaci. Služba Azure AD brání v odstranění posledního účtu globálního správce, ale nebrání tomu, aby se účet odstranil nebo zakázal místně. V obou případech může organizace účet obnovit.
- Nepředvídatelné okolnosti, jako je třeba přírodní nouzové havárie, během kterých nemusí být mobilní telefon nebo jiné sítě k dispozici. 

## <a name="create-emergency-access-accounts"></a>Vytvoření účtů pro nouzový přístup

Vytvořte dva nebo více účtů pro nouzový přístup. Tyto účty by měly být pouze cloudové účty, které používají \* doménu. onmicrosoft.com a které nejsou federované nebo synchronizované z místního prostředí.

Při konfiguraci těchto účtů je potřeba splnit tyto požadavky:

- Účty pro nouzový přístup by neměly být přidruženy k žádnému individuálnímu uživateli v organizaci. Ujistěte se, že vaše účty nejsou propojené s žádnými mobilními telefony dodanými zaměstnanci, hardwarovými tokeny, které se cestují s jednotlivými zaměstnanci, nebo s jinými přihlašovacími údaji určenými zaměstnanci Tato preventivní opatření zahrnují případy, kdy je individuální zaměstnanec nedosažitelný, když je potřeba přihlašovací údaje. Je důležité zajistit, aby všechna registrovaná zařízení byla udržována ve známém a zabezpečeném umístění, které má více prostředků komunikace se službou Azure AD.
- Mechanismus ověřování, který se používá pro účet pro nouzový přístup, by měl být odlišný od těch, které používá ostatní účty pro správu, včetně dalších účtů pro nouzový přístup.  Pokud například vaše normální přihlášení správce je prostřednictvím místního ověřování MFA, Azure MFA by byl jiný mechanismus.  Pokud je ale Azure MFA vaší primární součástí ověřování pro účty správců, zvažte pro ně jiný přístup, jako je například použití podmíněného přístupu se zprostředkovatelem MFA třetí strany prostřednictvím vlastních ovládacích prvků.
- Zařízení nebo přihlašovací údaje nesmí vypršet nebo být v oboru automatického čištění z důvodu nedostatku použití.  
- Měli byste nastavit trvalé přiřazení role globálního správce pro účty pro nouzový přístup. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Vyloučení aspoň jednoho účtu z telefonového ověřování na základě telefonu

Azure AD doporučuje pro všechny jednotlivé uživatele vyžadovat službu Multi-Factor Authentication, aby se snížilo riziko útoku vyplývajícího z napadeného hesla. Tato skupina zahrnuje správce a všechny ostatní (například finanční důstojníci), jejichž ohrožený účet by měl významný dopad.

Nejméně jeden z účtů pro nouzový přístup by však neměl mít stejný mechanismus vícefaktorového ověřování jako ostatní účty, které nejsou v nouzi. To zahrnuje řešení Multi-Factor Authentication od třetích stran. Pokud máte zásady podmíněného přístupu, které vyžadují [vícefaktorové ověřování pro každého správce](../authentication/howto-mfa-userstates.md) Azure AD a jiné připojené aplikace SaaS (software jako služba), měli byste z tohoto požadavku vyloučit účty pro nouzový přístup a místo toho nakonfigurovat jiný mechanismus. Navíc byste se měli ujistit, že účty nemají zásady vícefaktorového ověřování pro jednotlivé uživatele.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Vyloučení aspoň jednoho účtu ze zásad podmíněného přístupu

V naléhavosti nechcete, aby zásada pro odstranění problému mohla blokovat váš přístup. Ze všech zásad podmíněného přístupu by se měl vyloučit aspoň jeden účet pro nouzový přístup.

## <a name="federation-guidance"></a>Pokyny federace

Další možností pro organizace, které používají službu AD DS (Active Directory Services) a AD FS nebo podobného poskytovatele identity k federovatí do služby Azure AD, je konfigurace účtu pro nouzový přístup, jehož deklarace identity by mohla být poskytnuta zprostředkovatelem identity.  Účet pro nouzový přístup může být například zálohovaný certifikátem a dvojicí klíčů, jako je například jeden uložený na čipové kartě.  Když je tento uživatel ověřený ve službě AD, může služba ADFS dodat deklaraci identity službě Azure AD, která indikuje, že uživatel splnil požadavky MFA.  I s tímto přístupem organizace musí mít stále cloudové účty pro nouzový přístup v případě, že federaci nelze vytvořit. 

## <a name="store-account-credentials-safely"></a>Bezpečné ukládání přihlašovacích údajů k účtu

Organizace musí zajistit, aby pověření pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat. Někteří zákazníci používají čipovou kartu a jiné používají hesla. Heslo pro účet pro nouzový přístup se obvykle dělí na dvě nebo tři části, které jsou napsané na samostatných částech papíru a jsou uložené v zabezpečených fireproof bezpečích, které jsou bezpečné, oddělené umístění.

Pokud používáte hesla, ujistěte se, že účty obsahují silná hesla, která neprošla heslem. V ideálním případě by měla být hesla alespoň 16 znaků dlouhá a náhodně vygenerována.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorování přihlášení a protokolů auditu

Organizace by měly monitorovat aktivitu přihlášení a auditu z mimořádných účtů a aktivovat oznámení ostatním správcům. Když monitorete aktivitu na účtech s oddělovači, můžete ověřit, že tyto účty jsou používány pouze pro účely testování nebo k skutečným mimořádným událostem. Pomocí služby Azure Log Analytics můžete monitorovat protokoly přihlášení a aktivovat e-maily a upozornění serveru SMS vašim správcům, když se přihlásí účty se systémem Break.

### <a name="prerequisites"></a>Předpoklady

1. [Odešlete přihlašovací protokoly služby Azure AD](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) , aby se Azure monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Získat ID objektů pro účty skla

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přiřazeného k roli správce uživatele.
1. Vyberte **Azure Active Directory**  >  **Uživatelé**.
1. Vyhledejte účet Break a vyberte jméno uživatele.
1. Zkopírujte a uložte atribut ID objektu, abyste jej mohli použít později.
1. Opakujte předchozí kroky pro druhý účet Break.

### <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přiřazeného k roli Přispěvatel monitorování v Azure monitor.
1. Vyberte **všechny služby** , do Hledat zadejte Log Analytics a pak vyberte **Log Analytics pracovní prostory**.
1. Vyberte pracovní prostor.
1. V pracovním prostoru vyberte **výstrahy**  >  **nové pravidlo výstrahy**.
    1. V části **prostředek** ověřte, že se jedná o předplatné, se kterým chcete pravidlo výstrahy přidružit.
    1. V části **Podmínka** vyberte **Přidat**.
    1. V části **Název signálu** vyberte **vlastní prohledávání protokolu** .
    1. V části **vyhledávací dotaz** zadejte následující dotaz, který vloží ID objektů obou účtů.
        > [!NOTE]
        > Pro každý další účet pro oddělitelné sklo, který chcete zahrnout, přidejte do dotazu další (nebo UserId = = "ObjectGuid").

        ![Přidání ID objektu pro účty s oddělovači do pravidla výstrahy](./media/security-emergency-access/query-image1.png)

    1. V části **logika výstrahy** zadejte toto:

        - Podle: počet výsledků
        - Operátor: je větší než
        - Prahová hodnota: 0

    1. Pod položkou **vyhodnoceno na základě** vyberte **dobu (v minutách)** , po kterou se má dotaz spouštět, a **frekvence (v minutách)** , jak často chcete dotaz spouštět. Frekvence by měla být menší než nebo stejná jako perioda.

        ![logika výstrahy](./media/security-emergency-access/alert-image2.png)

    1. Vyberte **Hotovo**. Nyní můžete zobrazit Odhadované měsíční náklady na tuto výstrahu.
1. Vyberte skupinu akcí, které má výstraha informovat. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření skupiny akcí](#create-an-action-group).
1. Chcete-li upravit e-mailové oznámení odeslané členům skupiny akcí, vyberte akce v části **vlastní akce**.
1. V části **Podrobnosti o výstraze** zadejte název pravidla výstrahy a přidejte volitelný popis.
1. Nastavte **úroveň závažnosti** události. Doporučujeme, abyste ji nastavili na **kritickou (závažnost 0)**.
1. V části **Povolit pravidlo při vytváření** ponechte nastavenou **hodnotu Ano**.
1. Chcete-li vypnout výstrahy pro určitou dobu, zaškrtněte políčko **potlačit výstrahy** a zadejte dobu čekání před opětovným odesláním výstrahy a potom vyberte **Uložit**.
1. Klikněte na **Vytvořit pravidlo upozornění**.

### <a name="create-an-action-group"></a>Vytvoření skupiny akcí

1. Vyberte **vytvořit skupinu akcí**.

    ![Vytvoření skupiny akcí pro akce oznámení](./media/security-emergency-access/action-group-image3.png)

1. Zadejte název skupiny akcí a krátký název.
1. Ověřte předplatné a skupinu prostředků.
1. V části Typ akce vyberte **e-mail/SMS/Push/Voice**.
1. Zadejte název akce, například **Notify globální správce**.
1. Vyberte **typ akce** jako **e-mail/SMS/Push/Voice**.
1. Vyberte možnost **Upravit podrobnosti** a vyberte metody oznámení, které chcete nakonfigurovat, zadejte požadované kontaktní údaje a potom kliknutím na **tlačítko OK** uložte podrobnosti.
1. Přidejte další akce, které chcete aktivovat.
1. Vyberte **OK**.

## <a name="validate-accounts-regularly"></a>Pravidelné ověřování účtů

Když naučíte, aby pracovníci používali účty pro nouzový přístup a ověřili účty pro nouzový přístup, minimálně v pravidelných intervalech proveďte následující kroky:

- Zajistěte, aby pracovníci monitorování zabezpečení věděli, že aktivita kontroly účtu pokračuje.
- Zajistěte, aby byl proces nouzového skla pro použití těchto účtů zdokumentováný a aktuální.
- Zajistěte, aby měli správci a bezpečnostní důstojníci, kteří potřebují provést tyto kroky během nouze, vyškolený proces.
- Aktualizujte přihlašovací údaje účtu, zejména hesla pro účty pro nouzový přístup, a pak ověřte, že se účty pro nouzový přístup můžou přihlašovat a provádět úlohy správy.
- Zajistěte, aby uživatelé nezaregistrovali Multi-Factor Authentication ani Samoobslužné resetování hesla (SSPR) na jakékoli zařízení nebo osobní údaje uživatele. 
- Pokud jsou účty registrované pro Multi-Factor Authentication do zařízení, které se použijí během přihlašování nebo Aktivace rolí, ujistěte se, že je zařízení dostupné všem správcům, kteří ho můžou v naléhavosti potřebovat použít. Ověřte taky, že zařízení může komunikovat přes aspoň dvě síťové cesty, které nesdílejí běžný režim selhání. Zařízení může například komunikovat s internetem prostřednictvím bezdrátové sítě zařízení a sítě poskytovatele s buňkami.

Tyto kroky by se měly provádět v pravidelných intervalech a pro klíčové změny:

- Nejméně každých 90 dní
- V případě nedávné změny zaměstnanců IT, jako je například změna úlohy, odchod nebo nový pronájem
- Změny předplatných služby Azure AD v organizaci

## <a name="next-steps"></a>Další kroky

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](security-planning.md)
- [Přidání uživatelů pomocí Azure AD](../fundamentals/add-users-azure-active-directory.md) a [přiřazení nového uživatele k roli globálního správce](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- Pokud jste se ještě nezaregistrovali, [Zaregistrujte](../fundamentals/active-directory-get-started-premium.md)se do Azure AD Premium.
- [Jak pro uživatele vyžadovat dvoustupňové ověřování](../authentication/howto-mfa-userstates.md)
- [Konfigurace dalších ochran pro globální správce v Microsoft 365](/office365/enterprise/protect-your-global-administrator-accounts), pokud používáte Microsoft 365
- [Spusťte kontrolu přístupu globálních správců](../privileged-identity-management/pim-how-to-start-security-review.md) a [Převeďte stávající globální správce na další konkrétní role správce](permissions-reference.md) .