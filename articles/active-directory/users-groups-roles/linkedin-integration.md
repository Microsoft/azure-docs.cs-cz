---
title: Povolit připojení LinkedIn Microsoft aplikací a služeb v Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak povolit nebo zakázat s účtem LinkedIn pro aplikace Microsoftu ve službě Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871682"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>S účtem LinkedIn k aplikacím a službám Microsoftu
V tomto článku se dozvíte, jak spravovat s účtem LinkedIn pro vašeho tenanta v Centru pro správu Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> Funkce připojení účtu LinkedIn se právě zavádí s tenanty Azure AD. Když se nasazuje pro vašeho tenanta, je standardně povolená. Není k dispozici pro zákazníky ze státní správy USA a organizace s Exchange Online poštovních schránek hostovaných v Austrálii, Kanada, Čína, Francie, Německo, Indie, Jižní Korea, Spojené království, Japonsko a Jižní Afrika. Podpora těchto umístění poštovní schránky se připravuje.  Aktuální přehled o uvedení informace, najdete v článku [plán Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) stránky.

## <a name="benefit-to-users"></a>Výhody pro uživatele
Až uživatelé připojíte svůj účet LinkedIn, LinkedIn informace slouží k zobrazení individuálních informací a funkcí v různých Microsoft aplikacím nebo službám. Uživatelé mohou zobrazit informace o lidech, že pracovat na kartě profil Microsoft, i když jsou lidé mimo vaši organizaci. V průběhu času bude také jejich zkušenostem LinkedIn relevantní a přizpůsobené k jejich práci. Například můžete LinkedIn navrhnout nová připojení na základě kteří uživatelé pracují s nebo surface informací o lidech, jejich kalendáře daný den.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Jak se s účtem LinkedIn zobrazovat uživateli.
S účtem LinkedIn povolit uživatelům zobrazit veřejné informace o profilu LinkedIn do některých aplikací společnosti Microsoft. Uživatelé ve vašem tenantovi můžete připojit jejich LinkedIn a Microsoft pracovní nebo školní účty zobrazíte další informace o profilu LinkedIn. Další informace najdete v tématu [LinkedIn informace a funkce v aplikacím a službám Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740).

Pokud uživatelé ve vaší organizaci připojit svou práci LinkedIn a Microsoft nebo školních účtů, mají dvě možnosti: 
* Udělit oprávnění ke sdílení dat mezi oba účty. To znamená, že se udělit oprávnění pro svůj účet LinkedIn sdílet data s jejich pracovní nebo školní účet, jakož i jejich Microsoft pracovní nebo školní účet ke sdílení dat se svým účtem LinkedIn. Data, která je sdílena s Linkedinem opustí služeb online services. 
* Udělit oprávnění ke sdílení dat jenom z jejich účet LinkedIn, abyste svoje pracovní a školní účet

Další informace o datech, která jsou sdílena mezi Microsoft a LinkedIn uživatelova pracovní nebo školní účty, najdete v článku [Linkedinu v aplikacích Microsoftu na vaše firma nebo škola](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Uživatelům můžete odpojit účty](https://www.linkedin.com/help/linkedin/answer/85097) a odeberte oprávnění ke sdílení kdykoli data. 
* [Uživatelé můžou řídit, jak zobrazit své vlastní profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), včetně Určuje, zda jejich profil může zobrazovat v aplikacích Microsoftu.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů
Povolení LinkedIn účtu připojení umožňuje aplikacím a službám přístup k některé z vašich uživatelů LinkedIn informace Microsoftu. Přečtěte si [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement/) Další informace o aspektech ochrany osobních údajů při povolování s účtem LinkedIn ve službě Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Správa s účtem LinkedIn
Funkce připojení účtu LinkedIn je ve výchozím pro celého tenanta. Můžete s účtem LinkedIn pro celého tenanta, povolení nebo zakázání s účtem LinkedIn pro vybrané uživatele ve vašem tenantovi. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Povolit nebo zakázat připojení LinkedIn účtu pro vašeho tenanta na portálu Azure portal

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce pro tenanta Azure AD.
2. Vyberte **uživatelé**.
3. Na **uživatelé** okně vyberte **uživatelská nastavení**.
4. V části **s účtem LinkedIn**:
  * Vyberte **Ano** povolit s účtem LinkedIn pro všechny uživatele ve vašem tenantovi
  * Vyberte **vybrané** povolit účet LinkedIn připojení pouze vybrané tenanta uživatele
  * Vyberte **ne** zakázat s účtem LinkedIn pro všechny uživatele ![povolení s účtem LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Až to uděláte tak, že vyberete uložit nastavení **Uložit**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Povolení nebo zakázání s účtem LinkedIn pro aplikace Office 2016 vaší organizace pomocí zásad skupiny

1. Stáhněte si [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahovat **ADMX** soubory a zkopírujte je do centrálního úložiště.
3. Otevřete správu zásad skupiny.
4. Vytvoření objektu zásad skupiny s následujícím nastavením: **konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016**  >  **Různé** > **funkcí služby LinkedIn zobrazit v aplikacích Office**.
5. Vyberte **povolené** nebo **zakázané**.
  * Když je zásada **povoleno**, **funkcí služby LinkedIn zobrazit v aplikacích Office** je povolené nastavení v dialogovém okně Možnosti Office 2016. To také znamená, že uživatelé ve vaší organizaci můžou používat funkce Linkedinu v aplikacích Office.
  * Když je zásada **zakázané**, **funkcí služby LinkedIn zobrazit v aplikacích Office** nastavení nalezených v možnostech Office 2016 dialogového okna je nastavena na zakázaném stavu, a toto nastavení nejde změnit koncovým uživatelům. Uživatelé ve vaší organizaci nemůžou používat funkce Linkedinu ve svých aplikacích Office 2016.

Tyto zásady skupiny ovlivní pouze aplikace Office 2016 pro místní počítač. Uživatelé mohou vidět funkce Linkedinu v profilové karty v rámci Office 365, i když se zakážou LinkedIn ve svých aplikacích Office 2016. 

### <a name="learn-more"></a>Další informace 
* [LinkedIn informace a funkce v aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pro nápovědu LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Další postup
Pomocí následujícího odkazu zobrazíte vaše aktuální s účtem LinkedIn nastavení na portálu Azure portal:

[Konfigurace s účtem LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 