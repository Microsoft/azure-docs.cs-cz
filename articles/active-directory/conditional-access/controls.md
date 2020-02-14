---
title: Řízení přístupu v Azure Active Directory podmíněný přístup
description: Přečtěte si, jak ovládací prvky přístupu v Azure Active Directory fungují v podmíněném přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8894078a15bf37a5383cdff3721f4bf7be910
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186214"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Co jsou ovládací prvky přístupu v Azure Active Directory podmíněný přístup?

Pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)můžete řídit, jak autorizovaným uživatelům přistupují k vašim cloudovým aplikacím. V zásadách podmíněného přístupu definujete odpověď ("Udělejte to") na důvod, který aktivuje vaše zásady ("Pokud k tomu dojde").

![Řízení](./media/controls/10.png)

V kontextu podmíněného přístupu

- "**Pokud k tomu dojde**, se nazývají **podmínky** "
- **Pak se tato**možnost nazývá **řízení přístupu** .

Kombinace příkazu Condition s ovládacími prvky představuje zásadu podmíněného přístupu.

![Řízení](./media/controls/61.png)

Každý ovládací prvek je buď požadavkem, který musí splňovat osoba nebo systém přihlašování, nebo omezení, co může uživatel provést po přihlášení.

Existují dva typy ovládacích prvků:

- **Udělení řízení** – přístup k bráně
- **Řízení relace** – omezení přístupu v rámci relace

Toto téma vysvětluje různé ovládací prvky, které jsou k dispozici v rámci podmíněného přístupu Azure AD. 

## <a name="grant-controls"></a>Udělit ovládací prvky

Pomocí ovládacích prvků grant můžete buď zablokovat přístup zcela, nebo povolit přístup s dalšími požadavky výběrem požadovaných ovládacích prvků. Pro více ovládacích prvků můžete vyžadovat:

- Všechny vybrané ovládací prvky, které se mají splnit (*a*)
- Jeden vybraný ovládací prvek k splnění (*nebo*)

![Řízení](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Tento ovládací prvek můžete použít k vyžádání vícefaktorového ověřování pro přístup k zadané cloudové aplikaci. Tento ovládací prvek podporuje následující Multi-Factor Providers:

- Azure Multi-Factor Authentication
- Místní zprostředkovatel vícefaktorového ověřování v kombinaci s Active Directory Federation Services (AD FS) (AD FS).

Použití služby Multi-Factor Authentication pomáhá chránit prostředky před přístupem neoprávněného uživatele, který by mohl získat přístup k primárním přihlašovacím údajům platného uživatele.

### <a name="compliant-device"></a>Odpovídající zařízení

Můžete nakonfigurovat zásady podmíněného přístupu, které jsou založené na zařízení. Cílem zásad podmíněného přístupu na základě zařízení je udělit jenom přístup k vybraným cloudovým aplikacím ze [spravovaných zařízení](require-managed-devices.md). Vyžadování zařízení, které je označeno jako vyhovující, je jedna z možností, kterou musíte omezit přístup ke spravovaným zařízením. Zařízení může být označeno jako kompatibilní s Intune (pro libovolný operační systém zařízení) nebo systémem MDM jiného výrobce pro zařízení s Windows 10. Systémy MDM třetích stran pro jiné typy operačních systémů zařízení, než je Windows 10, se nepodporují. 

Vaše zařízení musí být zaregistrované ve službě Azure AD, aby bylo možné ho označit jako kompatibilní. K registraci zařízení máte tři možnosti: 

- Zařízení zaregistrovaná v Azure AD
- Zařízení připojená k Azure AD  
- Hybridní zařízení připojená k Azure AD

Tyto tři možnosti jsou popsány v článku [co je identita zařízení?](../devices/overview.md)

Další informace najdete v tématu [jak vyžadovat spravovaná zařízení pro přístup k cloudovým aplikacím pomocí podmíněného přístupu](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Zařízení připojené k hybridní službě Azure AD

Vyžadování zařízení připojeného k hybridní službě Azure AD je další možností, jak musíte nakonfigurovat zásady podmíněného přístupu na základě zařízení. Tento požadavek se vztahuje na stolní počítače, přenosné počítače a podnikové tablety, které jsou připojené k místní službě Active Directory. Pokud je vybraná tato možnost, zásada podmíněného přístupu udělí přístup k pokusům o přístup pomocí zařízení, která jsou připojená k místní službě Active Directory a k vašemu Azure Active Directory. Zařízení Mac nepodporují hybridní službu Azure AD JOIN.

Další informace najdete v tématu [nastavení Azure Active Directory zásad podmíněného přístupu na základě zařízení](require-managed-devices.md).

### <a name="approved-client-app"></a>Schválená klientská aplikace

Vzhledem k tomu, že vaši zaměstnanci používají mobilní zařízení pro osobní i pracovní úkoly, možná budete chtít chránit podniková data, která používají zařízení, i v případě, že je nespravujete sami.
[Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) můžete použít k ochraně firemních dat nezávisle na řešení správy mobilních zařízení (MDM).

Se schválenými klientskými aplikacemi můžete vyžadovat klientskou aplikaci, která se pokusí o přístup k vašim cloudovým aplikacím, aby podporovala [Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy). Můžete například omezit přístup k Exchangi Online do aplikace Outlook. Zásady podmíněného přístupu, které vyžadují schválené klientské aplikace, se taky označují jako [zásady podmíněného přístupu na základě aplikace](app-based-conditional-access.md). Seznam podporovaných autorizovaných klientských aplikací najdete v tématu [schválení klientské aplikace – požadavek](concept-conditional-access-grant.md#require-approved-client-app).

### <a name="app-protection-policy-preview"></a>Zásady ochrany aplikací (Preview)

Vzhledem k tomu, že vaši zaměstnanci používají mobilní zařízení pro osobní i pracovní úkoly, možná budete chtít chránit podniková data, která používají zařízení, i v případě, že je nespravujete sami.
[Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy) můžete použít k ochraně firemních dat nezávisle na řešení správy mobilních zařízení (MDM).

Pomocí zásad ochrany aplikací můžete omezit přístup k klientským aplikacím, které nahlásily službě Azure AD, a které přijaly [Zásady ochrany aplikací Intune](https://docs.microsoft.com/intune/app-protection-policy). Můžete například omezit přístup k Exchangi Online na aplikaci Outlook, která má zásady ochrany aplikací Intune. Zásady podmíněného přístupu, které vyžadují zásady ochrany aplikací, se taky označují jako [zásady podmíněného přístupu na základě ochrany aplikací](concept-conditional-access-session.md#application-enforced-restrictions). 

Aby bylo možné aplikaci označit jako chráněnou zásadou, musí být vaše zařízení zaregistrované ve službě Azure AD.

Seznam podporovaných klientských aplikací chráněných zásadami najdete v tématu [požadavky na zásady ochrany aplikací](concept-conditional-access-session.md#application-enforced-restrictions).

### <a name="terms-of-use"></a>Podmínky použití

Před udělením přístupu k prostředku můžete vyžadovat, aby uživatel ve vašem tenantovi souhlasil s podmínkami použití. Jako správce můžete nakonfigurovat a přizpůsobit podmínek použití nahráním dokumentu PDF. Pokud uživatel spadá do rozsahu tohoto řízení přístup k aplikaci, udělí se jenom v případě, že se podmínek použití dohodly.

## <a name="custom-controls-preview"></a>Vlastní ovládací prvky (Preview)

Vlastní ovládací prvky jsou funkcemi edice Azure Active Directory Premium P1. Při použití vlastních ovládacích prvků jsou vaši uživatelé přesměrováni na kompatibilní službu, aby splňovaly další požadavky mimo Azure Active Directory. Aby bylo možné tento ovládací prvek vyhovět, bude prohlížeč uživatele přesměrován na externí službu, provede všechny požadované ověřovací nebo ověřovací aktivity a pak bude přesměrován zpět na Azure Active Directory. Azure Active Directory ověří odpověď, a pokud byl uživatel úspěšně ověřen nebo ověřen, uživatel pokračuje v toku podmíněného přístupu.

Tyto ovládací prvky umožňují použití určitých externích nebo vlastních služeb jako řízení podmíněného přístupu a obecně rozšiřuje možnosti podmíněného přístupu.

Mezi poskytovatele, kteří aktuálně nabízejí kompatibilní služby, patří:

- [Zabezpečení Duo](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ověřit identitu](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Virtuální IP adresa Symantec](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (identita Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Další informace o těchto službách získáte přímo od poskytovatelů.

### <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Chcete-li vytvořit vlastní ovládací prvek, je třeba nejprve kontaktovat poskytovatele, kterého chcete využít. Každý poskytovatel od poskytovatele Microsoftu má svůj vlastní proces a požadavky na registraci, přihlášení k odběru nebo jinou součást služby a k označení, že chcete integraci s podmíněným přístupem. Od tohoto okamžiku vám poskytovatel poskytne blok dat ve formátu JSON. Tato data umožňují poskytovateli a podmíněným přístupům spolupracovat pro vašeho tenanta, vytvoří nový ovládací prvek a určí, jak podmíněný přístup může zjistit, jestli se uživatelé úspěšně provedli ověření u poskytovatele.

Vlastní ovládací prvky nelze použít s automatizací Identity Protection, která vyžaduje vícefaktorové ověřování, nebo zvýšení rolí v Privileged identity Manageru (PIM).

Zkopírujte data JSON a pak je vložte do příslušného textového pole. Neprovádějte žádné změny JSON, pokud výslovně nerozumíte změně, kterou provádíte. Provedení jakékoli změny by mohlo způsobit přerušení spojení mezi poskytovatelem a společností Microsoft a tím, že vás a vaši uživatelé budou moci uzamknout z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v části **Správa** na stránce **podmíněný přístup** .

![Řízení](./media/controls/82.png)

Kliknutím na **Nový vlastní ovládací prvek**otevře okno s textovým polem pro data JSON ovládacího prvku.  

![Řízení](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Odstranění vlastních ovládacích prvků

Pokud chcete odstranit vlastní ovládací prvek, musíte nejdřív zkontrolovat, že se nepoužívá v žádné zásadě podmíněného přístupu. Po dokončení:

1. Přejít na seznam vlastních ovládacích prvků
1. Klikněte na...  
1. Vyberte **Odstranit**.

### <a name="editing-custom-controls"></a>Úprava vlastních ovládacích prvků

Chcete-li upravit vlastní ovládací prvek, je nutné odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek s aktualizovanými informacemi.

## <a name="session-controls"></a>Ovládací prvky relace

Ovládací prvky relace umožňují omezené prostředí v rámci cloudové aplikace. Ovládací prvky relace jsou vynutily cloudové aplikace a využívají další informace, které Azure AD poskytuje do aplikace o této relaci.

![Řízení](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Použít omezení pro uplatnění aplikace

Tento ovládací prvek můžete použít, pokud chcete, aby služba Azure AD předávala informace o zařízení vybraným cloudovým aplikacím. Informace o zařízení umožňuje cloudovým aplikacím zjistit, jestli je připojení iniciované z kompatibilního zařízení nebo zařízení připojeného k doméně. Tento ovládací prvek podporuje pouze SharePoint Online a Exchange Online jako vybrané cloudové aplikace. Když vyberete tuto možnost, cloudová aplikace použije informace o zařízení k poskytování uživatelů v závislosti na stavu zařízení s omezeným nebo úplným prostředím.

Další informace naleznete v tématu:

- [Povolení omezeného přístupu na SharePointu Online](https://aka.ms/spolimitedaccessdocs)
- [Povolení omezeného přístupu pomocí Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
