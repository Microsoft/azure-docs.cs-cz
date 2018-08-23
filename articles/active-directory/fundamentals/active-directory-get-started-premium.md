---
title: Registrace služby Azure AD Premium | Microsoft Docs
description: Vysvětluje, jak si zaregistrovat edici Azure Active Directory Premium.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: a5ede142b7f3dd314d1a6b3eb8f100ec55a4ea4e
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42023953"
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Rychlý start: Registrace služby Azure Active Directory Premium
Pokud chcete začít pracovat s Azure Active Directory (Azure AD) Premium, můžete si koupit licence a přidružit je k vašemu předplatnému Azure. Pokud vytvoříte nové předplatné Azure, bude také potřeba aktivovat váš licenční plán a přístup do služby Azure AD podle postupu popsaného v následujících částech. 

## <a name="sign-up-for-active-directory-premium"></a>Registrace služby Active Directory Premium
Active Directory Premium si můžete registrovat několika způsoby: 
* Použitím předplatného Azure nebo Office 365.
* Použitím licenčního plánu Enterprise Mobility + Security.
* Použitím plánu multilicenčního programu společnosti Microsoft.

### <a name="azure-or-office-365"></a>Azure nebo Office 365 
Jako předplatitel Azure nebo Office 365 si můžete koupit Azure Active Directory Premium online. 

Podrobný postup naleznete v tématu [Jak zakoupit Azure Active Directory Premium – stávající zákazníci](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) nebo [Jak zakoupit Azure Active Directory Premium – noví zákazníci](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security (EMS) je pro organizace nákladově efektivní způsob používání následujících služeb společně v rámci jednoho licenčního plánu: Azure Active Directory Premium, Azure Information Protection a Microsoft Intune. Další informace o EMS najdete na [webu Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security). Další informace o typech licencí EMS, které se dají koupit, najdete na stránce [Cenové možnosti pro Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

Začít s Azure AD prostřednictvím licencí EMS můžete s využitím některé z následujících možností licencování:

- Vyzkoušení EMS pomocí bezplatné [zkušební verze předplatného Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)
- Nákup [licencí Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Nákup [licencí Enterprise Mobility + Security E3](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Multilicenční program společnosti Microsoft
Azure Active Directory Premium je k dispozici prostřednictvím programu [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 nebo více licencí) nebo [Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licencí).

Další informace o možnostech nákupu multilicencí najdete na stránce [Jak nakupovat prostřednictvím multilicenčního programu](https://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

> [!NOTE]
> Edice Premium a Basic služby Azure Active Directory jsou zákazníkům v Číně dostupné prostřednictvím celosvětové instance služby Azure Active Directory. Edice Premium a Basic služby Azure Active Directory nejsou aktuálně podporované ve službě Microsoft Azure provozované v Číně společností 21Vianet. Další informace si vyžádejte na [fóru služby Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Pokud jste už dříve koupili a aktivovali licence Azure AD pro stejné předplatné Azure, které jste použili v předchozích krocích, pak se licence automaticky aktivují ve stejném adresáři. V opačném případě pokračujte podle pokynů popsaných ve zbývající části tohoto článku.

## <a name="activate-your-license-plan"></a>Aktivace licenčního plánu
Je toto první licenční plán Azure AD, který jste si koupili od Microsoftu? Pokud ano, po dokončení nákupu obdržíte vygenerovaný potvrzovací e-mail. Tento e-mail je potřebný k aktivaci prvního licenčního plánu.

**Aktivaci licenčního plánu provedete následovně:**

1. Spusťte aktivaci kliknutím na možnost **Přihlásit se** nebo **Zaregistrovat**.
   
    ![Přihlášení](media/active-directory-get-started-premium/MOLSEmail.png)

    - Pokud máte existujícího klienta, klikněte na tlačítko **Přihlásit se** a přihlaste se pomocí svého stávajícího účtu správce. Přihlaste se pomocí přihlašovacích údajů globálního správce pro tenanta, ve kterém se mají aktivovat licence.

    - Pokud chcete vytvořit nového tenanta Azure AD, který se bude používat s vaším licenčním plánem, klikněte na **Zaregistrovat se** a otevře se okno **Vytvořit profil účtu**.

        ![Vytvoření profilu účtu](media/active-directory-get-started-premium/MOLSAccountProfile.png)

Až budete hotovi, zobrazí se následující dialogové okno s potvrzením aktivace licenčního plánu pro vašeho tenanta:

![Potvrzení](media/active-directory-get-started-premium/MOLSThankYou.png)

## <a name="activate-your-azure-active-directory-access"></a>Aktivace přístupu do služby Azure Active Directory
Pokud přidáváte nové licence Azure AD Premium do existujícího předplatného, přístup do služby Azure AD by už měl být aktivovaný. Jinak je potřeba aktivovat přístup do služby Azure AD po obdržení **uvítacího e-mailu**.  

Pokud jsou zakoupené licence zřízené ve vašem adresáři, bude vám zaslán **uvítací e-mail**. E-mail bude obsahovat potvrzení, že můžete začít spravovat licence a funkce Azure Active Directory Premium nebo Enterprise Mobility + Security. 

> [!TIP]
> Dokud neaktivujete přístup k adresáři služby Azure AD pomocí uvítacího e-mailu odeslaného automaticky při dokončení procesu zřizování licence, nebudete mít přístup do služby Azure AD pro vašeho nového tenanta. 

**Pokud chcete aktivovat přístup do služby Azure AD, postupujte takto:**

1. V **uvítacím e-mailu** klikněte na tlačítko **Přihlásit**. 
   
    ![Uvítací e-mail](media/active-directory-get-started-premium/AADEmail.png)
2. Po úspěšném přihlášení je potřeba dokončit také druhý faktor ověřování s použitím mobilního zařízení:
   
    ![Mobilní ověření](media/active-directory-get-started-premium/SignUppage.png)

Aktivace by měla trvat jenom pár minut a potom budete mít přístup ke správě služby Azure AD. 

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zjistili, jak si zaregistrovat službu Azure AD Premium a aktivovat přístup do služby Azure Active Directory. 

Pokud už máte předplatné Azure, můžete použít následující odkaz a začít se zkušební verzí nebo koupit licence Azure AD Premium na webu Azure Portal.

> [!div class="nextstepaction"]
> [Aktivace licencí Azure AD Premium](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade)
