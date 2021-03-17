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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93122839"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit přihlašování na portálu Azure Active Directory

Architektura vytváření sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu**  -  [Protokoly auditu](concept-audit-logs.md) poskytují informace o aktivitě systému o uživatelích a správě skupin, spravovaných aplikacích a aktivitách adresářů.
    - **Zřizování protokolů**  -  [Protokoly zřizování](./concept-provisioning-logs.md) umožňují zákazníkům monitorovat aktivity prostřednictvím služby zřizování, například vytvoření skupiny v ServiceNow nebo uživatele naimportovaného z Workday. 
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](../identity-protection/overview-identity-protection.md) je indikátorem pokusu o přihlášení někoho, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](../identity-protection/overview-identity-protection.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek obsahuje přehled sestavy přihlášení.

## <a name="prerequisites"></a>Předpoklady

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v rolích správce zabezpečení, čtecí modul zabezpečení, globální čtenář a čtecí modul sestav
* Globální správci
* Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?

Sestava aktivit přihlašování je dostupná ve [všech edicích Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) a dá se k nim dostat taky prostřednictvím rozhraní Microsoft Graph API.

## <a name="sign-ins-report"></a>Sestava přihlášení

Sestava přihlášení uživatelů poskytuje odpovědi na následující otázky:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

V nabídce [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory** nebo vyhledejte a vyberte **Azure Active Directory** na libovolné stránce.

![Vyberte Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

V části **monitorování** vyberte **přihlášení** a otevřete [sestavu přihlášení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Snímek obrazovky zobrazující přihlášení vybraná z nabídky monitorování](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aktivita přihlášení")

Může trvat až dvě hodiny, než se některé záznamy přihlášení zobrazí na portálu.

> [!IMPORTANT]
> Sestava přihlášení zobrazí pouze **interaktivní** přihlášení, tedy přihlášení, kde se uživatel ručně přihlásí pomocí uživatelského jména a hesla. Neinteraktivní přihlášení, jako je ověřování služby-služba, se v sestavě přihlášení nezobrazí. 

Protokol přihlášení má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum přihlášení
- Související uživatel
- Aplikace, ke které se uživatel přihlásil
- Stav přihlášení
- Stav detekce rizik
- Stav požadavku na vícefaktorové ověřování (MFA)

![Snímek obrazovky se zobrazí přihlášení k SharePointu Online v Office 365.](./media/concept-sign-ins/sign-in-activity.png "Aktivita přihlášení")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Snímek obrazovky ukazuje možnost sloupce na stránce přihlášení.](./media/concept-sign-ins/19.png "Aktivita přihlášení")

Dialog **sloupce** vám umožní přístup k volitelným atributům. V sestavě přihlášení nemůžete mít pole, která mají více než jednu hodnotu pro danou žádost o přihlášení jako sloupec. Jedná se například o hodnotu true pro podrobnosti o ověřování, data podmíněného přístupu a umístění v síti.   

![Snímek obrazovky se zobrazí v dialogovém okně sloupce, kde můžete vybrat atributy.](./media/concept-sign-ins/columns.png "Aktivita přihlášení")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![Snímek obrazovky ukazuje podrobné zobrazení informací.](./media/concept-sign-ins/basic-sign-in.png "Aktivita přihlášení")

> [!NOTE]
> Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím všech sestav přihlášení. Kliknutím na kartu **podmíněný přístup** pro záznam pro přihlášení mohou zákazníci zkontrolovat stav podmíněného přístupu a podrobně do podrobností o zásadách, které se vztahují k přihlašování a výsledku pro jednotlivé zásady.
> Další informace najdete v [nejčastějších dotazech k informacím o certifikační autoritě ve všech přihlášeních](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Nejprve zajistěte zúžení hlášených dat na úroveň, která vám bude vyhovovat. Za druhé filtrujte data přihlášení pomocí pole data jako výchozí filtr. Azure AD poskytuje širokou škálu dalších filtrů, které můžete nastavit:

![Snímek obrazovky ukazuje možnost přidat filtry.](./media/concept-sign-ins/04.png "Aktivita přihlášení")

**ID žádosti** – ID žádosti, o kterou vám záleží.

**Uživatel** – jméno nebo hlavní název uživatele (UPN) uživatele, o kterém máte starosti.

**Application** – název cílové aplikace.
 
**Stav** – stav přihlášení, o který máte starosti:

- Success

- Selhání

- Bylo


**IP adresa** – IP adresa zařízení používaného pro připojení k vašemu tenantovi.

**Umístění** – umístění, ze kterého bylo připojení iniciováno:

- City (Město)

- Kraj

- Země/oblast


**Prostředek** – název služby, která se používá pro přihlášení.


**ID prostředku** – ID služby použité pro přihlášení


**Klientská aplikace** – typ klientské aplikace používané pro připojení k vašemu tenantovi:

![Filtr klientských aplikací](./media/concept-sign-ins/client-app-filter.png)


|Name|Moderní ověřování|Description|
|---|:-:|---|
|Ověřený protokol SMTP| |Používá se pro odesílání e-mailových zpráv klientem POP a IMAP.|
|Automatického| |Používá se klienty Outlook a EAS k vyhledání a připojení k poštovním schránkám v Exchangi Online.|
|Exchange ActiveSync| |Tento filtr zobrazuje všechny pokusy o přihlášení, kde došlo k pokusu o provedení protokolu EAS.|
|Prohlížeč|![Modré zaškrtnutí](./media/concept-sign-ins/check.png)|Zobrazí všechny pokusy uživatelů o přihlášení pomocí webových prohlížečů.|
|Exchange ActiveSync| | Zobrazuje všechny pokusy o přihlášení od uživatelů s klientskými aplikacemi, které používají Exchange ActiveSync pro připojení k Exchangi Online.|
|Prostředí PowerShell pro Exchange Online| |Slouží k připojení k Exchangi Online pomocí vzdáleného prostředí PowerShell. Pokud zablokujete základní ověřování pro prostředí Exchange Online PowerShell, musíte k připojení použít modul prostředí Exchange Online PowerShell. Pokyny najdete v tématu [připojení k prostředí PowerShell pro Exchange Online pomocí služby Multi-Factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Webové služby Exchange| |Programovací rozhraní, které používá aplikace Outlook, Outlook pro Mac a aplikace třetích stran.|
|IMAP4| |Starší verze poštovního klienta, který používá IMAP k načtení e-mailu.|
|Rozhraní MAPI přes protokol HTTP| |Používá Outlook 2010 a novější.|
|Mobilní aplikace a klienti klasické pracovní plochy|![Modré zaškrtnutí](./media/concept-sign-ins/check.png)|Zobrazuje všechny pokusy o přihlášení od uživatelů s využitím mobilních aplikací a desktopových klientů.|
|Offline adresář| |Kopie kolekcí seznamu adres, které jsou staženy a používány aplikací Outlook.|
|Outlook odkudkoli (RPC over HTTP)| |Používá Outlook 2016 a starší.|
|Služba Outlook| |Používá se v e-mailu a aplikaci kalendáře pro Windows 10.|
|POP3| |Starší verze poštovního klienta pomocí protokolu POP3 k načtení e-mailu.|
|Webové služby vytváření sestav| |Používá se k načtení dat sestavy v Exchangi Online.|
|Ostatní klienti| |Zobrazuje všechny pokusy o přihlášení uživatelů, u kterých není klientská aplikace zahrnutá nebo neznámá.|



**Operační systém** – operační systém, který se používá v zařízení, se přihlašuje k vašemu tenantovi. 


**Prohlížeč zařízení** – Pokud bylo připojení iniciováno z prohlížeče, toto pole vám umožní filtrovat podle názvu prohlížeče.


**ID korelace** – ID korelace aktivity




**Podmíněný přístup** – stav použitých pravidel podmíněného přístupu

- **Nepoužito** : pro uživatele a aplikaci během přihlašování nebyly aplikovány žádné zásady.

- **Úspěch** : jedna nebo více zásad podmíněného přístupu použitých pro uživatele a aplikaci (ale ne nutně i v ostatních podmínkách) během přihlašování. 

- **Selhání** : přihlášení splnilo podmínku pro uživatele a aplikaci alespoň pro jednu zásadu podmíněného přístupu a udělení těchto ovládacích prvků není splněno nebo je nastaveno pro blokování přístupu.









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

![Snímek obrazovky zobrazuje graf přihlášení za měsíc.](./media/concept-sign-ins/06.png "Aktivita přihlášení")

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

Na stránce **Uživatelé** zobrazíte úplný přehled všech přihlášení uživatelů kliknutím na **Přihlášení** v části **Aktivita** .

![Snímek obrazovky ukazuje část aktivity, kde můžete vybrat přihlášení.](./media/concept-sign-ins/08.png "Aktivita přihlášení")

## <a name="usage-of-managed-applications"></a>Použití spravovaných aplikací

S použitím zobrazení dat přihlašování zaměřeného na aplikace můžete odpovídat na otázky tohoto typu:

* Kdo používá mé aplikace?
* Jaké jsou tři hlavní aplikace ve vaší organizaci?
* Jak funguje moje nejnovější aplikace?

Vstupním bodem k těmto datům jsou tři hlavní aplikace ve vaší organizaci. Data jsou obsažena v posledních 30 dnech sestavy v části **Přehled** v části **podnikové aplikace** .

![Snímek obrazovky ukazuje, kde můžete vybrat přehled.](./media/concept-sign-ins/10.png "Aktivita přihlášení")

Grafy využití aplikace týdenní agregovaná přihlášení k vašim horním třem aplikacím v daném časovém období. Výchozí časové období je 30 dnů.

![Snímek obrazovky ukazuje použití aplikace po dobu jednoho měsíce.](./media/concept-sign-ins/graph-chart.png "Aktivita přihlášení")

Pokud chcete, můžete se zaměřit na konkrétní aplikaci.

![Generování sestav](./media/concept-sign-ins/single-app-usage-graph.png "Přehledy")

Když v grafu využívání aplikací kliknete na konkrétní den, zobrazí se podrobný seznam aktivit přihlašování.

Možnost **Přihlášení** poskytuje úplný přehled o všech událostech přihlašování pro vaše aplikace.

## <a name="microsoft-365-activity-logs"></a>Protokoly aktivit Microsoft 365

Protokoly aktivit Microsoft 365 můžete zobrazit v centru pro [správu Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Vezměte v úvahu, že Microsoft 365 aktivity a protokoly aktivit služby Azure AD sdílejí velký počet prostředků adresáře. Úplné zobrazení protokolů aktivit Microsoft 365 zajišťuje pouze centrum pro správu Microsoft 365. 

Přístup k protokolům aktivit Microsoft 365 lze také programově pomocí [rozhraní API pro správu sady Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

* [Kódy chyb sestav aktivit přihlašování](reference-sign-ins-error-codes.md)
* [Zásady uchovávání dat v Azure AD](reference-reports-data-retention.md)
* [Latence sestav Azure AD](reference-reports-latencies.md)