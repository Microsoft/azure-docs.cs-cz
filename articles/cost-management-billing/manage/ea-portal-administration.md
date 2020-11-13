---
title: Správa portálu Azure EA
description: V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 10/27/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: e83af5baa4ca38a8e81dffa8bb81ab3da64e1e95
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411018"
---
# <a name="azure-ea-portal-administration"></a>Správa portálu Azure EA

V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA Portal (https://ea.azure.com). Portál Azure EA je online portál pro správu, kde můžou zákazníci spravovat náklady na služby Azure EA. Úvodní informace o portálu Azure EA najdete v článku [Začínáme s portálem Azure EA](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Aktivace registrace

Aktivace služeb probíhá tak, že počáteční podnikový správce otevře [portál Azure Enterprise](https://ea.azure.com) a přihlásí se pomocí e-mailové adresy z e-mailu s pozvánkou.

Pokud vás někdo nastavil jako podnikového správce, nemusíte čekat na aktivační e-mail. Přejděte na [portál Azure Enterprise](https://ea.azure.com) a přihlaste se pomocí své pracovní nebo školní e-mailové adresy nebo e-mailové adresy účtu Microsoft a hesla.

Pokud máte více než jednu registraci, vyberte tu, kterou chcete aktivovat. Ve výchozím nastavení se zobrazují jenom aktivní registrace. Historii registrací zobrazíte tak, že v pravém horním rohu portálu Azure Enterprise vymažete možnost **Active** (Aktivní).

Pod **registrací** se zobrazuje stav **Active** (Aktivní).

![Příklad znázorňující aktivní registraci](./media/ea-portal-administration/ea-enrollment-status.png)

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

![Příklad znázorňující dialogové okno pro přidání správce oddělení](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Jak nastavit přístup jen pro čtení

Správcům oddělení je možné udělit přístup jen pro čtení.

- Při vytváření nového správce oddělení nastavte možnost jen pro čtení na hodnotu **Yes** (Ano).

- Jak upravit existujícího správce oddělení:
   1. Vyberte oddělení a potom vyberte symbol tužky vedle **správce oddělení** , kterého chcete upravit.
   1. Nastavte otevírání jen pro čtení na **Yes** (Ano) a potom vyberte **Save** (Uložit).

Podnikoví správci získávají automaticky oprávnění správce oddělení.

## <a name="add-an-account"></a>Přidání účtu

Struktura účtů a předplatných ovlivňuje způsob jejich správy a jejich zobrazení na fakturách a v sestavách. Typické organizační struktury zahrnují například obchodní divize, funkční týmy a geografické oblasti.

Jak přidat účet:

1. V levé navigační oblasti na portálu Azure Enterprise vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet). Na stránce **Account** (Účet) vyberte **+Add Account** (+Přidat účet).
1. Vyberte oddělení nebo nechte účet nepřiřazený a pak vyberte požadovaný typ ověřování.
1. Zadejte popisný název, který vám usnadní identifikaci účtu ve vytvořených sestavách.
1. Zadejte **e-mailovou adresu vlastníka účtu** , kterou chcete přidružit k novému účtu.
1. Ověřte e-mailovou adresu a pak vyberte **Add** (Přidat).

![Příklad znázorňující seznam účtů a možnost pro přidání účtu](./media/ea-portal-administration/create-ea-add-an-account.png)

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

Seznamte se s těmito zásadami řízení přístupu na základě role (RBAC):

- Když provádíte přenosy předplatných mezi dvěma ID organizace ve stejném tenantovi, zachovají se zásady RBAC a přiřazené role správců služeb a spolusprávců.
- Jiné přenosy předplatných povedou ke ztrátě nastavených zásad RBAC a přiřazení rolí.
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

![Obrázek znázorňující symbol pro změnu vlastníka účtu](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Jak přenést vlastnictví účtu pro jedno předplatné:

1. Přihlaste se k portálu Azure Enterprise.
1. V levé navigační oblasti vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet) a najeďte myší na požadovaný účet.
1. Na pravé straně vyberte ikonu pro přenos předplatných. Ikona vypadá jako stránka.
1. Zvolte předplatné splňující podmínky a potom vyberte **Next** (Další).
1. Potvrďte přenos a pak vyberte **Submit** (Odeslat).

![Obrázek znázorňující symbol pro přenos předplatných](./media/ea-portal-administration/ea-transfer-subscriptions.png)

Podívejte se na toto video o správě uživatelů na portálu Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="associate-an-account-to-a-department"></a>Přidružit účet k oddělení

Podnikoví správci můžou přidružovat existující účty k oddělením v rámci registrace.

### <a name="to-associate-an-account-to-a-department"></a>Přidružení účtu k oddělení

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte **Oddělení**.
1. Najeďte myší na řádek s požadovaným účtem a vyberte ikonu tužky na pravé straně.
1. V rozevírací nabídce vyberte požadované oddělení.
1. Vyberte **Uložit**.

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

## <a name="department-spending-quotas"></a>Kvóty útraty oddělení

Zákazníci se smlouvou Enterprise můžou nastavit nebo změnit kvóty útraty pro každé oddělení v rámci registrace. Výše kvóty útraty se nastavuje pro aktuální období zálohy. Pokud nedojde k aktualizaci hodnot, na konci aktuálního období zálohy systém prodlouží stávající kvótu útraty na další období zálohy.

Správce oddělení může zobrazit kvótu útraty, ale pouze podnikový správce může aktualizovat její výši. Podnikový správce a správce oddělení obdrží oznámení, když dojde k dosažení 50 %, 75 %, 90 % a 100 % kvóty.

### <a name="enterprise-administrator-to-set-the-quota"></a>Nastavení kvóty podnikovým správcem:

 1. Otevřete portál Azure EA.
 1. V levém navigačním panelu vyberte **Manage** (Spravovat).
 1. Vyberte kartu **Oddělení**.
 1. Vyberte požadované oddělení.
 1. Vyberte symbol tužky v části Podrobnosti o oddělení nebo vyberte symbol **+ Přidat oddělení** a přidejte nové oddělení i s kvótou útraty.
 1. V části Podrobnosti o oddělení zadejte do pole Kvóta útraty (USD) výši kvóty útraty v měně registrace (hodnota musí být vyšší než 0).
    - V tuto chvíli můžete upravit také Název oddělení a Nákladové středisko.
 1. Vyberte **Uložit**.

Kvóta útraty oddělení se teď zobrazí na kartě Oddělení v zobrazení Seznam oddělení. Na konci aktuálního období zálohy se na portálu Azure EA zachovají kvóty útraty pro další období zálohy.

Výše kvóty oddělení je nezávislá na aktuální záloze na Azure a výše kvóty a související upozornění se vztahují pouze na využití první stranou. Kvóta útraty oddělení slouží pouze k informačním účelům a nevynucuje limity útraty.

### <a name="department-administrator-to-view-the-quota"></a>Zobrazení kvóty správcem oddělení:

1. Otevřete portál Azure EA.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Výběrem karty **Oddělení** zobrazte zobrazení Seznam oddělení s kvótami útraty.

Pokud jste nepřímý zákazník, váš distribuční partner musí povolit funkce nákladů.

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Na portálu Azure EA můžete spravovat náklady a použití Azure EA. Na portálu Azure EA jsou tři hlavní role:

- Správce smlouvy Enterprise
- Správce oddělení
- Vlastník účtu

Každá role má jinou úroveň přístupu a jiná oprávnění.

Další informace o rolích uživatelů najdete v tématu [Role podnikových uživatelů](https://docs.microsoft.com/azure/manage/understand-ea-roles#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Přidání účtu Azure EA

Účet Azure EA je organizační jednotka portálu Azure EA Portal. Slouží ke správě předplatných a také ke generování sestav. Pokud chcete získat přístup ke službám Azure, potřebujete vytvořit účet nebo vám ho někdo musí vytvořit.

Další informace o účtech Azure najdete v tématu [Přidání účtu](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-administration#add-an-account).

## <a name="enterprise-devtest-offer"></a>Nabídka Enterprise pro vývoj/testování

Jako podnikový správce Azure můžete vlastníkům účtů ve vaší organizaci umožnit vytvářet předplatná na základě nabídky EA pro vývoj/testování. Můžete to provést zaškrtnutím políčka Vývoj/testování u příslušného vlastníka účtu na portálu Azure EA Portal.

Po zaškrtnutí políčka Vývoj/testování informujte daného vlastníka účtu, aby mohl nastavit potřebná předplatná EA pro vývoj/testování pro týmy předplatitelů nabídky Vývoj/testování.

Tato nabídka umožňuje aktivním předplatitelům sady Visual Studio spouštět v Azure vývojové a testovací úlohy za speciální sazby pro vývoj/testování. Poskytuje přístup k celé galerii imagí pro vývoj/testování, včetně imagí Windows 8.1 a Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Nastavení nabídky Enterprise pro vývoj/testování:

1. Přihlaste se jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet).
1. Vyberte řádek účtu, kterému chcete povolit přístup k nabídce Vývoj/testování.
1. Vyberte symbol tužky napravo od řádku.
1. Zaškrtněte políčko Vývoj/testování.
1. Vyberte **Uložit**.

Když se uživatel přidá jako vlastník účtu přes portál Azure EA, všechna předplatná Azure na základě nabídky Vývoj/testování s průběžnými platbami nebo nabídek měsíčního kreditu pro předplatitele sady Visual Studio přidružená k danému vlastníkovi účtu se převedou na nabídku EA pro vývoj/testování. Předplatná na základě jiných typů nabídek, jako jsou nabídky průběžných plateb, přidružená k danému vlastníkovi účtu se převedou na nabídky Microsoft Azure Enterprise.

Nabídka Vývoj/testování se v současné době nevztahuje na zákazníky Azure Government.

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

## <a name="delete-subscription"></a>Odstranění předplatného

Odstranění předplatného, ve kterém jste vlastníkem účtu:

1. Přihlaste se k webu Azure Portal pomocí přihlašovacích údajů přidružených k vašemu účtu.
1. V nabídce centra vyberte **Předplatná**.
1. Na kartě Předplatná v levém horním rohu stránky vyberte předplatné, které chcete zrušit, a výběrem možnosti **Zrušit předplatné** otevřete kartu Zrušení.
1. Zadejte název předplatného, zvolte důvod pro zrušení a vyberte tlačítko **Zrušit předplatné**.

Předplatná můžou rušit pouze správci účtů.

Další informace najdete v části [Co se stane po zrušení předplatného?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Odstranění účtu

Odebrat je možné pouze aktivní účty bez aktivních předplatných.

1. V levém navigačním panelu portálu Enterprise Portal vyberte **Spravovat**.
1. Vyberte kartu **Account** (Účet).
1. V tabulce Účty vyberte účet, který chcete odstranit.
1. Vyberte symbol X na pravé straně řádku účtu.
1. Jakmile účet nebude obsahovat žádná aktivní předplatná, výběrem možnosti **Ano** pod řádkem účtu potvrďte odebrání účtu.

## <a name="update-notification-settings"></a>Aktualizace nastavení oznámení

Registrace podnikových správců probíhá automaticky, aby mohli dostávat oznámení o použití, která se týkají jejich registrace. Každý podnikový správce může změnit interval jednotlivých oznámení nebo může tato oznámení úplně vypnout.

Kontakty pro oznámení se na portálu Azure EA zobrazují v oddílu **Kontakt pro oznámení**. Správou kontaktů pro oznámení zajistíte doručování oznámení Azure EA v organizaci těm správným lidem.

Zobrazení aktuálního nastavení oznámení:

1. Na portálu Azure EA přejděte na **Správa** > **Kontakt pro oznámení**.
2. E-mailová adresa – e-mailová adresa přidružená k účtu Microsoft podnikového správce nebo k pracovnímu či školnímu účtu, na který chodí oznámení.
3. Frekvence oznamování nefakturovaného zůstatku – nastavená frekvence posílání oznámení každému podnikovému správci.

Přidání kontaktu:

1. Vyberte **+ Přidat kontakt**.
2. Zadejte e-mailovou adresu a potvrďte ji.
3. Vyberte **Uložit**.

Nový kontakt pro oznámení se zobrazí v oddílu **Kontakt pro oznámení**. Pokud chcete změnit frekvenci oznamování, vyberte kontakt pro oznámení a pak vyberte symbol tužky, který je napravo od vybraného řádku. Nastavte **denní** , **týdenní** , **měsíční** nebo **žádnou** frekvenci.

Můžete také potlačit _blížící se koncové datum pokrytého období_ a _vypnout a zrušit zřízené blížící se datum_ u oznámení o životním cyklu. Pokud vypnete oznámení o životním cyklu, potlačíte tím oznámení o pokrytém období a o datu ukončení smlouvy.

## <a name="azure-sponsorship-offer"></a>Nabídka Azure Sponsorship

Nabídka Azure Sponsorship se týká omezeného sponzorovaného účtu Microsoft Azure. Nabídka je k dispozici jenom na základě e-mailové pozvánky určené omezenému počtu zákazníků, které vybral Microsoft. Pokud máte na nabídku Microsoft Azure Sponsorship nárok, přijde vám e-mailem pozvánka určená pro vaše ID účtu.

Další informace získáte vytvořením [žádosti o podporu při aktivaci nabídky Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Převod na ověřování pomocí pracovního nebo školního účtu

Podnikoví uživatelé Azure můžou převést typ ověřování z ověřování pomocí účtu Microsoft (MSA nebo Live ID) na ověřování pomocí pracovního nebo školního účtu (s využitím Azure Active Directory).

Začněte následovně:

1. Na portálu Azure EA přidejte pracovní nebo školní účet do požadovaných rolí.
1. Pokud dojde k chybám, účet možná není v Active Directory platný.  Azure používá hlavní název uživatele (UPN), který není vždy stejný jako e-mailová adresa.
1. Ověřte se na portálu Azure EA pomocí pracovního nebo školního účtu.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Převod předplatných z účtů Microsoft na pracovní nebo školní účty:

1. Přihlaste se k portálu pro správu pomocí účtu Microsoft, který je vlastníkem předplatných.
1. Pomocí převodu vlastnictví účtu proveďte přesun na nový účet.
1. Účet Microsoft by teď neměl obsahovat žádná aktivní předplatná a můžete ho odstranit.
1. Všechny odstraněné účty se kvůli zachování historie fakturace budou na portálu dál zobrazovat v neaktivním stavu.  Zaškrtnutím políčka je můžete vyfiltrovat a zobrazit pouze aktivní účty.

## <a name="account-subscription-ownership-faq"></a>Nejčastější dotazy k vlastnictví předplatného účtu

V tomto dokumentu najdete odpovědi na nejčastější dotazy související s vlastnictvím předplatného účtu.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Mohu k registraci Azure EA přidružit stávající účet Azure?

Ano. Všechna předplatná Azure, pro která jste vlastníkem účtu, se převedou na vaši smlouvu Enterprise. Zahrnuta jsou předplatná, která využívají měsíční kredit, například Visual Studio, AzurePass, MPN, BizSpark a další. Při převodu těchto předplatných přijdete o měsíční kredit.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Kolik vlastníků účtů Azure je možné mít pro jedno předplatné?

V jednom předplatném je povolený pouze jeden vlastník účtu.  Další role je možné přidat pomocí řízení přístupu na základě role nebo řízení přístupu (IAM) na kartě předplatného v levém horním rohu stránky na webu[Azure Portal](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Je možné převést vlastnictví předplatného na jiný účet?

Ano, vlastnictví předplatného můžete převést na jiný účet. Například pokud účet A obsahuje tři předplatná, podnikový správce může převést jedno předplatné do účtu B, jedno do účtu C a jedno do účtu D, nebo všechna do účtu E.

Přenos předplatných:

1. Na portálu Azure Enterprise vyberte **Manage** (Spravovat) > **Account** (Účet).
1. Myší najeďte na **Account** (Účet) úplně napravo, aby se zobrazily možnosti **Transfer Ownership** (Převést vlastnictví) (ikona osoby) a **Transfer Subscription** (Převést předplatné) (ikona seznamu). Tyto možnosti jsou viditelné pouze u aktivních účtů.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Může být vlastník účtu Azure uvedený u více než jednoho oddělení?

Ne, vlastník účtu může být přidružený pouze k jednomu oddělení. Tato zásada pomáhá zajistit přesné monitorování a rozdělování útraty a nákladů přidružených k odpovídajícímu oddělení v rámci registrace EA na portálu Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Může být vlastník účtu Azure uvedený jako skupina zabezpečení?

Ne, vlastníkem předplatného musí být jedinečný účet Microsoft (MSA) nebo ověřování Azure Active Directory (Azure AD). Pokud chcete v rámci organizace zohlednit nástupnictví, můžete zvážit vytvoření obecných účtů a použití Azure AD ke správě přístupu k předplatným.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Může jeden uživatel vlastnit více předplatných?

Vlastník účtu Azure může vytvářet a spravovat neomezené množství předplatných.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Jak můžu zobrazit všechna předplatná organizace nebo k nim získat přístup?

V současné době je potřeba k tomu použít zásady. To znamená, že byste museli vyžadovat, aby se pro každé vytvořené předplatné váš účet pomocí řízení přístupu na základě role přidal do role předplatného.

### <a name="where-do-i-go-to-create-a-subscription"></a>Kde můžu vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure (EA), správce vaší registrace EA musí na portálu Azure EA Portal přidat váš účet do role vlastníka účtu. Pak se musíte přihlásit k portálu Azure EA Portal a získat oprávnění k vytváření předplatných nabídek EA. První předplatné EA doporučujeme vytvořit pomocí odkazu + Přidat předplatné na kartě předplatného na portálu EA Portal.  Jakmile však váš účet získá oprávnění, může být snadnější vytvářet předplatná na webu portal.azure.com na kartě předplatného v levém horním rohu stránky, kde můžete předplatné v jednom kroku vytvořit i přejmenovat.

### <a name="who-can-create-a-subscription"></a>Kdo může vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure, musíte na [portálu EA](https://ea.azure.com) mít oprávnění role vlastníka účtu.

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
- **Pracovní nebo školní účet:** Pro organizace, které si nastavily službu Azure Active Directory s federací na cloud a všechny účty jsou v jednom tenantovi.

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak vám můžou [rezervace virtuálních počítačů](ea-portal-vm-reservations.md) pomoci ušetřit peníze.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).
