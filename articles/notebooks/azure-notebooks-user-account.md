---
title: Přihlašovat Azure Notebooks Preview
description: Nakonfigurujte svůj uživatelský účet pro Azure Notebooks pomocí účet Microsoft nebo pracovního/školního účtu.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 929a21785b5b48c81c2d2016874feed61021d3c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85832181"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Váš uživatelský účet pro Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Můžete použít Azure Notebooks s přihlášením pomocí uživatelského účtu nebo bez něj:

- Bez přihlášení můžete vytvářet a spouštět poznámkové bloky, ale nemůžou uchovávat poznámkové bloky nebo datové soubory jako součást projektů. Uživatel, který obdrží odkaz na notebook Azure, může Poznámkový blok využít, aniž by se musel přihlašovat.
- Když se přihlásíte, Azure Notebooks uchová všechny vaše projekty s vaším účtem. Přihlášení uživatelé mají také ID uživatele, které jim umožní sdílet své projekty a poznámkové bloky s ostatními.
- Když se účet používaný pro Azure Notebooks taky přidruží k předplatnému Azure, získáte další výhody, jako je spuštění poznámkových bloků na výkonnějších serverech, vytváření privátních poznámkových bloků a udělení oprávnění pro poznámkové bloky jednotlivým uživatelům.

Přihlášení do Azure Notebooks vyžaduje buď účet Microsoft, nebo pracovní nebo školní účet. Při výběru příkazu pro **přihlášení** v pravé horní části stránky s poznámkovými bloky se zobrazí výzva k zadání účtu:

![Příkaz pro přihlášení pro Azure Notebooks](media/accounts/sign-in-command.png)

Veškerá práce, kterou provedete v Azure Notebooks, je přidružená k účtu, který používáte k přihlášení. Každý účet musí mít také jedinečné ID uživatele v [profilu uživatele](azure-notebooks-user-profile.md). V důsledku toho se můžete přihlásit k Azure Notebooks s různými účty, pokud potřebujete zachovat samostatné sady projektů a samostatné identity. Každý člen týmu pro datové vědy může například mít jednotlivé účty společně jako účet sdílené skupiny, který používají k tomu, aby svou práci mohli prezentovat lidem mimo jejich společnost. Instruktoři, podobně, můžou udržovat účet pro svou vzdělávací roli, který se liší od účtu používaného v externí poradenské nebo open source práci.

## <a name="microsoft-accounts"></a>Účty Microsoft

Účty Microsoft se používají k přihlášení k libovolnému počtu produktů a služeb od Microsoftu, jako jsou Windows, Azure, outlook.com, OneDrive a XBox Live. Pokud používáte některou z těchto služeb, je možné, že už máte účet Microsoft, který můžete používat s Azure Notebooks.

Pokud si nejste jistí, na příkazovém řádku účtu vyberte příkaz **vytvořit jeden** . Novou účet Microsoft můžete vytvořit pomocí libovolné e-mailové adresy libovolného poskytovatele.

![Příkaz pro vytvoření nového účet Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Pokud se pokusíte vytvořit nový účet s e-mailovou adresou, na které už je přidružený účet, může se vám zobrazit zpráva "nemůžete se zaregistrovat tady pomocí pracovní nebo školní e-mailové adresy. Použijte osobní e-mail, jako je například Gmail nebo Yahoo!, nebo Získejte nový Outlookový e-mail. V takovém případě se zkuste přihlásit pomocí pracovní e-mailové adresy, aniž byste museli vytvořit nový účet.

V případě podřízených účtů je přístup k Azure Notebooks ve výchozím nastavení blokován. Přihlášení pomocí podřízeného účtu zobrazuje níže uvedenou chybu:

![Při pokusu o přihlášení pomocí podřízeného účtu došlo k chybě: něco se nepovedlo, váš nadřazený přístup zablokoval.](media/accounts/child-account-error.png)

Aby bylo možné povolit přístup, musí nadřazený prvek provést následující kroky:

1. Navštivte `https://account.live.com/mk` nadřazený účet a přihlaste se.
1. V části pro příslušnou podřízenou položku vyberte **spravovat přístup tohoto dítěte k aplikacím třetích stran**.
1. Na další stránce vyberte **Povolit přístup**.
1. Když se k přihlášení k Azure Notebooks použije podřízený účet, vyberte v zobrazeném okně oprávnění **Ano** .

> [!Warning]
> Povolení přístupu k aplikacím třetích stran pro Azure Notebooks taky umožňuje přístup ke všem ostatním aplikacím třetích stran. U rodičů doporučujeme při povolování přístupu použít volitelné a může chtít monitorovat aktivitu dítěte podrobněji.

## <a name="work-or-school-accounts"></a>Pracovní nebo školní účty

Správce organizace vytvoří pracovní nebo školní účet, který členům organizace umožní přístup ke cloudovým službám Microsoftu, jako je například Microsoft 365, a také jako účet pro přihlášení do Windows na počítači připojeném k doméně. Pracovní nebo školní účet obvykle používá e-mailovou adresu organizace, například any-user@contoso.com .

Přihlášení k Azure Notebooks pomocí pracovního nebo školního účtu může vyžadovat souhlas správce, protože Azure Notebooks shromažďuje nebo používá (ale nezveřejňuje) informace, jako je e-mailová adresa účtu a informace o prohlížeči uživatele. (Data prohlížeče se používají k optimalizaci funkcí v souladu s oblíbeným využitím.)

Správce účtu organizace musí poskytnout souhlas jménem uživatele, pokud jsou uživatelé omezeni individuálně. V takovém případě se uživatelům zobrazí zpráva "nemůžete získat přístup k této aplikaci":

![Při použití pracovního nebo školního účtu nejde získat přístup k této aplikaci.](media/accounts/consent-permissions-denied.png)

K poskytnutí souhlasu s oprávněními správce použijte [stránku souhlasu správce](https://notebooks.azure.com/account/adminConsent), která vás provede procesem.

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Upravit svůj profil a ID uživatele](azure-notebooks-user-profile.md)
