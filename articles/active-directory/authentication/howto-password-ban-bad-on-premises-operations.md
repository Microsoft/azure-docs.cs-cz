---
title: Operace ve verzi preview Azure AD hesla ochrany a vytváření sestav
description: Azure AD hesla ve verzi preview po nasazení operace ochrany a vytváření sestav
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: a77a6dd8b408fd8151cb12b7d0269b8890ef929b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662410"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Verze Preview: Azure AD ochranu heslem, provozní postupy

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Po dokončení [instalace ochrany hesel služby Azure AD](howto-password-ban-bad-on-premises-deploy.md) místně, je několik položek, které musí být nakonfigurované na webu Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Proveďte konfiguraci seznamu vlastních zakázaných hesel

Postupujte podle pokynů v článku [konfigurace seznamu zakázaných hesel vlastní](howto-password-ban-bad-configure.md) pokyny k přizpůsobení seznamu zakázaných hesel pro vaši organizaci.

## <a name="enable-password-protection"></a>Povolit ochranu heslem

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory**, **metody ověřování**, pak **heslo Protection (Preview)**.
1. Nastavte **povolení ochrany hesla na Windows Server Active Directory** k **Ano**
1. Jak je uvedeno v [Průvodce nasazením](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), doporučuje se zpočátku nastaven **režimu** k **auditu**
   * Až si s funkcí, můžete přepnout **režimu** k **vynucené**
1. Klikněte na **Uložit**.

![Povolení součástí ochrany hesel služby Azure AD na webu Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Režim auditování

Režim auditování je určený jako způsob, jak spustit software v režimu "what if". Každá služba agenta řadiče domény vyhodnotí příchozí heslo podle aktuálně aktivních zásad. Pokud je aktuální zásady nakonfigurované v režimu auditování, "špatné" hesla za následek zprávy protokolu událostí, ale jsou přijaty. Toto je jediný rozdíl mezi audity a dohled nad vynutit režim všechny ostatní operace spuštění stejný.

> [!NOTE]
> Společnost Microsoft doporučuje, aby počáteční nasazení a testování vždy spustí v režimu auditování. Události v protokolu událostí by pak monitorovat se pokouší předvídat, zda by narušen všechny existující provozní procesy, když je povolený režim vynucení.

## <a name="enforce-mode"></a>Režim vynucení

Vynutit režim je určený jako konečnou konfiguraci. Stejně jako v režimu auditování výše každá služba agenta řadiče domény vyhodnotí příchozí hesla podle aktuálně aktivních zásad. Pokud je povolený režim vynucení, i když byl odmítnut heslo, které se považuje za nezabezpečené podle zásad.

Heslo je v režimu vynucení zamítnut agentem ochrany DC hesel služby Azure AD, se shoduje s co objeví pokud své heslo odmítl vynucení složitost hesla tradičních místních viditelné dopad vidět koncový uživatel. Uživatel například může zobrazit na obrazovku pro zadání hesla Windows logon\change následující tradiční chybová zpráva:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Tato zpráva je pouze jedním z několika možných výsledků. Určité chybové zprávě se může lišit v závislosti na skutečné softwaru nebo scénáře, který se pokouší nastavit nezabezpečené heslo.

Ovlivněné koncoví uživatelé potřebovat pro práci s jejich pracovníci IT pochopit nové požadavky a více moci rozhodnout zabezpečených hesel.

## <a name="enable-mode"></a>Povolit režim

Toto nastavení by měla zůstat normálně ve výchozím povolené (Ano) stavu. Konfiguraci tohoto nastavení na hodnotu zakázáno (ne) způsobí, že všechny nasazené agenty hesel služby Azure AD ochranu řadič domény přejděte do tichém režimu, ve kterém všechna hesla se přijímají jako-je, a žádné aktivity ověřování bude provedeno jednání (například, dokonce ani událostí auditu bude možné, protože ho).

## <a name="next-steps"></a>Další postup

[Monitorování ochrany hesel Azure AD](howto-password-ban-bad-on-premises-monitor.md)
