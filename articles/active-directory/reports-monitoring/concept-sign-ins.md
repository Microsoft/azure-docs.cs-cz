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
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246513"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit přihlašování na portálu Azure Active Directory

Architektura sestav ve službě Azure Active Directory (Azure AD) se skládá z následujících součástí:

- **Činnosti** 
    - **Přihlášení** – informace o využití spravovaných aplikací a aktivity přihlášení uživatelů.
    - **Protokoly auditu Protokoly** - [auditu](concept-audit-logs.md) poskytují informace o aktivitách systému uživatelů a správy skupin, spravovaných aplikací a aktivit adresářů.
- **Zabezpečení** 
    - **Riskantní přihlášení** – Riskantní [přihlášení](concept-risky-sign-ins.md) je indikátorem pro pokus o přihlášení někým, kdo není legitimním vlastníkem uživatelského účtu.
    - **Uživatelé označeni jako riziko** – [rizikový uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek poskytuje přehled sestavy přihlášení.

## <a name="prerequisites"></a>Požadavky

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v rolích Správce zabezpečení, Čtečka zabezpečení, Globální čtečka a Čtečka sestav
* Globální správci
* Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?

- Sestava aktivit přihlášení je k dispozici ve [všech edicích Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data).

- Pokud chcete získat přístup k přihlašovacím datům pomocí rozhraní API, váš tenant musí mít k němu přidruženou licenci [Azure Active Directory Premium.](../fundamentals/active-directory-get-started-premium.md)



## <a name="sign-ins-report"></a>Sestava přihlášení

Sestava přihlášení uživatele poskytuje odpovědi na následující otázky:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

V nabídce [Portál Azure](https://portal.azure.com) vyberte Azure **Active Directory**nebo vyhledejte a vyberte Azure **Active Directory** z libovolné stránky.

![Výběr služby Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

V části **Sledování**vyberte **Přihlášení** a otevřete [sestavu Přihlášení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Aktivita přihlášení](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aktivita přihlášení")

Může trvat až dvě hodiny, než se některé přihlašovací záznamy zobrazí na portálu.

> [!IMPORTANT]
> Sestava přihlášení zobrazuje pouze **interaktivní** přihlášení, tj. V sestavě přihlášení se nezobrazují neinteraktivní přihlášení, například ověřování mezi službami. 

Protokol přihlášení má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum přihlášení
- Související uživatel
- Aplikace, ke které se uživatel přihlásil
- Stav přihlášení
- Stav detekce rizik
- Stav požadavku na vícefaktorové ověřování (MFA)

![Aktivita přihlášení](./media/concept-sign-ins/sign-in-activity.png "Aktivita přihlášení")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Aktivita přihlášení](./media/concept-sign-ins/19.png "Aktivita přihlášení")

Dialogové okno **Sloupce** umožňuje přístup k volitelným atributům. V sestavě přihlášení nemůžete mít jako sloupec pole, která mají více než jednu hodnotu pro daný požadavek na přihlášení. To platí například pro podrobnosti ověřování, data podmíněného přístupu a umístění v síti.   

![Aktivita přihlášení](./media/concept-sign-ins/columns.png "Aktivita přihlášení")

Vyberte položku v zobrazení seznamu, abyste získali podrobnější informace.

![Aktivita přihlášení](./media/concept-sign-ins/basic-sign-in.png "Aktivita přihlášení")

> [!NOTE]
> Zákazníci teď můžou řešit zásady podmíněného přístupu prostřednictvím všech sestav přihlášení. Kliknutím na kartu **Podmíněný přístup** pro záznam přihlášení mohou zákazníci zkontrolovat stav podmíněného přístupu a ponořit se do podrobností o zásadách, které se vztahují k přihlášení a výsledku pro každou zásadu.
> Další informace naleznete [v tématu Nejčastější dotazy týkající se informací o certifikační autoritě ve všech přihlášeních](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Nejprve zúžení nahlášených dat na úroveň, která vám vyhovuje. Za druhé, data přihlášení filtru pomocí pole data jako výchozího filtru. Azure AD poskytuje širokou škálu dalších filtrů, které můžete nastavit:

![Aktivita přihlášení](./media/concept-sign-ins/04.png "Aktivita přihlášení")

**ID žádosti** – ID žádosti, na které vám záleží.

**Uživatel** – jméno nebo hlavní jméno uživatele (UPN) uživatele, na jehož zabezpečení vám záleží.

**Aplikace** - Název cílové aplikace.
 
**Stav** – Stav přihlášení, na který vám záleží:

- Úspěch

- Selhání

- Přerušen


**IP adresa** – IP adresa zařízení použitého k připojení k tenantovi.

**Umístění** - umístění, ze kterého bylo připojení inicializováno:

- Město

- Stát / Provincie

- Země/region


**Prostředek** – název služby použité pro přihlášení.


**ID prostředku** – ID služby použité pro přihlášení.


**Klientská aplikace** – typ klientské aplikace, která se používá k připojení k vašemu tenantovi:

![Filtr klientské aplikace](./media/concept-sign-ins/client-app-filter.png)


|Name (Název)|Moderní ověřování|Popis|
|---|:-:|---|
|Ověřený smtp| |Klient pop a IMAP používá k odesílání e-mailových zpráv.|
|Autodiscover| |Klienti Outlooku a EAS používají k vyhledání poštovních schránek exchange online a připojení k ní.|
|Exchange ActiveSync| |Tento filtr zobrazuje všechny pokusy o přihlášení, kde byl pokus o protokol EAS.|
|Prohlížeč|![Zaškrtnout](./media/concept-sign-ins/check.png)|Zobrazuje všechny pokusy o přihlášení od uživatelů pomocí webových prohlížečů.|
|Exchange ActiveSync| | Zobrazuje všechny pokusy o přihlášení od uživatelů pomocí klientských aplikací pomocí exchange acticesync pro připojení k Exchange Online|
|Exchange Online PowerShell| |Slouží k připojení k Exchange Online pomocí vzdáleného prostředí PowerShell. Pokud zablokujete základní ověřování pro Exchange Online PowerShell, budete muset použít modul Exchange Online PowerShell pro připojení. Pokyny najdete v [tématu Připojení k Exchange Online PowerShellpomocí vícefaktorového ověřování](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Webové služby Exchange| |Programovací rozhraní, které používají Outlook, Outlook pro Mac a aplikace třetích stran.|
|IMAP4| |Starší poštovní klient používající iMAP k načtení e-mailu.|
|MAPI přes PROTOKOL HTTP| |Používá aplikace Outlook 2010 a novější.|
|Mobilní aplikace a klienti pro stolní počítače|![Zaškrtnout](./media/concept-sign-ins/check.png)|Zobrazuje všechny pokusy o přihlášení od uživatelů, kteří používají mobilní aplikace a klienty pro stolní počítače.|
|Adresář offline| |Kopie kolekcí seznamů adres, které jsou staženy a používány aplikací Outlook.|
|Outlook anywhere (Vzdálené volání procedur přes protokol HTTP)| |Používá se v Outlooku 2016 a starším.|
|Služba Outlook| |Používá aplikace Pošta a Kalendář pro Windows 10.|
|POP3| |Starší poštovní klient používající protokol POP3 k načtení e-mailu.|
|Webová služba pro vytváření sestav| |Slouží k načtení dat sestavy v Exchange Online.|
|Ostatní klienti| |Zobrazuje všechny pokusy o přihlášení od uživatelů, u kterých klientská aplikace není zahrnuta nebo je neznámá.|



**Operační systém** – operační systém spuštěný na zařízení používá přihlášení k tenantovi. 


**Prohlížeč zařízení** – Pokud bylo připojení zahájeno z prohlížeče, umožňuje toto pole filtrovat podle názvu prohlížeče.


**ID korelace** – ID korelace aktivity.




**Podmíněný přístup** – stav použitých pravidel podmíněného přístupu

- **Nepoužito**: Při přihlašování nebyly pro uživatele a aplikace použity žádné zásady.

- **Úspěch**: Během přihlašování se na uživatele a aplikaci se použije jedna nebo více zásad podmíněného přístupu (ale ne nutně dalších podmínek). 

- **Selhání**: Jedna nebo více zásad podmíněného přístupu bylo použito a během přihlášení nebylo splněno.









## <a name="download-sign-in-activities"></a>Stažení aktivit přihlašování

Klepnutím na možnost **Stáhnout** vytvořte soubor CSV nebo JSON s nejnovějšími 250 000 záznamy. Začněte [se stažením přihlašovacích dat,](quickstart-download-sign-in-report.md) pokud s ním chcete pracovat mimo portál Azure.  

![Stáhnout](./media/concept-sign-ins/71.png "Stáhnout")

> [!IMPORTANT]
> Počet záznamů, které si můžete stáhnout, je omezen [zásadami uchovávání zpráv služby Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Datové zkratky pro přihlášení

Azure AD i portál Azure vám poskytují další vstupní body k datům přihlášení:

- Přehled ochrany zabezpečení identity
- Uživatelé
- Skupiny
- Podnikové aplikace

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Uživatelé přihlašovací data v ochraně zabezpečení identity

Graf přihlášení uživatele na stránce přehled **ochrany zabezpečení identity** zobrazuje týdenní agregace přihlášení. Výchozí hodnota pro časové období je 30 dní.

![Aktivita přihlášení](./media/concept-sign-ins/06.png "Aktivita přihlášení")

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
> Ip adresy jsou vydávány takovým způsobem, že neexistuje žádné konečné spojení mezi adresou IP a místem, kde je počítač s touto adresou fyzicky umístěn. Mapování IP adres je komplikováno skutečností, že mobilní operátoři a virtuální ny vydávají IP adresy z centrálních fondů, které jsou často velmi daleko od místa, kde se klientské zařízení skutečně používá. V současné době v sestavách Azure AD je převod IP adresy na fyzické umístění nejlepší úsilí založené na trasování, datech registru, zpětném vyhledávání a dalších informacích.

Na stránce **Uživatelé** zobrazíte úplný přehled všech přihlášení uživatelů kliknutím na **Přihlášení** v části **Aktivita**.

![Aktivita přihlášení](./media/concept-sign-ins/08.png "Aktivita přihlášení")

## <a name="usage-of-managed-applications"></a>Použití spravovaných aplikací

S použitím zobrazení dat přihlašování zaměřeného na aplikace můžete odpovídat na otázky tohoto typu:

* Kdo používá mé aplikace?
* Jaké jsou tři nejlepší aplikace ve vaší organizaci?
* Jak se daří mé nejnovější aplikaci?

Vstupní bod těchto dat je tři nejlepší aplikace ve vaší organizaci. Data jsou obsažena během posledních 30 dnů sestavy v části **Přehled** v části **Podnikové aplikace**.

![Aktivita přihlášení](./media/concept-sign-ins/10.png "Aktivita přihlášení")

Grafy využití aplikací týdenní agregace přihlášení pro vaše tři nejlepší aplikace v daném časovém období. Výchozí časové období je 30 dnů.

![Aktivita přihlášení](./media/concept-sign-ins/graph-chart.png "Aktivita přihlášení")

Pokud chcete, můžete se zaměřit na konkrétní aplikaci.

![Vytváření sestav](./media/concept-sign-ins/single-app-usage-graph.png "Vytváření sestav")

Když v grafu využívání aplikací kliknete na konkrétní den, zobrazí se podrobný seznam aktivit přihlašování.

Možnost **Přihlášení** poskytuje úplný přehled o všech událostech přihlašování pro vaše aplikace.

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Protokoly aktivit Office 365 si můžete prohlédnout v [Centru pro správu Microsoftu 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Vezměme si na místo, které Office 365 aktivity a protokoly aktivit Azure AD sdílet významný počet prostředků adresáře. Jenom Centrum pro správu Microsoftu 365 poskytuje úplný přehled o protokolech aktivit Office 365. 

K protokolům aktivit Office 365 můžete také přistupovat programově pomocí [api pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

* [Kódy chyb sestavy aktivit přihlášení](reference-sign-ins-error-codes.md)
* [Zásady uchovávání dat Azure AD](reference-reports-data-retention.md)
* [Latence sestavy Azure AD](reference-reports-latencies.md)

