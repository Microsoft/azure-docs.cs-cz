---
title: Operace ochrany ve verzi preview Azure AD hesel a vytváření sestav
description: Operace aktualizace ochrany ve verzi preview po nasazení pro Azure AD hesel a vytváření sestav
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163883"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>Ve verzi Preview: Azure AD hesla ochrany po nasazení

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po dokončení [instalace ochrany pomocí služby Azure AD hesla](howto-password-ban-bad-on-premises.md) místně, je několik položek, které musí být nakonfigurované na webu Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Proveďte konfiguraci seznamu vlastních zakázaných hesel

Postupujte podle pokynů v článku [konfigurace seznamu zakázaných hesel vlastní](howto-password-ban-bad.md) pokyny k přizpůsobení seznamu zakázaných hesel pro vaši organizaci.

## <a name="enable-password-protection"></a>Povolit ochranu heslem

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **ochrana heslem (Preview)**.
1. Nastavte **povolení ochrany hesla na Windows Server Active Directory** k **Ano**
1. Jak je uvedeno v [Průvodce nasazením](howto-password-ban-bad-on-premises.md#deployment-strategy), doporučuje se zpočátku nastaven **režimu** k **auditu**
   * Až si s funkcí, můžete přepnout **režimu** k **vynucené**
1. Klikněte na **Uložit**.

![Povolení součástí ochrany hesla Azure AD na webu Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Režim auditování

Režim auditování je určený jako způsob, jak spustit software v režimu "what if". Každá služba agenta řadiče domény vyhodnotí příchozí heslo podle aktuálně aktivních zásad. Pokud je aktuální zásady nakonfigurované v režimu auditování, "špatné" hesla za následek zprávy protokolu událostí, ale jsou přijaty. Toto je jediný rozdíl mezi audity a dohled nad vynutit režim všechny ostatní operace spuštění stejný.

> [!NOTE]
> Společnost Microsoft doporučuje, aby počáteční nasazení a testování vždy spustí v režimu auditování. Události v protokolu událostí by pak monitorovat se pokouší předvídat, zda by narušen všechny existující provozní procesy, když je povolený režim vynucení.

## <a name="enforce-mode"></a>Režim vynucení

Vynutit režim je určený jako konečnou konfiguraci. Stejně jako v režimu auditování výše každá služba agenta řadiče domény vyhodnotí příchozí hesla podle aktuálně aktivních zásad. Pokud je povolený režim vynucení, i když byl odmítnut heslo, které se považuje za nezabezpečené podle zásad.

Heslo je zamítnut v režimu vynucení ochrana hesel Azure AD agenta řadiče domény, se shoduje s co objeví pokud své heslo odmítl vynucení složitost hesla tradičních místních viditelné dopad vidět koncový uživatel. Uživatel například může zobrazit na obrazovku pro zadání hesla logon\change následující tradiční chybová zpráva:

"Nepovedlo se aktualizovat heslo. Hodnota zadaná pro nové heslo nesplňuje délku, složitost nebo historie požadavky domény."

Tato zpráva je pouze jedním z několika možných výsledků. Určité chybové zprávě se může lišit v závislosti na skutečné softwaru nebo scénáře, který se pokouší nastavit nezabezpečené heslo.

Ovlivněné koncoví uživatelé potřebovat pro práci s jejich pracovníci IT pochopit nové požadavky a více moci rozhodnout zabezpečených hesel.

## <a name="usage-reporting"></a>Generování sestav o využívání

`Get-AzureADPasswordProtectionSummaryReport` Rutina slouží k vytvoření souhrnné zobrazení aktivity. Ukázkový výstup této rutiny je následujícím způsobem:

```
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Rozsah rutiny reporting může jím můžou být jedním z – doménové struktury, domény nebo – DomainController parametrů. Bez zadání parametru znamená – doménová struktura.

> [!NOTE]
> Tato rutina funguje tak, že vzdáleně dotazování protokolu událostí správce každé služby agenta řadiče domény. Když protokoly událostí obsahují velký počet událostí, rutina může trvat dlouhou dobu pro dokončení. Kromě toho hromadné sítě dotazy velkých datových sad může ovlivnit výkon řadiče domény. Proto by měla tuto rutinu použít pečlivě v produkčním prostředí.

## <a name="next-steps"></a>Další postup

[Řešení potíží a informace o protokolování pro ochranu hesel Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
