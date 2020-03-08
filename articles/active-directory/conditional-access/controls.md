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
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671848"
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

## <a name="next-steps"></a>Další kroky

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)

- [Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
