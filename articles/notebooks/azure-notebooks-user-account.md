---
title: Přihlášení k poznámkovým blokům Azure Preview
description: Nakonfigurujte svůj uživatelský účet pro poznámkové bloky Azure pomocí účtu Microsoft nebo pracovního/školního účtu.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646292"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Váš uživatelský účet pro poznámkové bloky Azure Preview

Poznámkové bloky Azure můžete používat s uživatelským účtem nebo bez něj:

- Bez přihlášení můžete vytvářet a spouštět poznámkové bloky, ale nemůžete uchovávat poznámkové bloky nebo datové soubory jako součást projektů. Uživatelé, kteří obdrží odkaz na poznámkový blok Azure, například můžete vychutnat poznámkový blok bez nutnosti přihlášení.
- Po přihlášení si poznámkové bloky Azure uchovají všechny vaše projekty s vaším účtem. Přihlášení uživatelé mají také ID uživatele, které jim umožňuje sdílet své projekty a poznámkové bloky s ostatními.
- Když je účet používaný pro poznámkové bloky Azure také přidružený k předplatnému Azure, získáte další výhody, jako je spouštění poznámkových bloků na výkonnějších serverech, vytváření soukromých poznámkových bloků a udělování oprávnění poznámkovým blokům jednotlivým uživatelům.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Přihlášení k poznámkovým blokům Azure vyžaduje účet Microsoft nebo účet "Práce nebo škola". Při výběru příkazu **Přihlásit** se v pravé horní části stránky Poznámkové bloky se zobrazí výzva k zadání účtu:

![Příkaz pro přihlášení k poznámkovým blokům Azure](media/accounts/sign-in-command.png)

Veškerá práce, kterou v poznámkových blocích Azure děláte, je přidružená k účtu, který používáte k přihlášení. Každý účet musí mít také jedinečné ID uživatele ve vašem [uživatelském profilu](azure-notebooks-user-profile.md). V důsledku toho se můžete přihlásit k poznámkovým blokům Azure s různými účty, pokud potřebujete udržovat samostatné sady projektů a samostatných identit. Například každý člen týmu datové vědy může mít oba jednotlivé účty vedle jako účet sdílené skupiny, který používají k prezentaci své práce lidem mimo jejich společnost. Instruktoři, podobně, může udržovat účet pro jejich pedagogické role, která se liší od účtu používaného v externíporadenství nebo open-source práce.

## <a name="microsoft-accounts"></a>Účty Microsoft

Účty Microsoft se používají k přihlášení k libovolnému počtu produktů a služeb Microsoftu, jako jsou Windows, Azure, outlook.com, OneDrive a XBox Live. Pokud používáte některou z těchto služeb, je pravděpodobné, že už máte účet Microsoft, který můžete použít s poznámkovými bloky Azure.

Pokud si nejste jisti, vyberte v řádku účtu příkaz **Vytvořit jeden.** Nový účet Microsoft můžete vytvořit pomocí libovolné e-mailové adresy od libovolného poskytovatele.

![Příkaz k vytvoření nového účtu Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Pokud se pokusíte vytvořit nový účet s e-mailovou adresou, ke které je již přidružen účet, může se zobrazit zpráva "Nemůžete se sem zaregistrovat pomocí pracovní nebo školní e-mailové adresy. Použijte osobní e-mail, například Gmail nebo Yahoo!, nebo si pořažte nový e-mail outlooku." V takovém případě zkuste přihlásit pomocí pracovní e-mailové adresy bez vytvoření nového účtu.

U dětských účtů je přístup k poznámkovým blokům Azure ve výchozím nastavení blokovaný. Přihlášení pomocí dětského účtu zobrazí níže uvedenou chybu:

![Při pokusu o přihlášení pomocí dětského účtu došlo k chybě: něco se pokazilo, rodič zablokoval přístup](media/accounts/child-account-error.png)

Chcete-li povolit přístup, musí rodič provést následující kroky:

1. Navštivte `https://account.live.com/mk` a přihlaste se pomocí nadřazeného účtu.
1. V části pro dotyčné dítě vyberte **Spravovat přístup tohoto dítěte k aplikacím třetích stran**.
1. Na další stránce vyberte **Povolit přístup**.
1. Když se dětský účet použije k přihlášení k poznámkovým blokům Azure, vyberte v příkazu k zadání oprávnění, který se zobrazí, vyberte **Ano.**

> [!Warning]
> Povolení přístupu k aplikacím třetích stran pro poznámkové bloky Azure také umožňuje přístup pro všechny ostatní aplikace třetích stran. Rodičům se doporučuje, aby při umožnění přístupu používali diskrétnost, a mohou chtít podrobněji sledovat činnost svého dítěte.

## <a name="work-or-school-accounts"></a>Pracovní nebo školní účty

Pracovní nebo školní účet je vytvořen správcem organizace, aby umožnil členovi organizace přístup ke cloudovým službám Microsoftu, jako je Office 365, a také jako účet pro přihlášení k Systému Windows v počítači přilehlém k doméně. Pracovní nebo školní účet obvykle používá e-mailovou any-user@contoso.comadresu organizace, například .

Přihlášení k poznámkovým blokům Azure pomocí pracovního nebo školního účtu může vyžadovat souhlas správce, protože poznámkové bloky Azure shromažďují nebo používají (ale nezveřejňují) informace, jako je e-mailová adresa účtu a informace o prohlížeči uživatele. (Data prohlížeče se používají k optimalizaci funkcí podle oblíbeného použití.)

Správce účtu organizace musí poskytnout souhlas jménem uživatelů, pokud je uživatelům omezeno individuální souhlas. V tomto případě se uživatelům zobrazí zpráva "Nelze získat přístup k této aplikaci":

![Zpráva "Nemáte přístup k této aplikaci" při použití pracovního nebo školního účtu](media/accounts/consent-permissions-denied.png)

Chcete-li poskytnout souhlas jako správce, použijte [stránku souhlasu správce](https://notebooks.azure.com/account/adminConsent), která vás provede procesem.

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Úprava profilu a ID uživatele](azure-notebooks-user-profile.md)
