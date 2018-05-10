---
title: Povolit připojení LinkedIn pro Microsoft aplikace a služby v Azure Active Directory | Microsoft Docs
description: Vysvětluje, jak povolit nebo zakázat připojení LinkedIn účet Microsoft Apps v Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/02/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 3d73cae0975bea0332c2d266d2745773b373b7d8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Účet připojení LinkedIn Microsoft aplikace a služby
V tomto článku se můžete naučit ke správě připojení účtu LinkedIn pro vašeho klienta v Centru správy služby Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> Funkce připojení účtu LinkedIn se zrovna vydávají pro klienty Azure AD. Když je vrátit ke klientovi, povolí se ve výchozím nastavení. Není k dispozici pro Spojené státy government zákazníků a organizace s Exchange Online poštovním schránkám hostovaným v Austrálie, Kanada, Čína, Francie, Německo, Indie, Jižní Korea, Spojené království, Japonsko a Jihoafrická republika. Podpora pro tyto umístění poštovní schránky bude brzo.  Aktuální zobrazení informací o zavedení, najdete v článku [plán Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) stránky.

## <a name="benefit-to-users"></a>Využívat pro uživatele
Po uživatelé připojit svůj účet LinkedIn, LinkedIn informace slouží k zobrazení přizpůsobené informace a funkce v různých Microsoft aplikací nebo služeb. Uživatelé mohou vidět informace o osoby, že fungují s kartou profil společnosti Microsoft, i když jsou uživatelé mimo vaši organizaci. V čase bude také jejich prostředí LinkedIn více relevantní a šité na míru k jejich práci. Například můžete LinkedIn navrhovat nová připojení na základě kdo uživatelé pracovat s nebo plochy přehledy o osoby v jejich kalendáře daný den.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Jak LinkedIn účet připojení zobrazí uživateli
Připojení účtu LinkedIn povolit uživatelům zobrazit veřejné informace o LinkedIn profilu v některé z aplikace Microsoft. Uživatelé ve vašem klientovi se mohou připojit jejich LinkedIn a Microsoft pracovní či školní účty zobrazíte další informace o profilu LinkedIn. Další informace najdete v tématu [LinkedIn informace a funkce v Microsoft aplikace a služby](https://go.microsoft.com/fwlink/?linkid=850740).

Pokud uživatelé ve vaší organizaci připojit jejich LinkedIn a Microsoft pracovní nebo školní účty, mají dvě možnosti: 
* Udělte oprávnění ke sdílení dat mezi oba účty. To znamená, že jejich udělit oprávnění pro svůj účet LinkedIn sdílení dat s jejich Microsoft pracovní nebo školní účet, jakož i jejich Microsoft pracovní nebo školní účet ke sdílení dat pomocí svého účtu LinkedIn. Data, která je sdílena s LinkedIn zanechává online služeb. 
* Udělit oprávnění ke sdílení dat jenom z jejich LinkedIn účet jejich Microsoft pracovní a školní účet

Další informace o datech, která jsou sdílena mezi LinkedIn a Microsoft uživatelů pracovní nebo školní účty, najdete v části [LinkedIn v aplikacích společnosti Microsoft v práci nebo škole](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Uživatele můžete odpojit účty](https://www.linkedin.com/help/linkedin/answer/85097) a odeberte oprávnění ke sdílení kdykoli data. 
* [Uživatelé můžou řídit, jak zobrazit svůj vlastní profil LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), včetně jestli svůj profil lze zobrazit v aplikacích společnosti Microsoft.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů
Povolení LinkedIn umožňuje připojení účtu Microsoft aplikacím a službám přístup k některé z vašich uživatelů LinkedIn informací. Pro čtení [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement/) Další informace o aspektech ochrany osobních údajů při povolování LinkedIn účet připojení ve službě Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Správa připojení účtu LinkedIn
Funkce připojení účtu LinkedIn je ve výchozím pro celý klienta. Můžete zakázat LinkedIn účet připojení pro celý klienta nebo povolit LinkedIn účet připojení pro vybrané uživatele ve vašem klientovi. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Povolit nebo zakázat LinkedIn účet připojení pro vašeho tenanta na portálu Azure

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který je globální správce pro tenanta Azure AD.
2. Vyberte **uživatelé**.
3. Na **uživatelé** vyberte **uživatelská nastavení**.
4. V části **LinkedIn účet připojení**:
  * Vyberte **Ano** povolit připojení LinkedIn účet pro všechny uživatele ve vašem klientovi
  * Vyberte **vybrané** povolit účet LinkedIn připojení pouze vybraného klienta uživatele
  * Vyberte **ne** zakázat LinkedIn účet připojení pro všechny uživatele ![povolení LinkedIn účet připojení](./media/linkedin-integration/LinkedIn-integration.png)
5. Uložit nastavení, když jste hotovi výběrem **Uložit**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Povolení nebo zakázání LinkedIn účet připojení pro aplikace Office 2016 vaší organizace pomocí zásad skupiny

1. Stažení [souborů šablon pro správu Office 2016 (ADMX nebo ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahování **ADMX** soubory a zkopírujte je do centrálního úložiště.
3. Otevřete správu zásad skupiny.
4. Vytvoření objektu zásad skupiny s následujícím nastavením: **konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016**  >  **Různé** > **LinkedIn zobrazit funkce v aplikacích Office**.
5. Vyberte **povoleno** nebo **zakázané**.
  * Pokud tato zásada je **povoleno**, **LinkedIn zobrazit funkce v aplikacích Office** je povoleno nastavení v dialogovém okně Možnosti Office 2016. Také to znamená, že uživatelé ve vaší organizaci můžete použít funkce LinkedIn ve svých aplikacích Office.
  * Pokud tato zásada je **zakázáno**, **LinkedIn zobrazit funkce v aplikacích Office** nastavení nalezených v možnostech Office 2016 dialogové okno nastavena na zakázaném stavu, a toto nastavení nelze změnit koncovým uživatelům. Uživatelé ve vaší organizaci LinkedIn funkce nelze používat ve svých aplikacích Office 2016. 

Tato zásada skupiny ovlivní pouze aplikace Office 2016 pro místní počítač. Uživatelé mohou vidět LinkedIn funkcí v profilu karty v rámci služeb Office 365, i když se zakážou LinkedIn ve svých aplikacích Office 2016. 

### <a name="learn-more"></a>Další informace 
* [Informace o LinkedIn a funkcí ve vašich aplikacích společnosti Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pro nápovědu LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Další postup
Pomocí následujícího odkazu zobrazíte aktuální připojení účtu LinkedIn nastavení na portálu Azure:

[Konfigurace připojení účtu LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 