---
title: Začínáme s portálem Azure EA
description: Tento článek vysvětluje, jak mohou zákazníci Azure EA využívat portál Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992228"
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

![Diagram jednoduchých hierarchií Azure EA](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Ke správě služeb Azure v registraci je k dispozici pět různých rolí administrativního uživatele organizace:

- Podnikový správce
- Správce oddělení
- Vlastník účtu
- Správce služeb
- Kontakt na oznámení

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

Každý účet vyžaduje jedinečný pracovní či školní účet nebo účet Microsoft. Další informace o správcovských rolích na portálu Azure EA najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Správce služeb

Správce služeb má oprávnění ke správě služeb přes Azure Portal a k přiřazování role spolusprávce jiným uživatelům.

### <a name="notification-contact"></a>Kontakt na oznámení

Kontakt na oznámení obdrží oznámení o využití týkající se registrace.

## <a name="activate-your-enrollment"></a>Aktivace registrace

Aktivace služeb probíhá tak, že počáteční podnikový správce otevře portál Azure EA na adrese [https://ea.azure.com](https://ea.azure.com) a přihlásí se pomocí e-mailové adresy z e-mailu s pozvánkou.

Pokud jste nastavili jako správce EA, nemusíte dostávat aktivační e-mail, abyste se mohli přihlásit k portálu Azure EA. Můžete pokračovat [https://ea.azure.com](https://ea.azure.com) a přihlásit se pomocí e-mailové adresy (buď Work, School nebo Live ID), a hesla.

Pokud máte více než jednu registraci, vyberte tu, kterou chcete aktivovat. Ve výchozím nastavení se zobrazují jenom aktivní registrace. Historii registrací zobrazíte tak, že v pravém horním rohu portálu Azure EA vymažete možnost **Active** (Aktivní).

Pod registrací se zobrazuje stav **Active** (Aktivní).

![Příklad znázorňující aktivní registraci](./media/ea-portal-get-started/ea-enrollment-status.png)

Další podnikové správce mohou vytvářet jen stávající podnikoví správci Azure.

### <a name="create-another-enterprise-admin"></a>Vytvoření dalšího podnikového správce

- Přihlaste se k [portálu Azure EA](https://ea.azure.com), přejděte na **Manage** (Spravovat)  > **Enrollment Detail** (Podrobnosti o registraci) a klikněte na **+ Add Administrator** (+ Přidat správce) v pravém horním rohu stránky.

Ujistěte se, že máte e-mailové adresy daného uživatele a jeho preferovanou metodu ověřování (například přes pracovní či školní účet nebo účet Microsoft). Tyto informace potřebujete, abyste mohli uživatele přidat.

Pokud nejste správcem EA, požádejte některého z nich, aby vás do registrace přidal. Po přidání do registrace obdržíte aktivační e-mail.

Pokud vám daný správce EA nemůže pomoct, vytvořte [žádost o podporu na portálu Azure EA](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Zadejte tyto informace:

- Registrační číslo
- E-mailová adresa, která má být přidána, a typ ověřování (pracovní či školní účet nebo účet Microsoft)
- E-mailové schválení od některého ze stávajících správců EA
  - Pokud není stávající správce EA k dispozici, požádejte svého partnera nebo softwarového poradce, aby změnil informace o kontaktech pomocí nástroje VLSC.

Další informace o rolích podnikových správců najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

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

![Příklad znázorňující dialog pro přidání správce oddělení](./media/ea-portal-get-started/ea-create-add-department-admin.png)

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

![Příklad znázorňující seznam účtů a možnost pro přidání účtu](./media/ea-portal-get-started/create-ea-add-an-account.png)

Můžete přidat další účet, a to kliknutím na **Add Another Account** (Přidat další účet) nebo na **Add** (Přidat) v pravém dolním rohu levého panelu nástrojů.

Jak si ověřit vlastnictví účtu:

1. Přihlaste se k portálu Azure EA.
1. Vlastnictví účtu si ověříte podle jeho stavu. Stav by se měl změnit z hodnoty **Pending** (Čeká) na **počáteční a koncové datum**. Počáteční datum představuje den, kdy se uživatel poprvé přihlásil, a koncové datum je den, kdy končí smlouva.
1. Při prvním přihlášení k portálu Azure EA se zobrazí zpráva upozornění a vlastník účtu si musí účet aktivovat kliknutím na **Pokračovat**.


## <a name="change-account-owner"></a>Změna vlastníka účtu

Podnikoví správci mohou prostřednictvím portálu Azure EA přenést vlastnictví účtu s předplatnými v rámci registrace. Při této akci se všechna předplatná přenesou ze zdrojového uživatelského účtu na cílový.

Důležité body týkající se přenosu informací mezi uživatelskými účty:

- Jsou podporovány přenosy z pracovního či školního účtu na jiný pracovní nebo školní účet.
- Jsou podporovány přenosy z účtu Microsoft na pracovní nebo školní účet.
- Nejsou podporovány přenosy z pracovního či školního účtu na účet Microsoft.
- Jsou podporovány přenosy z účtu Microsoft na jiný účet Microsoft. Cílovým účtem pro přenos musí být platný účet Azure Commerce. U nových účtů budete při přihlašování k portálu Azure EA požádáni o vytvoření účtu Azure Commerce. U existujících účtů musíte nejdřív vytvořit nové předplatné Azure – teprve pak bude účet splňovat podmínky.
- Po dokončení přenosu předplatných aktualizuje Microsoft vlastníka účtu.

Zásady řízení přístupu na základě role:

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

![Obrázek znázorňující symbol pro změnu vlastníka účtu](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Jak přenést vlastnictví účtu pro jedno předplatné:

1. V levé navigační oblasti klikněte na **Manage** (Spravovat).
2. Klikněte na kartu **Account** (Účet) a najeďte myší na požadovaný účet.
3. Na pravé straně klikněte na symbol pro přenos předplatných. Symbol vypadá jako stránka.
4. Vyberte předplatné splňující podmínky a potom klikněte na **Next** (Další).
5. Potvrďte přenos a pak klikněte na **Submit** (Odeslat).

![Obrázek znázorňující symbol pro přenos předplatných](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Zde je video, které ukazuje správu uživatelů na portálu Azure EA:

[Video zachycující správu uživatelů na portálu Azure EA](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Vytvoření odběru

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

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Převod předplatného EA na předplatné s průběžnými platbami

Pokud chcete převést předplatné EA na jednotlivé předplatné s průběžnými platbami, musíte na portálu Azure EA vytvořit novou žádost o podporu. Žádost o podporu vytvoříte kliknutím na **+ Nová žádost o podporu** v oblasti Nápověda a podpora.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Přidružení stávajícího účtu k předplatnému s průběžnými platbami

Pokud již máte účet Microsoft Azure na portálu Microsoft Azure, který chcete přidružit k vaší registraci smlouvy Enterprise, zadejte přidružený účet Microsoft nebo pracovní nebo školní účet.

### <a name="associate-an-existing-account"></a>Přidružení stávajícího účtu

1. Na portálu Enterprise Portal klikněte na **Spravovat**.
1. Klikněte na kartu **Účet**.
1. Klikněte na **+ Přidat účet**.
1. Zadejte účet Microsoft nebo pracovní nebo školní účet přidružený ke stávajícímu účtu.
1. Potvrďte účet Microsoft nebo pracovní nebo školní účet přidružený ke stávajícímu účtu.
1. Zadejte název, který chcete použít k identifikaci tohoto účtu v sestavách.
1. Klikněte na tlačítko **Add** (Přidat).
1. Můžete znovu vybrat možnost **+ Přidat účet** a přidat další účet nebo se výběrem tlačítka **Správce** můžete vrátit na domovskou stránku.
1. Pokud si zobrazíte stránku **Účet**, nově přidaný účet se zobrazí ve stavu **Čeká na vyřízení**.

### <a name="confirm-account-ownership"></a>Potvrzení vlastnictví účtu

1. Přihlaste se k e-mailovému účtu přidruženému k účtu Microsoft nebo pracovnímu nebo školnímu účtu, který jste zadali.
1. Otevřete e-mailové oznámení s názvem _Pozvánka k aktivaci vašeho účtu ve službě Microsoft Azure z multilicenčního programu Microsoftu_.
1. Klikněte na odkaz **Přihlásit se k portálu Microsoft Azure Enterprise Portal** uvedený v pozvánce.
1. Klikněte na **Přihlásit se**.
1. Přihlaste se zadáním svého účtu Microsoft nebo pracovního nebo školního účtu a hesla a potvrďte vlastnictví účtu.

### <a name="azure-marketplace"></a>Azure Marketplace

Přestože se většina předplatných převádí z prostředí s průběžnými platbami na Enterprise Azure, služby z Azure Marketplace se nepřevádí. Pokud chcete získat jedno zobrazení všech předplatných a poplatků, doporučujeme přidat služby z Azure Marketplace na portál Enterprise Portal:

1. V levém navigačním panelu klikněte na **Spravovat**.
1. Klikněte na kartu **Registrace**.
1. Prohlédněte si část Podrobnosti o registraci.
1. Napravo od pole Azure Marketplace kliknutím na ikonu tužky povolte zobrazení a klikněte na **Uložit**.

Vlastník účtu si teď může zakoupit předplatná Azure Marketplace, která dříve vlastnil s průběžnými platbami.

Po aktivaci nových předplatných Azure Marketplace ve vaší registraci zrušte předplatná Marketplace vytvořená v prostředí s průběžnými platbami. Tento krok je zásadní proto, aby vaše předplatná Marketplace po vypršení platnosti vašeho platebního nástroje pro průběžné platby nepřešla do špatného stavu.

### <a name="msdn"></a>MSDN

Předplatná MSDN se automaticky převedou na předplatná MSDN pro vývoj/testování a nabídka EA ztratí veškerý stávající peněžní kredit.

### <a name="azure-in-open"></a>Azure v programu Open

Přidružením předplatného systému Azure v rámci licenčního programu Open ke smlouvě EA zaniknou veškeré nespotřebované kredity Azure v programu Open. Zákazníkům, kteří chtějí předejít zániku kreditů, doporučujeme spotřebovat veškeré kredity v předplatném systému Azure v rámci licenčního programu Open dřív, než účet přidají ke smlouvě EA.  

### <a name="accounts-with-support-subscriptions"></a>Účty s předplatnými podpory

Pokud na portál Enterprise Portal přidáváte existující účty s předplatným podpory (které ještě nemají předplatné podpory EA), mějte na paměti, že předplatné podpory MOSA se automaticky nepřevádí a podporu je potřeba v účtu EA zakoupit znovu. Abyste měli čas si znovu objednat podporu, až do konce následujícího měsíce budete mít k dispozici období odkladu.

## <a name="view-usage-summary-and-download-reports"></a>Zobrazení souhrnu využití a stahování sestav

Podnikoví správci si na portálu Azure EA mohou zobrazit souhrnná data o využití, výši spotřebovaného peněžního závazku a poplatky za další použití. Poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Zobrazení podrobných informací o využití pro konkrétní účty:

Stáhněte si sestavu s podrobnostmi o využití. Klikněte na **sestavy** a pak klikněte na kartu **použití ke stažení** . V seznamu sestav klikněte na **Stáhnout** pro měsíční sestavu, kterou chcete získat.

Sestava nezahrnuje příslušné daně. Mezi časem skutečného využití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až 8 hodin.

Zobrazení sestav a grafů se souhrnem využití:

1. Na portálu Azure EA klikněte v levém navigačním panelu na **Reports** (Sestavy) a zobrazte si kartu **Usage Summary** (Souhrn využití).  
  ![Vytvoření a zobrazení souhrnu využití a stahování sestav](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Vyberte období závazku.
3. Přepnutím položky **M** (měsíční) na **C** (vlastní) v pravém horním rohu stránky si zobrazíte **souhrn využití** pro období, které sami určíte zadáním počátečního a koncového data.  
  ![Vytvoření a zobrazení souhrnu využití a stahování sestav ve vlastním zobrazení](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Výběrem období nebo měsíce v grafu si zobrazíte další podrobnosti.
5. V grafu se zobrazuje meziměsíční využití s rozpisem spotřebovaného využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků z obchodu Marketplace.
6. Pro vybraný měsíc můžete pod grafem filtrovat podle oddělení, účtů a předplatných.
7. Můžete přepínat mezi volbami **Charge by Services** (Vyúčtování podle služeb) a **Charge by Hierarchy** (Vyúčtování podle hierarchie).
8. Rozbalením a sbalením položek **Azure Service** (Služby Azure), **Charges Billed Separately** (Samostatně fakturované poplatky) a **Azure Marketplace** si můžete zobrazit podrobnosti.

Zde je video, které ukazuje zobrazení využití:

[Video zachycující používání portálu Azure EA](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

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

![Příklad ukazující stránku pro stažení údajů o využití](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Zde je video, které ukazuje, jak si stáhnout informace o využití:

[Video zachycující používání portálu Azure EA](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Stažení rozšířené sestavy

Při generování sestav pro konkrétní rozsahy dat nebo účty je možné využít stažení rozšířené sestavy. Od 30. srpna 2016 se změnil formát výstupního souboru z .xslx na .csv, který podporuje větší sady záznamů.

1. Vyberte **Stažení rozšířené sestavy**.
1. Vyberte **odpovídající rozsah dat**.
1. Vyberte **odpovídající účty**.
1. Vyberte **Požádat o data o využití**.
1. Opakovaně vyberte tlačítko **Aktualizovat**, dokud se stav sestavy neaktualizuje na **Stáhnout**.
1. Stáhněte si sestavu.

## <a name="ea-term-glossary"></a>Glosář pojmů ve smlouvě EA

- **Účet**: organizační jednotka na portálu Azure EA, která slouží ke správě předplatných a využití pro vytváření sestav.
- **Vlastník účtu**: osoba identifikovaná za účelem správy předplatných a správců služeb na Microsoft Azure. Může zobrazit data o využití tohoto účtu a přidružených předplatných.
- **Předplatné dodatku**: jeden rok nebo předplatné coterminous v rámci změny registrace.
- **Závazek**: závazek roční finanční částky za Microsoft Azure služby za zlevněnou sazbu za využití na základě této zálohy.
- **Správce oddělení**: osoby identifikované za účelem správy oddělení, vytváření nových účtů a vlastníků účtů, zobrazení podrobností o využití pro oddělení, která spravují, a zobrazení nákladů v případě udělení oprávnění.
- **Registrační číslo**: jedinečný identifikátor poskytnutý Microsoftem, který identifikuje konkrétní registraci přidruženou ke smlouvě Enterprise.
- **Podnikový správce**: osoby identifikované za účelem správy oddělení a vlastníků oddělení a účtů a vlastníků účtů na Microsoft Azure. Můžou spravovat podnikové správce a zobrazit data o využití, fakturovaná množství a nefakturované poplatky napříč všemi účty a předplatnými přidruženými k dané podnikové registraci.
- **Smlouva Enterprise**: licenční smlouva Microsoft pro zákazníky s centralizovaným nákupem, kteří chtějí standardizovat celou organizaci na technologii Microsoftu a udržovat infrastrukturu informačních technologií na standardu softwaru Microsoftu.
- **Registrace smlouvy Enterprise**: registrace v programu Enterprise Agreement, který poskytuje produkty Microsoftu ve velkém poměru zvýhodněné sazby.
- **Účet Microsoft**: webová služba, která umožňuje zúčastněným webům ověřit uživatele s jednou sadou přihlašovacích údajů.
- **Microsoft Azure změnu registrace podnikového nasazení (změna registrace)** : dodatek podepsaný podnikem, který jim poskytne přístup k Microsoft Azure v rámci své podnikové registrace.
- **Portál EA pro Azure**: portál, který naši Podnikoví zákazníci používá ke správě svých účtů Microsoft Azure a jejich souvisejících předplatných.
- **Spotřebované množství prostředků**: množství jednotlivé Microsoft Azure služby, které bylo využito za měsíc.
- **Správce služeb**: osoba, která se identifikovala k přístupu a správě předplatných a vývojových projektů na portálu Azure EA.
- **Předplatné**: představuje předplatné portálu Azure EA a je kontejnerem Microsoft Azure služeb spravovaných stejným správcem služby.
- **Pracovní nebo školní účet**: organizace, které nastavily službu Active Directory s federaci na Cloud a všechny účty, jsou v jednom tenantovi.

### <a name="enrollment-statuses"></a>Stavy registrace:

- **Čeká na dokončení**: správce registrace musí přihlásit se k portálu Azure EA. Po přihlášení se stav registrace přepne na Aktivní.
- **Aktivní**: registrace je aktivní a účty a odběry se dají vytvořit na portálu Azure EA. Registrace zůstane aktivní až do koncového data smlouvy Enterprise.
- **Nekonečná rozšířená doba**: po uplynutí koncového data smlouvy Enterprise dojde k neurčitému rozšířenému termínu. Umožňuje zákazníkům EA, kteří jsou výslovně přihlášeni k období prodloužení, aby po skončení platnosti jejich smlouvy Enterprise pokračovali v používání Azure po neomezenou dobu. Před tím, než registrace EA dosáhne koncového data smlouvy Enterprise, by se správce registrace měl rozhodnout, jestli registraci prodlouží přidáním dalšího peněžního závazku, přejde na novou registraci, provede migraci na program Microsoft Online Subscription Program (MOSP), nebo potvrdí deaktivaci všech služeb přidružených k dané registraci.
- **Vypršela platnost**: zákazník EA se vykazuje prodlouženým termínem a registrace EA dosáhla koncového data smlouvy Enterprise, platnost zápisu vyprší a všechny přidružené služby budou zakázané.
- **Přenesené**: registrace, kde se všechny přidružené účty a služby přenesou do nové registrace, se zobrazí u přenesených stavů. Mějte na paměti, že pokud se při prodloužení vygeneruje nové číslo registrace, registrace se automaticky nepřevedou. Aby se usnadnil automatický převod, je potřeba v dokumentech zákazníka k prodloužení uvést předchozí číslo registrace.

## <a name="get-started-on-azure-ea-faq"></a>Začínáme s nejčastějšími dotazy k účtu Azure EA

Tento dokument obsahuje podrobnosti o typických dotazech zákazníků během procesu onboardingu.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Můžu ke smlouvě Enterprise přidružit stávající účet Azure?

Ano, je to možné. Je důležité poznamenat, že všechna předplatná Azure, pro která jste vlastníkem účtu, se převedou na vaši smlouvu Enterprise. Týká se to i předplatných využívajících měsíční kredit (např. předplatná sady Visual Studio, Azure Pass, MPN, BizSpark atd.). To znamená, že touto akcí o měsíční kredit přijdete.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Omylem jsem přidružil(a) svůj stávající účet Azure ke smlouvě Enterprise. Následkem toho se mi ztratil měsíční kredit. Můžu měsíční kredit získat zpět?

Pokud jste pro účet EA použili stejné přihlašovací údaje jako pro své předplatné sady Visual Studio a po ověření jako vlastník účtu EA chcete obnovit individuální výhodu Azure pro předplatné sady Visual Studio, musíte použít jeden z následujících postupů:
1. Odeberte nebo přesuňte všechna předplatná Azure tohoto vlastníka účtu, pak na portálu EA Portal tohoto vlastníka účtu odstraňte a řekněte mu, aby se znovu zaregistroval k individuálním výhodám Azure pro sadu Visual Studio.
 NEBO
1. Na webu pro správu ve VLSC odstraňte předplatitele sady Visual Studio a znovu mu přiřaďte předplatné s tím, že tentokrát musí použít jiné přihlašovací údaje – pak se může znovu zaregistrovat k individuálním výhodám Azure pro sadu Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Jaký typ předplatného mám vytvořit?

EA Portal nabízí podnikovým zákazníkům dva typy předplatného:

- Microsoft Azure Enterprise – ideální pro:
  - Veškeré produkční využití
  - Nejlepší ceny v závislosti na útratě za infrastrukturu
  - Další podrobnosti najdete na stránce https://azure.microsoft.com/pricing/enterprise-agreement/.
- Enterprise pro vývoj/testování – ideální pro:
  - Všechny týmové procesy vývoje/testování
  - Procesy vývoje/testování se středním až vysokým zatížením
  - Přístup ke speciálním imagím MSDN a preferenčním sazbám za služby
  - Další podrobnosti najdete na stránce https://azure.microsoft.com/offers/ms-azr-0148p/.

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Je možné převést vlastnictví předplatného na jiný účet?

Ano, vlastnictví předplatného je možné převést na jiný účet. Například pokud účet A obsahuje tři předplatná, podnikový správce může převést jedno předplatné do účtu B, jedno do účtu C a jedno do účtu D, nebo všechna do účtu E.

Můžete přejít do účtu EA, kliknout na Spravovat > Účet, najet myší na **Účet** (zcela vpravo) a zobrazí se Vlastnictví předplatného (ikona hlavy) a možnost Převést předplatné (ikona seznamu).

Tato možnost se zobrazí pouze u aktivních účtů.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Jako výchozí název předplatného se používá název nabídky. Mám název předplatného změnit na nějaký smysluplný název pro naši organizaci?

Pro každé vytvořené předplatné se jako výchozí hodnota použije zvolený typ nabídky. Doporučujeme změnit název předplatného na název, který vám usnadní sledování předplatného.

**Změna názvu:**
1. Přihlaste se k webu [https://account.windowsazure.com](https://account.windowsazure.com).
1. Klikněte na Seznam předplatných.
1. Vyberte předplatné.
1. Klikněte na ikonu **Spravovat předplatné**.
1. Upravte podrobnosti o předplatném.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Jak můžu sledovat vzniklé náklady podle nákladového střediska?

Abyste mohli sledovat náklady podle nákladového střediska, musíte definovat nákladové středisko na některé z následujících úrovní:
- Oddělení
- Účet
- Předplatné

Podle potřeby můžete stejné nákladové středisko použít ke sledování využití a nákladů souvisejících s konkrétním nákladovým střediskem.

Například pokud chcete sledovat náklady na speciální projekt, na kterém se podílí více oddělení, můžete ke sledování využití a nákladů použít nákladové středisko na úrovni předplatného.

Nákladové středisko není možné definovat na úrovni služby. V případě, že chcete sledovat využití na úrovni služby, můžete využít funkci značek, která je dostupná na úrovni služby.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Jak můžu sledovat využití a útratu v různých odděleních organizace?

V rámci registrace EA můžete vytvořit libovolný počet oddělení. Abyste mohli správně sledovat využití, musíte zajistit, aby se předplatná mezi odděleními nesdílela.

Po vytvoření oddělení a předplatného si můžete prohlédnout tok informací v sestavě využití, která vám pomůže sledovat využití a spravovat náklady a útratu na úrovni oddělení.

K informacím o využití můžete přistupovat také přes rozhraní API. Podrobné informace a vzorový kód najdete na stránce [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Můžu nastavit kvótu útraty a dostávat upozornění, když se budu blížit limitu?

Můžete nastavit kvótu útraty na úrovni oddělení a systém vás automaticky upozorní, když vaše limity útraty dosáhnou 50 %, 75 %, 90 % a 100 % definované kvóty.

Pokud chcete definovat kvótu útraty, klikněte na oddělení, ke kterému chcete přidat limit útraty, a klikněte na ikonu Upravit. Kliknutím na **Uložit** uložte podrobnosti.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>K implementaci řízení přístupu na základě role a sledování využití jsem použil(a) skupiny prostředků. Jak můžu zobrazit související podrobnosti o využití?

Informace například o skupinách prostředků nebo značkách (pokud se používají) se sledují na úrovni služby a jsou k dispozici v souboru s podrobnými informacemi o využití (CSV) ke stažení, který si můžete stáhnout z portálu Azure EA: [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

K informacím o využití můžete přistupovat také přes rozhraní API. Podrobné informace a vzorový kód najdete na stránce [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Mějte na paměti, že značky můžete používat pouze pro prostředky, které podporují operace Resource Manageru. Pokud jste vytvořili virtuální počítač, virtuální síť nebo úložiště prostřednictvím modelu nasazení Classic (například přes portál Classic), nemůžete pro tyto prostředky použít značky. Pokud chcete zajistit podporu označování, musíte tyto prostředky nasadit znovu prostřednictvím Resource Manageru. Všechny ostatní prostředky označování podporují.

### <a name="can-i-perform-analyses-using-power-bi"></a>Můžu provádět analýzy s využitím Power BI?

Ano. Balíček obsahu Microsoft Azure Enterprise pro Power BI umožňuje rychle importovat a analyzovat využití platformy Azure u podnikové registrace, zjistit, které oddělení, účet nebo předplatné se na využití podílelo nejvíce, kterou službu vaše organizace nejvíce používala, nebo sledovat útratu a trendy používání.

**Přejděte na web Power BI:**

 1. Přihlaste se pomocí platného pracovního nebo školního účtu.
    - Může se jednat o stejný pracovní nebo školní účet, který používáte pro přístup k registraci prostřednictvím portálu Azure EA, nebo jiný pracovní nebo školní účet.
 1. Na řídicím panelu služeb zvolte:
    - dlaždici Microsoft Azure Enterprise.
    - Klikněte na **Připojit**.
 1. Na obrazovce Připojit k Azure Enterprise zvolte:
    - adresu URL prostředí Azure: [https://ea.azure.com](https://ea.azure.com).
    - počet měsíců: vyberte hodnotu v rozmezí 1 až 36.
    - číslo registrace: zadejte číslo registrace.
    - Klikněte na **Další**.
 1. Do pole Ověřovací klíč zadejte klíč rozhraní API. Klíč rozhraní API můžete získat na portálu Azure EA na kartě Stažení údajů o využití kliknutím na **Přístupový klíč rozhraní API**.
    - Klíč zkopírujte a vložte ho do pole Klíč účtu.
    - Načtení dat v Power BI bude v závislosti na velikosti datové sady trvat přibližně 5 až 30 minut.

Generování sestav Power BI je k dispozici pro přímé a nepřímé zákazníky a partnery EA, kteří můžou zobrazit fakturační údaje.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
- Pokud potřebujete pomoct vyřešit potíže s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).
- Tady najdete [průvodce onboardingem účtu Azure EA](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
