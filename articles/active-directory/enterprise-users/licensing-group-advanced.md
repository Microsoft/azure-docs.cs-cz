---
title: Další scénáře licencování na základě skupin – Azure AD | Microsoft Docs
description: Další scénáře pro Azure Active Directory licencování na základě skupin
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a28aebe71809f3e47f9cc6936366cd027a32bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546671"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scénáře, omezení a známé problémy, které používají skupiny pro správu licencování v Azure Active Directory

Následující informace a příklady vám pomůžou získat pokročilejší princip licencování na základě skupin Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Umístění použití

Některé služby Microsoft nejsou dostupné ve všech umístěních. Předtím, než může být licence přiřazena uživateli, musí správce zadat vlastnost **umístění používání** u uživatele. V [Azure Portal](https://portal.azure.com)můžete určit umístění použití v  &gt;  &gt; **Nastavení** profilu uživatele.

Pro přiřazení licence skupiny budou mít všichni uživatelé bez zadaného umístění v adresáři k dispozici umístění adresáře. Pokud máte uživatele ve více umístěních, nezapomeňte se před přidáním uživatelů do skupin s licencemi správně projevit v svých uživatelských zdrojích.

> [!NOTE]
> Přiřazení skupinové licence nikdy nezmění existující hodnotu umístění využití na uživateli. Doporučujeme, abyste vždycky nastavili umístění využití jako součást toku vytváření uživatele ve službě Azure AD (např. prostřednictvím konfigurace služby AAD Connect). tím se zajistí, že se výsledek přiřazení licence vždycky opraví, a uživatelé neobdrží služby v umístěních, která nejsou povolená.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Použití licencování na základě skupin s dynamickými skupinami

Licencování na základě skupin můžete použít u libovolné skupiny zabezpečení, což znamená, že se dá kombinovat s dynamickými skupinami Azure AD. Dynamické skupiny spouštějí pravidla pro atributy prostředků uživatele k automatickému přidávání a odebírání uživatelů ze skupin.

Můžete například vytvořit dynamickou skupinu pro některé sady produktů, které chcete přiřadit uživatelům. Každá skupina je naplněna pravidlem, které přidávají uživatele podle jejich atributů a každá skupina jim přiřadí licence, které mají přijímat. Můžete přiřadit atribut místně a synchronizovat ho s Azure AD nebo můžete spravovat atribut přímo v cloudu.

Licence se uživateli po přidání do skupiny přiřadí krátce. Po změně atributu uživatel opustí skupiny a licence budou odebrány.

### <a name="example"></a>Příklad

Vezměte v úvahu příklad místního řešení pro správu identit, které určuje, kteří uživatelé mají mít přístup k webovým službám Microsoftu. Používá **extensionAttribute1** k uložení řetězcové hodnoty představující licence, které má uživatel mít. Azure AD Connect synchronizuje s Azure AD.

Uživatelé můžou potřebovat jednu licenci, ale ne jinou, nebo můžou potřebovat obojí. Tady je příklad, ve kterém distribuujete licence Office 365 Enterprise E5 a Enterprise Mobility + Security (EMS) uživatelům ve skupinách:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: základní služby

![Snímek obrazovky se základními službami Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencovaní uživatelé

![Snímek obrazovky Enterprise Mobility + Security licencovaných uživatelů](./media/licensing-group-advanced/o365-e5-licensed-users.png)

V tomto příkladu změňte jeden uživatel a nastavte jeho extensionAttribute1 na hodnotu, `EMS;E5_baseservices;` Pokud chcete, aby uživatel měl obě licence. Tuto úpravu můžete udělat místně. Jakmile se Změna synchronizuje s cloudem, uživatel se automaticky přidá do obou skupin a licence se jim přiřadí.

![Snímek obrazovky ukazující, jak nastavit extensionAttribute1 uživatele](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Při úpravách pravidla členství existující skupiny buďte opatrní. Když se pravidlo změní, členství ve skupině se znovu vyhodnotí a uživatelé, kteří už neodpovídají novému pravidlu, se odeberou (uživatelé, kteří se pořád shodují s novým pravidlem, nebudou během tohoto procesu ovlivněni). Tato uživatelé budou mít během procesu odebrané licence, což může vést ke ztrátě služby, nebo v některých případech ke ztrátě dat.
> 
> Pokud máte velkou dynamickou skupinu, na které závisíte při přiřazování licencí, zvažte možnost ověřit všechny významné změny v menší testovací skupině, než je použijete pro hlavní skupinu.

## <a name="multiple-groups-and-multiple-licenses"></a>Více skupin a více licencí

Uživatel může být členem více skupin s licencemi. Tady je několik věcí, které je potřeba vzít v úvahu:

- Víc licencí pro stejný produkt se může překrývat a výsledkem jsou všechny povolené služby, které se na uživatele používají. Následující příklad ukazuje dvě skupiny licencí: *E3 Base Services obsahuje základní* služby, které se nasadí jako první, pro všechny uživatele. A *E3 rozšířené služby* obsahují další služby (Sway a Planner), které se nasazují jenom pro některé uživatele. V tomto příkladu byl uživatel přidán do obou skupin:

  ![Snímek obrazovky s povolenými službami](./media/licensing-group-advanced/view-enabled-services.png)

  V důsledku toho má uživatel 7 z 12 služeb v povoleném produktu, přičemž pro tento produkt používá jenom jednu licenci.

- Výběrem licence *E3* zobrazíte další podrobnosti, včetně informací o tom, které služby jsou pro uživatele povoleny přiřazením licence skupiny.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Souběžné licence s licencemi skupin v koexistovat

Pokud uživatel zdědí licenci ze skupiny, nemůžete toto přiřazení licence ve vlastnostech uživatele přímo odebrat ani změnit. Změny musí být provedeny ve skupině a následně šířeny všem uživatelům.

Kromě zděděné licence je však možné přiřadit stejnou produktovou licenci přímo uživateli. Můžete povolit další služby z produktu pouze pro jednoho uživatele, aniž by to ovlivnilo jiné uživatele.

Přímo přiřazené licence lze odebrat a neovlivní zděděné licence. Vezměte v úvahu uživatele, který dědí licenci Office 365 Enterprise E3 ze skupiny.

Zpočátku uživatel zdědí licenci pouze ze skupiny *E3 Basic Services* , která umožňuje čtyři plány služeb.

1. Vyberte **přiřadit** a k uživateli přiřaďte licenci E3 přímo. V takovém případě budete chtít zakázat všechny plány služby s výjimkou Yammeru Enterprise.

    V důsledku toho uživatel stále používá pouze jednu licenci produktu E3. Přímé přiřazení ale povoluje pouze službu Yammer Enterprise Service pro tohoto uživatele. Můžete zjistit, které služby jsou povolené členstvím ve skupině, a to s přímým přiřazením.

1. Při použití přímého přiřazení jsou povoleny následující operace:

   - Yammer Enterprise je možné zapnout přímo na prostředku uživatele. Přepínač **zapnuto/vypnuto** na ilustraci byl povolen pro tuto službu, a to na rozdíl od ostatních přepínačů služby. Vzhledem k tomu, že je služba povolena přímo uživateli, lze ji změnit.
   - V rámci přímo přiřazené licence je možné povolit i další služby.
   - Tlačítko **Odebrat** lze použít k odebrání přímé licence uživatele. Vidíte, že uživatel teď má jenom zděděnou skupinu licencí a že jsou povolené jenom původní služby:

## <a name="managing-new-services-added-to-products"></a>Správa nových služeb přidaných do produktů

Když Microsoft přidá novou službu do plánu licence na produkt, bude ve výchozím nastavení povolená ve všech skupinách, ke kterým jste přiřadili licenci na produkt. Uživatelům ve vaší organizaci, kteří se přihlásí k odběru oznámení o změnách produktu, obdrží e-maily předem upozornění na nadcházející doplňky služby.

Jako správce můžete zkontrolovat všechny skupiny ovlivněné změnou a provedením akce, jako je například zakázání nové služby v každé skupině. Pokud jste například vytvořili skupiny zaměřené jenom na konkrétní služby pro nasazení, můžete tyto skupiny znovu navštívit a zajistit, aby byly všechny nově přidané služby zakázané.

Tady je příklad toho, co může tento proces vypadat takto:

1. Původně jste přiřadili produkt *Office 365 Enterprise E5* do několika skupin. Jedna z těchto skupin, označovaná jako *O365 E5-Exchange* , byla navržena tak, aby pro své členy povolovala pouze službu *Exchange Online (plán 2)* .

2. Obdrželi jste oznámení od společnosti Microsoft, že produkt E5 bude rozšířen o novou *Microsoft Stream* služby. Jakmile bude služba k dispozici ve vaší organizaci, můžete provést následující akce:

3. V okně [**Azure Active Directory > licence > všechny produkty**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) vyberte možnost *Office 365 Enterprise E5* a potom vyberte **licencované skupiny** , abyste zobrazili seznam všech skupin s tímto produktem.

4. Klikněte na skupinu, kterou chcete zkontrolovat (v tomto případě *O365 E5-Exchange*). Tím se otevře karta **licence** . Po kliknutí na licenci E5 se otevře okno se seznamem všech povolených služeb.
   > [!NOTE]
   > Služba *Microsoft Stream* byla v této skupině automaticky přidána a povolena, kromě služby *Exchange Online* :

   ![Snímek obrazovky nové služby přidané do skupinové licence](./media/licensing-group-advanced/manage-new-services.png)

5. Pokud chcete zakázat novou službu v této skupině, klikněte na přepínač **zapnuto/vypnuto** vedle služby a kliknutím na tlačítko **Uložit** potvrďte změnu. Azure AD teď bude všechny uživatele ve skupině zpracovat, aby se změny projevily. u všech nových uživatelů přidaných do skupiny nebude služba *Microsoft Stream* povolena.

   > [!NOTE]
   > Uživatelé můžou mít tuto službu stále povolenou prostřednictvím nějakého jiného přiřazení licence (další skupiny, které jsou členy nebo přiřazení přímé licence).

6. V případě potřeby proveďte stejné kroky pro další skupiny s přiřazeným produktem.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Použití PowerShellu k zobrazení zděděných a přímých licencí
Ke kontrole, jestli mají uživatelé přiřazenou licenci přímo nebo zděděné ze skupiny, můžete použít skript prostředí PowerShell.

1. Spusťte `connect-msolservice` rutinu pro ověření a připojení k vaší organizaci.

2. `Get-MsolAccountSku` dá se použít ke zjištění všech zřízených licencí produktu v organizaci Azure AD.

   ![Snímek obrazovky rutiny Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Použijte hodnotu *AccountSkuId* pro licenci, kterou vás zajímá s [tímto skriptem PowerShellu](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Tím se vytvoří seznam uživatelů, kteří mají tuto licenci, a informace o tom, jak je licence přiřazena.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Monitorování aktivit licencování na základě skupin pomocí protokolů auditu

Pomocí [protokolů auditu Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) můžete zobrazit veškerou aktivitu související s licencováním na základě skupin, včetně:
- kdo změnil licence u skupin
- Když systém zahájil zpracování změny licence skupiny a po jejím dokončení
- Jaké změny licencí byly provedeny uživateli v důsledku přiřazení skupinové licence.

>[!NOTE]
> Protokoly auditu jsou k dispozici ve většině oken v části Azure Active Directory na portálu. V závislosti na tom, kde k nim přistupujete, se filtry můžou předběžně použít jenom k zobrazení aktivity, která je relevantní pro kontext okna. Pokud nevidíte očekávané výsledky, Projděte [si možnosti filtrování](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) nebo získejte přístup k nefiltrovaným protokolům auditu v části [**Azure Active Directory > aktivita > protokoly auditu**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Zjistit, kdo upravil licenci skupiny

1. Nastavte filtr **aktivity** na *nastavit licenci skupiny* a klikněte na **použít**.
2. Výsledky zahrnují všechny případy, kdy jsou u skupin nastavené nebo upravené licence.
   >[!TIP]
   > Můžete také zadat název skupiny v *cílovém* filtru, abyste mohli určit rozsah výsledků.

3. Kliknutím na položku v seznamu zobrazíte podrobnosti o tom, co se změnilo. V části *upravené vlastnosti* jsou uvedené staré i nové hodnoty pro přiřazení licencí.

Tady je příklad poslední změny licence skupiny s podrobnostmi:

![Snímek obrazovky zobrazující stránku protokoly auditu se zvolenou položkou seznamu a otevřené podokno Podrobnosti o aktivitě protokolu auditu.](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Zjištění zahájení a dokončení zpracování změn skupiny

Když se ve skupině změní licence, služba Azure AD začne používat změny pro všechny uživatele.

1. Pokud chcete zjistit, kdy se skupiny začali zpracovávat, nastavte filtr **aktivity** tak, aby *se na uživatele spouštěla licence založená na skupinách*. Všimněte si, že objekt actor pro operaci je *Microsoft Azure AD Group-Based licencování* – systémový účet, který se používá k provádění všech změn všech skupinových licencí.
   >[!TIP]
   > Kliknutím na položku v seznamu zobrazíte pole *upravené vlastnosti* – zobrazí se změny licencí, které byly vyzvednuty ke zpracování. To je užitečné, pokud jste provedli více změn ve skupině a nejste si jisti, která z nich byla zpracována.

2. Podobně pokud chcete zjistit, kdy se skupiny dokončily, použijte hodnotu Filter *Dokončit použití skupinové licence pro uživatele*.
   > [!TIP]
   > V tomto případě obsahuje pole *změněné vlastnosti* souhrn výsledků – to je užitečné pro rychlé zjištění, zda zpracování vedlo k nějakým chybám. Ukázkový výstup:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Pokud chcete zobrazit úplný protokol pro zpracování skupiny, včetně všech změn uživatelů, nastavte následující filtry:
   - **Zahájil (actor)**: "Microsoft Azure AD Group-Based licencování"
   - **Rozsah kalendářních dat** (volitelné): vlastní rozsah, pro který znáte konkrétní skupinu spuštěnou a dokončenou se zpracováním

Tento ukázkový výstup ukazuje začátek zpracování, všechny výsledné změny uživatelů a dokončení zpracování.

![Změny licencí pro skupinu snímků obrazovky](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknutím na položky související s *změnou uživatelské licence* zobrazíte podrobnosti o změnách licencí použitých u jednotlivých uživatelů.

## <a name="deleting-a-group-with-an-assigned-license"></a>Odstranění skupiny s přiřazenou licencí

Skupinu s přiřazenou aktivní licencí není možné odstranit. Správce může odstranit skupinu, která neposkytuje, aby způsobila odebrání licencí z uživatelů – z tohoto důvodu vyžadujeme, aby se z této skupiny odebraly nejprve všechny licence, aby je bylo možné odstranit.

Při pokusu o odstranění skupiny v Azure Portal se může zobrazit oznámení o chybě, například: ![ odstranění skupiny snímku se nezdařilo.](./media/licensing-group-advanced/groupdeletionfailed.png)

Přejděte na kartu **licence** ve skupině a zkontrolujte, jestli jsou přiřazené nějaké licence. Pokud ano, odeberte tyto licence a pokuste se skupinu odstranit znovu.

Při pokusu o odstranění skupiny prostřednictvím PowerShellu nebo Graph API se můžou zobrazit podobné chyby. Pokud používáte skupinu synchronizovanou z místního prostředí, Azure AD Connect může také hlásit chyby, pokud se nepodaří odstranit skupinu ve službě Azure AD. V některých případech nezapomeňte zkontrolovat, jestli nejsou k této skupině přiřazené žádné licence, a nejdřív je odebrat.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

Pokud používáte licencování na základě skupin, je vhodné se seznámit s následujícím seznamem omezení a známých problémů.

- Licencování na základě skupin aktuálně nepodporuje skupiny, které obsahují další skupiny (vnořené skupiny). Pokud použijete licenci pro vnořenou skupinu, použije se jenom pro bezprostřední členy této skupiny na první úrovni.

- Tato funkce se dá použít jenom se skupinami zabezpečení a Microsoft 365mi skupinami, které mají securityEnabled = TRUE.

- [Centrum pro správu Microsoft 365](https://admin.microsoft.com) v současné době nepodporuje licencování na základě skupin. Pokud uživatel zdědí licenci ze skupiny, zobrazí se tato licence na portálu pro správu Office jako běžná Uživatelská licence. Pokud se pokusíte tuto licenci změnit nebo se pokusíte licenci odebrat, portál vrátí chybovou zprávu. Zděděné licence skupiny nelze upravovat přímo uživateli.

- Když se licence přiřadí nebo upraví pro velkou skupinu (například 100 000 uživatelů), může to mít vliv na výkon. Konkrétně objem změn generovaných službou Azure AD Automation může negativně ovlivnit výkon synchronizace adresářů mezi Azure AD a místními systémy.

- Pokud spravujete členství uživatelů pomocí dynamických skupin, zkontrolujte, jestli je daný uživatel součástí skupiny, což je k přiřazení licence nezbytné. V opačném případě [zkontrolujte stav zpracování pravidla členství](groups-create-rule.md) dané dynamické skupiny.

- V některých situacích s vysokým zatížením může trvat dlouhou dobu, než se změny licencí pro skupiny nebo změny členství ve skupinách s existujícími licencemi změní. Pokud se vám změny velikosti skupiny procesů 60K nebo méně projeví u svých změn, [otevřete prosím lístek podpory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) , který nám umožní prozkoumat. 

- Automatizace správy licencí automaticky nereaguje na všechny typy změn v prostředí. Například může vycházet z vydaných licencí, což způsobuje, že někteří uživatelé budou v chybovém stavu. K uvolnění dostupného počtu pracovních míst můžete odebrat některé přímo přiřazené licence od jiných uživatelů. Systém ale nereaguje na tuto změnu automaticky a opravuje uživatele v tomto chybovém stavu.

  Jako alternativní řešení těchto typů omezení můžete přejít do okna **skupiny** ve službě Azure AD a kliknout na znovu **zpracovat**. Tento příkaz zpracuje všechny uživatele v dané skupině a pokud je to možné, vyřeší chybové stavy.

## <a name="next-steps"></a>Další kroky

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Co je licencování na základě skupin v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Postup migrace uživatelů mezi licencemi k produktu pomocí licencování na základě skupin v Azure Active Directory](licensing-groups-change-licenses.md)
