---
title: Další scénáře licencování na základě skupiny – Azure AD | Dokumenty společnosti Microsoft
description: Další scénáře pro skupinové licencování Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266283"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scénáře, omezení a známé problémy pomocí skupin ke správě licencí ve službě Azure Active Directory

Pomocí následujících informací a příkladů získáte pokročilejší znalosti licencování na základě skupiny Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Umístění použití

Některé služby Microsoft nejsou dostupné ve všech umístěních. Před přiřazením licence uživateli musí správce zadat vlastnost **Umístění použití** uživatele. Na [webu Azure Portal](https://portal.azure.com)můžete určit umístění využití v **nastavení** **profilu** &gt; **uživatele** &gt; .

Pro přiřazení skupinové licence zdědí umístění adresáře všichni uživatelé bez zadaného umístění použití. Pokud máte uživatele ve více umístěních, ujistěte se, že odráží, že správně ve vašich uživatelských prostředků před přidáním uživatelů do skupin s licencemi.

> [!NOTE]
> Přiřazení skupinové licence nikdy nezmění existující hodnotu umístění použití pro uživatele. Doporučujeme vždy nastavit umístění využití jako součást toku vytváření uživatelů ve službě Azure AD (například prostřednictvím konfigurace AAD Connect) – to zajistí, že výsledek přiřazení licence je vždy správné a uživatelé nedostávají služby v umístěních, které nejsou povoleny.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Použití skupinových licencí s dynamickými skupinami

Licencování na základě skupiny můžete použít s libovolnou skupinou zabezpečení, což znamená, že je lze kombinovat s dynamickými skupinami Azure AD. Dynamické skupiny spouštějí pravidla proti atributům prostředků uživatelů, aby automaticky přidávaly a odevzdávala uživatele ze skupin.

Můžete například vytvořit dynamickou skupinu pro některé sady produktů, které chcete přiřadit uživatelům. Každá skupina je naplněna pravidlem, které přidává uživatele podle jejich atributů, a každé skupině jsou přiřazeny licence, které mají obdržet. Atribut můžete přiřadit místně a synchronizovat s Azure AD, nebo můžete spravovat atribut přímo v cloudu.

Licence jsou uživateli přiřazeny krátce po jejich přidání do skupiny. Při změně atributu uživatel opustí skupiny a licence jsou odebrány.

### <a name="example"></a>Příklad

Vezměme si příklad místního řešení pro správu identit, které rozhoduje o tom, kteří uživatelé by měli mít přístup k webovým službám společnosti Microsoft. Používá **extensionAttribute1** k uložení hodnoty řetězce představující licence, které by měl uživatel mít. Azure AD Connect synchronizuje s Azure AD.

Uživatelé mohou potřebovat jednu licenci, ale ne jinou, nebo mohou potřebovat obojí. Tady je příklad, ve kterém distribuujete licence Office 365 Enterprise E5 a Enterprise Mobility + Security (EMS) uživatelům ve skupinách:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: základní služby

![Snímek obrazovky se základními službami Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Podniková mobilita + zabezpečení: licencovaní uživatelé

![Snímek obrazovky s uživateli s licencí Enterprise Mobility + Security](./media/licensing-group-advanced/o365-e5-licensed-users.png)

V tomto příkladu upravte jednoho uživatele a nastavte `EMS;E5_baseservices;` jeho extensionAttribute1 na hodnotu, pokud chcete, aby uživatel měl obě licence. Tuto změnu můžete provést místně. Po synchronizaci změn s cloudem je uživatel automaticky přidán do obou skupin a licence jsou přiřazeny.

![Snímek obrazovky znázorňující nastavení rozšíření uživateleAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Při úpravách pravidla členství existující skupiny buďte opatrní. Při změně pravidla bude členství ve skupině přehodnoceno a uživatelé, kteří již neodpovídají novému pravidlu, budou odebráni (uživatelé, kteří stále odpovídají novému pravidlu, nebudou během tohoto procesu ovlivněni). Těmto uživatelům budou během procesu odebrány licence, což může vést ke ztrátě služby nebo v některých případech ke ztrátě dat.
> 
> Pokud máte velkou dynamickou skupinu, na které jste při přiřazení licence závislí, zvažte ověření všech důležitých změn v menší testovací skupině před jejich použitím na hlavní skupinu.

## <a name="multiple-groups-and-multiple-licenses"></a>Více skupin a více licencí

Uživatel může být členem více skupin s licencemi. Zde je několik věcí, které je třeba zvážit:

- Více licencí pro stejný produkt se může překrývat a výsledkem jsou všechny povolené služby používané pro uživatele. Následující příklad ukazuje dvě skupiny licencí: *Základní služby E3* obsahují základní služby nadace, které mají být nejprve nasazeny všem uživatelům. Rozšířené *služby E3* obsahují další služby (Sway a Planner), které lze nasadit pouze některým uživatelům. V tomto příkladu byl uživatel přidán do obou skupin:

  ![Snímek obrazovky s povolenými službami](./media/licensing-group-advanced/view-enabled-services.png)

  V důsledku toho má uživatel povoleno7 z 12 služeb v produktu, zatímco používá pouze jednu licenci pro tento produkt.

- Výběrem licence *E3* se zobrazí další podrobnosti, včetně informací o tom, které služby jsou pro uživatele povoleny přiřazením skupinové licence.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Přímé licence koexistují se skupinovými licencemi

Když uživatel zdědí licenci ze skupiny, nelze přímo odebrat nebo upravit toto přiřazení licence ve vlastnostech uživatele. Změny musí být provedeny ve skupině a potom rozšířeny všem uživatelům.

Kromě zděděné licence je však možné přiřadit stejnou licenci produktu přímo uživateli. Můžete povolit další služby z produktu pouze pro jednoho uživatele, aniž by to ovlivnilo ostatní uživatele.

Přímo přiřazené licence lze odebrat a neovlivní zděděné licence. Zvažte uživatele, který dědí licenci Office 365 Enterprise E3 ze skupiny.

Zpočátku uživatel dědí licenci pouze ze skupiny *základních služeb E3,* která umožňuje čtyři plány služeb.

1. Vyberte **Přiřadit,** chcete-li uživateli přímo přiřadit licenci E3. V takovém případě zakážete všechny plány služeb kromě Yammeru Enterprise.

    V důsledku toho uživatel stále používá pouze jednu licenci produktu E3. Přímé přiřazení však umožňuje službu Yammer Enterprise pouze pro tohoto uživatele. Můžete zjistit, které služby jsou povoleny členstvím ve skupině versus přímým přiřazením.

1. Při použití přímého přiřazení jsou povoleny následující operace:

   - Yammer Enterprise se dá vypnout přímo u uživatelského prostředku. Přepínač **Zapnuto/Vypnuto na** obrázku byl povolen pro tuto službu, na rozdíl od jiné služby přepíná. Vzhledem k tomu, že služba je povolena přímo na uživatele, lze ji upravit.
   - Jako součást přímo přiřazené licence lze povolit také další služby.
   - Tlačítko **Odebrat** lze použít k odebrání přímé licence uživateli. Můžete vidět, že uživatel má nyní pouze zděděnou skupinovou licenci a pouze původní služby zůstávají povoleny:

## <a name="managing-new-services-added-to-products"></a>Správa nových služeb přidaných k produktům

Pokud společnost Microsoft přidá novou službu do plánu licencí produktu, je ve výchozím nastavení povolena ve všech skupinách, ke kterým jste licenci produktu přiřadili. Uživatelé ve vašem tenantovi, kteří si předplatili oznámení o změnách produktu, obdrží e-maily předem, které je upozorní na nadcházející přidání služby.

Jako správce můžete zkontrolovat všechny skupiny ovlivněné změnou a provést akci, například zakázat novou službu v každé skupině. Pokud jste například vytvořili skupiny, které cílí pouze na konkrétní služby pro nasazení, můžete tyto skupiny znovu navštívit a ujistit se, že všechny nově přidané služby jsou zakázány.

Zde je příklad toho, jak tento proces může vypadat:

1. Původně jste produkt *Office 365 Enterprise E5* přiřadili několika skupinám. Jedna z těchto skupin, nazvaný *O365 E5 - Exchange pouze* byl navržen tak, aby povolit pouze *Exchange Online (plán 2)* služby pro své členy.

2. Od společnosti Microsoft jste obdrželi oznámení, že produkt E5 bude rozšířen o novou službu – *Microsoft Stream*. Jakmile bude služba dostupná ve vašem tenantovi, můžete provést následující kroky:

3. Přejděte do okna [**Licence služby Azure Active Directory > > Všechny produkty**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) a vyberte Office *365 Enterprise E5*a pak vyberte **Licencované skupiny,** chcete-li zobrazit seznam všech skupin s tímto produktem.

4. Klikněte na skupinu, kterou chcete zkontrolovat (v tomto případě *O365 E5 - pouze Exchange*). Otevře se karta **Licence.** Kliknutím na licenci E5 se otevře okno se seznamem všech povolených služeb.
   > [!NOTE]
   > Kromě služby *Exchange Online* byla v této skupině automaticky přidána a povolena služba *Microsoft Stream:*

   ![Snímek obrazovky s novou službou přidanou do skupinové licence](./media/licensing-group-advanced/manage-new-services.png)

5. Chcete-li zakázat novou službu v této skupině, klepněte na přepínač **Zapnuto/Vypnuto** vedle služby a klepnutím na tlačítko **Uložit** změnu potvrďte. Azure AD teď zpracuje všechny uživatele ve skupině, aby použili změnu. Žádní noví uživatelé připřidání do skupiny nebudou mít povolenou službu *Microsoft Stream.*

   > [!NOTE]
   > Uživatelé mohou mít stále povolenou službu prostřednictvím jiného přiřazení licence (jiné skupiny, jejíž jsou členy, nebo přímého přiřazení licence).

6. V případě potřeby proveďte stejné kroky pro ostatní skupiny s přiřazeným produktem.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Použití PowerShellu k zobrazení, kdo zdědil a přímé licence
Pomocí skriptu prostředí PowerShell můžete zkontrolovat, jestli mají uživatelé licenci přiřazenou přímo nebo zděděnou ze skupiny.

1. Spusťte rutinu `connect-msolservice` k ověření a připojení k tenantovi.

2. `Get-MsolAccountSku`lze použít ke zjištění všech zřízených licencí produktu v tenantovi.

   ![Snímek obrazovky rutiny Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Pomocí [tohoto skriptu prostředí PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)použijte hodnotu *AccountSkuId* pro licenci, která vás zajímá. Tím se vytvoří seznam uživatelů, kteří mají tuto licenci s informacemi o tom, jak je licence přiřazena.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Použití protokolů auditování ke sledování skupinové licenční činnosti

[Protokoly auditu Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) můžete použít k zobrazení všech aktivit souvisejících s licencováním založeným na skupině, včetně:
- kdo změnil licence ve skupinách
- když systém začal zpracovávat změnu skupinové licence a po dokončení
- jaké změny licencí byly provedeny uživateli v důsledku přiřazení skupinové licence.

>[!NOTE]
> Protokoly auditu jsou k dispozici na většině listů v části Azure Active Directory na portálu. V závislosti na tom, kde k nim přistupujete, mohou být filtry předem použity pouze pro zobrazení aktivity relevantní pro kontext okna. Pokud se nezobrazují výsledky, které očekáváte, zkontrolujte [možnosti filtrování](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) nebo získejte přístup k nefiltrovaným protokolům auditu v rámci [**protokolů Azure Active Directory > Aktivita > auditování**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Zjistěte, kdo upravil skupinovou licenci

1. Nastavte filtr **Aktivita** na *Nastavení skupinové licence* a klepněte na tlačítko **Použít**.
2. Výsledky zahrnují všechny případy, kdy jsou licence nastaveny nebo upravovány ve skupinách.
   >[!TIP]
   > Můžete také zadat název skupiny do filtru *Cíl,* který výsledky vymezí.

3. Vyberte položku v seznamu, abyste viděli podrobnosti o tom, co se změnilo. V části *Upravené vlastnosti* jsou uvedeny staré i nové hodnoty pro přiřazení licence.

Zde je příklad nedávných změn skupinové licence s podrobnostmi:

![Změny skupinové licence na snímek obrazovky](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Zjistit, kdy byly zahájeny a dokončeny změny skupiny

Když se licence změní ve skupině, Azure AD začne používat změny pro všechny uživatele.

1. Chcete-li zjistit, kdy skupiny začaly zpracovávat, nastavte filtr **Aktivita** na *Možnost Začít používat skupinovou licenci pro uživatele*. Všimněte si, že objekt actor pro operaci je *Microsoft Azure AD Group-Based Licensing* – systémový účet, který se používá ke spuštění všech změn licence skupiny.
   >[!TIP]
   > Kliknutím na položku v seznamu zobrazíte pole *Změněné vlastnosti* – zobrazí se změny licencí, které byly vyzvednuty ke zpracování. To je užitečné, pokud jste provedli více změn ve skupině a nejste si jisti, která z nich byla zpracována.

2. Podobně chcete-li zjistit, kdy skupiny dokončení zpracování, použijte hodnotu filtru *Dokončit použití skupiny na základě licence pro uživatele*.
   > [!TIP]
   > V tomto případě *pole Upravené vlastnosti* obsahuje souhrn výsledků – to je užitečné pro rychlou kontrolu, pokud zpracování vedlo k chybám. Ukázkový výstup:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Chcete-li zobrazit úplný protokol o tom, jak byla skupina zpracována, včetně všech uživatelských změn, nastavte následující filtry:
   - **Iniciováno (actor)**: "Licencování na základě skupiny Microsoft Azure AD"
   - **Rozsah dat** (volitelné): vlastní rozsah, pokud znáte spuštění a dokončení zpracování určité skupiny

Tento ukázkový výstup ukazuje začátek zpracování, všechny výsledné změny uživatele a dokončení zpracování.

![Změny skupinové licence na snímek obrazovky](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknutím na položky související se *změnou uživatelské licence* se zobrazí podrobnosti o změnách licencí, které se vztahují na jednotlivé uživatele.

## <a name="deleting-a-group-with-an-assigned-license"></a>Odstranění skupiny s přiřazenou licencí

Není možné odstranit skupinu s přiřazenou aktivní licencí. Správce by mohl odstranit skupinu, která si neuvědomuje, že způsobí odebrání licencí uživatelům – z tohoto důvodu požadujeme, aby byly všechny licence nejprve odebrány ze skupiny, než budou moci být odstraněny.

Při pokusu o odstranění skupiny na webu Azure Portal ![se může zobrazit upozornění na chybu, jako je toto: Odstranění skupiny snímek obrazovky se nezdařilo](./media/licensing-group-advanced/groupdeletionfailed.png)

Přejděte na kartu **Licence** ve skupině a zjistěte, zda jsou přiřazeny nějaké licence. Pokud ano, odeberte tyto licence a zkuste skupinu odstranit znovu.

Podobné chyby se mohou zobrazit při pokusu o odstranění skupiny prostřednictvím rozhraní PowerShell nebo Graph API. Pokud používáte skupinu synchronizované z místního prostředí, Azure AD Connect může také hlásit chyby, pokud se nedaří odstranit skupinu ve službě Azure AD. Ve všech těchto případech zkontrolujte, zda jsou skupině přiřazeny nějaké licence, a nejprve je odeberte.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

Pokud používáte skupinové licencování, je vhodné se seznámit s následujícím seznamem omezení a známých problémů.

- Licencování na základě skupin aktuálně nepodporuje skupiny, které obsahují další skupiny (vnořené skupiny). Pokud použijete licenci pro vnořenou skupinu, použije se jenom pro bezprostřední členy této skupiny na první úrovni.

- Tuto funkci lze použít pouze se skupinami zabezpečení a se skupinami Office 365, které mají funkci securityEnabled=TRUE.

- [Centrum pro správu Microsoftu 365](https://admin.microsoft.com) aktuálně nepodporuje skupinové licencování. Pokud uživatel zdědí licenci ze skupiny, zobrazí se tato licence na portálu pro správu Office jako běžná uživatelská licence. Pokud se pokusíte tuto licenci upravit nebo licenci odebrat, portál vrátí chybovou zprávu. Zděděné skupinové licence nelze upravovat přímo pro uživatele.

- Pokud jsou licence přiřazeny nebo změněny pro velkou skupinu (například 100 000 uživatelů), může to mít vliv na výkon. Konkrétně objem změn generovaných automatizací Azure AD může negativně ovlivnit výkon synchronizace adresářů mezi Azure AD a místními systémy.

- Pokud spravujete členství uživatelů pomocí dynamických skupin, zkontrolujte, jestli je daný uživatel součástí skupiny, což je k přiřazení licence nezbytné. V opačném případě [zkontrolujte stav zpracování pravidla členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) dané dynamické skupiny.

- V určitých situacích s vysokým zatížením může zpracování změn licencí pro skupiny nebo změny členství ve skupinách s existujícími licencemi trvat dlouho. Pokud se zobrazí zpracování skupiny o velikosti 60 tisíc uživatelů nebo méně, [otevřete lístek podpory,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) který nám umožní prošetřit. 

- Automatizace správy licencí automaticky nereaguje na všechny typy změn v prostředí. Například je možné, že vám došidovaly licence, což způsobilo, že někteří uživatelé byli v chybovém stavu. Chcete-li uvolnit počet volných míst, můžete odebrat některé přímo přiřazené licence od jiných uživatelů. Systém však automaticky nereaguje na tuto změnu a opravuje uživatele v tomto chybovém stavu.

  Jako řešení těchto typů omezení můžete přejít do okna **Skupiny** ve službě Azure AD a kliknout na **znovu zpracovat**. Tento příkaz zpracuje všechny uživatele v této skupině a pokud možno vyřeší chybové stavy.

## <a name="next-steps"></a>Další kroky

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Co je skupinové licencování ve službě Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrovat uživatele mezi licencemi produktů pomocí skupinových licencí ve službě Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
