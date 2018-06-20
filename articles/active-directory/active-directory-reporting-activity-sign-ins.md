---
title: Sestavy aktivit přihlašování na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit přihlašování na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/17/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d9c2f210204122947c24eb42f643450537f3b9a8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232294"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit přihlašování na portálu Azure Active Directory

Generování sestav Azure Active Directory (Azure AD) na webu [Azure Portal](https://portal.azure.com) umožňuje získat všechny informace potřebné ke zjištění stavu vašeho prostředí.

Architektuře generování sestav v Azure Active Directory se skládá z těchto součástí:

- **Aktivita** 
    - **Aktivity přihlašování** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů
    - **Protokoly auditu** – informace aktivit systému o správě uživatelů a skupin, spravovaných aplikacích a aktivitách adresářů
- **Zabezpečení** 
    - **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další podrobnosti najdete v tématu Riziková přihlášení.
    - **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další podrobnosti najdete v tématu Uživatelé označení příznakem rizika.

V toto tématu najdete přehled aktivit přihlašování.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v roli Správce zabezpečení, Čtenář zabezpečení nebo Čtenář sestav
* Globální správci
* Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?
* Klient musí mít přiřazenou licenci Azure AD Premium, aby mohl zobrazit sestavu veškerých aktivit přihlašování.


## <a name="sign-in-activities"></a>Aktivity přihlašování

Na základě informací poskytnutých sestavou přihlašování uživatelů najdete odpovědi na otázky tohoto typu:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

Váš první vstupní bod pro všechna data aktivit přihlašování je **Přihlášení** v části Aktivity služby **Azure Active**


![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/61.png "Aktivita přihlašování")


Protokol přihlášení má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum přihlášení
- Související uživatel
- Aplikace, ke které se uživatel přihlásil
- Stav přihlášení
- Stav detekce rizik
- Stav požadavku na vícefaktorové ověřování (MFA) 

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/01.png "Aktivita přihlašování")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/19.png "Aktivita přihlašování")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/02.png "Aktivita přihlašování")

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/03.png "Aktivita přihlašování")


## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících výchozích polí:

- Uživatel
- Aplikace
- Stav přihlášení
- Stav detekce rizik
- Datum


![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/04.png "Aktivita přihlašování")

Filtr **Uživatel** umožňuje určit jméno nebo hlavní název uživatele (UPN) pro uživatele, o kterého vám jde. 

Filtr **Aplikace** umožňuje určit název aplikace, o kterou vám jde.  

Filtr **Stav přihlášení** umožňuje vybrat jednu z následujících možností:

- Vše 
- Úspěch
- Selhání

Filtr **Detekované riziko** umožňuje vybrat jednu z následujících možností:

- Vše
- Ano
- Ne 


Filtr **Datum** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Pokud do svého zobrazení přihlášení přidáte další pole, tato pole se automaticky přidají do seznamu filtrů. Například přidáním pole **Klientská aplikace** do vašeho seznamu získáte také další možnost filtru, která vám umožní nastavit následující filtry:

- Prohlížeč      
- Exchange ActiveSync (podporované)               
- Exchange ActiveSync (nepodporované)
- Ostatní klienti               
    - IMAP
    - MAPI
    - Starší klienti Office
    - POP
    - SMTP


![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/12.png "Aktivita přihlašování")


> [!TIP] 
> Kromě výchozích filtrů se stanou pole filtru ze všech dalších polí, která přidáte do zobrazení přihlášení.


## <a name="download-sign-in-activities"></a>Stažení aktivit přihlašování

Pokud chcete pracovat s daty aktivit přihlašování mimo web Azure Portal, můžete si je stáhnout. Kromě tlačítka pro stahování máte také na webu Azure Portal možnost vygenerovat skript, který data stáhne.  

![Stáhnout](./media/active-directory-reporting-activity-sign-ins/71.png "Stáhnout")

Kliknutím na tlačítko **Stáhnout** vytvoříte soubor CSV s 5000 nejnovějších záznamů. Pokud potřebujete větší flexibilitu, můžete použít řešení se skriptem. Kliknutím na tlačítko **skriptu** vytvoří skript prostředí PowerShell, který obsahuje všechny filtry, které jste nastavili. Stáhněte a spusťte tento skript v **režimu správce** ke generování souboru CSV. Kromě technické implementace je počet záznamů, které si můžete stáhnout, také omezený [zásadami uchování sestav Azure Active Directory](active-directory-reporting-retention.md).  



## <a name="sign-in-activities-shortcuts"></a>Zkratky pro aktivity přihlašování

Kromě Azure Active Directory poskytuje web Azure Portal dva další vstupní body k datům aktivit přihlašování:

- Přehled ochrany zabezpečení identit
- Uživatelé
- Skupiny
- Podnikové aplikace


### <a name="users-sign-ins-activities"></a>Aktivity přihlašování uživatelů

Na základě informací poskytnutých sestavou přihlašování uživatelů najdete odpovědi na otázky tohoto typu:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?



Vaším vstupním bodem k těmto datům je graf přihlašování uživatelů na stránce přehledu **ochrany zabezpečení identit**. V grafu přihlašování uživatelů jsou znázorněny týdenní agregace přihlášení všech uživatelů za dané časové období. Výchozí časové období je 30 dnů.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/06.png "Aktivita přihlašování")

Když v grafu přihlašování kliknete na konkrétní den, zobrazí se přehled aktivit přihlašování pro tento den.


Na každém řádku v seznamu aktivit přihlašování se zobrazí:

* Kdo se přihlásil?
* Která aplikace byla cílem přihlášení?
* Jaký je stav přihlášení?
* Jaký je stav MFA přihlášení?

Kliknutím na položku zobrazíte další podrobnosti o přihlašovací operaci:

- ID uživatele
- Uživatel
- Uživatelské jméno
- ID aplikace
- Aplikace
- Klient
- Umístění
- IP adresa
- Datum
- Vyžaduje se MFA
- Stav přihlášení

 
Na stránce **Uživatelé** zobrazíte úplný přehled všech přihlášení uživatelů kliknutím na **Přihlášení** v části **Aktivita**.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/08.png "Aktivita přihlašování")




## <a name="usage-of-managed-applications"></a>Použití spravovaných aplikací

S použitím zobrazení dat přihlašování zaměřeného na aplikace můžete odpovídat na otázky tohoto typu:

* Kdo používá mé aplikace?
* Které jsou 3 nejpoužívanější aplikace v organizaci?
* Nedávno jsem zpřístupnil aplikaci. Jak to s ní vypadá?

Vaším vstupním bodem k těmto datům je sestava *3 nejpoužívanějších aplikací ve vaší organizaci za posledních 30 dnů* v části **Přehled** na stránce **Podnikové aplikace**.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/10.png "Aktivita přihlašování")

Graf využívání aplikací s týdenními agregacemi přihlašování pro 3 nejpoužívanější aplikace v daném časovém období. Výchozí časové období je 30 dnů.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/47.png "Aktivita přihlašování")

Pokud chcete, můžete se zaměřit na konkrétní aplikaci.


![Vytváření sestav](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Vytváření sestav")

Když v grafu využívání aplikací kliknete na konkrétní den, zobrazí se podrobný seznam aktivit přihlašování.




Možnost **Přihlášení** poskytuje úplný přehled o všech událostech přihlašování pro vaše aplikace.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins/11.png "Aktivita přihlašování")



## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět více o kódech chyb aktivit přihlašování, přečtěte si téma [Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

