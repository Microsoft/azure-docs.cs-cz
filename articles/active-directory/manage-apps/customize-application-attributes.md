---
title: Přizpůsobování mapování atributů Azure AD | Microsoft Docs
description: Přečtěte si, jaké mapování atributů pro aplikace SaaS v Azure Active Directory způsob jejich úprav, abyste mohli řešit své obchodní potřeby.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240995"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Přizpůsobení uživatelského zřizování atributů – mapování pro SaaS aplikace v Azure Active Directory

Microsoft Azure AD poskytuje podporu pro zřizování uživatelů pro aplikace SaaS třetích stran, jako jsou Salesforce, G Suite a další. Pokud povolíte zřizování uživatelů pro aplikaci SaaS třetí strany, Azure Portal řídí její hodnoty atributu prostřednictvím mapování atributů.

K dispozici je předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a všemi uživatelskými objekty aplikace v SaaS. Některé aplikace spravují jiné typy objektů spolu s uživateli, jako jsou skupiny.

Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů.

## <a name="editing-user-attribute-mappings"></a>Úpravy atributu uživatele – mapování

Pomocí těchto kroků můžete získat přístup k funkci **mapování** zřizování uživatelů:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com).
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte libovolnou aplikaci a načtěte její podokno Správa aplikací, kde můžete zobrazit sestavy a spravovat nastavení aplikace.
1. Vyberte **zřizování** pro správu nastavení zřizování uživatelských účtů pro vybranou aplikaci.
1. Rozbalením **mapování** můžete zobrazit a upravit atributy uživatele, které budou tok mezi Azure AD a cílovou aplikací. Pokud cílová aplikace tuto aplikaci podporuje, Tato část vám umožní volitelně nakonfigurovat zřizování skupin a uživatelských účtů.

   ![Použití mapování k zobrazení a úpravě atributů uživatele](./media/customize-application-attributes/21.png)

1. Vyberte konfiguraci **mapování** , aby se otevřela obrazovka **mapování souvisejících atributů** . Některá mapování atributů vyžaduje aplikace SaaS, aby fungovala správně. U vyžadovaných atributů není dostupná funkce **Odstranit** .

   ![Použití mapování atributů ke konfiguraci mapování atributů pro aplikace](./media/customize-application-attributes/22.png)

   Na tomto snímku obrazovky vidíte, že atribut **username** spravovaného objektu v Salesforce se naplní hodnotou **userPrincipalName** odkazovaného objektu Azure Active Directory.

1. Vyberte existující **mapování atributů** a otevřete tak obrazovku **Upravit atribut** . Tady můžete upravit atributy uživatele, které se budou přesměrovat mezi Azure AD a cílovou aplikací.

   ![Pro úpravu atributů uživatele použijte úpravu atributu](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Porozumění typům mapování atributů

Pomocí mapování atributů můžete řídit způsob naplnění atributů v aplikaci SaaS třetí strany.
Podporovány jsou čtyři různé typy mapování:

- **Direct** – cílový atribut se naplní hodnotou atributu propojeného objektu ve službě Azure AD.
- **Constant** – cílový atribut je naplněný konkrétním řetězcem, který jste zadali.
- **Výraz** – cílový atribut je vyplněný na základě výsledku výrazu podobného skriptu.
  Další informace najdete v tématu [zápis výrazů pro mapování atributů v Azure Active Directory](functions-for-customizing-application-data.md).
- **None** – cílový atribut zůstane beze změny. Nicméně pokud cílový atribut je někdy prázdný, je vyplněný výchozí hodnotou, kterou určíte.

Spolu s těmito čtyřmi základními typy jsou vlastní mapování atributů podporují koncept volitelného přiřazení **výchozí** hodnoty. Výchozí přiřazení hodnoty zajišťuje, že cílový atribut je naplněný hodnotou, pokud neexistuje hodnota ve službě Azure AD nebo v cílovém objektu. Nejběžnější konfigurací je ponecháno toto prázdné.

### <a name="understanding-attribute-mapping-properties"></a>Porozumění vlastnostem mapování atributů

V předchozí části jste již zavedli vlastnost typu mapování atributů.
Spolu s touto vlastností také mapování atributů podporuje následující atributy:

- **Zdrojový atribut** – atribut uživatele ze zdrojového systému (příklad: Azure Active Directory).
- **Cílový atribut** – atribut uživatele v cílovém systému (příklad: ServiceNow).
- **Porovnává objekty pomocí tohoto atributu** – určuje, jestli se má toto mapování použít k jednoznačné identifikaci uživatelů mezi zdrojovým a cílovým systémem. Obvykle se nastavuje u atributu userPrincipalName nebo mail v Azure AD, který je obvykle namapován na pole username v cílové aplikaci.
- **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.
- **Použít toto mapování**
  - **Vždy** – použít toto mapování na akce vytvoření a aktualizace uživatele
  - **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele.

## <a name="editing-group-attribute-mappings"></a>Úprava atributu skupiny – mapování

Vybraný počet aplikací, například ServiceNow, box a G Suite, podporuje možnost zřídit objekty skupiny a uživatelské objekty. Skupinové objekty můžou obsahovat vlastnosti skupiny, jako jsou zobrazovaná jména a e-mailové aliasy, spolu se členy skupiny.

![Příklad ukazuje ServiceNow se zřízené objekty skupiny a uživatele.](./media/customize-application-attributes/24.png)

Zřizování skupin se dá volitelně zapnout nebo vypnout tak, že v části **mapování**vyberete mapování skupiny a na obrazovce **mapování atributů** **povolíte** požadované možnosti.

Atributy zřízené jako součást objektů skupiny lze přizpůsobit stejným způsobem jako objekty uživatele popsané dříve. 

> [!TIP]
> Zřizování skupinových objektů (vlastností a členů) je jedinečný koncept z [přiřazování skupin](assign-user-or-group-access-portal.md) do aplikace. Je možné přiřadit skupinu k aplikaci, ale pouze zřídit objekty uživatele obsažené ve skupině. Zřizování úplných objektů skupiny není vyžadováno pro používání skupin v přiřazeních.

## <a name="editing-the-list-of-supported-attributes"></a>Úpravy seznamu podporovaných atributů

Atributy uživatele podporované pro danou aplikaci jsou předem nakonfigurované. Většina rozhraní API pro správu uživatelů aplikace nepodporuje zjišťování schématu. Proto služba zřizování Azure AD nemůže dynamicky generovat seznam podporovaných atributů tím, že provádí volání do aplikace.

Některé aplikace ale podporují vlastní atributy a služba zřizování Azure AD může číst a zapisovat do vlastních atributů. Pokud chcete do Azure Portal zadat jejich definice, zaškrtněte políčko **Zobrazit pokročilé možnosti** v dolní části obrazovky **mapování atributů** a potom vyberte **Upravit seznam atributů pro** vaši aplikaci.

Mezi aplikace a systémy, které podporují přizpůsobení seznamu atributů, patří:

- Salesforce
- ServiceNow
- Den
- Azure Active Directory (podporují se[výchozí atributy služby Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) a vlastní rozšíření adresáře)
- Aplikace, které podporují [SCIM 2,0](https://tools.ietf.org/html/rfc7643), ve kterých je potřeba přidat atributy definované ve [schématu Core](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> Úpravy seznamu podporovaných atributů se doporučují jenom správcům, kteří přizpůsobili schéma svých aplikací a systémů, a mají první znalosti o tom, jak jsou vlastní atributy definované. To někdy vyžaduje znalost rozhraní API a vývojářských nástrojů poskytovaných aplikací nebo systémem.

Při úpravách seznamu podporovaných atributů jsou k dispozici následující vlastnosti:

- **Název** – systémový název atributu, jak je definovaný ve schématu cílového objektu.
- **Typ** – typ dat, který atributy ukládají, jak je definováno ve schématu cílového objektu, což může být jeden z následujících typů:
  - *Binární* atribut obsahuje binární data.
  - Atribut *Boolean* -Attribute obsahuje hodnotu true nebo false.
  - *Hodnota DateTime* -atribut obsahuje řetězec data.
  - *Celočíselný* atribut obsahuje celé číslo.
  - *Odkaz* -atribut obsahuje ID, které odkazuje na hodnotu uloženou v jiné tabulce cílové aplikace.
  - *Řetězcový* atribut obsahuje textový řetězec.
- **Primární klíč?** – Určuje, zda je atribut definován jako pole primárního klíče ve schématu cílového objektu.
- **Požadovanou?** – Určuje, zda je atribut požadován k naplnění cílové aplikace nebo systému.
- **Více hodnot?** – Zda atribut podporuje více hodnot.
- **Přesný případ?** – Zda jsou hodnoty atributů vyhodnocovány způsobem, který rozlišuje velká a malá písmena.
- **Výraz rozhraní API** – nepoužívejte, pokud k tomu nejste vyzváni v dokumentaci pro konkrétní zřizovací konektor (například Workday).
- **Odkazovaný atribut objektu** – Pokud se jedná o atribut typu odkazu, pak v této nabídce můžete vybrat tabulku a atribut v cílové aplikaci, která obsahuje hodnotu přidruženou k atributu. Například pokud máte atribut s názvem "Department", jehož uložená hodnota odkazuje na objekt v samostatné tabulce "oddělení", vyberte "Departments.Name". Referenční tabulky a pole primárních ID podporovaná pro danou aplikaci jsou předem nakonfigurované a v současné době není možné upravovat pomocí Azure Portal, ale můžete je upravovat pomocí [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Chcete-li přidat nový atribut, posuňte se na konec seznamu podporovaných atributů, naplňte pole výše pomocí zadaných vstupů a vyberte **Přidat atribut**. Po dokončení přidávání atributů vyberte **Uložit** . Pak je nutné znovu načíst kartu **zřizování** pro nové atributy, které budou k dispozici v editoru mapování atributů.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Obnovení výchozích atributů a mapování atributů

Pokud potřebujete začít znovu a obnovit stávající mapování zpátky do výchozího stavu, můžete zaškrtnout políčko **Obnovit výchozí mapování** a uložit konfiguraci. Tím se nastaví všechna mapování, jako by byla aplikace právě přidána do vašeho tenanta Azure AD z Galerie aplikací.

Výběrem této možnosti efektivně vynutíte opětovnou synchronizaci všech uživatelů, když je služba zřizování spuštěná.

> [!IMPORTANT]
> Před vyvoláním této možnosti důrazně doporučujeme, aby byl **stav zřizování** nastavený na **vypnuto** .

## <a name="what-you-should-know"></a>Co byste měli znát

- Microsoft Azure AD poskytuje efektivní implementaci procesu synchronizace. V inicializovaném prostředí jsou během synchronizačního cyklu zpracovávány pouze objekty vyžadující aktualizace.
- Aktualizace mapování atributů má vliv na výkon synchronizačního cyklu. Aktualizace konfigurace mapování atributů vyžaduje opětovné vyhodnocení všech spravovaných objektů.
- Doporučeným postupem je udržet počet po sobě jdoucích změn na mapování atributů minimálně.
- Přidání atributu Photo, který se má zřídit pro aplikaci, není dnes podporované, protože nemůžete zadat formát pro synchronizaci fotografie. Můžete požádat o funkci na [hlas uživatele](https://feedback.azure.com/forums/169401-azure-active-directory) .
- Atribut IsSoftDeleted je často součástí výchozího mapování pro aplikaci. IsSoftdeleted může mít hodnotu true v jednom ze čtyř scénářů (uživatel je mimo rozsah z důvodu nepřiřazené k aplikaci, uživatel je mimo rozsah, protože nesplňuje filtr oborů, uživatel byl v Azure AD neúspěšně odstraněn nebo vlastnost AccountEnabled je nastavená na hodnotu false.  na uživateli). 
- Služba zřizování Azure AD nepodporuje zřizování hodnot null.

## <a name="next-steps"></a>Další kroky

- [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md)
- [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
- [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
- [Použití SCIM k povolení automatického zřizování uživatelů a skupin od Azure Active Directory k aplikacím](use-scim-to-provision-users-and-groups.md)
- [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)
