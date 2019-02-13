---
title: Na základě skupin licencí další scénáře – Azure Active Directory | Dokumentace Microsoftu
description: Další scénáře pro licencování na základě skupin Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7b194b8aeb6df54814069a3acd2e2f0f3ad1a13
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180670"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scénáře, omezeních a známých problémech použití skupin pro správu licencování v Azure Active Directory

Použijte následující informace a příklady pro získání rozsáhlejšími znalostmi licencování na základě skupin Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Místo použití

Některé služby Microsoft nejsou dostupné ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, Správce musí mít **místo využívání** vlastnost na uživatele. V [na webu Azure portal](https://portal.azure.com), můžete zadat v **uživatele** &gt; **profilu** &gt; **nastavení**.

Přiřazení licencí skupiny zdědí všechny uživatele bez zadaného místa využití umístění adresáře. Pokud máte uživatele v několika umístěních, ujistěte se, aby to odrážel správně v uživatelské objekty před přidáním uživatelů do skupin licencí.

> [!NOTE]
> Přiřazení licencí skupiny se nikdy nemění existující hodnoty umístění využití určitého uživatele. Doporučujeme vždy nastavit místo využívání jako součást vytváření toku uživatele v Azure AD (např. pomocí AAD Connect konfigurace) –, který poskytuje výsledek přiřazení licencí se vždy správný, a uživatelé neobdrží služby v umístění, které nejsou povoleny.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Použití licencování na základě skupiny s dynamickými skupinami

Můžete použít licencování na základě skupiny s žádnou skupinu zabezpečení, což znamená, že je možné kombinovat s dynamických skupin Azure AD. Dynamické skupiny spustit pravidla pro uživatele objektu atributů, které mají automaticky přidávat a odebírat uživatele ze skupin.

Můžete například vytvořit dynamické skupiny pro některé sadu produktů, které chcete přiřadit uživatelům. Každá skupina je vyplněn pravidla přidávání uživatelů podle jejich atributy a každá skupina přiřazena licence, které chcete, aby mohli dostávat. Můžete přiřadit atribut v místním a synchronizovat s Azure AD, nebo je můžete spravovat atribut přímo v cloudu.

Přiřazení licencí uživateli krátce po se přidají do skupiny. Při změně atributu uživatel odejde ze skupiny a licencí se odeberou.

### <a name="example"></a>Příklad:

Podívejte se na příklad z místní řešení pro správu identit, která určuje, na které uživatelé by měli mít přístup k webovým službám Microsoftu. Používá **extensionAttribute1** k uložení řetězcovou hodnotu představující licence musí mít uživatel. Azure AD Connect synchronizuje se službou Azure AD.

Uživatelé mohou potřebovat licenci ale nejsou jiné, nebo obojí. Tady je příklad, ve kterém je distribuován Office 365 Enterprise E5 a Enterprise Mobility + Security (EMS) licencí pro uživatele ve skupinách:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: základní služby

![Snímek obrazovky z Office 365 Enterprise E5 základní služby](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencovaní uživatelé

![Snímek obrazovky s Enterprise Mobility + Security licencovaní uživatelé](./media/licensing-group-advanced/o365-e5-licensed-users.png)

V tomto příkladu úpravě jednoho uživatele a nastavení jejich extensionAttribute1 hodnotě `EMS;E5_baseservices;` Pokud chcete, aby uživatel měl obě licence. Provedete tuto úpravu na místě. Po změně synchronizuje s cloudem, uživatel je automaticky přidán do obou skupin a přiřazení licencí.

![Snímek obrazovky ukazující, jak nastavit extensionAttribute1 uživatele](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Buďte opatrní při úpravě pravidla členství ve stávající skupině. Při změně pravidlo členství ve skupině se znovu zhodnotí a uživatelé, kteří už neodpovídá nové pravidlo budou odebrané (uživatelé, kteří stále neshoduje s nové pravidlo nebude mít vliv během tohoto procesu). Tito uživatelé budou mít svoje licence odebraných během procesu, který může mít za následek výpadek služby, nebo v některých případech ke ztrátě dat.

> Pokud máte velké dynamické skupiny, které závisí na přiřazení licencí, vezměte v úvahu před použitím do hlavní skupiny ověřování žádnými většími změnami v menších testovací skupině.

## <a name="multiple-groups-and-multiple-licenses"></a>Více skupinami a více licencí

Uživatel může být členem více skupin licencí. Tady je pár věcí k uvážení:

- Může dojít k překrytí pro stejný produkt, více licencí a jejich výsledkem všechny povolené služby se použijí na uživatele. Následující příklad ukazuje dva skupiny licencí: *Základní služby E3* obsahuje služby foundation k nasazení, nejprve pro všechny uživatele. A *E3 rozšířené služby* obsahuje další služby (Swayi a plánovač) nasadit jenom pro některé uživatele. V tomto příkladu byl uživatel přidán do skupiny:

  ![Snímek obrazovky povolené služby](./media/licensing-group-advanced/view-enabled-services.png)

  V důsledku toho uživatel má 7 12 služeb v produktu povoleno při používání pouze jedna licence pro tento produkt.

- Výběr *E3* licence zobrazíte další podrobnosti, včetně informací o tom, které skupiny způsobil, jaké služby pro daného uživatele povoleno.

  ![Snímek obrazovky povolené služby podle skupin](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Přímé licence existovat současně s licencemi skupiny

Když uživatel licenci dědí ze skupiny, nedá ručně odebrat ani změnit přiřazení licencí ve vlastnostech daného uživatele. Změny musí být ve skupině a potom rozšíří na všechny uživatele.

Je však možné, přiřadit licenci na stejný produkt přímo na uživatele, kromě zděděnou licenci. Můžete povolit další služby z produktu pouze pro jednoho uživatele, aniž by to ovlivnilo ostatní uživatelé.

Přímo přiřazené licence je možné odebrat a nemají vliv na zděděné licence. Vezměte v úvahu uživatele, který dědí ze skupiny pro licence k Office 365 Enterprise E3.

1. Na začátku uživatel dědí licenci pouze z *základní služby E3* skupinu, která umožňuje čtyř plánů služeb, jak je znázorněno:

  ![Snímek obrazovky E3 skupiny povolené služby](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. Můžete vybrat **přiřadit** přímo přiřadit uživateli licenci E3. V takovém případě se chystáte zakázat všechny plány služby s výjimkou Yammer Enterprise:

  ![Snímek obrazovky přiřazení licence přímo na uživatele](./media/licensing-group-advanced/assign-license-to-user.png)

3. V důsledku toho uživatel dál používá pouze jednu licenci na produkt E3. Ale umožňuje přímé přiřazení Yammer Enterprise service pouze pro tohoto uživatele. Můžete zobrazit, které služby jsou povoleny ve srovnání s přímého přiřazení členství ve skupině:

  ![Snímek obrazovky zděděné oproti přímého přiřazení](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Při použití přímého přiřazení jsou povoleny následující operace:

  - Yammer Enterprise můžete vypnout v objektu user přímo. **Zapnuto/vypnuto** přepnout na obrázku byl povolen pro tuto službu, na rozdíl od jiných služeb přepínačů. Protože je služba zapnutá přímo na uživatele, může být upraveno.
  - Další služby je možné povolit stejně, jako součást přímo přiřazenou licenci.
  - **Odebrat** tlačítko slouží k odebrání přímých licencí od uživatele. Uvidíte, že teď má uživatel jenom zděděné skupinu licencí a zůstat zapnuté, jenom původní služby:

    ![Snímek obrazovky ukazující, jak odebrat přímého přiřazení](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Správa nové služby přidán do produktů
Pokud Microsoft přidá novou službu do produktu, povolí se ve výchozím nastavení ve všech skupinách, do které jste přiřadili licenci na produkt. Uživatelé ve vašem tenantovi, kteří se přihlásili k odběru oznámení o změnách produktu budou dostávat e-maily předem oznámení o dodatcích připravovanou službu.

Jako správce můžete zkontrolovat všechny skupiny, které změna ovlivňuje a provádějí akci, jako je například zakázat novou službu v každé skupině. Například pokud vytvoříte skupiny cílí na pouze určité služby pro nasazení, můžete návštěvě těchto skupin a ujistěte se, že všechny nově přidané, že je vypnuto.

Tady je příklad tohoto procesu může vypadat:

1. Původně byly přiřazeny *Office 365 Enterprise E5* produktu do několika skupin. Jedna z těchto skupin, volá se, *O365 E5 - Exchange pouze* bylo cílem, aby pouze *Exchange Online (plán 2)* služby jejích členů.

2. Oznámení jste dostali od Microsoftu, která se prodlouží se E5 produktů služeb - *Microsoft Stream*. Až tato služba bude k dispozici ve vašem tenantovi, máte následující:

3. Přejděte [ **Azure Active Directory > licence > všechny produkty** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) okna a vyberte *Office 365 Enterprise E5*a pak vyberte **licencované skupiny** zobrazíte seznam všech skupin s tímto produktem.

4. Klikněte na skupinu, kterou chcete zkontrolovat (v tomto případě *O365 E5 - Exchange pouze*). Tím se otevře **licence** kartu. Kliknutím na E5 licence, které se otevře okno výpis všech služeb povolených.
> [!NOTE]
> *Microsoft Stream* služby byl automaticky přidán a povoleno v této skupině, kromě *Exchange Online* služby:

  ![Snímek obrazovky s novou službu přidali do skupiny licencí](./media/licensing-group-advanced/manage-new-services.png)

5. Pokud chcete zakázat nová služba v této skupině, klikněte na tlačítko **zapnuto/vypnuto** přepnout vedle služby a klikněte na tlačítko **Uložit** potvrďte změny. Azure AD bude nyní zpracovat všechny uživatele ve skupině na použití změny; Každý nový uživatel přidán do skupiny nebude mít *Microsoft Stream* povolenou službu.

  > [!NOTE]
  > Uživatelé mohou mít i nadále službu pomocí některé jiné přiřazení licencí (jiné skupiny jsou členy nebo přiřazení přímých licencí) povolena.

6. V případě potřeby proveďte stejné kroky pro jiné skupiny s tímto produktem přiřazené.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Podívejte se, kdo má zděděné a přímé licence pomocí Powershellu
Skript prostředí PowerShell můžete kontrolovat, jestli uživatelé mají licenci přímo přiřadit nebo zděděno od skupiny.

1. Spustit `connect-msolservice` rutiny k ověřování a připojení k vašemu tenantovi.

2. `Get-MsolAccountSku` umožňuje zjistit všechny licence zřízené produktu v tenantovi.

  ![Snímek obrazovky rutiny Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Použití *AccountSkuId* hodnotu licence, které máte zájem se [tento skript Powershellu](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Vznikne seznam uživatelů, kteří mají tento licenční informace o přiřazování licencí.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Monitorování na základě skupin licencí pomocí protokolů auditu

Můžete použít [protokoly auditu Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) zobrazíte všechny aktivity související s licencováním na základě skupin, včetně:
- kdo změnil licence ve skupinách
- Při spuštění systému zpracování změny skupiny licencí, a po jeho dokončení
- jaké změny licence byly provedeny na uživatele v důsledku přiřazení licence skupině.

>[!NOTE]
> Protokoly auditu jsou k dispozici na většina oken v části Azure Active Directory na portálu. V závislosti na tom, kde můžete k nim přistupovat může být předem použitý k zobrazit pouze aktivit související s místní okno filtry. Pokud nevidíte nezískáte očekávané výsledky, prozkoumejte [možnosti filtrování](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) nebo přístup k protokolům auditu nefiltrované v rámci [ **Azure Active Directory > aktivita > protokoly auditu** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Zjistěte, kdo upravil skupinu licencí

1. Nastavte **aktivity** filtrovat *sadu skupiny licencí* a klikněte na tlačítko **použít**.
2. Výsledky budou zahrnovat všechny případy se nastavit nebo změnit na skupiny licencí.
>[!TIP]
> Můžete také zadat název skupiny v *cílové* filtr k určení rozsahu výsledky.

3. Klikněte na položku v zobrazení seznamu můžete zobrazit podrobnosti o co se změnilo. V části *upravit vlastnosti* staré a nové hodnoty pro přiřazení licence jsou uvedeny.

Tady je příklad nedávné změny skupiny licencí, s podrobnostmi:

![Změny licencí skupiny – snímek obrazovky](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Zjistit, kdy změny skupiny spuštění a dokončení zpracování

Pokud se na skupinu licencí, Azure AD se spustí aplikování změn pro všechny uživatele.

1. Zobrazíte zahájení zpracování skupiny nastavit **aktivity** filtrovat *Start použití skupinové licence pro uživatele*. Všimněte si, že je objekt actor pro operaci *Microsoft Azure AD na základě skupinu Licensing* -systémový účet, který se používá k provedení všech změn skupiny licencí.
>[!TIP]
> Klikněte na položku v seznamu zobrazíte *upravit vlastnosti* pole – zobrazuje změny licencí, které byly, vyberou se pro zpracování. To je užitečné, pokud jste provedli několik změn do skupiny a si nejste jisti, která byla zpracována.

2. Podobně po dokončení zpracování skupin zobrazíte pomocí hodnota filtru *ukončení použití skupinové licence pro uživatele*.
>[!TIP]
> V takovém případě *upravit vlastnosti* pole obsahuje souhrn výsledků – to je užitečné můžete rychle zjistit, pokud zpracování výsledkem nějaké chyby. Ukázkový výstup:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Pokud chcete zobrazit úplný protokol pro způsob zpracování skupiny, včetně změny všech uživatelů, nastavte následující filtry:
  - **Iniciátor (Actor)**: "Microsoft Azure AD na základě skupin licencí."
  - **Rozsah kalendářních dat** (volitelné): Pokud znáte konkrétní skupinu vlastní rozsah spuštění a dokončení zpracování

Tento ukázkový výstup ukazuje začátek zpracování, všechny výsledné změny uživatelů a dokončení zpracování.

![Změny licencí skupiny – snímek obrazovky](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknutím na položky související s *změna uživatelské licence* se zobrazí podrobnosti pro licenční provedené v jednotlivých uživatelů.

## <a name="deleting-a-group-with-an-assigned-license"></a>Odstraňuje se skupina s přiřazenou licenci.

Není možné odstranit skupinu s přiřazenou licenci aktivní. Správce může odstranit skupinu není si uvědomili, že by licence k odebrání z uživatelů – z tohoto důvodu, že požadujeme, aby všechny licence odebrat ze skupiny nejprve dříve, než je možné ji odstranit.

Při pokusu o odstranění skupiny na webu Azure Portal se může zobrazit oznámení o chybě takto: ![Nepovedlo se odstranit skupinu snímek obrazovky](./media/licensing-group-advanced/groupdeletionfailed.png)

Přejděte **licence** kartě ve skupině a jestli jsou všechny přiřazené licence. Pokud ano, odeberte tyto licence a zkuste to znovu odstranit skupinu.

Při pokusu o odstranění skupiny prostřednictvím prostředí PowerShell nebo rozhraní Graph API se může zobrazit podobné chyby. Pokud používáte skupiny synchronizované z místní, Azure AD Connect může také nahlásit chyby Pokud se nedaří odstranit skupinu ve službě Azure AD. V takových případech Ujistěte se, že zkontrolujte, jestli jsou všechny licence přiřazené ke skupině a nejprve odeberte.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

Pokud používáte licencování na základě skupin, je vhodné se seznámit s následující seznam omezení a známé problémy.

- Licencování pro skupiny aktuálně nepodporuje skupiny, které obsahují další skupiny (vnořené skupiny). Pokud použijete licenci pro vnořenou skupinu, použije se jenom pro bezprostřední členy této skupiny na první úrovni.

- Tuto funkci jde použít jenom pomocí skupin zabezpečení a skupiny Office 365, které mají securityEnabled = TRUE.

- [Portálu pro správu Office 365](https://portal.office.com ) aktuálně nepodporuje licencování na základě skupiny. Pokud uživatel licenci dědí ze skupiny, platnost této licence se zobrazí na portálu správy Office jako se vytvářejí běžné uživatelské licence. Pokud se pokusíte upravit licence nebo zkuste odebrat licenci, portálu vrátí chybovou zprávu. Licencí zděděných skupiny nelze změnit přímo na uživatele.

- Když licence jsou přiřazeny nebo upraveny pro velkou skupinu (například 100 000 uživatelů), může ovlivnit výkon. Konkrétně objemu změn generovaných automatizace Azure AD může mít negativní vliv na výkon vaší synchronizace adresářů mezi službami Azure AD a místními systémy.

- Pokud spravujete členství uživatelů pomocí dynamických skupin, zkontrolujte, jestli je daný uživatel součástí skupiny, což je k přiřazení licence nezbytné. V opačném případě [zkontrolujte stav zpracování pravidla členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule#check-processing-status-for-a-membership-rule) dané dynamické skupiny. 

- V některých situacích vysokého zatížení může trvat dlouhou dobu zpracování změny licence pro skupiny nebo změny členství ve skupinách se stávající licence. Pokud se zobrazí vaše změny trvat déle než 24 hodin pro zpracování skupiny velikost 60 tisíc uživatelů nebo méně, prosím [vytvořit lístek podpory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) umožňuje nám prozkoumat. 

- Automatizace správy licencí nereaguje automaticky pro všechny typy změn v prostředí. Například můžete pravděpodobně vyčerpala volné licence, způsobí někteří uživatelé se v chybovém stavu. Chcete uvolnit dostupné současného počtu můžete odebrat některé přímo přiřazené licence od jiných uživatelů. Systém však automaticky reagovat na tuto změnu a opravte uživatelé v tomto stavu chyba.

  Jako alternativní řešení pro tyto typy omezení, můžete přejít na **skupiny** okno ve službě Azure AD a klikněte na tlačítko **znovu zpracovat**. Tento příkaz zpracovává všechny uživatele v této skupině a řeší chybové stavy, pokud je to možné.

## <a name="next-steps"></a>Další postup

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Co je skupina založená na licencování v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
