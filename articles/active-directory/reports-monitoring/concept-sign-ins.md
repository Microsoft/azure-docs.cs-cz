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
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc8d3525ab7cdbdf298ecbbc686ced16fa7bc77c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055022"
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
* Uživatelé v správce zabezpečení, čtenář zabezpečení, role čtenáře sestav
* Globální správci
* Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?
* Klient musí mít přiřazenou licenci Azure AD Premium, aby mohl zobrazit sestavu veškerých aktivit přihlašování.


## <a name="sign-in-activities"></a>Aktivity přihlašování

Na základě informací poskytnutých sestavou přihlašování uživatelů najdete odpovědi na otázky tohoto typu:

* Jaký je vzorec přihlašování uživatele?
* Kolik uživatelů se přihlásilo za týden?
* Jaký je stav těchto přihlášení?

Váš první vstupní bod pro všechna přihlášení data aktivit je **přihlášení** v části aktivity služby **Azure Active Directory**.


![Aktivita přihlašování](./media/concept-sign-ins/61.png "Aktivita přihlašování")


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

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.

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
- Stav detekce rizik
- Datum

![Aktivita přihlašování](./media/concept-sign-ins/04.png "Aktivita přihlašování")

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


![Aktivita přihlašování](./media/concept-sign-ins/12.png "Aktivita přihlašování")


## <a name="download-sign-in-activities"></a>Stažení aktivit přihlašování

Pokud chcete pracovat s daty aktivit přihlašování mimo web Azure Portal, můžete si je stáhnout. Kliknutím na tlačítko **Stáhnout** vytvoříte soubor CSV s 5000 nejnovějších záznamů.  Kromě tlačítka pro stahování máte také na webu Azure Portal možnost vygenerovat skript, který data stáhne.  

![Stáhnout](./media/concept-sign-ins/71.png "Stáhnout")

Pokud potřebujete větší flexibilitu, můžete použít řešení se skriptem. Kliknutím na **skript** vytvoří skript Powershellu, který zahrnuje všechny filtry, které jste nastavili. Stáhněte a spusťte tento skript **režimu správce** ke generování souboru CSV. 

### <a name="running-the-script-on-a-windows-10-machine"></a>Spuštění skriptu na počítač s Windows 10

Pokud chcete spustit skript v **Windows 10** počítače, musíte nejprve provést několik dalších kroků. 

1. Nainstalujte [modulu AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Otevřete příkazový řádek Powershellu a spuštěním příkazu importujte modul **Import-Module AzureRM**.
3. Spustit **Set-ExecutionPolicy unrestricted** a zvolte **Ano všem**. 
4. Teď můžete spouštět staženého skriptu prostředí PowerShell v režimu správce k vygenerování souboru CSV.

Kromě technické implementace je počet záznamů, které si můžete stáhnout, také omezený [zásadami uchování sestav Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-in-activities-shortcuts"></a>Zkratky pro aktivity přihlašování

Kromě Azure Active Directory, na webu Azure portal vám poskytne další vstupní body k přihlášení v datech o aktivitách:

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



## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět více o kódech chyb aktivit přihlašování, přečtěte si téma [Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory](reference-sign-ins-error-codes.md).

