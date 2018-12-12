---
title: Přihlaste se k Azure poznámkové bloky
description: Konfigurace uživatelského účtu pro poznámkových bloků Azure pomocí účtu Microsoft nebo pracovní nebo školní účet.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f95edde6a501cf33c45daccc2dbd2605c077217d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092598"
---
# <a name="your-user-account-for-azure-notebooks"></a>Váš uživatelský účet pro poznámkové bloky Azure

Poznámkových bloků Azure můžete použít i bez přihlášení pomocí uživatelského účtu:

- Bez přihlášení, můžete vytvořit a možnost spouštět poznámkové bloky, ale nemůžete ukládat poznámkových bloků nebo datové soubory projektů v rámci. Uživatelé, kteří přijímají odkaz poznámkovému bloku Azure, například televizního Poznámkový blok bez nutnosti přihlášení.
- Po přihlášení si poznámkových bloků Azure uchovává všechny projekty s vaším účtem. Přihlášených uživatelů také mít uživatelské ID, která umožňuje sdílet svoje projekty a poznámkové bloky s ostatními.
  - Když má účet používaný pro poznámkových bloků Azure je také spojen s s předplatným Azure, získáte další výhody, jako je například spouštění poznámkových bloků na výkonnější serverech vytváří privátní poznámkových bloků a udělení oprávnění pro poznámkové bloky pro jednotlivé uživatele.

Přihlašování do poznámkových bloků Azure vyžaduje Account Microsoft nebo účet "Pracovní nebo školní". Zobrazí se výzva k účtu při výběru **Sign In** příkazů v horní pravé části stránky poznámkových bloků:

![V příkazu zaregistrujte poznámkových bloků Azure](media/accounts/sign-in-command.png)

Veškerou práci, kterou provedete v poznámkových bloků Azure je přidružená k účtu, který používáte k přihlášení. Každý účet musí mít rovněž jedinečné ID uživatele ve vaší [profilu uživatele](azure-notebooks-user-profile.md). V důsledku toho se můžete přihlásit do poznámkových bloků Azure s různými účty Pokud je potřeba udržovat samostatnou sadu projektů a samostatné identity. Pro každý člen týmu data science může mít například oba jednotlivé účty spolu s jako sdílené skupinového účtu, které používají, zobrazíte jejich práce lidem mimo jejich společnost. Podobně, školitelů, může udržovat pro jejich roli výuky, která se liší od účtu používanému v externí konzultační nebo open source pracovní účet.

## <a name="microsoft-accounts"></a>Účty Microsoft

Účty Microsoft se používají k přihlašování do libovolného počtu svých produktů a služeb, jako je Windows, Azure, outlook.com, OneDrive, XBox Live. Pokud použijete některou z těchto služeb, je pravděpodobné, že už máte Account Microsoft, který vám pomůže s poznámkovými bloky Azure.

Pokud si nejste jistí, vyberte **vytvořit jeden** příkazu v příkazovém řádku účtu. Můžete vytvořit nový účet Microsoft pomocí libovolné e-mailovou adresu z libovolného poskytovatele.

![Příkaz pro vytvoření nového účtu Microsoft](media/accounts/create-new-microsoft-account.png)

Pro dětské účty blokovaný přístup k poznámkových bloků Azure ve výchozím nastavení. Přihlašování pomocí účtu podřízený se zobrazí chyba je uvedeno níže:

![Chyba při pokusu přihlásit pomocí účtu podřízených: došlo k nějaké chybě, zablokoval přístup](media/accounts/child-account-error.png)

Pokud chcete povolit přístup, musí nadřazený proveďte následující kroky:

1. Navštivte `https://account.live.com/mk` a přihlaste se pomocí účtu nadřazené.
1. V části pro dítě dotyčný vybrat **spravovat přístup tohoto dítěte k aplikacím třetích stran**.
1. Na další stránce vyberte **povolení přístupu**.
1. Když se účet dítěte dále používá k přihlášení do poznámkových bloků Azure, vyberte **Ano** v příkazovém řádku oprávnění, která se zobrazí.

> [!Warning]
> Povolení přístupu k aplikacím třetích stran pro Azure poznámkových bloků také umožňuje přístup pro všechny ostatní aplikace třetích stran. Doporučujeme použít podle vlastního uvážení při povolování rodiče přístup a může být vhodné lépe sledovat jejich podřízené aktivity.

## <a name="work-or-school-accounts"></a>Pracovní nebo školní účty

Pracovní nebo školní účet je vytvořen správcem v organizaci, který členovi organizace na přístup ke cloudovým službám Microsoftu, jako je Office 365 a také jako účet pro přihlášení do Windows na počítači připojeném k doméně. Pracovní nebo školní účet, jako obvykle používá organizace e-mailovou adresu, any-user@contoso.com.

Přihlášení do poznámkových bloků Azure pomocí pracovního nebo školního účtu může vyžadovat souhlas správce, protože poznámkových bloků Azure shromažďuje a používá (ale nesmí vyzradit) informace, jako jsou e-mailovou adresu účtu a informace o prohlížeči uživatele. (Data prohlížeče se používá pro optimalizaci funkcí z podle oblíbených využití.)

Správce účtu organizace musíte zadat souhlas jménem uživatelů, pokud uživatelé omezuje od vyjádření souhlasu jednotlivě. V takovém případě uživatelům zobrazí zpráva "Nelze přístup této aplikaci":

![Zpráva "Nelze přístup této aplikaci" při použití pracovního nebo školního účtu](media/accounts/consent-permissions-denied.png)

Chcete-li poskytnout souhlas správce, použijte [stránka pro odsouhlasení podmínek správce](https://notebooks.azure.com/account/adminConsent), které vás provede procesem.

## <a name="next-steps"></a>Další postup  

> [!div class="nextstepaction"]
> [Upravit profil a uživatelské ID](azure-notebooks-user-profile.md)
