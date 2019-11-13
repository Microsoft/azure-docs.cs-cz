---
title: Sestavy aktivit přihlašování na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit přihlašování na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 778353621491f912d3237900785e6dee17bf975e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014491"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit přihlašování na portálu Azure Active Directory

Architektura vytváření sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu** - [protokoly auditu](concept-audit-logs.md) poskytují informace o činnosti systému týkající se správy uživatelů a skupin, spravovaných aplikací a aktivit adresáře.
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](concept-risky-sign-ins.md) je indikátorem pokusu o přihlášení někoho, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek obsahuje přehled sestavy přihlášení.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v rolích správce zabezpečení, čtenář zabezpečení a čtenář sestav
* Globální správci
* Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?

* K vaší straně klienta musí být přidružená licence Azure AD Premium, aby bylo možné zobrazit sestavu všech aktivit přihlašování. Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) . Bude trvat několik dní, než se data zobrazí v sestavách po upgradu na licenci Premium bez jakýchkoli aktivit dat před upgradem.

## <a name="sign-ins-report"></a>Sestava přihlášení

Sestava přihlášení uživatelů poskytuje odpovědi na následující otázky:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

Začněte s [Azure Portal](https://portal.azure.com). Pokud chcete získat přístup k sestavě přihlášení, vyberte **přihlášení**a pokračujte **monitorováním.** Může trvat až dvě hodiny, než se některé záznamy přihlášení zobrazí na portálu.

![Přihlašovací aktivita](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Aktivita přihlášení")

> [!IMPORTANT]
> Sestava přihlášení zobrazí pouze **interaktivní** přihlášení, tedy přihlášení, kde se uživatel ručně přihlásí pomocí uživatelského jména a hesla. Neinteraktivní přihlášení, jako je ověřování služby-služba, se v sestavě přihlášení nezobrazí. 

Protokol přihlášení má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum přihlášení
- Související uživatel
- Aplikace, ke které se uživatel přihlásil
- Stav přihlášení
- Stav detekce rizik
- Stav požadavku na vícefaktorové ověřování (MFA)

![Přihlašovací aktivita](./media/concept-sign-ins/sign-in-activity.png "Aktivita přihlášení")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Přihlašovací aktivita](./media/concept-sign-ins/19.png "Aktivita přihlášení")

Zobrazí další pole nebo odebrat již zobrazená pole.

![Přihlašovací aktivita](./media/concept-sign-ins/02.png "Aktivita přihlášení")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![Přihlašovací aktivita](./media/concept-sign-ins/basic-sign-in.png "Aktivita přihlášení")

> [!NOTE]
> Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím všech sestav přihlášení. Kliknutím na kartu **podmíněný přístup** pro záznam pro přihlášení mohou zákazníci zkontrolovat stav podmíněného přístupu a podrobně do podrobností o zásadách, které se vztahují k přihlašování a výsledku pro jednotlivé zásady.
> Další informace najdete v [nejčastějších dotazech k informacím o certifikační autoritě ve všech přihlášeních](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Nejprve zajistěte zúžení hlášených dat na úroveň, která vám bude vyhovovat. Za druhé filtrujte data přihlášení pomocí pole data jako výchozí filtr. Azure AD poskytuje širokou škálu dalších filtrů, které můžete nastavit.

![Přihlašovací aktivita](./media/concept-sign-ins/04.png "Aktivita přihlášení")

Filtr **Uživatel** umožňuje určit jméno nebo hlavní název uživatele (UPN) pro uživatele, o kterého vám jde.

Filtr **Aplikace** umožňuje určit název aplikace, o kterou vám jde.

Filtr **Stav přihlášení** umožňuje vybrat jednu z následujících možností:

- Vše
- Úspěch
- Selhání

Filtr **podmíněného přístupu** umožňuje vybrat stav zásad certifikační autority pro přihlášení:

- Vše
- Nepoužito
- Úspěch
- Selhání

Filtr **Datum** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- Jeden měsíc
- 7 dní
- 24 hodin
- Vlastní časový interval

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Pokud do svého zobrazení přihlášení přidáte další pole, tato pole se automaticky přidají do seznamu filtrů. Například přidáním pole **Klientská aplikace** do vašeho seznamu získáte také další možnost filtru, která vám umožní nastavit následující filtry:  
![Přihlašovací aktivita](./media/concept-sign-ins/12.png "Aktivita přihlášení")

- **Prohlížeč**  
    Tento filtr zobrazuje všechny události, u kterých došlo k pokusu o přihlášení pomocí toků prohlížeče.
- **Exchange ActiveSync (podporováno)**  
    Tento filtr zobrazuje všechny pokusy o přihlášení, u kterých došlo k pokusu o spuštění protokolu Exchange ActiveSync (EAS) z podporovaných platforem, jako je iOS, Android a Windows Phone.
- **Exchange ActiveSync (nepodporované)**  
    Tento filtr zobrazuje všechny pokusy o přihlášení, u kterých došlo k pokusu o spuštění protokolu EAS z nepodporovaných platforem, jako je Linux distribuce.
- **Klienti Mobile Apps a desktopových klientů** Filtr zobrazuje všechny pokusy o přihlášení, které nepoužívaly toky prohlížeče. Například mobilní aplikace z jakékoli platformy pomocí libovolného protokolu nebo klientských aplikací klasické pracovní plochy, jako je Office ve Windows nebo MacOS.
  
- **Ostatní klienti**
    - **IMAP**  
        Starší verze poštovního klienta, který používá IMAP k načtení e-mailu.
    - **MAPI**  
        Office 2013, kde je povolena ADAL a používá rozhraní MAPI.
    - **Stará klienti Office**  
        Sada Office 2013 v její výchozí konfiguraci, kde není povolená knihovna ADAL a kterou používá rozhraní MAPI, nebo Office 2016, kde je ADAL zakázaná
    - **POP**  
        Starší verze poštovního klienta pomocí protokolu POP3 k načtení e-mailu.
    - **SMTP**  
        Starší verze poštovního klienta pomocí protokolu SMTP k odeslání e-mailu.

## <a name="download-sign-in-activities"></a>Stažení aktivit přihlašování

Klikněte na možnost **Stáhnout** a vytvořte soubor CSV nebo soubor JSON s nejnovějšími záznamy 250 000. Začněte [stažením přihlašovacích údajů](quickstart-download-sign-in-report.md) , pokud chcete s ní pracovat mimo Azure Portal.  

![Stáhnout](./media/concept-sign-ins/71.png "Stáhnout")

> [!IMPORTANT]
> Počet záznamů, které si můžete stáhnout, je omezený o [Azure Active Directory zásady uchovávání sestav](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Zástupci dat přihlášení

Azure AD a Azure Portal poskytují další vstupní body pro data přihlášení:

- Přehled ochrany zabezpečení identity
- Uživatelé
- Skupiny
- Podnikové aplikace

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Data přihlášení uživatelů v ochraně zabezpečení identity

Graf přihlašování uživatelů na stránce Přehled **ochrany zabezpečení identity** zobrazuje týdenní agregace přihlášení. Výchozí hodnota pro časové období je 30 dní.

![Přihlašovací aktivita](./media/concept-sign-ins/06.png "Aktivita přihlášení")

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

> [!NOTE]
> IP adresy se vydávají takovým způsobem, že mezi IP adresou neexistuje konečné připojení a kde je počítač s touto adresou fyzicky umístěný. Mapování IP adres je složité, protože mobilní poskytovatelé a sítě VPN vydávají IP adresy z centrálních fondů, které jsou často příliš daleko od místa, kde je klientské zařízení skutečně použito. V současné době se v sestavách Azure AD převádějí IP adresa na fyzické místo, což je nejlepší úsilí na základě trasování, dat registru, zpětného vyhledávání a dalších informací.

Na stránce **Uživatelé** zobrazíte úplný přehled všech přihlášení uživatelů kliknutím na **Přihlášení** v části **Aktivita**.

![Přihlašovací aktivita](./media/concept-sign-ins/08.png "Aktivita přihlášení")

## <a name="usage-of-managed-applications"></a>Použití spravovaných aplikací

S použitím zobrazení dat přihlašování zaměřeného na aplikace můžete odpovídat na otázky tohoto typu:

* Kdo používá mé aplikace?
* Jaké jsou tři hlavní aplikace ve vaší organizaci?
* Jak funguje moje nejnovější aplikace?

Vstupním bodem k těmto datům jsou tři hlavní aplikace ve vaší organizaci. Data jsou obsažena v posledních 30 dnech sestavy v části **Přehled** v části **podnikové aplikace**.

![Přihlašovací aktivita](./media/concept-sign-ins/10.png "Aktivita přihlášení")

Grafy využití aplikace týdenní agregovaná přihlášení k vašim horním třem aplikacím v daném časovém období. Výchozí časové období je 30 dnů.

![Přihlašovací aktivita](./media/concept-sign-ins/graph-chart.png "Aktivita přihlášení")

Pokud chcete, můžete se zaměřit na konkrétní aplikaci.

![Vytváření sestav](./media/concept-sign-ins/single-app-usage-graph.png "Vytváření sestav")

Když v grafu využívání aplikací kliknete na konkrétní den, zobrazí se podrobný seznam aktivit přihlašování.

Možnost **Přihlášení** poskytuje úplný přehled o všech událostech přihlašování pro vaše aplikace.

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Protokoly aktivit Office 365 můžete zobrazit v centru pro [správu Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Vezměte v úvahu bod, který znamená, že aktivita Office 365 a protokoly aktivit služby Azure AD sdílejí velký počet prostředků adresáře. Úplné zobrazení protokolů aktivit Office 365 nabízí jenom centrum pro správu Microsoft 365. 

Přístup k protokolům aktivit Office 365 můžete také programově pomocí [rozhraní API pro správu sady office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

* [Kódy chyb sestav aktivit přihlašování](reference-sign-ins-error-codes.md)
* [Zásady uchovávání dat v Azure AD](reference-reports-data-retention.md)
* [Latence sestav Azure AD](reference-reports-latencies.md)

