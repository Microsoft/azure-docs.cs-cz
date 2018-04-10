---
title: Sestavy aktivit auditu na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit auditu na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Generování sestav v Azure Active Directory (Azure AD) umožňuje získat všechny informace potřebné ke zjištění stavu vašeho prostředí.

Architektura generování sestav v Azure AD se skládá z následujících komponent:

- **Aktivita** 
    - **Aktivity přihlašování** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů
    - **Protokoly auditu** – informace aktivit systému o správě uživatelů a skupin, spravovaných aplikacích a aktivitách adresářů
- **Zabezpečení** 
    - **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další podrobnosti najdete v tématu Riziková přihlášení.
    - **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další podrobnosti najdete v tématu Uživatelé označení příznakem rizika.

V toto tématu najdete přehled aktivit auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v roli Správce zabezpečení nebo Čtenář zabezpečení
* Globální správci
* Jednotliví uživatelé (bez oprávnění správce) mohou zobrazit své vlastní aktivity.


## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu v Azure Active Directory obsahují záznamy aktivit systému pro zajištění dodržování předpisů.  
Prvním vstupním bodem k veškerým datům auditování je možnost **Protokoly auditu** v oddílu **Aktivita** služby **Azure Active Directory**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/61.png "Protokoly auditu")

Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- iniciátor/aktér aktivity (*kdo*) 
- aktivita (*co*) 
- cíl

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/18.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/19.png "Protokoly auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/21.png "Protokoly auditu")


Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/22.png "Protokoly auditu")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících polí:

- Rozsah dat
- Spustil(a) (činitel)
- Kategorie
- Typ prostředku aktivity
- Aktivita

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/23.png "Protokoly auditu")


Filtr pro **rozsah dat** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Filtr **Spustil(a)** umožňuje definovat jméno aktéra nebo hlavní název uživatele (UPN).

Filtr **Kategorie** umožňuje vybrat jeden z následujících filtrů:

- Vše
- Základní kategorie
- Základní adresář
- Samoobslužná správa hesel
- Samoobslužná správa skupin
- Zřizování účtů – automatická změna hesel
- Pozvaní uživatelé
- Služba MIM
- Identity Protection
- B2C

Filtr **Typ prostředku aktivity** umožňuje vybrat jeden z následujících filtrů:

- Vše 
- Skupina
- Adresář
- Uživatel
- Aplikace
- Zásada
- Zařízení
- Ostatní

Když jako **Typ prostředku aktivity** vyberete **Skupina**, zobrazí se další kategorie filtru, která umožňuje zadat také **Zdroj**:

- Azure AD
- O365


Filtr **Aktivita** je založený na vybrané kategorii a typu prostředku aktivity. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditu můžete získat pomocí Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, kde $tenantdomain = název domény. Také se můžete podívat na článek o [událostech sestavy auditování](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Zástupci pro protokoly auditu

Kromě **Azure Active Directory** poskytuje web Azure Portal dva další vstupní body k datům auditu:

- Uživatelé a skupiny
- Podnikové aplikace

### <a name="users-and-groups-audit-logs"></a>Protokoly auditu uživatelů a skupin

S použitím sestav auditu orientovaných na uživatele a skupiny můžete najít odpovědi na otázky tohoto typu:

- Jaké typy aktualizací uživatelé použili?

- Kolik uživatelů bylo změněno?

- Kolik hesel bylo změněno?

- Co provedl správce v adresáři?

- Které skupiny byly přidány?

- Došlo u některých skupin ke změnám členství?

- Došlo ke změnám vlastníků skupiny?

- Jaké licence byly přiřazeny skupině nebo uživateli?

Pokud chcete jenom zkontrolovat data auditování týkající se uživatelů a skupin, najdete filtrované zobrazení v sekci **Protokoly auditu** v oddílu **Aktivity** v části **Uživatelé a skupiny**. Tento vstupní bod má jako **Typ prostředku aktivity** předem vybranou možnost **Uživatelé a skupiny**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/93.png "Protokoly auditu")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Které aplikace byly přidány nebo aktualizovány?
* Které aplikace byly odebrány?
* Změnil se instanční objekt pro aplikaci?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete jenom zkontrolovat data auditování týkající se aplikací, najdete filtrované zobrazení v sekci **Protokoly auditu** v oddílu **Aktivity** v části **Podnikové aplikace**. Tento vstupní bod má jako **Typ prostředku aktivity** předem vybranou možnost **Podniková aplikace**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/134.png "Protokoly auditu")

Toto zobrazení je možné dál filtrovat až na samotné **skupiny** nebo **uživatele**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/25.png "Protokoly auditu")



## <a name="azure-ad-audit-activity-list"></a>Seznam aktivit auditu Azure AD

Tato část obsahuje seznam všech aktivit, které je možné protokolovat. 


|Název služby|Kategorie auditu|Typ prostředku aktivity|Aktivita|
|---|---|---|---|
|Zřizování účtů|Správa aplikací|Aplikace|Správa|
|Zřizování účtů|Správa aplikací|Aplikace|Operace adresáře|
|Zřizování účtů|Správa aplikací|Aplikace|Export|
|Zřizování účtů|Správa aplikací|Aplikace|Import|
|Zřizování účtů|Správa aplikací|Aplikace|Ostatní|
|Zřizování účtů|Správa aplikací|Aplikace|Podmíněné zpracování|
|Zřizování účtů|Správa aplikací|Aplikace|Akce synchronizačního pravidla|
|Proxy aplikací|Správa aplikací|Aplikace|Přidání aplikace|
|Proxy aplikací|Prostředek|Prostředek|Přidání certifikátu SSL aplikace|
|Proxy aplikací|Prostředek|Prostředek|Odstranění vazby SSL|
|Proxy aplikací|Správa aplikací|Aplikace|Odstranění aplikace|
|Proxy aplikací|Správa adresářů|Adresář|Zákaz jednotného přihlašování na počítači|
|Proxy aplikací|Správa adresářů|Adresář|Zákaz jednotného přihlašování na počítači pro konkrétní doménu|
|Proxy aplikací|Správa adresářů|Adresář|Zákaz proxy aplikací|
|Proxy aplikací|Správa adresářů|Adresář|Zákaz předávacího ověřování|
|Proxy aplikací|Správa adresářů|Adresář|Povolení jednotného přihlašování na počítači|
|Proxy aplikací|Správa adresářů|Adresář|Povolení jednotného přihlašování na počítači pro konkrétní doménu|
|Proxy aplikací|Správa adresářů|Adresář|Povolení proxy aplikací|
|Proxy aplikací|Správa adresářů|Adresář|Povolení předávacího ověřování|
|Proxy aplikací|Prostředek|Prostředek|Registrace konektoru|
|Proxy aplikací|Správa aplikací|Aplikace|Aktualizace aplikace|
|Proxy aplikací|Správa aplikací|Aplikace|Aktualizace režimu jednotného přihlašování k aplikacím|
|Automatická změna hesel|Správa aplikací|Aplikace|Automatická změna hesel|
|B2C|Správa aplikací|Aplikace|Přidání oprávnění aplikací V2|
|B2C|Autorizace|Autorizace|Přidání oprávnění aplikací V2|
|B2C|Klíč|Klíč|Přidání klíče založeného na tajném klíči ASCII do kontejneru klíčů CPIM|
|B2C|Autorizace|Autorizace|Přidání klíče založeného na tajném klíči ASCII do kontejneru klíčů CPIM|
|B2C|Klíč|Klíč|Přidání klíče do kontejneru klíčů CPIM|
|B2C|Autorizace|Autorizace|Přidání klíče do kontejneru klíčů CPIM|
|B2C|Prostředek|Prostředek|AdminPolicyDatas-RemoveResources|
|B2C|Autorizace|Autorizace|AdminPolicyDatas-SetResources|
|B2C|Prostředek|Prostředek|AdminPolicyDatas-SetResources|
|B2C|Prostředek|Prostředek|AdminUserJourneys-GetResources|
|B2C|Autorizace|Autorizace|AdminUserJourneys-GetResources|
|B2C|Autorizace|Autorizace|AdminUserJourneys-RemoveResources|
|B2C|Prostředek|Prostředek|AdminUserJourneys-RemoveResources|
|B2C|Prostředek|Prostředek|AdminUserJourneys-SetResources|
|B2C|Autorizace|Autorizace|AdminUserJourneys-SetResources|
|B2C|Autorizace|Autorizace|Create IdentityProvider|
|B2C|Prostředek|Prostředek|Create IdentityProvider|
|B2C|Autorizace|Autorizace|Vytvoření aplikace V1|
|B2C|Správa aplikací|Aplikace|Vytvoření aplikace V1|
|B2C|Správa aplikací|Aplikace|Vytvoření aplikace V2|
|B2C|Autorizace|Autorizace|Vytvoření aplikace V2|
|B2C|Správa adresářů|Adresář|Vytvoření vlastních domén v tenantovi|
|B2C|Autorizace|Autorizace|Vytvoření vlastních domén v tenantovi|
|B2C|Autorizace|Autorizace|Vytvoření nového objektu AdminUserJourney|
|B2C|Prostředek|Prostředek|Vytvoření nového objektu AdminUserJourney|
|B2C|Prostředek|Prostředek|Vytvoření kódu JSON lokalizovaného prostředku|
|B2C|Autorizace|Autorizace|Vytvoření kódu JSON lokalizovaného prostředku|
|B2C|Autorizace|Autorizace|Vytvoření nového vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Vytvoření nového vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Vytvoření nového zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Vytvoření nového zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Vytvoření nebo aktualizace prostředku adresáře B2C|
|B2C|Prostředek|Prostředek|Vytvoření nebo aktualizace prostředku adresáře B2C|
|B2C|Prostředek|Prostředek|Vytvoření zásad|
|B2C|Autorizace|Autorizace|Vytvoření zásad|
|B2C|Autorizace|Autorizace|Vytvoření zásad trustFramework|
|B2C|Prostředek|Prostředek|Vytvoření zásad trustFramework|
|B2C|Autorizace|Autorizace|Vytvoření zásad trustFramework s konfigurovatelnou předponou|
|B2C|Prostředek|Prostředek|Vytvoření zásad trustFramework s konfigurovatelnou předponou|
|B2C|Prostředek|Prostředek|Vytvoření atributu uživatele|
|B2C|Autorizace|Autorizace|Vytvoření atributu uživatele|
|B2C|Autorizace|Autorizace|CreateTrustFrameworkPolicy|
|B2C|Prostředek|Prostředek|CreateTrustFrameworkPolicy|
|B2C|Autorizace|Autorizace|Vytvoří nebo aktualizuje nový objekt AdminUserJourney|
|B2C|Prostředek|Prostředek|Odstranění zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Odstranění zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Vytvoření objektu IdentityProvider|
|B2C|Autorizace|Autorizace|Vytvoření objektu IdentityProvider|
|B2C|Správa aplikací|Aplikace|Odstranění aplikace V1|
|B2C|Autorizace|Autorizace|Odstranění aplikace V1|
|B2C|Správa aplikací|Aplikace|Odstranění aplikace V2|
|B2C|Autorizace|Autorizace|Odstranění aplikace V2|
|B2C|Autorizace|Autorizace|Odstranění udělení oprávnění aplikací V2|
|B2C|Správa aplikací|Aplikace|Odstranění udělení oprávnění aplikací V2|
|B2C|Prostředek|Prostředek|Odstranění prostředku adresáře B2C|
|B2C|Autorizace|Autorizace|Odstranění prostředku adresáře B2C|
|B2C|Klíč|Klíč|Odstranění kontejneru klíčů CPIM|
|B2C|Autorizace|Autorizace|Odstranění kontejneru klíčů CPIM|
|B2C|Klíč|Klíč|Odstranění kontejneru klíčů|
|B2C|Prostředek|Prostředek|Odstranění zásad trustFramework|
|B2C|Autorizace|Autorizace|Odstranění zásad trustFramework|
|B2C|Prostředek|Prostředek|Odstranění atributu uživatele|
|B2C|Autorizace|Autorizace|Odstranění atributu uživatele|
|B2C|Autorizace|Autorizace|Povolení funkce B2C|
|B2C|Správa adresářů|Adresář|Povolení funkce B2C|
|B2C|Prostředek|Prostředek|Získání prostředků adresáře B2C ve skupině prostředků|
|B2C|Prostředek|Prostředek|Získání prostředků adresáře B2C v předplatném|
|B2C|Autorizace|Autorizace|Získání prostředků adresáře B2C v předplatném|
|B2C|Autorizace|Autorizace|Získání vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Získání vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Získání zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Získání zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Získání aplikací V1 a V2|
|B2C|Správa aplikací|Aplikace|Získání aplikací V1 a V2|
|B2C|Autorizace|Autorizace|Získání aplikace V1|
|B2C|Správa aplikací|Aplikace|Získání aplikace V1|
|B2C|Autorizace|Autorizace|Získání aplikací V1|
|B2C|Správa aplikací|Aplikace|Získání aplikací V1|
|B2C|Správa aplikací|Aplikace|Získání aplikace V2|
|B2C|Autorizace|Autorizace|Získání aplikace V2|
|B2C|Správa aplikací|Aplikace|Získání aplikací V2|
|B2C|Autorizace|Autorizace|Získání aplikací V2|
|B2C|Prostředek|Prostředek|Získání prostředku adresáře B2C|
|B2C|Autorizace|Autorizace|Získání prostředku adresáře B2C|
|B2C|Autorizace|Autorizace|Získání seznamu vlastních domén v tenantovi|
|B2C|Správa adresářů|Adresář|Získání seznamu vlastních domén v tenantovi|
|B2C|Autorizace|Autorizace|Získání cesty uživatele|
|B2C|Prostředek|Prostředek|Získání cesty uživatele|
|B2C|Prostředek|Prostředek|Získání povolených deklarací identity aplikace pro cestu uživatele|
|B2C|Autorizace|Autorizace|Získání povolených deklarací identity aplikace pro cestu uživatele|
|B2C|Prostředek|Prostředek|Získání povolených deklarací identity s vlastním potvrzením pro cestu uživatele|
|B2C|Autorizace|Autorizace|Získání povolených deklarací identity s vlastním potvrzením pro cestu uživatele|
|B2C|Prostředek|Prostředek|Získání povolených deklarací identity s vlastním potvrzením pro zásady|
|B2C|Autorizace|Autorizace|Získání povolených deklarací identity s vlastním potvrzením pro zásady|
|B2C|Prostředek|Prostředek|Získání seznamu dostupných výstupních deklarací identity|
|B2C|Autorizace|Autorizace|Získání seznamu dostupných výstupních deklarací identity|
|B2C|Prostředek|Prostředek|Získání definic obsahu pro cestu uživatele|
|B2C|Autorizace|Autorizace|Získání definic obsahu pro cestu uživatele|
|B2C|Autorizace|Autorizace|Získání zprostředkovatelů identity pro konkrétní tok správy|
|B2C|Prostředek|Prostředek|Získání zprostředkovatelů identity pro konkrétní tok správy|
|B2C|Klíč|Klíč|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|B2C|Autorizace|Autorizace|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|B2C|Klíč|Klíč|Získání metadat kontejneru klíčů|
|B2C|Prostředek|Prostředek|Získání seznamu všech toků správy|
|B2C|Autorizace|Autorizace|Získání seznamu všech toků správy|
|B2C|Autorizace|Autorizace|Získání seznamu značek pro všechny toky správy všech uživatelů|
|B2C|Prostředek|Prostředek|Získání seznamu značek pro všechny toky správy všech uživatelů|
|B2C|Prostředek|Prostředek|Získání seznamu tenantů pro uživatele|
|B2C|Autorizace|Autorizace|Získání seznamu tenantů pro uživatele|
|B2C|Prostředek|Prostředek|Získání deklarací identity s vlastním potvrzením pro místní účty|
|B2C|Autorizace|Autorizace|Získání deklarací identity s vlastním potvrzením pro místní účty|
|B2C|Prostředek|Prostředek|Získání kódu JSON lokalizovaného prostředku|
|B2C|Autorizace|Autorizace|Získání kódu JSON lokalizovaného prostředku|
|B2C|Autorizace|Autorizace|Získání operací poskytovatele prostředků Microsoft.AzureActiveDirectory|
|B2C|Prostředek|Prostředek|Získání operací poskytovatele prostředků Microsoft.AzureActiveDirectory|
|B2C|Prostředek|Prostředek|Získání zásad|
|B2C|Autorizace|Autorizace|Získání zásad|
|B2C|Prostředek|Prostředek|Získání zásady|
|B2C|Autorizace|Autorizace|Získání zásady|
|B2C|Správa adresářů|Adresář|Získání vlastností prostředku tenanta|
|B2C|Autorizace|Autorizace|Získání vlastností prostředku tenanta|
|B2C|Prostředek|Prostředek|Získání seznamu podporovaných zprostředkovatelů identity|
|B2C|Autorizace|Autorizace|Získání seznamu podporovaných zprostředkovatelů identity|
|B2C|Prostředek|Prostředek|Získání seznamu podporovaných zprostředkovatelů identity cesty uživatele|
|B2C|Autorizace|Autorizace|Získání seznamu podporovaných zprostředkovatelů identity cesty uživatele|
|B2C|Správa adresářů|Adresář|Získání informací o tenantovi|
|B2C|Autorizace|Autorizace|Získání informací o tenantovi|
|B2C|Správa adresářů|Adresář|Získání povolených funkcí tenanta|
|B2C|Autorizace|Autorizace|Získání povolených funkcí tenanta|
|B2C|Autorizace|Autorizace|Získání seznamu vlastních zprostředkovatelů identity definovaného pro tenanta|
|B2C|Prostředek|Prostředek|Získání seznamu vlastních zprostředkovatelů identity definovaných v tenantovi|
|B2C|Prostředek|Prostředek|Získání seznamu zprostředkovatelů identity definovaných v tenantovi|
|B2C|Autorizace|Autorizace|Získání seznamu zprostředkovatelů identity definovaných v tenantovi|
|B2C|Prostředek|Prostředek|Získání seznamu místních zprostředkovatelů identity definovaných v tenantovi|
|B2C|Autorizace|Autorizace|Získání seznamu místních zprostředkovatelů identity definovaných v tenantovi|
|B2C|Prostředek|Prostředek|Získání podrobností o tenantovi pro uživatele pro vytvoření prostředku|
|B2C|Autorizace|Autorizace|Získání podrobností o tenantovi pro uživatele pro vytvoření prostředku|
|B2C|Autorizace|Autorizace|Získání seznamu tenantů|
|B2C|Autorizace|Autorizace|Získání objektu tenantDomains|
|B2C|Správa adresářů|Adresář|Získání objektu tenantDomains|
|B2C|Prostředek|Prostředek|Získání výchozí podporované kultury pro CPIM|
|B2C|Autorizace|Autorizace|Získání výchozí podporované kultury pro CPIM|
|B2C|Prostředek|Prostředek|Získání podrobností o taku správy|
|B2C|Autorizace|Autorizace|Získání podrobností o toku správy|
|B2C|Autorizace|Autorizace|Získání seznamu objektů UserJourney pro tohoto tenanta|
|B2C|Prostředek|Prostředek|Získání seznamu objektů UserJourney pro tohoto tenanta|
|B2C|Autorizace|Autorizace|Získání sady dostupných podporovaných kultur pro CPIM|
|B2C|Prostředek|Prostředek|Získání sady dostupných podporovaných kultur pro CPIM|
|B2C|Autorizace|Autorizace|Získání zásad trustFramework|
|B2C|Prostředek|Prostředek|Získání zásad trustFramework|
|B2C|Autorizace|Autorizace|Získání zásad trustFramework ve formátu XML|
|B2C|Prostředek|Prostředek|Získání zásad trustFramework ve formátu XML|
|B2C|Prostředek|Prostředek|Získání atributu uživatele|
|B2C|Autorizace|Autorizace|Získání atributu uživatele|
|B2C|Autorizace|Autorizace|Získání atributů uživatele|
|B2C|Prostředek|Prostředek|Získání atributů uživatele|
|B2C|Autorizace|Autorizace|Získání seznamu cest uživatele|
|B2C|Prostředek|Prostředek|Získání seznamu cest uživatele|
|B2C|Autorizace|Autorizace|GetIEFPolicies|
|B2C|Prostředek|Prostředek|GetIEFPolicies|
|B2C|Autorizace|Autorizace|GetIdentityProviders|
|B2C|Prostředek|Prostředek|GetIdentityProviders|
|B2C|Prostředek|Prostředek|GetTrustFrameworkPolicy|
|B2C|Autorizace|Autorizace|GetTrustFrameworkPolicy|
|B2C|Klíč|Klíč|Získá kontejner klíčů CPIM ve formátu JWK|
|B2C|Autorizace|Autorizace|Získá kontejner klíčů CPIM ve formátu JWK|
|B2C|Klíč|Klíč|Získá seznam kontejnerů klíčů v tenantovi|
|B2C|Autorizace|Autorizace|Získá seznam kontejnerů klíčů v tenantovi|
|B2C|Autorizace|Autorizace|Získá typ tenanta|
|B2C|Správa adresářů|Adresář|Získá typ tenanta|
|B2C|Authentication|Authentication|Vydání přístupového tokenu pro aplikaci|
|B2C|Authentication|Authentication|Vydání ověřovacího kódu pro aplikaci|
|B2C|Ostatní|Ostatní|Vydání ověřovacího kódu pro aplikaci|
|B2C|Authentication|Authentication|Vydání tokentu id_token pro aplikaci|
|B2C|Ostatní|Ostatní|Vydání tokentu id_token pro aplikaci|
|B2C|Autorizace|Autorizace|MigrateTenantMetadata|
|B2C|Prostředek|Prostředek|MigrateTenantMetadata|
|B2C|Prostředek|Prostředek|Přesunutí prostředků|
|B2C|Autorizace|Autorizace|Oprava objektu IdentityProvider|
|B2C|Prostředek|Prostředek|Oprava objektu IdentityProvider|
|B2C|Prostředek|Prostředek|PutTrustFrameworkPolicy|
|B2C|Autorizace|Autorizace|PutTrustFrameworkPolicy|
|B2C|Autorizace|Autorizace|PutTrustFrameworkpolicy|
|B2C|Prostředek|Prostředek|PutTrustFrameworkpolicy|
|B2C|Prostředek|Prostředek|Odebrání cesty uživatele|
|B2C|Autorizace|Autorizace|Odebrání cesty uživatele|
|B2C|Autorizace|Autorizace|Obnovení zálohy kontejneru klíčů CPIM|
|B2C|Klíč|Klíč|Obnovení zálohy kontejneru klíčů CPIM|
|B2C|Správa aplikací|Aplikace|Načtení udělení oprávnění aplikace V2|
|B2C|Autorizace|Autorizace|Načtení udělení oprávnění aplikace V2|
|B2C|Správa aplikací|Aplikace|Načtení instančních objektů aplikace V2 v aktuálním tenantovi|
|B2C|Autorizace|Autorizace|Načtení instančních objektů aplikace V2 v aktuálním tenantovi|
|B2C|Klíč|Klíč|Uložení kontejneru klíčů|
|B2C|Autorizace|Autorizace|Aktualizace vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Aktualizace vlastního zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Aktualizace zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Aktualizace zprostředkovatele identity|
|B2C|Prostředek|Prostředek|Aktualizace místního zprostředkovatele identity|
|B2C|Autorizace|Autorizace|Aktualizace místního zprostředkovatele identity|
|B2C|Správa aplikací|Aplikace|Aktualizace aplikace V1|
|B2C|Autorizace|Autorizace|Aktualizace aplikace V1|
|B2C|Správa aplikací|Aplikace|Aktualizace aplikace V2|
|B2C|Autorizace|Autorizace|Aktualizace aplikace V2|
|B2C|Správa aplikací|Aplikace|Aktualizace udělení oprávnění aplikace V2|
|B2C|Autorizace|Autorizace|Aktualizace udělení oprávnění aplikace V2|
|B2C|Prostředek|Prostředek|Aktualizace prostředku adresáře B2C|
|B2C|Prostředek|Prostředek|Aktualizace zásad|
|B2C|Autorizace|Autorizace|Aktualizace zásad|
|B2C|Prostředek|Prostředek|Aktualizace stavu předplatného|
|B2C|Prostředek|Prostředek|Aktualizace atributu uživatele|
|B2C|Autorizace|Autorizace|Aktualizace atributu uživatele|
|B2C|Klíč|Klíč|Nahrání šifrovaného klíče CPIM|
|B2C|Autorizace|Autorizace|Nahrání šifrovaného klíče CPIM|
|B2C|Autorizace|Autorizace|Autorizace uživatelů: Rozhraní API je zakázané pro sadu funkcí tenanta|
|B2C|Autorizace|Autorizace|Autorizace uživatelů: Uživatel má udělený přístup jako správce tenanta|
|B2C|Autorizace|Autorizace|Autorizace uživatelů: Uživatel má udělená přístupová práva ověřeného uživatele|
|B2C|Authentication|Authentication|Ověření přihlašovacích údajů místního účtu|
|B2C|Prostředek|Prostředek|Ověření přesunu prostředků|
|B2C|Authentication|Authentication|Kontrola ověření uživatele|
|B2C|Správa adresářů|Adresář|Ověření povolení funkce B2C|
|B2C|Autorizace|Autorizace|Ověření povolení funkce B2C|
|B2C|Autorizace|Autorizace|Ověření povolení funkce|
|B2C|Správa adresářů|Adresář|Ověření povolení funkce|
|Základní adresář|Správa aplikací|Aplikace|Přidání OAuth2PermissionGrant|
|Základní adresář|Správa jednotek pro správu|AdministrativeUnit|Přidání jednotky pro správu|
|Základní adresář|Správa uživatelů|Uživatel|Přidání přiřazení role aplikace k uživateli|
|Základní adresář|Správa skupin|Skupina|Přidání přiřazení role aplikace ke skupině|
|Základní adresář|Správa aplikací|Aplikace|Přidání přiřazení role aplikace k instančnímu objektu|
|Základní adresář|Správa aplikací|Aplikace|Přidání aplikace|
|Základní adresář|Prostředek|Prostředek|Přidání zařízení|
|Základní adresář|Prostředek|Prostředek|Přidání konfigurace zařízení|
|Základní adresář|Správa rolí|Role|Přidání oprávněného člena do role|
|Základní adresář|Správa skupin|Skupina|Přidání skupiny|
|Základní adresář|Správa jednotek pro správu|AdministrativeUnit|Přidání člena do jednotky pro správu|
|Základní adresář|Správa skupin|Skupina|Přidání člena do skupiny|
|Základní adresář|Správa rolí|Role|Přidání člena do role|
|Základní adresář|Správa aplikací|Aplikace|Přidání vlastníka do aplikace|
|Základní adresář|Správa skupin|Skupina|Přidání vlastníka do skupiny|
|Základní adresář|Správa zásad|Zásada|Přidání vlastníka k zásadám|
|Základní adresář|Správa aplikací|Aplikace|Přidání vlastníka k instančnímu objektu|
|Základní adresář|Správa adresářů|Adresář|Přidání partnera ke společnosti|
|Základní adresář|Správa zásad|Zásada|Přidání zásad|
|Základní adresář|Správa aplikací|Aplikace|Přidání zásad k instančnímu objektu|
|Základní adresář|Prostředek|Prostředek|Přidání registrovaného vlastníka zařízení|
|Základní adresář|Prostředek|Prostředek|Přidání registrovaných uživatelů zařízení|
|Základní adresář|Správa rolí|Role|Přidání přiřazení role do definice role|
|Základní adresář|Správa rolí|Role|Přidání role ze šablony|
|Základní adresář|Správa rolí|Role|Přidání vymezeného člena do role|
|Základní adresář|Správa aplikací|Aplikace|Přidání instančního objektu|
|Základní adresář|Správa aplikací|Aplikace|Přidání přihlašovacích údajů instančního objektu|
|Základní adresář|Správa adresářů|Adresář|Přidání neověřené domény|
|Základní adresář|Správa uživatelů|Uživatel|Přidání uživatele|
|Základní adresář|Správa uživatelů|Uživatel|Přidání podrobností o mobilní aplikaci silného ověřování uživatele|
|Základní adresář|Správa adresářů|Adresář|Přidání ověřené domény|
|Základní adresář|Správa uživatelů|Uživatel|Změna uživatelské licence|
|Základní adresář|Správa uživatelů|Uživatel|Změna hesla uživatele|
|Základní adresář|Správa aplikací|Aplikace|Souhlas s aplikací|
|Základní adresář|Správa uživatelů|Uživatel|Převod federovaného uživatele na spravovaného|
|Základní adresář|Správa uživatelů|Uživatel|Vytvoření hesla aplikace pro uživatele|
|Základní adresář|Správa adresářů|Adresář|Vytvoření společnosti|
|Základní adresář|Správa adresářů|Adresář|Vytvoření nastavení společnosti|
|Základní adresář|Správa skupin|Skupina|Vytvoření nastavení skupin|
|Základní adresář|Správa jednotek pro správu|AdministrativeUnit|Odstranění jednotky pro správu|
|Základní adresář|Správa aplikací|Aplikace|Odstranění aplikace|
|Základní adresář|Správa uživatelů|Uživatel|Odstranění hesla aplikace pro uživatele|
|Základní adresář|Správa adresářů|Adresář|Odstranění nastavení společnosti|
|Základní adresář|Prostředek|Prostředek|Odstranění zařízení|
|Základní adresář|Prostředek|Prostředek|Odstranění konfigurace zařízení|
|Základní adresář|Správa skupin|Skupina|Odstranění skupiny|
|Základní adresář|Správa skupin|Skupina|Odstranění nastavení skupin|
|Základní adresář|Správa zásad|Zásada|Odstranění zásad|
|Základní adresář|Správa uživatelů|Uživatel|Odstranění uživatele|
|Základní adresář|Správa adresářů|Adresář|Snížení úrovně partnera|
|Základní adresář|Prostředek|Prostředek|Zařízení už nevyhovuje předpisům|
|Základní adresář|Prostředek|Prostředek|Zařízení už není spravované|
|Základní adresář|Správa adresářů|Adresář|Adresář je odstraněný|
|Základní adresář|Správa adresářů|Adresář|Adresář je trvale odstraněný|
|Základní adresář|Správa adresářů|Adresář|Adresář s naplánovaným odstraněním|
|Základní adresář|Správa uživatelů|Uživatel|Zákaz účtu|
|Základní adresář|Správa uživatelů|Uživatel|Povolení silného ověřování|
|Základní adresář|Správa skupin|Skupina|Ukončení použití skupinové licence pro uživatele|
|Základní adresář|Správa aplikací|Aplikace|Trvalé odstranění aplikace|
|Základní adresář|Správa skupin|Skupina|Trvalé odstranění skupiny|
|Základní adresář|Správa uživatelů|Uživatel|Trvalé odstranění uživatele|
|Základní adresář|Správa adresářů|Adresář|Propagace společnosti u partnera|
|Základní adresář|Správa adresářů|Adresář|Vyprázdnění vlastností Rights Management|
|Základní adresář|Správa aplikací|Aplikace|Odebrání OAuth2PermissionGrant|
|Základní adresář|Správa skupin|Skupina|Odebrání přiřazení role aplikace ze skupiny|
|Základní adresář|Správa aplikací|Aplikace|Odebrání přiřazení role aplikace z instančního objektu|
|Základní adresář|Správa uživatelů|Uživatel|Odebrání uživateli přiřazení role aplikace|
|Základní adresář|Správa rolí|Role|Odebrání oprávněného člena z role|
|Základní adresář|Správa jednotek pro správu|AdministrativeUnit|Odebrání člena z jednotky pro správu|
|Základní adresář|Správa skupin|Skupina|Odebrání člena ze skupiny|
|Základní adresář|Správa rolí|Role|Odebrání člena z role|
|Základní adresář|Správa aplikací|Aplikace|Odebrání vlastníka z aplikace|
|Základní adresář|Správa skupin|Skupina|Odebrání vlastníka ze skupiny|
|Základní adresář|Správa aplikací|Aplikace|Odebrání vlastníka z instančního objektu|
|Základní adresář|Správa adresářů|Adresář|Odebrání partnera ze společnosti|
|Základní adresář|Správa zásad|Zásada|Odebrání přihlašovacích údajů zásad|
|Základní adresář|Správa aplikací|Aplikace|Odebrání zásad z instančního objektu|
|Základní adresář|Prostředek|Prostředek|Odebrání registrovaného vlastníka ze zařízení|
|Základní adresář|Prostředek|Prostředek|Odebrání registrovaných uživatelů ze zařízení|
|Základní adresář|Správa rolí|Role|Odebrání přiřazení role z definice role|
|Základní adresář|Správa rolí|Role|Odebrání vymezeného člena z role|
|Základní adresář|Správa aplikací|Aplikace|Odebrání instančního objektu|
|Základní adresář|Správa aplikací|Aplikace|Odebrání přihlašovacích údajů instančního objektu|
|Základní adresář|Správa adresářů|Adresář|Odebrání neověřené domény|
|Základní adresář|Správa uživatelů|Uživatel|Odebrání podrobností o mobilní aplikaci silného ověřování uživatele|
|Základní adresář|Správa adresářů|Adresář|Odebrání ověřené domény|
|Základní adresář|Správa uživatelů|Uživatel|Resetování hesla uživatele|
|Základní adresář|Správa skupin|Skupina|Obnovení skupiny|
|Základní adresář|Správa aplikací|Aplikace|Obnovení aplikace|
|Základní adresář|Správa uživatelů|Uživatel|Obnovení uživatele|
|Základní adresář|Správa aplikací|Aplikace|Odvolání souhlasu|
|Základní adresář|Správa adresářů|Adresář|Nastavení informací o společnosti|
|Základní adresář|Správa adresářů|Adresář|Nastavení funkce DirSync|
|Základní adresář|Správa adresářů|Adresář|Nastavení příznaku DirSyncEnabled|
|Základní adresář|Správa adresářů|Adresář|Nastavení partnerství|
|Základní adresář|Správa adresářů|Adresář|Nastavení prahové hodnoty náhodného odstranění|
|Základní adresář|Správa adresářů|Adresář|Nastavení povoleného umístění dat společnosti|
|Základní adresář|Správa adresářů|Adresář|Povolení vícejazykové funkce společnosti|
|Základní adresář|Správa adresářů|Adresář|Nastavení funkce adresáře v tenantovi|
|Základní adresář|Správa adresářů|Adresář|Nastavení doménového ověřování|
|Základní adresář|Správa adresářů|Adresář|Nastavení federování v doméně|
|Základní adresář|Správa uživatelů|Uživatel|Nastavení vynucené změny hesla uživatele|
|Základní adresář|Správa skupin|Skupina|Nastavení skupinové licence|
|Základní adresář|Správa skupin|Skupina|Nastavení správy skupiny uživatelem|
|Základní adresář|Správa adresářů|Adresář|Nastavení zásad hesel|
|Základní adresář|Správa adresářů|Adresář|Nastavení vlastností Rights Management|
|Základní adresář|Správa uživatelů|Uživatel|Nastavení správce uživatelů|
|Základní adresář|Správa uživatelů|Uživatel|Nastavení povolení metadat tokenu OAuth uživatele|
|Základní adresář|Správa skupin|Skupina|Zahájení použití skupinové licence pro uživatele|
|Základní adresář|Správa skupin|Skupina|Aktivace přepočtu skupinové licence|
|Základní adresář|Správa uživatelů|Uživatel|Aktualizace časového razítka StsRefreshTokenValidFrom|
|Základní adresář|Správa jednotek pro správu|AdministrativeUnit|Aktualizace jednotky pro správu|
|Základní adresář|Správa aplikací|Aplikace|Aktualizace aplikace|
|Základní adresář|Správa adresářů|Adresář|Aktualizace společnosti|
|Základní adresář|Správa adresářů|Adresář|Aktualizace nastavení společnosti|
|Základní adresář|Prostředek|Prostředek|Aktualizace zařízení|
|Základní adresář|Prostředek|Prostředek|Aktualizace konfigurace zařízení|
|Základní adresář|Správa adresářů|Adresář|Aktualizace domény|
|Základní adresář|Správa uživatelů|Uživatel|Aktualizace externích tajných klíčů|
|Základní adresář|Správa aplikací|Aplikace|Aktualizace externích tajných klíčů|
|Základní adresář|Správa skupin|Skupina|Aktualizace skupiny|
|Základní adresář|Správa skupin|Skupina|Aktualizace nastavení skupin|
|Základní adresář|Správa zásad|Zásada|Aktualizace zásad|
|Základní adresář|Správa rolí|Role|Aktualizace role|
|Základní adresář|Správa aplikací|Aplikace|Aktualizace instančního objektu|
|Základní adresář|Správa uživatelů|Uživatel|Aktualizace uživatele|
|Základní adresář|Správa adresářů|Adresář|Ověření domény|
|Základní adresář|Správa adresářů|Adresář|Ověření domény s e-mailovým ověřením|
|Identity Protection|Správa uživatelů|Uživatel|Správce generuje dočasné heslo|
|Identity Protection|Správa uživatelů|Uživatel|Správce vyžaduje resetování hesla uživatelem|
|Identity Protection|Ostatní|Ostatní|Stažení rizikových událostí jednoho typu|
|Identity Protection|Ostatní|Ostatní|Stažení správců a stavu týdenního přehledu vyjádření souhlasu|
|Identity Protection|Ostatní|Ostatní|Stažení rizikových událostí všech typů|
|Identity Protection|Ostatní|Ostatní|Stažení rizikových událostí bezplatného uživatele|
|Identity Protection|Ostatní|Ostatní|Stažení uživatelů označených příznakem rizika|
|Identity Protection|Správa adresářů|Adresář|Onboarding|
|Identity Protection|Správa zásad|Zásada|Nastavení zásad registrace MFA|
|Identity Protection|Správa zásad|Zásada|Nastavení zásad rizika přihlašování|
|Identity Protection|Správa zásad|Zásada|Nastavení zásad rizika uživatele|
|Identity Protection|Správa adresářů|Adresář|Aktualizace nastavení výstrah|
|Identity Protection|Správa adresářů|Adresář|Aktualizace nastavení týdenního přehledu|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|Přiřazení externího uživatele k aplikaci|
|Pozvaní uživatelé|Ostatní|Ostatní|Zpracované hromadné pozvánky|
|Pozvaní uživatelé|Ostatní|Ostatní|Odeslané hromadné pozvánky|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|E-mail se neodeslal, uživatel zrušil odběr|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|Pozvání externího uživatele|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|Uplatnění pozvání externího uživatele|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|Vytvoření virálního tenanta|
|Pozvaní uživatelé|Správa uživatelů|Uživatel|Vytvoření virálního uživatele|
|Microsoft Identity Manager (MIM)|Správa skupin|Skupina|Přidání člena|
|Microsoft Identity Manager (MIM)|Správa skupin|Skupina|Vytvoření skupiny|
|Microsoft Identity Manager (MIM)|Správa skupin|Skupina|Odstranění skupiny|
|Microsoft Identity Manager (MIM)|Správa skupin|Skupina|Odebrání člena|
|Microsoft Identity Manager (MIM)|Správa skupin|Skupina|Aktualizace skupiny|
|Microsoft Identity Manager (MIM)|Správa uživatelů|Uživatel|Registrace hesla uživatele|
|Microsoft Identity Manager (MIM)|Správa uživatelů|Uživatel|Resetování hesla uživatele|
|Privileged Identity Management|Správa rolí|Role|AccessReview_Review|
|Privileged Identity Management|Správa rolí|Role|AccessReview_Update|
|Privileged Identity Management|Správa rolí|Role|ActivationAborted|
|Privileged Identity Management|Správa rolí|Role|ActivationApproved|
|Privileged Identity Management|Správa rolí|Role|ActivationCanceled|
|Privileged Identity Management|Správa rolí|Role|ActivationRequested|
|Privileged Identity Management|Správa rolí|Role|Přidáno|
|Privileged Identity Management|Správa rolí|Role|Přiřazení|
|Privileged Identity Management|Správa rolí|Role|Zvýšení oprávnění|
|Privileged Identity Management|Správa rolí|Role|Odebráno|
|Privileged Identity Management|Správa rolí|Role|Změny nastavení role|
|Privileged Identity Management|Správa rolí|Role|ScanAlertsNow|
|Privileged Identity Management|Správa rolí|Role|Registrace|
|Privileged Identity Management|Správa rolí|Role|Pozastavení|
|Privileged Identity Management|Správa rolí|Role|UpdateAlertSettings|
|Privileged Identity Management|Správa rolí|Role|UpdateCurrentState|
|Samoobslužná správa skupin|Správa skupin|Skupina|Schválení čekající žádosti o připojení ke skupině|
|Samoobslužná správa skupin|Správa skupin|Skupina|Zrušení čekající žádosti o připojení ke skupině|
|Samoobslužná správa skupin|Správa skupin|Skupina|Vytvoření zásad správy životního cyklu|
|Samoobslužná správa skupin|Správa skupin|Skupina|Odstranění čekající žádosti o připojení ke skupině|
|Samoobslužná správa skupin|Správa skupin|Skupina|Odmítnutí čekající žádosti o připojení ke skupině|
|Samoobslužná správa skupin|Správa skupin|Skupina|Obnovení skupiny|
|Samoobslužná správa skupin|Správa skupin|Skupina|Žádost o připojení ke skupině|
|Samoobslužná správa skupin|Správa skupin|Skupina|Nastavení vlastností dynamických skupin|
|Samoobslužná správa skupin|Správa skupin|Skupina|Aktualizace zásad správy životního cyklu|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Blokování samoobslužného resetování hesla|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Změna hesla (samoobslužná)|
|Samoobslužná správa hesel|Správa adresářů|Adresář|Zákaz zpětného zápisu hesel pro adresář|
|Samoobslužná správa hesel|Správa adresářů|Adresář|Povolení zpětného zápisu hesel pro adresář|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Resetování hesla (správcem)|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Resetování hesla (samoobslužné)|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Průběh aktivity toku samoobslužného resetování hesla|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Průběh aktivity toku samoobslužného resetování hesla|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Odemknutí uživatelského účtu (samoobslužné)|
|Samoobslužná správa hesel|Správa uživatelů|Uživatel|Registrace uživatele pro samoobslužné resetování hesla|
|Podmínky použití|Správa zásad|Zásada|Přijetí podmínek použití|
|Podmínky použití|Správa zásad|Zásada|Vytvoření podmínek použití|
|Podmínky použití|Správa zásad|Zásada|Odmítnutí podmínek použití|
|Podmínky použití|Správa zásad|Zásada|Odstranění podmínek použití|
|Podmínky použití|Správa zásad|Zásada|Úprava podmínky použití|
|Podmínky použití|Správa zásad|Zásada|Publikování podmínek použití|
|Podmínky použití|Správa zásad|Zásada|Zrušení publikování podmínek použití|




## <a name="next-steps"></a>Další kroky

Přehled generování sestav najdete v tématu [Generování sestav v Azure Active Directory](active-directory-reporting-azure-portal.md).

