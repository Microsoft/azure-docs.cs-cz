---
title: Začínáme s portálem Azure Enterprise
description: Tento článek vysvětluje, jak zákazníci se smlouvou Azure Enterprise (Azure EA) používají portál Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 0404e3b268f5797e34b3818df8eebeefbe68a1f3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371945"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Začínáme s portálem Azure Enterprise

Tento článek pomáhá přímým i nepřímým zákazníkům se smlouvou Azure Enterprise (Azure EA) začít používat [portál Azure Enterprise](https://ea.azure.com). Získáte základní informace o:

- Struktuře portálu Azure Enterprise
- Rolích používaných na portálu Azure Enterprise
- Vytvoření předplatného
- Analýze nákladů na portálu Azure Enterprise a webu Azure Portal

Podívejte se na toto video, které předvádí celý postup onboardingu portálu Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarchie portálu Azure Enterprise

Hierarchie portálu Azure Enterprise se skládá z těchto částí:

- **Portál Azure Enterprise** je online portál pro správu, který pomáhá se správou nákladů na služby Azure EA. Můžete:

  - Vytvořit hierarchii Azure EA s odděleními, účty a předplatnými.
  - Párovat náklady na spotřebované služby, stahovat sestavy o využití a zobrazovat ceníky.
  - Vytvořit klíče rozhraní API pro registraci.

- **Oddělení** usnadňují segmentaci nákladů do logických seskupení. Oddělení umožňují nastavit rozpočet nebo kvótu na úrovni oddělení.

- **Účty** jsou organizační jednotky na portálu Azure Enterprise. Pomocí účtů můžete spravovat předplatná a přistupovat k sestavám.

- **Předplatná** představují nejmenší jednotku na portálu Azure Enterprise. Jedná se o kontejnery pro služby Azure spravované správcem služeb.

Následující diagram znázorňuje jednoduché hierarchie Azure EA.

![Diagram jednoduchých hierarchií Azure EA](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Následující role administrativního uživatele jsou součástí registrace Enterprise:

- Podnikový správce
- Správce oddělení
- Vlastník účtu
- Správce služeb
- Kontakt pro oznámení

Role plní úlohy na dvou různých portálech. [Portál Azure Enterprise](https://ea.azure.com) se používá ke správě fakturace a nákladů a web [Azure Portal](https://portal.azure.com) ke správě služeb Azure.

Role uživatelů se přidružují k jednotlivým uživatelským účtům. Pro ověření identity uživatele je nutné, aby měl platný pracovní nebo školní účet nebo účet Microsoft. Zajistěte, aby byl každý účet přidružený k e-mailové adrese, která je aktivně sledovaná. Na tuto e-mailovou adresu se budou odesílat oznámení týkající se účtu.

Při nastavování uživatelů můžete k roli Podnikový správce přiřadit více účtů. Roli vlastníka účtu ale může mít pouze jeden účet. Jednomu účtu můžete také přiřadit jak roli podnikového správce, tak roli vlastníka účtu.

### <a name="enterprise-administrator"></a>Podnikový správce

Uživatelé s touto rolí mají nejvyšší úroveň přístupu. Mohou:

- spravovat účty a jejich vlastníky,
- spravovat jiné podnikové správce,
- spravovat správce oddělení,
- spravovat kontakty pro oznámení,
- zobrazovat využití napříč všemi účty,
- zobrazovat nefakturované poplatky napříč všemi účty.

V podnikové registraci můžete mít více podnikových správců. Podnikovým správcům můžete udělit přístup jen pro čtení. Všichni navíc přebírají roli Správce oddělení.

### <a name="department-administrator"></a>Správce oddělení

Uživatelé s tou rolí mohou:

- vytvářet a spravovat oddělení,
- vytvářet nové vlastníky účtů,
- zobrazovat podrobnosti o využití pro oddělení, která spravují,
- zobrazovat náklady, pokud mají udělená potřebná oprávnění.

V podnikové registraci můžete mít více správců oddělení.

Při úpravách nebo vytváření nového správce oddělení můžete tomuto správci udělit přístup jen pro čtení. Možnost Read-Only (Jen pro čtení) nastavte na **Yes** (Ano).

### <a name="account-owner"></a>Vlastník účtu

Uživatelé s tou rolí mohou:

- vytvářet a spravovat předplatná,
- vytvářet správce služeb,
- zobrazovat využití u předplatných.

Každý účet vyžaduje jedinečný pracovní nebo školní účet nebo účet Microsoft. Další informace o správcovských rolích na portálu Azure Enterprise najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Správce služeb

Role Správce služeb má oprávnění ke správě služeb přes Azure Portal a k přiřazování role spolusprávce jiným uživatelům.

### <a name="notification-contact"></a>Kontakt pro oznámení

Kontakt pro oznámení dostává oznámení o využití související s příslušnou smlouvou.

## <a name="activate-your-enrollment"></a>Aktivace registrace

Aktivace služeb probíhá tak, že počáteční podnikový správce otevře [portál Azure Enterprise](https://ea.azure.com) a přihlásí se pomocí e-mailové adresy z e-mailu s pozvánkou.

Pokud vás někdo nastavil jako podnikového správce, nemusíte čekat na aktivační e-mail. Přejděte na [portál Azure Enterprise](https://ea.azure.com) a přihlaste se pomocí své pracovní nebo školní e-mailové adresy nebo e-mailové adresy účtu Microsoft a hesla.

Pokud máte více než jednu registraci, vyberte tu, kterou chcete aktivovat. Ve výchozím nastavení se zobrazují jenom aktivní registrace. Historii registrací zobrazíte tak, že v pravém horním rohu portálu Azure Enterprise vymažete možnost **Active** (Aktivní).

Pod **registrací** se zobrazuje stav **Active** (Aktivní).

![Příklad znázorňující aktivní registraci](./media/ea-portal-get-started/ea-enrollment-status.png)

Další podnikové správce mohou vytvářet jen stávající podnikoví správci Azure.

### <a name="create-another-enterprise-administrator"></a>Vytvoření dalšího podnikového správce

Přidání dalšího podnikového správce:

1. Přihlaste se k [portálu Azure Enterprise](https://ea.azure.com).
1. Přejděte na **Manage** (Spravovat)  > **Enrollment Detail** (Podrobnosti registrace).
1. Vpravo nahoře vyberte **+ Add Administrator** (+Přidat správce).

Ujistěte se, že máte e-mailovou adresu daného uživatele a jeho preferovanou metodu ověřování (například přes pracovní či školní účet nebo účet Microsoft).

Pokud nejste podnikovým správcem, požádejte některého z nich, aby vás do registrace přidal. Po přidání do registrace obdržíte aktivační e-mail.

Pokud vám daný podnikový správce nemůže pomoct, vytvořte [žádost o podporu na portálu Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Zadejte tyto informace:

- Číslo registrace
- E-mailová adresa, která má být přidána, a typ ověřování (pracovní či školní účet nebo účet Microsoft)
- E-mailové schválení od některého ze stávajících podnikových správců
  - Pokud není stávající podnikový správce k dispozici, požádejte svého partnera nebo softwarového poradce, aby změnil informace o kontaktech pomocí nástroje VLSC (Volume Licensing Service Center).

Další informace o rolích podnikových správců najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Vytvoření oddělení Azure Enterprise

Podnikoví správci a správci oddělení využívají oddělení k uspořádání podnikových služeb Azure a také k vykazování jejich využití, které probíhá podle oddělení a nákladového střediska. Podnikový správce může:

- přidávat nebo odebírat oddělení,
- přidružit účet k oddělení,
- vytvářet správce oddělení,
- povolit správcům oddělení zobrazování cen a nákladů.

Správce oddělení může do svých oddělení přidávat nové účty. Účty může i odebírat, ale jen ze svých oddělení, ne z celé registrace.

Jak přidat oddělení:

1. Přihlaste se k portálu Azure Enterprise.
1. V levém podokně vyberte **Manage** (Spravovat).
1. Vyberte kartu **Department** (Oddělení) a potom vyberte **+ Add Department** (+ Přidat oddělení).
1. Zadejte požadované informace.
   Jediným povinným polem je název oddělení. Musí obsahovat aspoň 3 znaky.
1. Po dokončení vyberte **Add** (Přidat).

## <a name="add-a-department-administrator"></a>Přidání správce oddělení

Po vytvoření oddělení může podnikový správce přidat správce oddělení a přidružit je k tomuto oddělení. Správci oddělení mohou u svých oddělení provádět následující akce:

- Vytvářet jiné správce oddělení
- Zobrazovat a upravovat vlastnosti oddělení, jako je název nebo nákladové středisko
- Přidání účtů
- Odebírat účty
- Stahovat podrobnosti o využití
- Zobrazovat měsíční využití a poplatky <sup>1</sup>

> <sup>1</sup> Tato oprávnění musí udělit podnikový správce. Pokud vám bylo uděleno oprávnění k zobrazení měsíčního využití a poplatků pro dané oddělení, ale tyto údaje nevidíte, obraťte se na svého partnera.

### <a name="to-add-a-department-administrator"></a>Přidání správce oddělení

Jako podnikový správce:

1. Přihlaste se k portálu Azure Enterprise.
1. V levém podokně vyberte **Manage** (Spravovat).
1. Vyberte kartu **Department** (Oddělení) a potom příslušné oddělení.
1. Vyberte **+ Add Administrator** (+ Přidat správce) a přidejte požadované údaje.
1. Pro přístup jen pro čtení nastavte možnost **Read-Only** (Jen pro čtení) na **Yes** (Ano) a pak vyberte **Add** (Přidat).

![Příklad znázorňující dialogové okno pro přidání správce oddělení](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Jak nastavit přístup jen pro čtení

Správcům oddělení je možné udělit přístup jen pro čtení.

- Při vytváření nového správce oddělení nastavte možnost jen pro čtení na hodnotu **Yes** (Ano).

- Jak upravit existujícího správce oddělení:
   1. Vyberte oddělení a potom vyberte symbol tužky vedle **správce oddělení**, kterého chcete upravit.
   1. Nastavte otevírání jen pro čtení na **Yes** (Ano) a potom vyberte **Save** (Uložit).

Podnikoví správci získávají automaticky oprávnění správce oddělení.

## <a name="add-an-account"></a>Přidání účtu

Struktura účtů a předplatných ovlivňuje způsob jejich správy a jejich zobrazení na fakturách a v sestavách. Typické organizační struktury zahrnují například obchodní divize, funkční týmy a geografické oblasti.

Jak přidat účet:

1. V levé navigační oblasti na portálu Azure Enterprise vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet). Na stránce **Account** (Účet) vyberte **+Add Account** (+Přidat účet).
1. Vyberte oddělení nebo nechte účet nepřiřazený a pak vyberte požadovaný typ ověřování.
1. Zadejte popisný název, který vám usnadní identifikaci účtu ve vytvořených sestavách.
1. Zadejte **e-mailovou adresu vlastníka účtu**, kterou chcete přidružit k novému účtu.
1. Ověřte e-mailovou adresu a pak vyberte **Add** (Přidat).

![Příklad znázorňující seznam účtů a možnost pro přidání účtu](./media/ea-portal-get-started/create-ea-add-an-account.png)

Pokud chcete přidat další účet, vyberte **Add Another Account** (Přidat další účet) nebo **Add** (Přidat) v pravém dolním rohu levého panelu nástrojů.

Jak si ověřit vlastnictví účtu:

1. Přihlaste se k portálu Azure Enterprise.
1. Zobrazte stav.

   Stav by se měl změnit z hodnoty **Pending** (Čeká) na **počáteční a koncové datum**. Počáteční datum představuje den, kdy se uživatel poprvé přihlásil, a koncové datum je den, kdy končí smlouva.
1. Při prvním přihlášení k portálu Azure Enterprise se zobrazí zpráva **Warning** (Upozornění) a vlastník účtu si musí účet aktivovat výběrem možnosti **Continue** (Pokračovat).

## <a name="change-account-owner"></a>Změna vlastníka účtu

Podnikoví správci mohou prostřednictvím portálu Azure Enterprise přenést vlastnictví účtu s předplatnými v rámci registrace. Při této akci se všechna předplatná přenesou ze zdrojového uživatelského účtu na cílový.

Při přenášení účtů mějte na paměti toto:

- Můžete provést tyto přenosy:
  - Z pracovního nebo školního účtu na jiný pracovní nebo školní účet.
  - Z účtu Microsoft na pracovní nebo školní účet.
  - Z účtu Microsoft na jiný účet Microsoft.

    Cílovým účtem pro přenos musí být platný účet Azure Commerce. U nových účtů budete při přihlašování k portálu Azure Enterprise požádáni o vytvoření účtu Azure Commerce. U existujících účtů musíte nejdřív vytvořit nové předplatné Azure – teprve pak bude účet splňovat podmínky.

- Přenos z pracovního nebo školního účtu na účet Microsoft nelze provést.

- Po dokončení přenosu předplatných aktualizuje Microsoft vlastníka účtu.

Seznamte se s těmito zásadami řízení přístupu na základě role v Azure (Azure RBAC):

- Když provádíte přenosy předplatných mezi dvěma ID organizace ve stejném tenantovi, zachovají se zásady Azure RBAC a přiřazené role správců služeb a spolusprávců.
- Jiné přenosy předplatných povedou ke ztrátě nastavených zásad Azure RBAC a přiřazení rolí.
- Zásady a role správců se nepřenášejí mezi různými adresáři. U správců služeb proběhne aktualizace na vlastníka cílového účtu.

Před změnou vlastníka účtu:

1. Na portálu Azure Enterprise zobrazte kartu **Account** (Účet) a určete zdrojový účet. Zdrojový účet musí být aktivní.
1. Určete cílový účet a zkontrolujte, že je aktivní.

Jak přenést vlastnictví účtu pro všechna předplatná:

1. Přihlaste se k portálu Azure Enterprise.
1. V levé navigační oblasti vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet) a najeďte myší na požadovaný účet.
1. Na pravé straně vyberte ikonu pro změnu vlastníka účtu. Ikona vypadá jako osoba.
1. Zvolte účet splňující podmínky a potom vyberte **Next** (Další).
1. Potvrďte přenos a vyberte **Submit** (Odeslat).

![Obrázek znázorňující symbol pro změnu vlastníka účtu](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Jak přenést vlastnictví účtu pro jedno předplatné:

1. Přihlaste se k portálu Azure Enterprise.
1. V levé navigační oblasti vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet) a najeďte myší na požadovaný účet.
1. Na pravé straně vyberte ikonu pro přenos předplatných. Ikona vypadá jako stránka.
1. Zvolte předplatné splňující podmínky a potom vyberte **Next** (Další).
1. Potvrďte přenos a pak vyberte **Submit** (Odeslat).

![Obrázek znázorňující symbol pro přenos předplatných](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Podívejte se na toto video o správě uživatelů na portálu Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Vytvoření odběru

Vlastníci účtů mohou zobrazovat a spravovat předplatná. Prostřednictvím předplatných můžete týmům ve vaší organizaci poskytovat přístup k vývojovým prostředím a projektům. Například k testování, produkci, vývoji a přípravě.

Vytvořením různých předplatných pro jednotlivá aplikační prostředí přispějete k jejich lepšímu zabezpečení.

- Ke každému předplatnému můžete také přiřadit jiný účet správce služeb.
- Předplatná můžete přidružit k libovolnému počtu služeb.
- Předplatná vytváří vlastník účtu, který také ke každému předplatnému v rámci jeho účtu přiřadí účet správce služeb.

### <a name="add-a-subscription"></a>Přidání předplatného

Při přidávání předplatného vycházejte z následujících informací.

Až budete poprvé přidávat předplatné ke svému účtu, budete požádáni o přijetí smlouvy MOSA (Microsoft Online Subscription Agreement) a plánu sazeb. I když se smlouva MOSA a plány sazeb nevztahují na zákazníky se smlouvou Enterprise, jsou nutné pro vytvoření předplatného. Vyšší váhu má nadále verze smlouvy Microsoft Azure Enterprise platná pro vaši registraci a váš smluvní vztah se nijak nemění. Po zobrazení výzvy zaškrtněte políčko označující, že s těmito podmínkami souhlasíte.

Při vytvoření předplatného je výchozím názvem _Microsoft Azure Enterprise_. Název můžete změnit, aby ho bylo možné odlišit od ostatních předplatných v rámci registrace a zajistit, že je v sestavách na podnikové úrovni rozpoznatelný.

Jak přidat předplatné:

1. Na portálu Azure Enterprise se přihlaste ke svému účtu.
1. Vyberte kartu **Admin** (Správce) a potom nahoře na stránce vyberte **Subscription** (Předplatné).
1. Ověřte, že jste přihlášeni jako vlastník účtu.
1. Vyberte **+Add Subscription** (+Přidat předplatné) a potom vyberte **Purchase** (Koupit).

   Při prvním přidávání předplatného k účtu musíte zadat své kontaktní údaje. Při přidávání dalších předplatných se vaše kontaktní údaje vloží automaticky.

1. Vyberte **Subscriptions** (Předplatná) a potom vyberte předplatné, které jste vytvořili.
1. Vyberte **Edit Subscription Details** (Upravit podrobnosti předplatného).
1. Upravte **název předplatného** a **správce služeb** a potom zaškrtněte políčko.

   Název předplatného se zobrazí na sestavách. Jedná se o název projektu, který je k předplatnému přidružený na vývojovém portálu.

Může trvat až 24 hodin, než se nové předplatné objeví v seznamu předplatných. Po vytvoření předplatného můžete:

- [Upravit podrobnosti předplatného](https://account.azure.com/Subscriptions)
- [Spravovat služby v předplatném](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Přenos předplatného Enterprise na předplatné s průběžnými platbami

Pokud chcete převést předplatné Enterprise na jednotlivé předplatné s průběžnými platbami, musíte na portálu Azure Enterprise vytvořit novou žádost o podporu. Žádost o podporu vytvoříte výběrem možnosti **+ New support request** (+ Nová žádost o podporu) v oblasti **Help and Support** (Nápověda a podpora).

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Přidružení stávajícího účtu k předplatnému s průběžnými platbami

Pokud už máte účet Microsoft Azure na webu Azure Portal, který chcete přidružit k vaší registraci smlouvy Enterprise, zadejte přidružený účet Microsoft nebo pracovní nebo školní účet.

### <a name="associate-an-existing-account"></a>Přidružení stávajícího účtu

1. Na portálu Azure Enterprise vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet).
1. Vyberte **+Add an account** (+Přidat účet).
1. Zadejte účet Microsoft nebo pracovní nebo školní účet přidružený ke stávajícímu účtu Azure.
1. Potvrďte účet přidružený ke stávajícímu účtu Azure.
1. Zadejte název, který chcete použít k identifikaci tohoto účtu v sestavách.
1. Vyberte **Přidat**.
1. Pokud chcete přidat další účet, můžete znovu vybrat **+Add an Account** (+Přidat účet) nebo se vrátit na domovskou stránku výběrem tlačítka **Admin** (Správce).
1. Pokud si zobrazíte stránku **Account** (Účet), nově přidaný účet se zobrazí ve stavu **Pending** (Čeká na vyřízení).

### <a name="confirm-account-ownership"></a>Potvrzení vlastnictví účtu

1. Přihlaste se k e-mailovému účtu přidruženému k pracovnímu nebo školnímu účtu nebo účtu Microsoft, který jste zadali.
1. Otevřete e-mailové oznámení s názvem _Pozvánka k aktivaci vašeho účtu ve službě Microsoft Azure z multilicenčního programu Microsoftu_.
1. Vyberte odkaz **Log into the Microsoft Azure Enterprise Portal** (Přihlásit se k portálu Microsoft Azure Enterprise Portal) uvedený v pozvánce.
1. Vyberte **Sign in** (Přihlásit se).
1. Přihlaste se zadáním svého pracovního nebo školního účtu nebo účtu Microsoft a hesla a potvrďte vlastnictví účtu.

### <a name="azure-marketplace"></a>Azure Marketplace

Přestože většinu předplatných je možné převést z prostředí s průběžnými platbami na smlouvu Azure Enterprise, u služeb z Azure Marketplace to možné není. Pokud chcete získat jedno zobrazení všech předplatných a poplatků, doporučujeme přidat služby z Azure Marketplace na portál Azure Enterprise:

1. Přihlaste se k portálu Azure Enterprise.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Enrollment** (Registrace).
1. Prohlédněte si část **Enrollment Detail** (Podrobnosti o registraci).
1. Napravo od pole Azure Marketplace výběrem ikony tužky povolte zobrazení. Vyberte **Uložit**.

Vlastník účtu si teď může zakoupit služby Azure Marketplace, která dříve vlastnil v předplatném s průběžnými platbami.

Po aktivaci nových předplatných Azure Marketplace ve vaší registraci Azure EA zrušte služby Azure Marketplace vytvořené v prostředí s průběžnými platbami. Tento krok je zásadní proto, aby vaše předplatná Azure Marketplace po vypršení platnosti vašeho platebního nástroje pro průběžné platby nepřešla do špatného stavu.

### <a name="msdn"></a>MSDN

Předplatná MSDN se automaticky převedou na předplatná MSDN pro vývoj/testování a nabídka Azure EA ztratí veškerý stávající peněžní kredit.

### <a name="azure-in-open"></a>Azure v rámci licenčního programu Open

Přidružením předplatného Azure v rámci licenčního programu Open License ke smlouvě Enterprise zaniknou veškeré nespotřebované kredity Azure v rámci licenčního programu Open License. Z tohoto důvodu doporučujeme, abyste před přidáním účtu do smlouvy Enterprise vyčerpali veškerý kredit předplatného Azure v rámci licenčního programu Open License.  

### <a name="accounts-with-support-subscriptions"></a>Účty s předplatnými podpory

Pokud vaše smlouva Enterprise nemá předplatné podpory a přidáte stávající účet s předplatným podpory na portál Azure Enterprise, vaše předplatné podpory MOSA se automaticky nepřenese. Budete si muset znovu koupit předplatné podpory v Azure EA během poskytnuté lhůty – do konce následujícího měsíce.

## <a name="view-usage-summary-and-download-reports"></a>Zobrazení souhrnu využití a stahování sestav

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebované zálohy na Azure a poplatky za další využití. Poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Pokud se chcete podívat na podrobné využití u konkrétních účtů, stáhněte si sestavu s podrobnostmi o využití:

1. Přihlaste se k portálu Azure Enterprise.
1. Vyberte **Sestavy**.
1. Vyberte kartu **Download Usage** (Stáhnout data o využití).
1. V seznamu sestav vyberte **Download** (Stáhnout) u měsíční sestavy, kterou chcete získat.

   > [!NOTE]
   > Sestava s podrobnostmi využití nezahrnuje příslušné daně.
   >
   > Mezi časem skutečného využití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až 8 hodin.

Zobrazení sestav a grafů se souhrnem využití:

1. Přihlaste se k portálu Azure Enterprise Portal.

1. Vyberte období zálohy.

   Pokud chcete změnit rozsah data pro **Usage Summary** (Souhrn využití), můžete vpravo nahoře na stránce přepnout z **M** (Monthly) (Měsíčně) na **C** (Custom) (Vlastní) a zadat vlastní počáteční a koncové datum.

   ![Vytvoření a zobrazení souhrnu využití a stahování sestav ve vlastním zobrazení](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Pokud se chcete podívat na další podrobnosti, můžete vybrat období nebo měsíc v grafu.

   - V grafu se zobrazuje meziměsíční využití s rozpisem spotřebovaného využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků z Azure Marketplace.
   - Pro vybraný měsíc můžete pomocí polí pod grafem filtrovat podle oddělení, účtů a předplatných.
   - Můžete přepínat mezi volbami **Charge by Services** (Vyúčtování podle služeb) a **Charge by Hierarchy** (Vyúčtování podle hierarchie).
   - Rozbalením a sbalením příslušných oddílů **Azure Service** (Služba Azure), **Charges Billed Separately** (Samostatně fakturované poplatky) a **Azure Marketplace** si můžete zobrazit podrobnosti.

Podívejte se na toto video, ze kterého se dozvíte, jak zobrazit informace o využití:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Stahování sestav CSV

Podnikoví správci si mohou ze stránky s měsíčními sestavami stáhnout následující sestavy ve formátu CSV:

- Zůstatek a poplatky
- Podrobnosti o využití
- Poplatky za Azure Marketplace
- Ceník

Jak sestavy stáhnout:

1. Na portálu Azure Enterprise vyberte **Reports** (Sestavy).
2. V horní části stránky vyberte **Download Usage** (Stáhnout data o využití).
3. Vyberte tlačítko **Download** (Stáhnout) vedle sestavy pro požadovaný měsíc.

   > [!NOTE]
   > Mezi datem skutečného využití a jeho zobrazením v sestavě může docházet ke zpoždění až 5 dní.
   >
   > U uživatelů, kteří si soubory CSV stahují přes Safari do Excelu, může docházet k chybám formátování. Chybám se vyhnete tak, že soubor otevřete pomocí textového editoru.

![Příklad ukazující stránku pro stažení údajů o využití](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Podívejte se na toto video, které ukazuje, jak si stáhnout informace o využití:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Stažení rozšířené sestavy

Pomocí stažení rozšířené sestavy můžete získat sestavy, které pokrývají konkrétní rozsahy kalendářních dat nebo účty. Výstupní soubor je ve formátu CSV, aby pojal rozsáhlé sady záznamů.

1. Na portálu Azure Enterprise vyberte **Advanced Report Download** (Stažení rozšířené sestavy).
1. Vyberte příslušný rozsah data a příslušné účty.
1. Vyberte **Požádat o data o využití**.
1. Opakovaně vyberte tlačítko **Refresh** (Aktualizovat), dokud se stav sestavy neaktualizuje na **Download** (Stáhnout).
1. Stáhněte sestavu.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Stažení sestav využití a fakturačních údajů pro předchozí registraci

Sestavy využití a fakturační údaje pro předchozí registraci si můžete stáhnout až po přenosu registrace. Portál Azure Enterprise i správa nákladů umožňuje generování historických sestav.

Portál Azure Enterprise vyfiltruje neaktivní registrace, aby nebyly vidět. Aby se zobrazily neaktivní přenesené registrace, budete muset zrušit zaškrtnutí políčka **Active** (Aktivní).  

![Zrušení zaškrtnutí políčka Active (Aktivní) umožní uživateli zobrazit neaktivní registrace](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Glosář pojmů ve smlouvě Azure EA

- **Účet:** Organizační jednotka na portálu Azure Enterprise. Slouží ke správě předplatných a ke generování sestav.
- **Vlastník účtu:** Osoba, která spravuje předplatná a správce služeb v Azure. Může zobrazit data o využití tohoto účtu a přidružených předplatných.
- **Doplňující předplatné:** Roční nebo překrývající se předplatné na základě dodatku k registraci.
- **Záloha:** Záloha určité roční peněžní částky za služby Azure se zvýhodněnou sazbou za využití při předplacení.
- **Správce oddělení:** Osoba, která spravuje oddělení, vytváří nové účty a vlastníky účtů, zobrazuje podrobnosti o využití pro oddělení, která spravuje, a může zobrazit náklady (když k tomu má udělená oprávnění).
- **Číslo registrace:** Jedinečný identifikátor od Microsoftu, který umožňuje identifikovat konkrétní registraci přidruženou ke smlouvě Enterprise.
- **Podnikový správce:** Osoba, která spravuje oddělení, vlastníky oddělení, účty a vlastníky účtů v Azure. Můžou spravovat podnikové správce a zobrazit data o využití, fakturovaná množství a nefakturované poplatky napříč všemi účty a předplatnými přidruženými k dané podnikové registraci.
- **Smlouva Enterprise:** Licenční smlouva se společností Microsoft pro zákazníky využívající centrální nakupování, kteří chtějí v rámci celé organizace standardizovat používání technologií Microsoftu a udržovat v infrastruktuře informačních technologií standardy softwaru Microsoftu.
- **Registrace smlouvy Enterprise:** Registrace v programu Smlouva Enterprise, který umožňuje hromadně nakupovat produkty Microsoftu se zvýhodněnými sazbami.
- **Účet Microsoft:** Webová služba umožňující ověřování uživatelů na zapojených webech pomocí jedné sady přihlašovacích údajů.
- **Dodatek k registraci Microsoft Azure Enterprise (dodatek k registraci):** Dodatek podepsaný podnikem, který podniku v rámci podnikové registrace umožňuje přístup k Azure.
- **Portál Azure Enterprise:** Portál, pomocí kterého naši podnikoví zákazníci spravují své účty Azure a související předplatná.
- **Spotřebované množství prostředků:** Množství jednotlivé služby Azure využité za měsíc.
- **Správce služeb:** Osoba, která může přistupovat k předplatným a vývojovým projektům a spravovat je na portálu Azure Enterprise.
- **Předplatné:** Představuje předplatné portálu Azure Enterprise a jedná se o kontejner služeb Azure spravovaných stejným správcem služeb.
- **Pracovní nebo školní účet:** Pro organizace, které si nastavily službu Active Directory s federací na cloud a všechny účty jsou v jednom tenantovi.

### <a name="enrollment-statuses"></a>Stavy registrace

- **Nová:** Tento stav je vytvořené rezervaci přiřazený po dobu 24 hodin a během dalších 24 hodin se aktualizuje na Čeká na vyřízení.
- **Čeká na vyřízení:** Správce registrace se musí přihlásit k portálu Azure Enterprise. Po přihlášení se stav registrace přepne na Aktivní.
- **Aktivní:** Registrace je aktivní a na portálu Azure Enterprise je možné vytvářet účty a předplatná. Registrace zůstane aktivní až do koncového data smlouvy Enterprise.
- **Neurčené období prodloužení:** Stav Neurčené období prodloužení nastane po uplynutí koncového data smlouvy Enterprise. Umožňuje zákazníkům Azure EA, kteří jsou výslovně přihlášeni k období prodloužení, aby po skončení platnosti jejich smlouvy Enterprise pokračovali v používání služeb Azure po neomezenou dobu.

   Než registrace Azure EA dosáhne koncového data smlouvy Enterprise, měl by se správce registrace rozhodnout pro jednu z těchto variant:

  - Prodloužení registrace přidáním další zálohy na Azure
  - Přenos na novou registraci
  - Migrace do programu Microsoft Online Subscription (MOSP)
  - Potvrzení deaktivace všech služeb přidružených k registraci
- **Platnost vypršela:** Zákazník Azure EA je výslovně vyřazen z období prodloužení a registrace Azure EA dosáhla data ukončení smlouvy Enterprise. Platnost registrace vyprší a všechny přidružené služby se deaktivují.
- **Přesunuto:** Stav Přesunuto se zobrazí u registrací, jejichž přidružené účty a služby se převedly do nové registrace.
  >[!NOTE]
  > Registrace se nepřesouvají automaticky, pokud se při prodloužení vygeneruje nové číslo registrace. Aby se usnadnil automatický převod, musíte v dokumentech k prodloužení uvést číslo předchozí registrace.

## <a name="get-started-on-azure-ea---faq"></a>Začínáme s Azure EA – Nejčastější dotazy

Tato část obsahuje podrobnosti o typických dotazech zákazníků během procesu onboardingu.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Mohu k registraci Azure EA přidružit stávající účet Azure?

Ano. Všechna předplatná Azure, pro která jste vlastníkem účtu, se převedou na vaši smlouvu Enterprise. Zahrnuta jsou předplatná, která využívají měsíční kredit, například Visual Studio, AzurePass, MPN, BizSpark a další. Při převodu těchto předplatných přijdete o měsíční kredit.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Omylem jsem přidružil(a) svůj stávající účet Azure k registraci Azure EA. Následkem toho se mi ztratil měsíční kredit. Mohu měsíční kredit získat zpět?

Pokud jste se přihlásili jako vlastník účtu Azure EA pomocí stejných přihlašovacích údajů, které používáte pro Visual Studio Subscription, můžete obnovit svoje výhody Azure služby Visual Studio Subscription provedením jedné z následujících akcí:

- Po odebrání nebo přesunutí všech přidružených předplatných Azure odstraňte roli vlastníka účtu z portálu Azure Enterprise. Potom si znovu zaregistrujte jednotlivé výhody Azure pro Visual Studio.
- Odstraňte předplatitele sady Visual Studio z lokality správy na webu VLSC a znovu přiřaďte předplatné k účtu, ale tentokrát s jinými přihlašovacími údaji. Potom si znovu zaregistrujte jednotlivé výhody Azure pro Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Jaký typ předplatného mám vytvořit?

Portál Azure Enterprise nabízí podnikovým zákazníkům dva typy předplatného:

- Microsoft Azure Enterprise, které je ideální pro:
  - Veškeré produkční využití
  - Nejlepší ceny v závislosti na útratě za infrastrukturu

  Pokud potřebujete další informace, [kontaktujte obchodní oddělení Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise pro vývoj/testování, které je ideální pro:
  - Všechny týmové úlohy vývoje/testování
  - Úlohy vývoje/testování se středním až vysokým zatížením
  - Přístup ke speciálním imagím MSDN a preferenčním sazbám za služby

  Další informace najdete v tématu, které se věnuje [nabídce Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Je možné převést vlastnictví předplatného na jiný účet?

Ano, vlastnictví předplatného můžete převést na jiný účet. Například pokud účet A obsahuje tři předplatná, podnikový správce může převést jedno předplatné do účtu B, jedno do účtu C a jedno do účtu D, nebo všechna do účtu E.

Přenos předplatných:

1. Na portálu Azure Enterprise vyberte **Manage** (Spravovat) > **Account** (Účet).
1. Myší najeďte na **Account** (Účet) úplně napravo, aby se zobrazily možnosti **Transfer Ownership** (Převést vlastnictví) (ikona osoby) a **Transfer Subscription** (Převést předplatné) (ikona seznamu). Tyto možnosti jsou viditelné pouze u aktivních účtů.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Název mého předplatného je stejný jako název nabídky. Mám název předplatného změnit na nějaký smysluplný název pro naši organizaci?

Při vytváření předplatného se jako výchozí název používá typ nabídky, kterou zvolíte. Doporučujeme změnit název předplatného na název, který vám usnadní sledování předplatného.

Změna názvu:

1. Přihlaste se k webu [https://account.windowsazure.com](https://account.windowsazure.com).
1. Vyberte seznam předplatných.
1. Vyberte předplatné, které chcete upravit.
1. Vyberte ikonu **Manage Subscription** (Spravovat předplatné).
1. Upravte podrobnosti o předplatném.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Jak můžu sledovat vzniklé náklady podle nákladového střediska?

Pokud chcete sledovat náklady podle nákladového střediska, musíte definovat nákladové středisko na jedné z následujících úrovní:

- Oddělení
- Účet
- Předplatné

Podle potřeby můžete stejné nákladové středisko použít ke sledování využití a nákladů souvisejících s konkrétním nákladovým střediskem.

Například pokud chcete sledovat náklady na speciální projekt, na kterém se podílí více oddělení, můžete ke sledování využití a nákladů definovat nákladové středisko na úrovni předplatného.

Nákladové středisko není možné definovat na úrovni služby. V případě, že chcete sledovat využití na úrovni služby, můžete využít funkci _značek_, která je dostupná na úrovni služby.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Jak můžu sledovat využití a útratu v různých odděleních organizace?

V rámci registrace Azure EA můžete vytvořit libovolný počet oddělení. Abyste mohli správně sledovat využití, zajistěte, aby se předplatná mezi odděleními nesdílela.

Po vytvoření oddělení a předplatných uvidíte v sestavě využití data. Tyto informace vám pomohou sledovat využití a spravovat náklady a útratu na úrovni oddělení.

K datům využití se můžete dostat také v rozhraní API pro generování sestav. Podrobné informace a ukázkový kód najdete v tématu [Rozhraní Azure Enterprise REST API](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Mohu nastavit kvótu útraty a dostávat upozornění, když se budu blížit limitu?

Můžete nastavit kvótu útraty na úrovni oddělení a systém vás automaticky upozorní, když vaše limity útraty dosáhnou 50 %, 75 %, 90 % a 100 % definované kvóty.

Pokud chcete definovat kvótu útraty, vyberte oddělení a potom ikonu úprav. Po úpravě podrobností limitu útraty vyberte **Save** (Uložit).

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>K implementaci řízení přístupu Azure RBAC a sledování využití jsem použil(a) skupiny prostředků. Jak mohu zobrazit související podrobnosti o využití?

Informace o používání _skupin prostředků_ a _značek_ se sledují na úrovni služby a jsou k dispozici v souboru s podrobnými informacemi o využití (CSV), který si můžete stáhnout. Na portálu Azure Enterprise přejděte na [stažení sestavy využití](https://ea.azure.com/report/downloadusage).

K informacím o využití se dostanete také přes rozhraní API. Podrobné informace a ukázkový kód najdete v tématu [Rozhraní Azure Enterprise REST API](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

> [!NOTE]
> Značky můžete používat pouze u prostředků, které podporují operace Azure Resource Manageru. Pokud jste vytvořili virtuální počítač, virtuální síť nebo úložiště prostřednictvím modelu nasazení Classic (například přes portál Classic), nemůžete pro tyto prostředky použít značky. Pokud chcete zajistit podporu označování, musíte tyto prostředky nasadit znovu prostřednictvím Resource Manageru. Všechny ostatní prostředky označování podporují.

### <a name="can-i-perform-analyses-using-power-bi"></a>Můžu provádět analýzy s využitím Power BI?

Ano. Balíček obsahu Microsoft Azure Enterprise pro Power BI umožňuje:

- rychle importovat a analyzovat využití Azure u podnikové registrace,
- zjistit, které oddělení, účet nebo předplatné se na využití podílelo nejvíce,
- zjistit, kterou službu vaše organizace nejvíce používala,
- sledovat útratu a trendy používání.

Použití Power BI:

1. Přejděte na web Power BI.
1. Přihlaste se pomocí platného pracovního nebo školního účtu.

   Může se jednat o stejný pracovní nebo školní účet, který používáte pro přístup k registraci prostřednictvím portálu Azure Enterprise, nebo jiný pracovní nebo školní účet.
1. Na řídicím panelu služeb vyberte dlaždici Microsoft Azure Enterprise a vyberte **Připojit**.
1. Na obrazovce **Připojit k Azure Enterprise** zadejte:
    - Adresu URL prostředí Azure: [https://ea.azure.com](https://ea.azure.com)
    - Počet měsíců: hodnota v rozmezí 1 až 36
    - Číslo registrace: číslo vaší registrace
1. Vyberte **Další**.
1. Do pole **Klíč účtu** zadejte klíč rozhraní API.

   Klíč rozhraní API najdete na portálu Azure Enterprise. Pod kartou **Download Usage** (Stáhnout využití) vyberte **API Access Key** (Přístupový klíč rozhraní API). Zkopírujte ho a vložte do pole **Klíč účtu** v Power BI.

V závislosti na velikosti datové sady může načtení dat do Power BI trvat 5 až 30 minut.

Generování sestav Power BI je k dispozici pro přímé a nepřímé zákazníky a partnery Azure EA, kteří můžou zobrazit fakturační údaje.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure Enterprise by si měli přečíst téma [Správa portálu Azure Enterprise](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure Enterprise, přečtěte si téma [Řešení potíží s přístupem k portálu Azure Enterprise](ea-portal-troubleshoot.md).
- Tady najdete [průvodce onboardingem účtu Azure EA (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
