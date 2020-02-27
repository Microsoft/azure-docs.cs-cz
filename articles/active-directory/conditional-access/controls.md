---
title: Vlastní ovládací prvky v podmíněném přístupu Azure AD
description: Přečtěte si, jak vlastní ovládací prvky v Azure Active Directory podmíněný přístup fungují.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620715"
---
# <a name="custom-controls-preview"></a>Vlastní ovládací prvky (Preview)

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

## <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Chcete-li vytvořit vlastní ovládací prvek, je třeba nejprve kontaktovat poskytovatele, kterého chcete využít. Každý poskytovatel od poskytovatele Microsoftu má svůj vlastní proces a požadavky na registraci, přihlášení k odběru nebo jinou součást služby a k označení, že chcete integraci s podmíněným přístupem. Od tohoto okamžiku vám poskytovatel poskytne blok dat ve formátu JSON. Tato data umožňují poskytovateli a podmíněným přístupům spolupracovat pro vašeho tenanta, vytvoří nový ovládací prvek a určí, jak podmíněný přístup může zjistit, jestli se uživatelé úspěšně provedli ověření u poskytovatele.

Vlastní ovládací prvky nelze použít s automatizací Identity Protection, která vyžaduje vícefaktorové ověřování, nebo zvýšení rolí v Privileged identity Manageru (PIM).

Zkopírujte data JSON a pak je vložte do příslušného textového pole. Neprovádějte žádné změny JSON, pokud výslovně nerozumíte změně, kterou provádíte. Provedení jakékoli změny by mohlo způsobit přerušení spojení mezi poskytovatelem a společností Microsoft a tím, že vás a vaši uživatelé budou moci uzamknout z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v části **Správa** na stránce **podmíněný přístup** .

![Řízení](./media/controls/82.png)

Kliknutím na **Nový vlastní ovládací prvek**otevře okno s textovým polem pro data JSON ovládacího prvku.  

![Řízení](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Odstranění vlastních ovládacích prvků

Pokud chcete odstranit vlastní ovládací prvek, musíte nejdřív zkontrolovat, že se nepoužívá v žádné zásadě podmíněného přístupu. Po dokončení:

1. Přejít na seznam vlastních ovládacích prvků
1. Klikněte na...  
1. Vyberte **Odstranit**.

## <a name="editing-custom-controls"></a>Úprava vlastních ovládacích prvků

Chcete-li upravit vlastní ovládací prvek, je nutné odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek s aktualizovanými informacemi.

## <a name="session-controls"></a>Ovládací prvky relace

Ovládací prvky relace umožňují omezené prostředí v rámci cloudové aplikace. Ovládací prvky relace jsou vynutily cloudové aplikace a využívají další informace, které Azure AD poskytuje do aplikace o této relaci.

![Řízení](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Použít omezení pro uplatnění aplikace

Tento ovládací prvek můžete použít, pokud chcete, aby služba Azure AD předávala informace o zařízení vybraným cloudovým aplikacím. Informace o zařízení umožňuje cloudovým aplikacím zjistit, jestli je připojení iniciované z kompatibilního zařízení nebo zařízení připojeného k doméně. Tento ovládací prvek podporuje pouze SharePoint Online a Exchange Online jako vybrané cloudové aplikace. Když vyberete tuto možnost, cloudová aplikace použije informace o zařízení k poskytování uživatelů v závislosti na stavu zařízení s omezeným nebo úplným prostředím.

Další informace naleznete v tématu:

- [Povolení omezeného přístupu na SharePointu Online](https://aka.ms/spolimitedaccessdocs)
- [Povolení omezeného přístupu pomocí Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
