---
title: Access Control a Privileged Identity Management na základě rolí v Austrálii v Azure
description: Pokyny k implementaci Access Control na základě rolí a Privileged Identity Management v rámci australské oblasti pro splnění konkrétních požadavků na zásady australské vlády, předpisy a právní předpisy.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982679"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Access Control na základě rolí (RBAC) a Privileged Identity Management (PIM)

Správa oprávnění správce je důležitým krokem při zajišťování zabezpečení v rámci jakéhokoli IT prostředí. Omezení oprávnění správce prostřednictvím použití minimálního zabezpečení oprávnění je požadavek na [acsc ISM](https://acsc.gov.au/infosec/ism/index.htm) a formuláře, které jsou součástí [acsc základního 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) seznamu doporučení zabezpečení.

Společnost Microsoft poskytuje sadu ovládacích prvků pro implementaci za běhu a stačí mít k dispozici pouze přístup v rámci Microsoft Azure. Porozumění těmto ovládacím prvkům je nezbytné pro efektivní stav zabezpečení v cloudu. Tato příručka poskytuje přehled o samotných ovládacích prvcích a klíčové faktory návrhu při jejich implementaci.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

Access Control na základě rolí je centrální Správa přístupu ke všem prostředkům v rámci Microsoft Azure a správě Azure Active Directory (Azure AD). RBAC se dá implementovat souběžně s řadou doplňkových funkcí dostupných v Azure. Tento článek se zaměřuje na implementaci efektivní RBAC pomocí Skupiny pro správu Azure, Azure Active Directory skupin a Azure Privileged Identity Management (PIM).

V případě vysoké úrovně vyžaduje implementace RBAC tři komponenty:

![RBAC – přehled](media/rbac-overview.png)

* **Objekty zabezpečení**: Objekt zabezpečení může být některý z následujících: uživatel, skupina, [objekty služby](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)nebo [spravovaná identita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Objektům zabezpečení by se měla přiřadit oprávnění pomocí skupin Azure Active Directory.

* **Definice rolí**: Definice role, označovaná také jako role, je kolekce oprávnění. Tato oprávnění definují operace, které mohou být provedeny pomocí objektů zabezpečení přiřazených k definici role. Tuto funkci poskytují role prostředků Azure a Azure Active Directory role správců. Azure obsahuje sadu předdefinovaných rolí (odkazů), které se dají rozšířit o vlastní role.

* **Rozsah**: Obor je sada prostředků Azure, na které se vztahuje definice role. Role Azure je možné přiřadit k prostředkům Azure pomocí Azure Skupiny pro správu.

Tyto tři komponenty kombinují přístup k objektům zabezpečení, které jsou definované v definicích rolí, pro všechny prostředky, které spadají do oboru Azure Skupiny pro správu, se označuje jako přiřazení role. K objektu zabezpečení lze přiřadit více definic rolí a k jednomu oboru lze přiřadit více objektů zabezpečení.

### <a name="azure-active-directory-groups"></a>Skupiny Azure Active Directory

Při přiřazování oprávnění jednotlivcům nebo týmům, kdykoli je to možné, by přiřazení mělo být propojeno se skupinou Azure Active Directory a není přiřazeno přímo k danému uživateli. Jedná se o stejný doporučený postup, který je zděděný z místních implementací služby Active Directory. V případě, že je možné vytvořit skupiny Azure Active Directory pro každý tým, doplňují logickou strukturu Skupiny pro správu Azure, kterou jste vytvořili.

V případě hybridního cloudu se můžou místní skupiny zabezpečení služby Active Directory Windows serveru synchronizovat s vaší instancí Azure Active Directory. Pokud jste už implementovali RBAC v místním prostředí pomocí těchto skupin zabezpečení služby Active Directory Windows serveru, můžete tyto skupiny po synchronizaci použít k implementaci služby RBAC pro prostředky Azure. V opačném případě se vaše cloudové prostředí může zobrazit jako čistá tiskárna pro návrh a implementaci robustního plánu správy oprávnění sestaveného kolem vaší Azure Active Directory implementace.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Role prostředků Azure versus Azure Active Directory role správců

Microsoft Azure nabízí širokou škálu integrovaných rolí pro [prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) a [Azure Active Directory správu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Oba typy rolí poskytují konkrétní podrobný přístup k prostředkům Azure nebo správcům služby Azure AD. Je důležité si uvědomit, že role prostředků Azure nejde použít k poskytnutí přístupu pro správu k Azure AD a rolím Azure AD neposkytuje konkrétní přístup k prostředkům Azure.

Některé příklady typů přístupu, které je možné přiřadit k prostředku Azure pomocí předdefinované role:

* Jednomu uživateli můžete povolit správu virtuálních počítačů v předplatném a jinému uživateli správu virtuálních sítí.
* Skupině DBA můžete povolit správu databází SQL v předplatném.
* Uživateli můžete povolit správu všech prostředků ve skupině prostředků, například virtuálních počítačů, webů a podsítí.
* Aplikaci můžete povolit přístup ke všem prostředkům ve skupině prostředků.

Příklady typů přístupu, které je možné přiřadit ke správě služby Azure AD:

* Umožněte pracovníkům technické podpory resetovat hesla uživatelů.
* Umožněte zaměstnancům pozvat externí uživatele do instance Azure AD pro spolupráci B2B.
* Umožnění administrativních pracovníků oprávnění ke čtení pro přihlášení a audit sestav
* Umožněte zaměstnancům spravovat všechny uživatele a skupiny, včetně resetování hesel.

Je důležité, abyste porozuměli úplnému seznamu povolených akcí, které poskytuje integrovaná role, aby se zajistilo, že se neudělí neoprávněný přístup. Seznam předdefinovaných rolí a přístup, které poskytují, se neustále vyvíjí. úplný seznam rolí a jejich definic si můžete prohlédnout v dokumentaci výše nebo pomocí rutiny Azure PowerShell:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

nebo příkaz Azure CLI:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

V případě potřeby je také možné vytvořit vlastní role prostředků Azure. Tyto vlastní role se dají vytvořit v Azure Portal, prostřednictvím PowerShellu nebo pomocí Azure CLI. Při vytváření vlastních rolí je důležité zajistit, aby účel role byl jedinečný a aby její funkce ještě nebyla poskytnuta stávající rolí prostředků Azure. Tím se snižuje průběžná složitost správy a snižuje riziko, že objekty zabezpečení získávají nepotřebná oprávnění. Příkladem je vytvoření vlastní role prostředku Azure, která se nachází mezi integrovanými rolemi prostředků Azure, přispěvatelem virtuálních počítačů a přihlášením Správce virtuálního počítače.

Vlastní role může být založená na stávající roli přispěvatele, která uděluje následující přístup:

| Prostředek Azure | Úroveň přístupu |
| --- | --- |
| Virtuální počítače | Může spravovat, ale nemůže mít přístup. |
| Virtual Network připojená k virtuálnímu počítači | Nejde získat přístup. |
| Úložiště připojené k virtuálnímu počítači | Nejde získat přístup. |
|

Vlastní role by mohla zachovat tento základní přístup, ale uživatelům, kteří mají určitá základní oprávnění, můžou změnit konfiguraci sítě virtuálních počítačů.

Role prostředků Azure mají také výhodu, že je možné je přiřadit k prostředkům prostřednictvím Azure Skupiny pro správu.

### <a name="azure-management-groups"></a>Skupiny pro správu Azure

Službu Azure Skupiny pro správu může organizace využít ke správě přiřazení rolí ke všem předplatným a jejich prostředkům v rámci architektury Azure. Azure Skupiny pro správu jsou navržené tak, aby vám umožnily vytvářet hierarchie pro správu, včetně možnosti mapovat organizační strukturu hierarchicky v rámci Azure. Vytváření organizačních jednotek jako samostatných logických entit umožňuje v rámci organizace použít oprávnění na základě specifických požadavků každého týmu. Azure Skupiny pro správu lze použít k definování hierarchie správy až na šest úrovní.

![Skupiny pro správu](media/management-groups.png)

Azure Skupiny pro správu jsou namapovány na předplatná Azure v rámci architektury Azure. Díky tomu může organizace oddělení prostředků Azure, které patří do konkrétních obchodních jednotek, poskytovat úroveň detailního řízení jak pro správu nákladů, tak pro přiřazení oprávnění.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Společnost Microsoft implementovala za běhu (just-in-time) a stačí mít k dispozici (JEA) prostřednictvím Azure Privileged Identity Management. Tato služba umožňuje administrativním pracovníkům řídit, spravovat a monitorovat privilegovaný přístup k prostředkům Azure. PIM umožňuje, aby se objekty zabezpečení nastavily jako oprávněné pro roli administrativními pracovníky, což umožňuje uživatelům požádat o aktivaci role prostřednictvím Azure Portal nebo prostřednictvím rutin prostředí PowerShell. Ve výchozím nastavení se přiřazení rolí dá aktivovat po dobu mezi 1 a 72 hodinami. V případě potřeby může uživatel požádat o rozšíření přiřazení role a existuje možnost, že přiřazení role bude trvalé. Požadavek na službu Multi-Factor Authentication se dá vyhovět i v případě, že si uživatelé vyžádají aktivaci svých oprávněných rolí. Jakmile vyprší přidělená doba trvání aktivace role, objekt zabezpečení již nemá privilegovaný přístup udělený rolí.

Použití PIM zabraňuje problémům s přiřazením běžných oprávnění, ke kterým může dojít v prostředích, která nepoužívají přístup za běhu, nebo neprovádí běžné audity přiřazení oprávnění. Jednou z běžných potíží je přiřazení zvýšených oprávnění, která jsou zapomenout a zbývající na konci, po dokončení úlohy vyžadující zvýšená oprávnění. Dalším problémem je šíření zvýšených oprávnění v rámci prostředí prostřednictvím klonování přístupu přiřazeného k objektu zabezpečení při konfiguraci jiných podobných objektů zabezpečení.

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

Při navrhování strategie RBAC s cílem vynucení minimálního zabezpečení oprávnění je potřeba zvážit tyto požadavky na zabezpečení:

* Žádosti o privilegovaný přístup se ověřují.
* Oprávnění správce se omezují na minimální přístup, který je nezbytný k provádění určitých povinností.
* Oprávnění správce se omezují na minimální dobu potřebnou k provedení určitých povinností.
* Provádějí se pravidelné kontroly udělených oprávnění správce.

Proces návrhu strategie RBAC bude vyžadovat Detailní přezkoumání obchodních funkcí, aby bylo možné pochopit rozdíl v přístupu mezi různými obchodními rolemi a typem a frekvencí práce, která vyžaduje zvýšená oprávnění. Rozdíl mezi funkcí operátora zálohování, správcem zabezpečení a auditorem bude vyžadovat různé úrovně přístupu v různých časech s různou úrovní průběžné kontroly.

## <a name="validate-requests-for-access"></a>Ověření žádostí pro přístup

Zvýšená oprávnění musí být explicitně schválena. V takovém případě je nutné vyvinout schvalovací proces a příslušné zaměstnance zodpovědné za ověření, že všechny žádosti o další oprávnění jsou legitimní. Privileged Identity Management poskytuje více možností pro schválení přiřazení role. Žádost o aktivaci role se dá nakonfigurovat tak, aby povolovala schválení svým držitelem, nebo aby se mohla ověřit a vyžadovat, aby pojmenovaná schvalovatelé ručně zkontrolovali a schválili všechny žádosti o aktivaci role. Žádosti o aktivaci je taky možné nakonfigurovat tak, aby vyžadovaly další podpůrné informace, jako jsou třeba čísla lístků.

### <a name="restrict-privilege-based-on-duties"></a>Omezení oprávnění na základě povinností

Omezení úrovně oprávnění udělených objektům zabezpečení je velmi důležité, protože přiřazení oprávnění je pro běžný vektor útoku na zabezpečení IT. Typy prostředků, které spravujete, a příslušné týmy, musí být vyhodnoceny, aby bylo možné přiřadit minimální úroveň oprávnění, která jsou požadována pro každodenní denní poplatky. Další oprávnění, která přesahují rámec těch, které jsou potřeba pro denní povinnosti, by se měla udělit jenom po dobu potřebnou k provedení konkrétního úkolu. Příkladem může být přispěvatel, který bude mít přístup ke Správci zákazníka, ale umožní jim požádat o oprávnění vlastníka pro prostředek Azure na konkrétní úkol vyžadující dočasný přístup na nejvyšší úrovni.

Tím se zajistí, že každý správce má pro nejkratší dobu jenom zvýšený přístup. Dodržování těchto postupů snižuje celkové nároky na útok pro jakékoli organizace IT infrastruktury.

### <a name="regular-evaluation-of-administrative-privilege"></a>Pravidelné vyhodnocení oprávnění správce

Je důležité, aby objekty zabezpečení v rámci prostředí byly rutinně auditovány, aby bylo zajištěno, že je aktuálně přiřazena správná úroveň oprávnění. Microsoft Azure poskytuje několik způsobů, jak auditovat a vyhodnocovat oprávnění přiřazená objektům zabezpečení Azure. Privileged Identity Management umožňuje administrativním pracovníkům pravidelně provádět "kontroly přístupu" rolí udělených objektům zabezpečení. Je možné provést kontrolu přístupu, která bude auditovat přiřazení role prostředku Azure a Azure Active Directory přiřazení administrativní role. Kontrola přístupu se dá nakonfigurovat s následujícími vlastnostmi:

* **Zkontrolujte název a zkontrolujte počáteční a koncové datum**: Kontroly by měly být nakonfigurované tak, aby byly pro jmenované uživatele dostatečně dlouhé, aby je dokončily.

* **Role, která se má zkontrolovat**: Každá kontrola přístupu se zaměřuje na jednu roli Azure.

* **Jmenovaní kontroloři**: Existují tři možnosti, jak provést kontrolu. Tuto kontrolu můžete přiřadit někomu jinému, abyste ji mohli provést sami, nebo můžete nechat každého uživatele, aby si zkontrolovali vlastní přístup.

* **Vyžadovat, aby uživatelé zadali důvod pro přístup**: Uživatelé mohou být požádáni, aby při dokončování kontroly přístupu zadali důvod pro zachování jejich úrovně oprávnění.

Průběh probíhajících kontrol přístupu je možné sledovat kdykoli prostřednictvím řídicího panelu v Azure Portal. Přístup k revizi role zůstane beze změny, dokud se kontrola přístupu nedokončí. Je také možné auditovat [](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) všechna přiřazení a aktivace uživatelů PIM v rámci navrženého časového období.

## <a name="next-steps"></a>Další postup

Přečtěte si článek [monitorování systému v Austrálii Azure](system-monitor.md).
