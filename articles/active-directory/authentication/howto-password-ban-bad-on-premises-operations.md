---
title: Operace ochrany ve verzi preview Azure AD hesel a vytváření sestav
description: Operace aktualizace ochrany ve verzi preview po nasazení pro Azure AD hesel a vytváření sestav
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e728162da8221046b8496cced8671695c7794164
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978379"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Ve verzi Preview: Azure AD hesla ochranu provozní postupy

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po dokončení [instalace ochrany pomocí služby Azure AD hesla](howto-password-ban-bad-on-premises-deploy.md) místně, je několik položek, které musí být nakonfigurované na webu Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Proveďte konfiguraci seznamu vlastních zakázaných hesel

Postupujte podle pokynů v článku [konfigurace seznamu zakázaných hesel vlastní](howto-password-ban-bad-configure.md) pokyny k přizpůsobení seznamu zakázaných hesel pro vaši organizaci.

## <a name="enable-password-protection"></a>Povolit ochranu heslem

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **ochrana heslem (Preview)**.
1. Nastavte **povolení ochrany hesla na Windows Server Active Directory** k **Ano**
1. Jak je uvedeno v [Průvodce nasazením](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), doporučuje se zpočátku nastaven **režimu** k **auditu**
   * Až si s funkcí, můžete přepnout **režimu** k **vynucené**
1. Klikněte na **Uložit**.

![Povolení součástí ochrany hesla Azure AD na webu Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Režim auditování

Režim auditování je určený jako způsob, jak spustit software v režimu "what if". Každá služba agenta řadiče domény vyhodnotí příchozí heslo podle aktuálně aktivních zásad. Pokud je aktuální zásady nakonfigurované v režimu auditování, "špatné" hesla za následek zprávy protokolu událostí, ale jsou přijaty. Toto je jediný rozdíl mezi audity a dohled nad vynutit režim všechny ostatní operace spuštění stejný.

> [!NOTE]
> Společnost Microsoft doporučuje, aby počáteční nasazení a testování vždy spustí v režimu auditování. Události v protokolu událostí by pak monitorovat se pokouší předvídat, zda by narušen všechny existující provozní procesy, když je povolený režim vynucení.

## <a name="enforce-mode"></a>Režim vynucení

Vynutit režim je určený jako konečnou konfiguraci. Stejně jako v režimu auditování výše každá služba agenta řadiče domény vyhodnotí příchozí hesla podle aktuálně aktivních zásad. Pokud je povolený režim vynucení, i když byl odmítnut heslo, které se považuje za nezabezpečené podle zásad.

Heslo je zamítnut v režimu vynucení ochrana hesel Azure AD agenta řadiče domény, se shoduje s co objeví pokud své heslo odmítl vynucení složitost hesla tradičních místních viditelné dopad vidět koncový uživatel. Uživatel například může zobrazit na obrazovku pro zadání hesla Windows logon\change následující tradiční chybová zpráva:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Tato zpráva je pouze jedním z několika možných výsledků. Určité chybové zprávě se může lišit v závislosti na skutečné softwaru nebo scénáře, který se pokouší nastavit nezabezpečené heslo.

Ovlivněné koncoví uživatelé potřebovat pro práci s jejich pracovníci IT pochopit nové požadavky a více moci rozhodnout zabezpečených hesel.

## <a name="enable-mode"></a>Povolit režim

Toto nastavení by měla zůstat normálně ve výchozím povolené (Ano) stavu. Konfiguraci tohoto nastavení na hodnotu zakázáno (ne) způsobí, že všechny nasazené hesla Azure AD agenty ochrany řadič domény přejděte do tichém režimu, ve kterém všechna hesla se přijímají jako-je, a žádné aktivity ověřování bude provedeno jednání (například, dokonce ani událostí auditu bude možné, protože ho).

## <a name="usage-reporting"></a>Generování sestav o využívání

`Get-AzureADPasswordProtectionSummaryReport` Rutina slouží k vytvoření souhrnné zobrazení aktivity. Ukázkový výstup této rutiny je následujícím způsobem:

```Powershell
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
> Tato rutina funguje tak, že otevřete relaci Powershellu na každém řadiči domény. Aby bylo možné úspěšné, musí být povolena podpora vzdálené relace Powershellu na každém řadiči domény a klient musí mít dostatečná oprávnění. Další informace o požadavcích vzdálené relace prostředí Powershell spusťte v okně Powershellu "Get-Help about_Remote_Troubleshooting".

> [!NOTE]
> Tato rutina funguje tak, že vzdáleně dotazování protokolu událostí správce každé služby agenta řadiče domény. Když protokoly událostí obsahují velký počet událostí, rutina může trvat dlouhou dobu pro dokončení. Kromě toho hromadné sítě dotazy velkých datových sad může ovlivnit výkon řadiče domény. Proto by měla tuto rutinu použít pečlivě v produkčním prostředí.

## <a name="dc-agent-discovery"></a>Zjišťování agentů DC

`Get-AzureADPasswordProtectionDCAgent` Rutiny lze zobrazit základní informace o různých agentů DC spuštěných v doméně nebo doménové struktuře. Tyto informace získány z serviceConnectionPoint objekty registrované ve spuštěné službě agenta řadiče domény. Ukázkový výstup této rutiny je následujícím způsobem:

```Powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Různé vlastnosti se aktualizují každou službu agenta DC přibližně hodinu. Data se stále latenci replikace služby Active Directory.

Rozsah rutiny dotazu může jím můžou být buď pomocí – doménová struktura nebo – doména parametry.

## <a name="next-steps"></a>Další postup

[Řešení potíží a monitorování pro ochranu hesel Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
