---
title: Sestavy aktivit přihlašování na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit přihlašování na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f47de0544d6d708d3c8b104be4edada86c11551
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190360"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit přihlašování na portálu Azure Active Directory

Architektura generování sestav v Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu** - [protokoly auditu](concept-audit-logs.md) poskytují informace aktivit systému o uživatelích a Správa skupin, spravovaných aplikacích a aktivitách adresářů.
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikových přihlášení](concept-risky-sign-ins.md) je indikátorem pokusu přihlásit, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika** – [rizikový uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožený.

Toto téma poskytuje přehled o sestavy přihlášení.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v rolích zabezpečení správce, čtenáře zabezpečení a čtečky sestav
* Globální správci
* Kromě toho může každý uživatel (bez oprávnění správce) přístup k vlastní přihlášení 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?
* Váš tenant musí mít licenci Azure AD Premium přidružené ho, abyste viděli sestavu aktivity všech přihlášení. Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory. Všimněte si, že pokud nemáte žádná data aktivity před upgradem, bude trvat několik dní daná data zobrazit v sestavách po upgradu na licenci premium.

## <a name="sign-ins-report"></a>Sestava přihlášení

Sestava přihlášení uživatele poskytuje odpovědi na následující otázky:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

Sestavy přihlášení můžete přistupovat výběrem **přihlášení** v **aktivity** část **Azure Active Directory** okna portálu [webuAzureportal](https://portal.azure.com). Všimněte si, že může trvat až dvě hodiny pro některé záznamy přihlášení se zobrazí na portálu.

![Aktivita přihlašování](./media/concept-sign-ins/61.png "Aktivita přihlašování")

> [!IMPORTANT]
> Sestavy přihlášení zobrazí pouze **interaktivní** přihlášení, které je, přihlášení, kde uživatel ručně přihlásí pomocí svého uživatelského jména a hesla. Neinteraktivní přihlášení, jako je například ověřování služba služba, se nezobrazují v sestavě přihlášení. 

Protokol přihlášení má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum přihlášení
- Související uživatel
- Aplikace, ke které se uživatel přihlásil
- Stav přihlášení
- Stav detekce rizik
- Stav požadavku na vícefaktorové ověřování (MFA)

![Aktivita přihlašování](./media/concept-sign-ins/01.png "Aktivita přihlašování")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Aktivita přihlašování](./media/concept-sign-ins/19.png "Aktivita přihlašování")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Aktivita přihlašování](./media/concept-sign-ins/02.png "Aktivita přihlašování")

Vyberte položku v zobrazení seznamu zobrazíte podrobnější informace.

![Aktivita přihlašování](./media/concept-sign-ins/03.png "Aktivita přihlašování")

> [!NOTE]
> Zákazníci teď můžete řešit zásady podmíněného přístupu všechny sestavy přihlášení. Kliknutím na **podmíněného přístupu** kartu záznam přihlašování zákazníků můžete zkontrolovat stav podmíněného přístupu a informace o zásadách, které se použijí k přihlášení a výsledek pro jednotlivé zásady.
> Další informace najdete v tématu [– nejčastější dotazy o informace certifikační Autority v všechna přihlášení](reports-faq.md#conditional-access).

![Aktivita přihlašování](./media/concept-sign-ins/ConditionalAccess.png "Aktivita přihlašování")


## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících výchozích polí:

- Uživatel
- Aplikace
- Stav přihlášení
- Podmíněný přístup
- Datum

![Aktivita přihlašování](./media/concept-sign-ins/04.png "Aktivita přihlašování")

Filtr **Uživatel** umožňuje určit jméno nebo hlavní název uživatele (UPN) pro uživatele, o kterého vám jde.

Filtr **Aplikace** umožňuje určit název aplikace, o kterou vám jde.

Filtr **Stav přihlášení** umožňuje vybrat jednu z následujících možností:

- Vše
- Úspěch
- Selhání

**Podmíněného přístupu** filtr umožňuje vybrat stav zásad certifikační Autority pro přihlášení:

- Vše
- Nevztahuje se.
- Úspěch
- Selhání

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


![Aktivita přihlašování](./media/concept-sign-ins/12.png "Aktivita přihlašování")


## <a name="download-sign-in-activities"></a>Stažení aktivit přihlašování

Je možné [stáhnout data přihlášení](quickstart-download-sign-in-report.md) Pokud budete chtít pracovat s ním mimo na webu Azure portal. Kliknutím na tlačítko **Stáhnout** vytvoříte soubor CSV s 5000 nejnovějších záznamů.  Kromě tlačítko Stáhnout na webu Azure portal také poskytuje možnost [vygenerovat skript k stáhnout data](tutorial-signin-logs-download-script.md).  

![Stáhnout](./media/concept-sign-ins/71.png "Stáhnout")

Pokud potřebujete větší flexibilitu, můžete použít řešení se skriptem. Kliknutím na **skript** vytvoří skript Powershellu, který zahrnuje všechny filtry, které jste nastavili. Stáhněte a spusťte tento skript **režimu správce** ke generování souboru CSV. 

> [!IMPORTANT]
> Počet záznamů, které si můžete stáhnout, které je omezená [zásady uchování sestav Azure Active Directory](reference-reports-data-retention.md).  

### <a name="running-the-script-on-a-windows-10-machine"></a>Spuštění skriptu na počítač s Windows 10

Pokud chcete spustit skript v **Windows 10** počítače, musíte nejprve provést několik dalších kroků. 

1. Nainstalujte [Az modulu](/powershell/azure/install-az-ps).
2. Otevřete příkazový řádek Powershellu a spuštěním příkazu importujte modul **Import-Module Az**.
3. Spustit **Set-ExecutionPolicy unrestricted** a zvolte **Ano všem**. 
4. Teď můžete spouštět staženého skriptu prostředí PowerShell v režimu správce k vygenerování souboru CSV.

## <a name="sign-ins-data-shortcuts"></a>Klávesové zkratky data přihlášení

Kromě služby Azure AD na webu Azure portal vám poskytne další vstupní body k datům přihlášení:

- Přehled ochrany zabezpečení identit
- Uživatelé
- Skupiny
- Podnikové aplikace

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Data přihlašování uživatelů v ochrany zabezpečení identit

Uživatele v grafu přihlašování v **ochrany zabezpečení identit** stránka s přehledem jsou znázorněny týdenní agregace přihlášení všech uživatelů v daném časovém období. Výchozí časové období je 30 dnů.

![Aktivita přihlašování](./media/concept-sign-ins/06.png "Aktivita přihlašování")

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
> IP adresy se vydávají takovým způsobem, že neexistuje žádná konečné připojení mezi IP adresy a kdy je počítač s touto adresou fyzicky umístěn. Mapování IP adres je složité fakt, poskytovatelů mobilních a sítím VPN a vydávat IP adresy z centrální fondů, které jsou často velmi daleko od skutečně použití klientského zařízení. V sestavách služby Azure AD, převod IP adres na fyzické umístění je aktuálně pokusí na základě trasování, klíče registru, zpětného vyhledávání a další informace.

Na stránce **Uživatelé** zobrazíte úplný přehled všech přihlášení uživatelů kliknutím na **Přihlášení** v části **Aktivita**.

![Aktivita přihlašování](./media/concept-sign-ins/08.png "Aktivita přihlašování")

## <a name="usage-of-managed-applications"></a>Použití spravovaných aplikací

S použitím zobrazení dat přihlašování zaměřeného na aplikace můžete odpovídat na otázky tohoto typu:

* Kdo používá mé aplikace?
* Které jsou 3 nejpoužívanější aplikace v organizaci?
* Nedávno jsem zpřístupnil aplikaci. Jak to s ní vypadá?

Vaším vstupním bodem k těmto datům jsou 3 nejpoužívanější aplikace v organizaci v rámci sestavy za posledních 30 dnů v oddílu **Přehled** v části **Podnikové aplikace**.

![Aktivita přihlašování](./media/concept-sign-ins/10.png "Aktivita přihlašování")

Graf využívání aplikací s týdenními agregacemi přihlašování pro 3 nejpoužívanější aplikace v daném časovém období. Výchozí časové období je 30 dnů.

![Aktivita přihlašování](./media/concept-sign-ins/47.png "Aktivita přihlašování")

Pokud chcete, můžete se zaměřit na konkrétní aplikaci.

![Vytváření sestav](./media/concept-sign-ins/single_spp_usage_graph.png "Vytváření sestav")

Když v grafu využívání aplikací kliknete na konkrétní den, zobrazí se podrobný seznam aktivit přihlašování.

Možnost **Přihlášení** poskytuje úplný přehled o všech událostech přihlašování pro vaše aplikace.

![Aktivita přihlašování](./media/concept-sign-ins/11.png "Aktivita přihlašování")

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Můžete zobrazit protokoly aktivit Office 365 z [centrum pro správu Office 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). I když aktivita Office 365 a Azure AD aktivity protokoly sdílejí velké množství prostředků adresáře pouze Office 365 centru pro správu poskytuje úplný přehled protokolů aktivit Office 365. 

Programově pomocí protokolů aktivit Office 365 se dá dostat taky [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další postup

* [Kódy chyb sestavě aktivit přihlašování](reference-sign-ins-error-codes.md)
* [Zásady uchovávání dat služby Azure AD](reference-reports-data-retention.md)
* [Latence sestav Azure AD](reference-reports-latencies.md)

