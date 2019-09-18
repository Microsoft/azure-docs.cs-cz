---
title: Začínáme s portálem Azure EA
description: Tento článek vysvětluje, jak mohou zákazníci Azure EA využívat portál Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900926"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Začínáme s portálem Azure EA

Cílem tohoto článku je usnadnit přímým i nepřímým zákazníkům začátky při používání [portálu Azure EA](https://ea.azure.com). Obsahuje základní informace z těchto oblastí:

- Jak je portál Azure EA strukturovaný
- Role používané na portálu Azure EA
- Jak začít vytvářet předplatná
- Analýza nákladů na portálu Azure EA a webu Azure Portal

Zde je video, které ukazuje kompletní postup onboardingu na portálu Azure EA:

[Video zachycující onboarding na portálu Azure EA](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hierarchie portálu Azure EA

Hierarchie portálu Azure EA se skládá z těchto částí:

**Portál Microsoft Azure EA:** Jde o online portál pro správu, na kterém můžete spravovat náklady na své služby Azure EA. Slouží k vytvoření hierarchie EA Azure, která zahrnuje oddělení, účty a předplatná. Lze ho také využívat k párování nákladů na spotřebované služby, ke stahování sestav o využití a k zobrazování ceníku. A můžete na něm vytvářet klíče rozhraní API pro vaši registraci.

**Oddělení:** Můžete vytvořit oddělení, která vám pomohou rozdělit náklady do logických skupin, a pak nastavit rozpočet nebo kvótu na úrovni oddělení.

**Účty:** Jde o organizační jednotky na portálu EA Azure, které se používají se ke správě předplatných. Používají se i pro vytváření sestav.

**Předplatná:** Představují nejmenší jednotku na portálu Azure EA. Jedná se o kontejnery pro služby Azure spravované správcem služeb.

Následující diagram znázorňuje jednoduché hierarchie Azure EA.

![Diagram jednoduchých hierarchií Azure EA](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Ke správě služeb Azure ve vaší registraci jsou k dispozici čtyři různé uživatelské role pro podnikovou správu:

- Podnikový správce
- Správce oddělení
- Vlastník účtu
- Správce služeb

Role se používají při provádění úloh na dvou různých portálech Microsoft Azure. Portál Azure EA (https://ea.azure.com) slouží k usnadnění správy fakturace a nákladů). Azure Portal (https://portal.azure.com) slouží ke správě služeb Azure.

Role uživatelů se přidružují k jednotlivým uživatelským účtům. Pro ověření identity uživatele je nutné, aby měl platný pracovní či školní účet nebo účet Microsoft. Zajistěte, aby byl každý účet přidružený k e-mailové adrese, která je aktivně sledovaná. Na tuto e-mailovou adresu se budou odesílat oznámení týkající se účtu.

Při nastavování uživatelů můžete k roli Podnikový správce přiřadit více pracovních či školních účtů nebo účtů Microsoft. K roli Vlastník účtu ale můžete přiřadit jenom jeden pracovní či školní účet nebo účet Microsoft. Kromě toho může existovat jen jeden pracovní či školní účet nebo účet Microsoft, který má přiřazenou roli Podnikový správce i roli Vlastník účtu.

### <a name="enterprise-administrator"></a>Podnikový správce

Role Podnikový správce má nejvyšší úroveň přístupu. Uživatelé s tou rolí mohou:

- Spravovat účty a jejich vlastníky
- Spravovat jiné podnikové správce
- Spravovat správce oddělení
- Spravovat kontakty pro oznámení
- Zobrazovat využití napříč všemi účty
- Zobrazovat nefakturované poplatky napříč všemi účty

V podnikové registraci můžete mít více podnikových správců. Podnikovým správcům můžete udělit přístup jen pro čtení. Všichni navíc přebírají roli Správce oddělení.

### <a name="department-administrator"></a>Správce oddělení

Uživatelé s tou rolí mohou:

- Vytvářet a spravovat oddělení
- Vytvářet nové vlastníky účtů
- Zobrazovat podrobnosti o využití pro oddělení, která spravují
- Zobrazovat náklady, pokud mají udělená potřebná oprávnění

V podnikové registraci můžete mít více správců oddělení.

Správcům oddělení můžete udělit přístup jen pro čtení. Pokud chcete správci oddělení udělit přístup jen pro čtení, zahajte jeho úpravy nebo vytvořte nového správce a nastavte možnost jen pro čtení na hodnotu **Yes** (Ano).

### <a name="account-owner"></a>Vlastník účtu

Uživatelé s tou rolí mohou:

- Vytvářet a spravovat předplatná
- Vytvářet správce služeb
- Zobrazovat využití u předplatných

Každý účet vyžaduje jedinečný pracovní či školní účet nebo účet Microsoft. Další informace o správcovských rolích na portálu Azure EA najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Správce služeb

Správce služeb má oprávnění ke správě služeb přes Azure Portal a k přiřazování role spolusprávce jiným uživatelům.

## <a name="activate-your-enrollment"></a>Aktivace registrace

Aktivace služeb probíhá tak, že počáteční podnikový správce otevře portál Azure EA na adrese [https://ea.azure.com](https://ea.azure.com) a přihlásí se pomocí e-mailové adresy z e-mailu s pozvánkou.

Pokud máte více než jednu registraci, vyberte tu, kterou chcete aktivovat. Ve výchozím nastavení se zobrazují jenom aktivní registrace. Historii registrací zobrazíte tak, že v pravém horním rohu portálu Azure EA vymažete možnost **Active** (Aktivní).

Pod registrací se zobrazuje stav **Active** (Aktivní).

![Příklad znázorňující aktivní registraci](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Další podnikové správce mohou vytvářet jen stávající podnikoví správci Azure.

### <a name="create-another-enterprise-admin"></a>Vytvoření dalšího podnikového správce

- Přihlaste se k [portálu Azure EA](https://ea.azure.com), přejděte na **Manage** (Spravovat)  > **Enrollment Detail** (Podrobnosti o registraci) a klikněte na **+ Add Administrator** (+ Přidat správce) v pravém horním rohu stránky.

Ujistěte se, že máte e-mailové adresy daného uživatele a jeho preferovanou metodu ověřování (například přes pracovní či školní účet nebo účet Microsoft). Tyto informace potřebujete, abyste mohli uživatele přidat.

Pokud nejste správcem EA, požádejte některého z nich, aby vás do registrace přidal. Po přidání do registrace obdržíte aktivační e-mail.

Pokud vám daný správce EA nemůže pomoct, vytvořte [žádost o podporu na portálu Azure EA](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Zadejte tyto informace:

- Číslo registrace
- E-mailová adresa, která má být přidána, a typ ověřování (pracovní či školní účet nebo účet Microsoft)
- E-mailové schválení od některého ze stávajících správců EA
  - Pokud není stávající správce EA k dispozici, požádejte svého partnera nebo softwarového poradce, aby změnil informace o kontaktech pomocí nástroje VLSC.

Další informace o rolích podnikových správců najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Vytvoření oddělení Azure EA

Podnikoví správci a správci oddělení využívají oddělení k uspořádání podnikových služeb Azure a také k vykazování jejich využití, které probíhá podle oddělení a nákladového střediska. Podnikový správce může:

- Přidávat nebo odebírat oddělení
- Přidružit účet k oddělení
- Vytvářet správce oddělení
- Povolit správcům oddělení zobrazování cen a nákladů

Správce oddělení může do svých oddělení přidávat nové účty. Účty může i odebírat, ale jen ze svých oddělení, ne z celé registrace.

Jak přidat oddělení:

1. V levé navigační oblasti klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Department** (Oddělení) a na **+ Add Department** (+ Přidat oddělení) a potom zadejte požadované informace.
3. Jediným povinným polem je název oddělení. Musí obsahovat aspoň 3 znaky.
4. Až to dokončíte, klikněte na **Add** (Přidat).

## <a name="add-a-department-admin"></a>Přidání správce oddělení

Po vytvoření oddělení může podnikový správce Azure přidat správce oddělení a přidružit je k tomuto oddělení. Správce oddělení může:

- Vytvářet jiné správce oddělení
- Zobrazovat a upravovat vlastnosti oddělení, jako je název nebo nákladové středisko
- Přidávat účty do svých oddělení
- Odebírat účty ze svých oddělení
- Stahovat podrobnosti o využití pro svá oddělení
- Zobrazovat měsíční využití a poplatky pro své oddělení, pokud mu správce udělil oprávnění <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Jak přidat správce oddělení

Jako podnikový správce:

1. V levé navigační oblasti klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Department** (Oddělení) a potom na požadované oddělení.
3. Klikněte na **+ Add Administrator** (+ Přidat správce) a přidejte požadované údaje.
4. Pro přístup jen pro čtení nastavte možnost **Read-Only** (Jen pro čtení) na **Yes** (Ano) a pak klikněte na **Add** (Přidat).

![Příklad znázorňující dialog pro přidání správce oddělení](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Jak nastavit přístup jen pro čtení

Správcům oddělení je možné udělit přístup jen pro čtení. Při vytváření nového správce oddělení:

- Možnost Read-Only (Jen pro čtení) nastavte na **Yes** (Ano).

Jak upravit existujícího správce oddělení:

1. Vyberte oddělení a potom klikněte na symbol tužky vedle **správce oddělení**, kterého chcete upravit.
2. Možnost Read-Only (Jen pro čtení) nastavte na **Yes** (Ano) a pak klikněte na **Save** (Uložit).

Uživatelé s rolí Podnikový správce získávají automaticky oprávnění správce oddělení.

<sup>1</sup> Pokud vám bylo uděleno oprávnění k zobrazení měsíčního využití a poplatků pro dané oddělení, ale tyto údaje nevidíte, obraťte se na svého partnera.

## <a name="add-an-account"></a>Přidání účtu

Struktura účtů a předplatných ovlivňuje způsob jejich správy a jejich zobrazení na fakturách a v sestavách. Typická uspořádání zahrnují například strukturování podle obchodních divizí, funkčních týmů a geografických oblastí.

Jak přidat účet:

1. V levé navigační oblasti portálu Azure EA klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Account** (Účet) a pak na stránce **Account** (Účet) klikněte na **+ Add Account** (+ Přidat účet).
3. Vyberte oddělení nebo nechte účet nepřiřazený a pak vyberte požadovaný typ ověřování.
4. Zadejte popisný název, který vám usnadní identifikaci účtu ve vytvořených sestavách.
5. Zadejte **e-mailovou adresu vlastníka účtu**, kterou chcete přidružit k novému účtu.
6. Ověřte e-mailovou adresu a pak klikněte na **Add** (Přidat).

![Příklad znázorňující seznam účtů a možnost pro přidání účtu](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Můžete přidat další účet, a to kliknutím na **Add Another Account** (Přidat další účet) nebo na **Add** (Přidat) v pravém dolním rohu levého panelu nástrojů.

Jak si ověřit vlastnictví účtu:

1. Přihlaste se k portálu Azure EA.
2. Vlastnictví účtu si ověříte podle jeho stavu. Stav by se měl změnit z hodnoty **Pending** (Čeká) na **počáteční a koncové datum**. Počáteční datum představuje den, kdy se uživatel poprvé přihlásil, a koncové datum je den, kdy končí smlouva.


## <a name="change-account-owner"></a>Změna vlastníka účtu

Podnikoví správci mohou prostřednictvím portálu Azure EA přenést vlastnictví účtu s předplatnými v rámci registrace. Při této akci se všechna předplatná přenesou ze zdrojového uživatelského účtu na cílový.

Důležité body týkající se přenosu informací mezi uživatelskými účty:

- Jsou podporovány přenosy z pracovního či školního účtu na jiný pracovní nebo školní účet.
- Jsou podporovány přenosy z účtu Microsoft na pracovní nebo školní účet.
- Nejsou podporovány přenosy z pracovního či školního účtu na účet Microsoft.
- Jsou podporovány přenosy z účtu Microsoft na jiný účet Microsoft. Cílovým účtem pro přenos musí být platný účet Azure Commerce. U nových účtů budete při přihlašování k portálu Azure EA požádáni o vytvoření účtu Azure Commerce. U existujících účtů musíte nejdřív vytvořit nové předplatné Azure – teprve pak bude účet splňovat podmínky.
- Po dokončení přenosu předplatných aktualizuje Microsoft vlastníka účtu.

Zásady řízení přístupu na základě role (RBAC):

- Nastavené zásady řízení přístupu na základě role (RBAC) v rámci Azure a přiřazené role správců služeb a spolusprávců se zachovají jenom při přenosech předplatných Azure mezi dvěma ID organizace ve stejném tenantovi. Jiné přenosy předplatných povedou ke ztrátě nastavených zásad RBAC a přiřazených rolí správců služeb a spolusprávců. Zásady a role správců se nepřenášejí mezi různými adresáři. U správců služeb proběhne aktualizace na vlastníka cílového účtu.
- Když provádíte přenosy předplatných mezi dvěma ID organizace ve stejném tenantovi, zachovají se zásady RBAC a přiřazené role správců služeb a spolusprávců.

Před změnou vlastníka účtu:

1. Zobrazte kartu **Account** (Účet) a určete zdrojový účet. Zdrojový účet musí být aktivní.
2. Určete cílový účet. Musí být aktivní.

Jak přenést vlastnictví účtu pro všechna předplatná:

1. V levé navigační oblasti klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Account** (Účet) a najeďte myší na požadovaný účet.
3. Na pravé straně klikněte na symbol pro změnu vlastníka účtu. Symbol vypadá jako osoba.
4. Vyberte účet splňující podmínky a potom klikněte na **Next** (Další).
5. Potvrďte přenos a klikněte na **Submit** (Odeslat).

![Obrázek znázorňující symbol pro změnu vlastníka účtu](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Jak přenést vlastnictví účtu pro jedno předplatné:

1. V levé navigační oblasti klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Account** (Účet) a najeďte myší na požadovaný účet.
3. Na pravé straně klikněte na symbol pro přenos předplatných. Symbol vypadá jako stránka.
4. Vyberte předplatné splňující podmínky a potom klikněte na **Next** (Další).
5. Potvrďte přenos a pak klikněte na **Submit** (Odeslat).

![Obrázek znázorňující symbol pro přenos předplatných](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Zde je video, které ukazuje správu uživatelů na portálu Azure EA:

[Video zachycující správu uživatelů na portálu Azure EA](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Vytvoření předplatného

Vlastníci účtů mohou zobrazovat a spravovat předplatná. Prostřednictvím předplatných můžete týmům ve vaší organizaci poskytovat přístup k vývojovým prostředím a projektům. Například k testování, produkci, vývoji a přípravě. Vytvořením různých předplatných pro jednotlivá aplikační prostředí přispějete k jejich lepšímu zabezpečení. Ke každému předplatnému můžete také přiřadit jiný účet správce služeb. Předplatná můžete přidružit k libovolnému počtu služeb. Předplatná vytváří vlastník účtu, který také ke každému předplatnému v rámci jeho účtu přiřadí účet správce služeb.

### <a name="add-a-subscription"></a>Přidání předplatného

Při přidávání předplatného vycházejte z následujících informací.

Až budete poprvé přidávat předplatné ke svému účtu, budete požádáni o přijetí smlouvy MOSA a plánu sazeb. I když se tyto dokumenty nevztahují na zákazníky se smlouvou Enterprise, jsou nutné pro vytvoření předplatného. Vyšší váhu má nadále verze smlouvy Microsoft Azure Enterprise platná pro vaši registraci a váš smluvní vztah se nijak nemění. Po zobrazení výzvy zaškrtněte políčko označující, že s těmito podmínkami souhlasíte.

Všechna nová předplatná se vytvoří s výchozím názvem předplatného _Microsoft Azure Enterprise_. Název předplatného můžete aktualizovat, abyste ho odlišili ho od jiných předplatných ve vaší registraci. Také bude díky tomu rozpoznatelné v sestavách na úrovni podniku.

Jak přidat předplatné:

1. Na portálu Azure EA se přihlaste ke svému účtu.
2. Klikněte na kartu **Admin** (Správce) a pak na **Subscription** (Předplatné) v horní části stránky.
2. Ověřte, že jste přihlášeni jako vlastník účtu.
3. Klikněte na **+Add Subscription** (+ Přidat předplatné) a pak na **Purchase** (Koupit).
  Při prvním přidávání předplatného k účtu musíte zadat své kontaktní údaje. Při přidávání dalších předplatných se vaše kontaktní údaje vloží automaticky.
4. Klikněte na **Subscriptions** (Předplatná), vyberte předplatné, které jste vytvořili, a pak klikněte na **Edit Subscription Details** (Upravit podrobnosti předplatného).
5. Aktualizujte **název předplatného** a **správce služeb** a potom zaškrtněte políčko.
  Název předplatného se zobrazuje na sestavách a představuje název projektu, který je k předplatnému přidružený na vývojovém portálu.

Může trvat až 24 hodin, než se nové předplatné objeví v seznamu předplatných. Po vytvoření předplatného můžete:

- [Upravit podrobnosti předplatného](https://account.azure.com/Subscriptions)
- [Spravovat služby v předplatném](https://portal.azure.com/#home)

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Převod předplatného s průběžnými platbami na předplatné EA

Pokud chcete jednotlivé předplatné se sazbami stanovenými pro průběžné platby převést na předplatné EA, musíte vytvořit novou žádost o podporu přes Azure Portal. Žádost o podporu vytvoříte kliknutím na **+ Nová žádost o podporu** v oblasti Nápověda a podpora.


## <a name="view-usage-summary-and-download-reports"></a>Zobrazení souhrnu využití a stahování sestav

Podnikoví správci si na portálu Azure EA mohou zobrazit souhrnná data o využití, výši spotřebovaného peněžního závazku a poplatky za další použití. Poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Jak zobrazit podrobné využití pro konkrétní účty

Stáhněte si sestavu s podrobnostmi o využití. Klikněte na **Reports** (Sestavy) a pak na **Download Usage** (Stažení údajů o využití). V seznamu sestav klikněte na tlačítko **Download** (Stáhnout) u měsíční sestavy, kterou chcete získat.

Sestava nezahrnuje příslušné daně. Mezi časem skutečného využití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až 8 hodin.

Jak zobrazit sestavy a grafy se souhrnem využití:

1. Na portálu Azure EA klikněte v levém navigačním panelu na **Reports** (Sestavy) a zobrazte si kartu **Usage Summary** (Souhrn využití).  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Vyberte období závazku.
3. Přepnutím položky **M** (měsíční) na **C** (vlastní) v pravém horním rohu stránky si zobrazíte **souhrn využití** pro období, které sami určíte zadáním počátečního a koncového data.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Výběrem období nebo měsíce v grafu si zobrazíte další podrobnosti.
5. V grafu se zobrazuje meziměsíční využití s rozpisem spotřebovaného využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků z obchodu Marketplace.
6. Pro vybraný měsíc můžete pod grafem filtrovat podle oddělení, účtů a předplatných.
7. Můžete přepínat mezi volbami **Charge by Services** (Vyúčtování podle služeb) a **Charge by Hierarchy** (Vyúčtování podle hierarchie).
8. Rozbalením a sbalením položek **Azure Service** (Služby Azure), **Charges Billed Separately** (Samostatně fakturované poplatky) a **Azure Marketplace** si můžete zobrazit podrobnosti.

Zde je video, které ukazuje zobrazení využití:

[Video zachycující zobrazení využití na portálu Azure EA](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Stahování sestav CSV

Podnikoví správci si mohou ze stránky s měsíčními sestavami stáhnout několik sestav ve formátu CSV. Patří mezi ně:

- Zůstatek a souhrn
- Podrobnosti o využití
- Poplatky za marketplace
- Ceník

Jak sestavy stáhnout:


1. Na portálu Azure EA klikněte na **Reports** (Sestavy).
2. V horní části stránky klikněte na **Download Usage** (Stažení údajů o využití).
3. Vyberte tlačítko **Download** (Stáhnout) vedle sestavy pro požadovaný měsíc.

Mezi datem skutečného využití a jeho zobrazením v sestavě může docházet ke zpoždění až 5 dní.

U uživatelů, kteří si soubory CSV stahují přes Safari do Excelu, může docházet k chybám formátování. Chybám se vyhnete tak, že soubor otevřete pomocí textového editoru.

![Příklad ukazující stránku pro stažení údajů o využití](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Zde je video, které ukazuje, jak si stáhnout informace o využití:

[Video zachycující používání portálu Azure EA](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Naplánování onboardingového hovoru

Pokud si chcete pro zákaznický onboarding zamluvit osobní relaci, vytvořte žádost o podporu v [podpoře k portálu Azure EA](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Vyberte **Onboarding** jako **kategorii problému**.

## <a name="next-steps"></a>Další kroky
- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](billing-ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
- Pokud potřebujete pomoct vyřešit potíže s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](billing-ea-portal-troubleshoot.md).
