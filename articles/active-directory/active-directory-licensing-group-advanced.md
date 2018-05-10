---
title: Azure Active Directory na základě skupin licencí další scénáře | Microsoft Docs
description: Další scénáře pro Azure Active Directory na základě skupiny licencí
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: piotrci
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb8bd727618eda2a887cc9e1b739889204eb87fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scénáře, omezení a známé problémy, použití skupin ke správě licencování v Azure Active Directory

Použijte následující informace a příklady pro získání rozsáhlejšími znalostmi na základě skupiny licencování Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Místo použití

Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, Správce musí určit **umístění využití** vlastnost na uživatele. V [portálu Azure](https://portal.azure.com), můžete zadat v **uživatele** &gt; **profil** &gt; **nastavení**.

Pro přiřazení skupiny licencí zdědí všechny uživatele bez využití umístění zadané umístění adresáře. Pokud máte uživatele na více místech, zajistěte, aby tak, aby odrážela který správně v uživatelské objekty před přidáním uživatelů do skupin s licencí.

> [!NOTE]
> Přiřazení licencí skupiny nikdy upraví stávající hodnotu umístění využití určitého uživatele. Doporučujeme vždy nastavit umístění využití jako součást vaší toku vytvoření uživatele ve službě Azure AD (např. přes AAD Connect konfigurace) –, který zajistí výsledek přiřazení licence se vždy správný, a uživatelé neobdrží služby v umístění, které nejsou povoleny.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Správa na základě skupiny licencí s dynamickými skupinami

Na základě skupiny licencování s žádnou skupinu zabezpečení, což znamená, že ho mohou být kombinovány s dynamických skupin Azure AD, můžete použít. Dynamické skupiny spustit pravidla pro uživatele atributů objektu automaticky přidat a odebrat uživatele ze skupin.

Můžete například vytvořit skupinu dynamické pro některé sadu produkty, které chcete přiřadit uživatelům. Každý skupina se zaplní pravidlem přidávání uživatelů podle jejich atributů a každou skupinu je přiřazena licence, které mají přijímat. Můžete přiřadit atribut na místě a synchronizovat s Azure AD, nebo je můžete spravovat atribut přímo v cloudu.

Licence jsou přiřazeny uživateli krátce po budou přidány do skupiny. Při změně atributu, uživatel odejde skupiny a jsou odebrány licence.

### <a name="example"></a>Příklad:

Podívejte se na příklad řešení správy identity místně, rozhodne, které uživatelé by měli mít přístup k webovým službám společnosti Microsoft. Používá **extensionAttribute1** k uložení řetězcovou hodnotu představující licence, musí mít uživatel. Azure AD Connect synchronizuje se službou Azure AD.

Uživatelé mohou potřebovat licence ale není jiný, nebo může být nutné obě. Tady je příklad, ve kterém jsou distribuci Office 365 Enterprise E5 a Enterprise Mobility + Security (EMS) licence pro uživatele ve skupinách:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: základní služby

![Snímek obrazovky z Office 365 Enterprise E5 základní služby](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencovaní uživatelé

![Snímek obrazovky Enterprise Mobility + Security licencované uživatele](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

V tomto příkladu úprava jednoho uživatele a nastavte jejich extensionAttribute1 na hodnotu `EMS;E5_baseservices;` Pokud má uživatel obou licence. Tato úprava můžete provést místní. Po změnu synchronizuje s cloudem, uživatel se automaticky přidá do obou skupinách a přiřazené licence.

![Snímek obrazovky ukazující, jak nastavit extensionAttribute1 uživatele](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Buďte opatrní při úpravě pravidla členství ve stávající skupině. Při změně pravidla členství ve skupině bude znovu zhodnotí a budou uživatelé, kteří už neodpovídá nové pravidlo odebrané (uživatelé, kteří odpovídají nové pravidlo nebude mít vliv během tohoto procesu je stále). Tito uživatelé budou mít jejichž licence jsou během procesu, který může mít za následek ztrátu služby, nebo v některých případech ke ztrátě dat odebrány.

> Pokud máte velké dynamická skupina, které závisí na pro přiřazení licence, vezměte v úvahu ověřování žádnými většími změnami v testovací skupině, menší než je použijete do hlavní skupiny.

## <a name="multiple-groups-and-multiple-licenses"></a>Více skupin a více licencí

Uživatel může být členem více skupin s licencí. Zde jsou některé věci vzít v úvahu:

- Více licencí pro stejný produkt, může dojít k překrytí a budou mít za následek všechny povolené služby, které bylo použito pro uživatele. Následující příklad ukazuje dva licencování skupin: *základní služby E3* obsahuje foundation služby pro nasazení, nejprve pro všechny uživatele. A *E3 rozšířené služby* obsahuje další služby (boční výkyvy a Planner) nasadit jenom pro některé uživatele. V tomto příkladu uživatel byl přidán do obou skupin:

  ![Snímek obrazovky povolené služby](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  V důsledku toho uživatel má 7 12 služeb v produktu povoleno, při použití pouze jednu licenci tohoto produktu.

- Výběr *E3* licence ukazuje další podrobnosti, včetně informací o tom, které skupiny způsobila, co services povolit pro uživatele.

  ![Snímek obrazovky povolené služby podle skupiny](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Přímé licence souběžnou existenci s skupiny licencí

Když uživatel dědí licenci ze skupiny, nedá přímo odebrat ani změnit tohoto přiřazení licence ve vlastnostech daného uživatele. Změny, musí být provedené ve skupině a pak se rozšíří na všechny uživatele.

Je však možné, přiřaďte stejné licenci produktu přímo na uživatele, kromě zděděné licence. Další služby z produktu jen pro jednoho uživatele, můžete povolit bez ovlivnění jiných uživatelů.

Přímo přiřazené licence lze odebrat a nemají vliv zděděná licence. Vezměte v úvahu uživatele, který dědí ze skupiny licenci pro Office 365 Enterprise E3.

1. Původně uživatele dědí licence pouze z *základní služby E3* skupinu, která umožňuje čtyři plány služby, jak je znázorněno:

  ![Snímek obrazovky E3 skupiny povolené služby](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Můžete vybrat **přiřadit** přímo uživateli přiřadit licenci E3. V takovém případě se chystáte zakažte všechny plány služby s výjimkou Yammer Enterprise:

  ![Snímek obrazovky přiřazení licence přímo na uživatele](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. V důsledku toho uživatel pořád používají pouze jednu licenci produktu E3. Ale umožňuje přímé přiřazení Služba Yammer Enterprise pouze pro tohoto uživatele. Můžete zjistit, které služby jsou povolené ve členství ve skupině a přímé přiřazení:

  ![Snímek obrazovky zděděná versus přímé přiřazení](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Pokud používáte přímé přiřazení, jsou povoleny následující operace:

  - Yammer Enterprise může být vypnuto v objektu user přímo. **Zapnutí nebo vypnutí** pro tuto službu, a další služby přepínačů bylo povoleno přepnutí na obrázku. Vzhledem k tomu, že je služba zapnutá přímo na uživatele, můžete změnit.
  - Další služby můžete povolit taky jako součást přímo přiřazené licence.
  - **Odebrat** tlačítko slouží k odebrání přímé licence od uživatele. Uvidíte, že má uživatel nyní pouze zděděné skupinu licencí a zůstat zapnuté, pouze původní služby:

    ![Snímek obrazovky ukazující, jak odebrat přímé přiřazení](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Správa nové služby přidán do produktů
Když Microsoft přidá novou službu pro určitý produkt, bude povoleno ve výchozím nastavení ve všech skupinách, na které jste přiřadili licenci produktu. Uživatelé ve vašem klientovi, kteří jsou přihlášeni k odběru oznámení o změnách produktu bude dostávat e-maily s předstihem jim informace o přidání nadcházející služby.

Jako správce můžete revidovat všechny skupiny vliv změn a provést akci, například zakázání nové služby v každé skupině. Například pokud jste vytvořili skupiny cílení pouze konkrétní služby pro nasazení, můžete pokroku těchto skupin a ujistěte se, že všechny nově přidaní služby jsou zakázány.

Tady je příklad, jak může vypadat tento proces:

1. Původně, jste přiřadili *Office 365 Enterprise E5* produktu do několika skupin. Jeden z těchto skupin nazývaných *O365 E5 - Exchange pouze* byla navržená tak, aby povolit pouze *Exchange Online (plán 2)* služby pro její členy.

2. Obdržíte oznámení od společnosti Microsoft, který produkt E5 bude rozšířeno o novou službu - *Microsoft Stream*. Jakmile služba k dispozici ve vašem klientovi, můžete provést následující:

3. Přejděte na [ **Azure Active Directory > licence > všechny produkty** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) a vyberte *Office 365 Enterprise E5*, pak vyberte **licenci skupiny** Chcete-li zobrazit seznam všech skupin s tohoto produktu.

4. Klikněte na skupinu, které chcete zkontrolovat (v tomto případě *O365 E5 - Exchange pouze*). Otevře se **licence** kartě. Kliknutím na E5 licence, které se otevře okno výpis všech služeb povoleno.
> [!NOTE]
> *Microsoft Stream* služby je automaticky přidán a povoleno v této skupině kromě *Exchange Online* služby:

  ![Snímek obrazovky nové služby, které jsou přidány do skupiny licencí](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Pokud chcete zakázat službu nové v této skupině, klikněte na tlačítko **zapnutí nebo vypnutí** přepnutí vedle služby a klikněte na tlačítko **Uložit** tlačítko pro potvrzení změny. Azure AD bude nyní zpracovat všechny uživatele ve skupině na použití změny; nebude mít žádné noví uživatelé přidaní do skupiny *Microsoft Stream* služby povolena.

  > [!NOTE]
  > Uživatelé mohou mít i nadále službu pomocí některé jiné přiřazení licence (jiné skupiny jsou členy nebo přiřazení přímé licence) povolena.

6. V případě potřeby proveďte s tímto produktem přiřazen stejný postup pro jiné skupiny.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Pomocí prostředí PowerShell, který dědí a přímé licencí
Skript prostředí PowerShell můžete použít ke kontrole, pokud uživatelé mít licenci přiřazené přímo nebo zděděno od skupiny.

1. Spustit `connect-msolservice` rutiny k ověření a připojení ke klientovi.

2. `Get-MsolAccountSku` umožňuje zjistit všechny licence zřízené produktu v klientovi.

  ![Snímek obrazovky rutinu Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Použití *AccountSkuId* hodnotu pro licence, které vás zajímá, [tento skript prostředí PowerShell](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Vznikne tak seznam uživatelů, kteří mají tuto licenci s informacemi o tom, jak přiřadit licence.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Protokoly auditu pomocí monitorování na základě skupiny licencování

Můžete použít [protokoly auditu Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) zobrazíte všechny aktivity související s na základě skupiny licencí, včetně:
- kdo změnil licencí na skupiny
- Při spuštění systému zpracování změnu skupinu licencí a po jeho dokončení
- jaké změny licencí byly provedeny na uživatele v důsledku přiřazení licencí skupiny.

>[!NOTE]
> Protokoly auditu jsou k dispozici na většině oken v části služby Azure Active Directory na portálu. V závislosti na tom, odkud je může být filtry předem použít pouze zobrazuje relevantní pro kontextu okna aktivity. Pokud nevidíte očekáváte, že výsledky, prozkoumejte [možnosti filtrování](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) nebo přístup k protokolům nefiltrované auditu v části [ **Azure Active Directory > Aktivity > protokoly auditu** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Zjistěte, kdo upravit skupinu licencí

1. Nastavte **aktivity** filtrovat, aby *sady skupiny licencí* a klikněte na tlačítko **použít**.
2. Výsledky budou zahrnovat všechny případy licencí se nastavit nebo změnit na skupiny.
>[!TIP]
> Můžete také zadat název skupiny v *cíl* filtr k určení rozsahu výsledky.

3. Klikněte na položku v zobrazení seznamu zobrazíte podrobnosti o co se změnilo. V části *upravit vlastnosti* staré a nové hodnoty pro přiřazení licence jsou uvedené.

Tady je příklad nedávné změny skupiny licencí, s podrobnostmi:

![Změny licencí skupiny – snímek obrazovky](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Zjistit, kdy změny skupiny spuštění a dokončení zpracování

Když se změní licenci na skupinu, Azure AD se spustí použití změn, pro všechny uživatele.

1. Při spuštění skupiny zpracování najdete nastavit **aktivity** filtrovat, aby *spustit použití skupinu na základě licencí pro uživatele*. Všimněte si, že je objektu actor pro operaci *Microsoft Azure AD na základě skupiny Licensing* -systémový účet, který se používá k provedení všech změn skupiny licencí.
>[!TIP]
> Klikněte na položku v seznamu zobrazíte *upravit vlastnosti* pole - zobrazuje změny licencí, které byly zachyceny pro zpracování. To je užitečné, pokud jste provedli více změn do skupiny a si nejste jisti, která byla zpracována.

2. Podobně, pokud chcete zobrazit, dokončení zpracování skupiny, použijte hodnota filtru *dokončit použití skupinu na základě licencí pro uživatele*.
>[!TIP]
> V takovém případě *upravit vlastnosti* pole obsahuje souhrn výsledků – to je užitečné můžete rychle zjistit, pokud zpracování výsledkem případné chyby. Ukázkový výstup:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Chcete-li naleznete v protokolu dokončení jak byla zpracována skupiny, včetně změny všech uživatelů, nastavte následující filtry:
  - **Zahájit (objektu Actor)**: "Microsoft Azure AD na základě skupin licencí."
  - **Rozsah** (volitelné): Pokud znáte konkrétní skupinu vlastní rozsah spuštění a dokončení zpracování

Tento ukázkový výstup ukazuje spuštění zpracování, výsledné změny uživatelů a dokončit zpracování.

![Změny licencí skupiny – snímek obrazovky](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknutím na položky související s *uživatelské licence pro změnu* zobrazí podrobnosti pro licenční změny použité pro každý jednotlivý uživatel.

## <a name="deleting-a-group-with-an-assigned-license"></a>Odstraňuje se skupina s přiřazenou licencí

Není možné odstranit skupinu active přiřazené licence. Správce může odstranit skupinu není porozumění, může to způsobit licencí, které chcete odebrat z uživatelů - z tohoto důvodu, že je nutné, aby licence odebrat ze skupiny poprvé, předtím, než mohl být odstraněn.

Při pokusu o odstranění skupiny na portálu Azure, mohou se zobrazit oznámení o chybě takto: ![odstranění skupiny – snímek obrazovky se nezdařilo](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Přejděte na **licence** kartě ve skupině a jestli jsou všechny přiřazené licence. Pokud ano, odeberte těchto licencí a pokuste se znovu odstranit skupinu.

Při pokusu o odstranění skupiny pomocí prostředí PowerShell nebo rozhraní Graph API, může se zobrazit podobné chyby. Pokud používáte skupiny synchronizované z místní, Azure AD Connect může také zprávy o chybách Pokud se nedaří odstranit skupinu ve službě Azure AD. V takových případech nezapomeňte ověřit, zda jsou všechny licence přiřazeny skupině a nejprve odeberte.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

Pokud chcete použít, na základě skupiny licencí, je vhodné se seznámit s následující seznam omezení a známé problémy.

- Na základě skupiny licencování aktuálně nepodporuje skupiny, které obsahují další skupiny (vnořené skupiny). Pokud použijete licenci pro vnořené skupiny, mají pouze členy okamžitou první úrovně uživatelské skupiny licencí použít.

- Tuto funkci lze použít pouze se skupinami zabezpečení. Aktuálně nejsou podporované skupiny Office a nebude možné použít při procesu přiřazení licence.

- [Portálu pro správu Office 365](https://portal.office.com ) současné době nepodporuje na základě skupin licencí. Pokud uživatel dědí licenci ze skupiny, zobrazí se tuto licenci na portálu správy Office jako běžných uživatelských licencí. Pokud se pokusíte změnit licencích nebo pokuste se odebrat licence, portálu vrátí chybovou zprávu. Licence zděděné skupiny nemůže být upraven přímo na uživatele.

- Když uživatel se odebere ze skupiny a ztratí licence, plány služby z této licence (například služby SharePoint Online) jsou nastaveny na **pozastaveno** stavu. Plány služeb nejsou nastavené konečné, zakázaném stavu. Toto opatření se můžete vyhnout náhodného odstranění dat uživatele, pokud správce zadá chybu ve správě členství skupiny.

- Když jsou licence přiřadit nebo změnit pro velké skupinu (například 100 000 uživatelů), může ovlivnit výkon. Konkrétně objemu změn generované automatizace Azure AD může mít negativní vliv na výkon vaší synchronizace adresářů mezi službami Azure AD a místních systémech.

- V některých situacích vysokého zatížení zpracování licence může být zpoždění a změní jako je například přidávání nebo odebírání skupinu licencí, nebo přidávání nebo odebírání uživatelů ze skupiny, může trvat dlouho mají být zpracovány. Pokud se zobrazí vaše změny trvat déle než 24 hodin zpracovat prosím [otevřete lístek podpory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) umožňuje nám prozkoumat. Předtím, než dorazí jsme se zvyšuje výkonové charakteristiky této funkce *obecné dostupnosti*.

- Automatizace správy licencí nereaguje automaticky pro všechny typy změny v prostředí. Například můžete pravděpodobně vyčerpala volné licence, způsobuje někteří uživatelé v chybovém stavu. K uvolněte počet dostupných stanici, můžete odebrat některé přímo přiřazené licence od jiných uživatelů. Systém však automaticky reagovat na tuto změnu a opravte uživatelé v tomto stavu chyby.

  Jako řešení pro tyto typy omezení, můžete přejít na **skupiny** okno ve službě Azure AD a klikněte na tlačítko **znovu zpracovat**. Tento příkaz zpracovává všechny uživatele v dané skupině a přeloží chybové stavy, pokud je to možné.

- Na základě skupiny licencování nezaznamenává chyby, pokud licenci nelze přiřadit uživateli kvůli konfiguraci proxy duplicitní adresa v systému Exchange Online; tyto uživatele jsou přeskočeny při přiřazení licence. Další informace o tom, jak identifikovat a vyřešit tento problém, naleznete v části [v této části](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Další postup

Další informace o scénáře pro správu licencí prostřednictvím na základě skupin licencí najdete v tématu:

* [Co je na základě skupin licencování v Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Přiřazování licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identifikace a řešení potíží s licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Postup migrace na základě skupiny licencí v Azure Active Directory pro jednotlivé licencovaní uživatelé](active-directory-licensing-group-migration-azure-portal.md)
