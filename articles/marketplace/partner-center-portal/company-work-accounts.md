---
title: Společnost pracovních účtů a partnerského centra
description: Jak zkontrolovat, jestli má vaše společnost pracovní účet s Microsoft, vytvořte nový pracovní účet nebo nastavení více pracovních účtů pro použití s partnerského centra.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806278"
---
# <a name="company-work-accounts-and-partner-center"></a>Společnost pracovních účtů a partnerského centra

Partnerské centrum společnosti pracovních účtů, označované také jako tenantů Azure Active Directory (AD) používá ke správě přístupu k účtu pro více uživatelů, řídit oprávnění, skupiny hostitelů a aplikace a spravovat data profilu. Odkazování na váš účet v partnerském centru společnosti pracovní e-mailu účet domény, zaměstnanci vaší společnosti můžete přihlásit do partnerského centra a spravovat své vlastní pracovní účet uživatelských jmen a hesel pomocí nabídky na webu marketplace.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Zkontrolujte, jestli už má vaše společnost pracovní účet

Pokud vaše společnost se připojila ke cloudové službě Microsoftu, jako je například Azure, Microsoft Intune nebo Office 365, už máte pracovní e-mailu účet domény (také označované jako tenanta služby Azure Active Directory), která lze použít s partnerského centra.

Postupujte podle těchto kroků ke kontrole:
1. Přihlaste se k portálu pro správu Azure na https://portal.azure.com.
2. Vyberte **Azure Active Directory** z levé navigační nabídce a potom vyberte **vlastní názvy domén**.
3. Pokud už máte pracovní účet, zobrazí se název domény.

Pokud vaše společnost ještě nemá pracovní účet, bude vytvořen pro vás během procesu registrace partnerského centra.

## <a name="set-up-multiple-work-accounts"></a>Nastavit víc pracovních účtů

Než se rozhodnete použít stávající pracovní účet, zvažte, kolik uživatelů použije pracovní účet potřebovat pro přístup k partnerskému centru. Pokud pracovní účet, který nebudete potřebovat pro přístup k partnerskému centru máte uživatele, můžete zvážit vytvoření více pracovních účtů, tak, aby pouze uživatelé, kteří potřebují přístup k partnerskému centru jsou reprezentovány v konkrétním účtu.

## <a name="create-a-new-work-account"></a>Vytvořte nový pracovní účet

K vytvoření nového pracovního účtu vaší společnosti, postupujte podle následujících kroků. Budete muset požádat o pomoc, kdo má oprávnění správce k účtu vaší společnosti Microsoft Azure.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).
2. V levé navigační nabídce vyberte **Azure Active Directory** -> **uživatelé**.
3. Vyberte **nového uživatele** a vytvořit nový účet Azure pracovní tak, že zadáte jméno a e-mailovou adresu. Ujistěte se, že **role adresáře** je nastavena na **uživatele** a vyberte **zobrazit heslo** zaškrtávací políčko v dolní části zobrazení a poznamenejte si automaticky vytvořené heslo.
4. Vyberte **vytvořit** uložit nového uživatele.

E-mailovou adresu pro uživatelský účet musí být název ověřené domény ve vašem adresáři. Můžete vytvořit seznam ověřených domén ve vašem adresáři tak, že vyberete **Azure Active Directory** -> **vlastní názvy domén** v levé navigační nabídce.

Další informace o přidávání vlastních domén v Azure Active Directory najdete v tématu [přidat nebo přidružení domény ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Řešení potíží s přihlášení pracovní e-mailu

Pokud máte potíže při přihlašování ke svému pracovnímu účtu (označované také jako vašeho tenanta Azure AD), najít že scénář v diagramu níže, které bude nejlépe odpovídá vaší situaci a proveďte doporučené kroky.

![Diagram pro řešení potíží s přihlášením pracovní účet](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Další postup

- [Správa účtu komerční Marketplace v partnerském centru](./manage-account.md) 
