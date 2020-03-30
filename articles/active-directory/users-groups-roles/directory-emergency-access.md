---
title: Správa účtů správce nouzového přístupu – Azure AD | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat účty nouzového přístupu, abyste zabránili nechtěnému uzamčení organizace Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028129"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Správa účtů pro nouzový přístup ve službě Azure AD

Je důležité, abyste zabránili náhodnému uzamčení organizace Azure Active Directory (Azure AD), protože se nemůžete přihlásit nebo aktivovat účet jiného uživatele jako správce. Dopad náhodného nedostatku přístupu pro správu můžete zmírnit vytvořením dvou nebo více *účtů pro nouzový přístup* ve vaší organizaci.

Účty pro nouzový přístup jsou vysoce privilegované a nejsou přiřazeny konkrétním osobám. Účty nouzového přístupu jsou omezeny na nouzové nebo "rozbít sklo", kde nelze použít běžné administrativní účty. Doporučujeme, abyste si zachovávají cíl omezit používání nouzového účtu pouze na dobu, kdy je to naprosto nezbytné.

Tento článek obsahuje pokyny pro správu účtů nouzového přístupu ve službě Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Proč používat účet pro nouzový přístup

Organizace může potřebovat použít účet pro nouzový přístup v následujících situacích:

- Uživatelské účty jsou federovány a federace je aktuálně nedostupná z důvodu přerušení mobilní sítě nebo výpadku zprostředkovatele identity. Například pokud hostitel identity hostitele ve vašem prostředí došlo k poklesu, uživatelé nemusí být schopen přihlásit, když Azure AD přesměruje na svého poskytovatele identity.
- Správci jsou registrovaní prostřednictvím azure multifaktorového ověřování a všechna jejich jednotlivá zařízení nejsou k dispozici nebo služba není k dispozici. Uživatelé pravděpodobně nebudou moci dokončit vícefaktorové ověřování pro aktivaci role. Výpadek mobilní sítě jim například brání v přijímání telefonních hovorů nebo přijímání textových zpráv, což jsou jediné dva mechanismy ověřování, které zaregistrovali pro své zařízení.
- Osoba s nejnovějším přístupem globálního správce opustila organizaci. Azure AD zabrání odstranění posledního účtu globálního správce, ale nezabrání jeho odstranění nebo zakázání místního účtu. V obou situacích může organizace neobnovit účet.
- Nepředvídané okolnosti, jako je případ nouze při přírodních katastrofách, během něhož může být mobilní telefon nebo jiné sítě nedostupné. 

## <a name="create-emergency-access-accounts"></a>Vytvoření účtů nouzového přístupu

Vytvořte dva nebo více účtů nouzového přístupu. Tyto účty by měly být \*pouze cloudové účty, které používají doménu .onmicrosoft.com a které nejsou federovány nebo synchronizovány z místního prostředí.

Při konfiguraci těchto účtů musí být splněny následující požadavky:

- Účty nouzového přístupu by neměly být přidruženy k žádnému jednotlivému uživateli v organizaci. Ujistěte se, že vaše účty nejsou propojeny s žádnými mobilními telefony dodanými zaměstnanci, hardwarovými tokeny, které cestují s jednotlivými zaměstnanci, nebo s jinými přihlašovacími údaji specifickými pro zaměstnance. Toto opatření se vztahuje na případy, kdy je jednotlivý zaměstnanec nedostupný, když je potřeba pověření. Je důležité zajistit, aby všechna registrovaná zařízení jsou uchovávány ve známém, zabezpečeném umístění, které má více prostředků komunikace s Azure AD.
- Mechanismus ověřování používaný pro účet pro nouzový přístup by měl být odlišný od mechanismu používaného jinými účty pro správu, včetně jiných účtů pro nouzový přístup.  Například pokud normální přihlášení správce je prostřednictvím místního vícefaktorového ověřování, pak Azure MFA by jiný mechanismus.  Pokud je však Azure MFA primární součástí ověřování pro vaše účty pro správu, zvažte pro ně jiný přístup, například použití podmíněného přístupu s poskytovatelem mfa třetí strany.
- Platnost zařízení nebo pověření nesmí vypršet nebo být v rozsahu automatického čištění z důvodu nedostatečného použití.  
- Přiřazení role globálního správce by mělo být trvalé pro vaše účty pro nouzový přístup. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Vyloučit alespoň jeden účet z vícefaktorového ověřování založeného na telefonu

Chcete-li snížit riziko útoku vyplývající z kompromitované heslo, Azure AD doporučuje, abyste vyžadovali vícefaktorové ověřování pro všechny jednotlivé uživatele. Do této skupiny patří správci a všichni ostatní (například finanční úředníci), jejichž kompromitovaný účet by měl významný dopad.

Alespoň jeden z vašich účtů pro nouzový přístup by však neměl mít stejný mechanismus vícefaktorového ověřování jako ostatní účty, které nejsou nouzové. To zahrnuje řešení vícefaktorového ověřování jiných výrobců. Pokud máte zásady podmíněného přístupu vyžadovat [vícefaktorové ověřování pro každého správce](../authentication/howto-mfa-userstates.md) pro Azure AD a další připojený software jako služby (SaaS) aplikace, měli byste vyloučit nouzové přístupové účty z tohoto požadavku a místo toho nakonfigurovat jiný mechanismus. Kromě toho byste se měli ujistit, že účty nemají zásady vícefaktorového ověřování pro jednotlivé uživatele.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Vyloučit alespoň jeden účet ze zásad podmíněného přístupu

V případě nouze nechcete, aby zásady potenciálně blokovat váš přístup k vyřešení problému. Ze všech zásad podmíněného přístupu by měl být vyloučen alespoň jeden účet nouzového přístupu. Pokud jste povolili [zásady směrného plánu](../conditional-access/baseline-protection.md), měli byste vyloučit účty nouzového přístupu.

## <a name="federation-guidance"></a>Federační pokyny

Další možností pro organizace, které používají služby AD Domain Services a ADFS nebo podobného zprostředkovatele identity k federate do Azure AD, je konfigurace účtu nouzového přístupu, jehož deklarace mfa by mohla být poskytnuta tímto poskytovatelem identity.  Účet pro nouzový přístup může být například zálohován certifikátem a dvojicí klíčů, například například na čipové kartě.  Když je tento uživatel ověřen ve službě AD, služba ADFS může do služby Azure AD zadat deklaraci, která označuje, že uživatel splnil požadavky vícefaktorové ověřování.  I s tímto přístupem musí mít organizace stále cloudové účty pro nouzový přístup v případě, že federaci nelze vytvořit. 

## <a name="store-account-credentials-safely"></a>Bezpečně uložit přihlašovací údaje účtu

Organizace musí zajistit, aby přihlašovací údaje pro účty nouzového přístupu byly zabezpečeny a známy pouze jednotlivcům, kteří jsou oprávněni je používat. Někteří zákazníci používají čipovou kartu a jiní používají hesla. Heslo pro nouzový přístupový účet je obvykle rozděleno na dvě nebo tři části, napsané na samostatných listech papíru a uloženo v zabezpečených, ohnivzdorných trezorech, které jsou na bezpečných, oddělených místech.

Pokud používáte hesla, ujistěte se, že účty mají silná hesla, jejichž platnost nevyprší. V ideálním případě by hesla měla být alespoň 16 znaků dlouhé a náhodně generované.

## <a name="monitor-sign-in-and-audit-logs"></a>Sledování protokolů přihlášení a auditu

Organizace by měly sledovat aktivitu přihlášení a auditování protokolu z nouzových účtů a aktivovat oznámení ostatním správcům. Při sledování aktivity na účtech break glass můžete ověřit, že tyto účty se používají pouze pro testování nebo skutečné mimořádné události. Azure Log Analytics můžete použít ke sledování přihlašovacích protokolů a aktivaci e-mailů a SMS upozornění pro správce při každém přihlášení k break glass.

### <a name="prerequisites"></a>Požadavky

1. [Odešlete protokoly přihlášení Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) do Azure Monitoru.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Získat ID objektů z účtů přerušit sklo

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přiřazeného k roli správce uživatele.
1. Vyberte**možnost Uživatelé** **služby Azure active directory** > .
1. Vyhledejte účet s rozbitím skla a vyberte jméno uživatele.
1. Zkopírujte a uložte atribut ID objektu, abyste ho mohli později použít.
1. Opakujte předchozí kroky pro druhý break-glass účet.

### <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přiřazeného k roli přispěvatele monitorování v Azure Monitoru.
1. Vyberte **Všechny služby**", zadejte "log analytics" ve vyhledávání a pak vyberte **Log Analytics pracovní prostory**.
1. Vyberte pracovní prostor.
1. V pracovním prostoru vyberte **Upozornění** > **nové pravidlo výstrah**.
    1. V **části Prostředek**ověřte, zda je předplatné to, ke kterému chcete přidružit pravidlo výstrahy.
    1. V části **Podmínka**vyberte **Přidat**.
    1. V části **Název signálu**vyberte **Vlastní hledání protokolu** .
    1. V části **Vyhledávací dotaz**zadejte následující dotaz a vložte ID objektů dvou účtů přerušit sklo.
        > [!NOTE]
        > Pro každý další účet přerušit sklo, který chcete zahrnout, přidejte další "nebo UserId == "ObjectGuid"" do dotazu.

        ![Přidání ID objektů účtů přerušit sklo do pravidla výstrahy](./media/directory-emergency-access/query-image1.png)

    1. V části **Logika výstrah**zadejte následující:

        - Na základě: Počet výsledků
        - Operátor: Větší než
        - Prahová hodnota: 0

    1. V **části Vyhodnoceno na ,** vyberte období **(v minutách)** na jak dlouho má dotaz spustit a frekvence **(v minutách)** pro jak často chcete dotaz spustit. Frekvence by měla být menší nebo rovna období.

        ![logika výstrah](./media/directory-emergency-access/alert-image2.png)

    1. Vyberte **Done** (Hotovo). Nyní můžete zobrazit odhadované měsíční náklady na tuto výstrahu.
1. Vyberte skupinu akcí uživatelů, které mají být upozorněny výstrahou. Pokud ji chcete vytvořit, přečtěte si informace [o vytvoření skupiny akcí](#create-an-action-group).
1. Chcete-li přizpůsobit e-mailové oznámení odeslané členům skupiny akcí, vyberte akce v části **Přizpůsobit akce**.
1. V **části Podrobnosti výstrahy**zadejte název pravidla výstrahy a přidejte volitelný popis.
1. Nastavte **úroveň závažnosti** události. Doporučujeme nastavit na **kritickou (Sev 0)**.
1. V části **Povolit pravidlo při vytváření**ponechejte nastavené jako **ano**.
1. Chcete-li výstrahy na chvíli vypnout, zaškrtněte políčko **Potlačit výstrahy,** před znovu upozorňováním zadejte dobu čekání a pak vyberte **Uložit**.
1. Klepněte na **tlačítko Vytvořit pravidlo výstrahy**.

### <a name="create-an-action-group"></a>Vytvoření skupiny akcí

1. Vyberte **Vytvořit skupinu akcí**.

    ![vytvoření skupiny akcí pro akce oznámení](./media/directory-emergency-access/action-group-image3.png)

1. Zadejte název skupiny akcí a krátký název.
1. Ověřte předplatné a skupinu prostředků.
1. V části Typ akce vyberte **E-mail/SMS/Push/Voice**.
1. Zadejte název akce, například **Upozornit globálního správce**.
1. Vyberte **typ akce** jako **e-mail/SMS/Push/Voice**.
1. Vyberte **Upravit podrobnosti,** chcete-li vybrat metody oznámení, které chcete konfigurovat, a zadat požadované kontaktní informace a pak vyberte **Ok,** chcete-li podrobnosti uložit.
1. Přidejte všechny další akce, které chcete spustit.
1. Vyberte **OK**.

## <a name="validate-accounts-regularly"></a>Pravidelné ověřování účtů

Když školíte zaměstnance, aby používali účty nouzového přístupu a ověřovali účty nouzového přístupu, proveďte v pravidelných intervalech alespoň následující kroky:

- Ujistěte se, že pracovníci monitorování zabezpečení jsou si vědomi, že aktivita kontroly účtu probíhá.
- Ujistěte se, že proces nouzového přerušení skla pro použití těchto účtů je dokumentován a aktuální.
- Ujistěte se, že správci a pracovníci zabezpečení, kteří mohou potřebovat provést tyto kroky během nouzové situace, jsou vyškoleni v procesu.
- Aktualizujte přihlašovací údaje účtu, zejména všechna hesla, pro vaše účty pro nouzový přístup a poté ověřte, zda se účty pro nouzový přístup mohou přihlásit a provádět úkoly správy.
- Ujistěte se, že uživatelé nezaregistrovali vícefaktorové ověřování nebo samoobslužné resetování hesla (SSPR) do zařízení nebo osobních údajů žádného jednotlivého uživatele. 
- Pokud jsou účty registrované pro vícefaktorové ověřování na zařízení, pro použití během přihlášení nebo aktivace role, ujistěte se, že zařízení je přístupné všem správcům, kteří jej možná potřebují použít během nouze. Ověřte také, zda zařízení může komunikovat prostřednictvím alespoň dvou síťových cest, které nesdílejí společný režim selhání. Zařízení může například komunikovat s internetem prostřednictvím bezdrátové sítě zařízení i sítě poskytovatele mobilních sítí.

Tyto kroky by měly být prováděny v pravidelných intervalech a pro klíčové změny:

- Alespoň každých 90 dní
- Pokud došlo k nedávné změně v it personálu, jako je změna zaměstnání, odchod nebo nový zaměstnanec
- Když se změní předplatná Azure AD v organizaci

## <a name="next-steps"></a>Další kroky

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](directory-admin-roles-secure.md)
- [Přidání uživatelů pomocí služby Azure AD](../fundamentals/add-users-azure-active-directory.md) a [přiřazení nového uživatele k roli globálního správce](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Zaregistrujte se k Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), pokud jste se ještě nezaregistrovali
- [Jak vyžadovat dvoustupňové ověření pro uživatele](../authentication/howto-mfa-userstates.md)
- [Konfigurace dalších ochran pro globální správce v Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), pokud používáte Office 365
- [Spuštění kontroly přístupu globálních správců](../privileged-identity-management/pim-how-to-start-security-review.md) a [přechod stávajících globálních správců na konkrétnější role správce](directory-assign-admin-roles.md)
