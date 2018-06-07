---
title: Pouze ve virtuálním počítači čas přístup v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje jak jenom na dobu přístup virtuálních počítačů v Azure Security Center vám pomůže řízení přístupu k virtuální počítače Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2018
ms.author: terrylan
ms.openlocfilehash: 60a5de16f4146e112a85d74634c662e228a0854f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640553"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Spravovat přístup k virtuálním počítačům pomocí právě v čase

Právě v čas virtuální počítač (VM) přístupu slouží zamknout příchozí přenosy na virtuální počítače Azure, snižuje riziko napadení a snadného přístupu pro připojení k virtuálním počítačům v případě potřeby.

> [!NOTE]
> Právě v čase funkce je dostupná na úrovni Standard služby Security Center.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Scénář útoku

Útok hrubou silou útokům běžně cílové porty správy jako prostředek k získání přístupu k virtuálnímu počítači. V případě úspěšného útočník může převzít kontrolu nad virtuálního počítače a vytvořit dostane do vašeho prostředí.

Chcete-li omezit množství času, který je otevřený port je jedním ze způsobů, aby se snížila zranitelnost vůči útoku hrubou silou. Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Pokud právě v čase je povoleno, Security Center používá [skupinu zabezpečení sítě](../virtual-network/security-overview.md#security-rules) (NSG) pravidla, která omezit přístup k portům správy, takže nemůžou být cílem útočníků.

![Jenom v případě čas][1]

## <a name="how-does-just-in-time-access-work"></a>Jak jenom při přístup k časovému funguje?

Pokud je povolený přístup JIT (právě včas), Security Center uzamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla NSG. Můžete vybrat porty na virtuálním počítači, do které se uzamkne příchozí provoz směrem dolů. Tyto porty jsou řízeny jenom v době řešení.

Když uživatel požaduje přístup k virtuálnímu počítači, Security Center zkontroluje, zda má uživatel [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) oprávnění, které poskytují přístup pro zápis pro virtuální počítač. Pokud mají oprávnění k zápisu, jeho žádost se schválí a Security Center automaticky nakonfiguruje skupin zabezpečení sítě (Nsg) chcete povolit příchozí přenosy na vybrané porty pro množství času, který jste zadali. Po dobu vypršení platnosti, Security Center obnoví skupin Nsg do předchozího stavu. Tato připojení, které jsou už vytvořené se nepřerušují, ale.

> [!NOTE]
> Security Center, které jsou právě čas virtuálních počítačů přístup aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Správce Azure Resource Manager. Další informace o modelu nasazení Resource Manager i classic najdete [Azure Resource Manager oproti nasazení classic](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Použití pouze v přístup k časovému

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně vyberte **těsně v čas virtuálních počítačů přístup**.

![Právě v čase virtuální počítač přístup k dlaždici][2]

**Těsně v čas virtuálních počítačů přístup** otevře se okno.

![Právě v čase virtuální počítač přístup k dlaždici][10]

V části **Přístup k virtuálním počítačům podle potřeby** se zobrazí informace o stavu vašich virtuálních počítačů:

- **Nakonfigurované** – Virtuální počítače s nakonfigurovanou podporou přístupu podle potřeby. Data uvedená za poslední týden a zahrnuje počet schválené žádosti, datum posledního přístupu a čas a naposledy uživatele pro každý virtuální počítač.
- **Doporučené** – Virtuální počítače, které můžou podporovat přístup podle potřeby, ale ještě tak nebyly nakonfigurované. Doporučujeme povolit jenom při řízení přístupu čas virtuálních počítačů pro tyto virtuální počítače. V tématu [konfigurace jenom v zásadách přístupu čas](#configuring-a-just-in-time-access-policy).
- **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
  - Chybějící NSG – Řešení přístupu podle potřeby vyžaduje existenci NSG.
  - Klasický virtuální počítač – Přístup k virtuálním počítačům podle potřeby v Security Center aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Nasazení classic není podporována pouze v době řešení.
  - Jiné – Virtuální počítač je v této kategorii, pokud je řešení přístupu podle potřeby vypnuté v zásadách zabezpečení předplatného nebo skupiny prostředků nebo pokud virtuální počítač nemá veřejnou IP adresu a NSG.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurace jenom v zásadách přístupu čas

Vyberte virtuální počítače, které chcete povolit:

1. V části **těsně v čas virtuálních počítačů přístup**, vyberte **doporučeno** kartě.

  ![Povolit přístup k časovému][3]

2. V části **VIRTUÁLNÍHO počítače**, vyberte virtuální počítače, které chcete povolit. To převádí zatržení vedle virtuálního počítače.
3. Vyberte **povolení JIT na virtuálních počítačích**.
4. Vyberte **Uložit**.

### <a name="default-ports"></a>Výchozí porty

Můžete zobrazit výchozí porty, které Security Center doporučuje povolení jenom v čase.

1. V části **těsně v čas virtuálních počítačů přístup**, vyberte **doporučeno** kartě.

  ![Zobrazit výchozí porty][6]

2. V části **virtuální počítače**, vyberte virtuální počítač. To vloží zatržení vedle virtuálního počítače a otevře **JIT konfiguraci přístupu**. Toto okno se zobrazí výchozí porty.

### <a name="add-ports"></a>Přidat porty

V části **JIT konfiguraci přístupu**, můžete také přidávat a konfigurovat nový port, na kterém chcete povolit právě v době řešení.

1. V části **JIT konfiguraci přístupu**, vyberte **přidat**. Tím se otevře **konfiguraci portů přidat**.

  ![Konfigurace portu][7]

2. V části **konfiguraci portů přidat**, identifikovat port, typ protokolu, povolené zdrojové adresy IP a doba maximální požadavku.

  Povolené zdrojové adresy IP jsou rozsahy IP moct získat přístup na schválené žádost.

  Doba požadavku maximální je maximální časový interval, může být otevřena specifického portu.

3. Vyberte **OK**.

## <a name="requesting-access-to-a-vm"></a>Žádají o přístup do virtuálního počítače

Chcete-li požádat o přístup k virtuálnímu počítači:

1. V části **těsně v čas virtuálních počítačů přístup**, vyberte **konfigurovaná** kartě.
2. V části **virtuální počítače**, vyberte virtuální počítače, které chcete povolit přístup. To převádí zatržení vedle virtuálního počítače.
3. Vyberte **požádat o přístup**. Tím se otevře **požádat o přístup**.

  ![Požádat o přístup k virtuálnímu počítači][4]

4. V části **požádat o přístup**, můžete nakonfigurovat pro každý virtuální počítač portech, které spolu s zdrojové IP adresy, který se otevře port pro a časový interval, pro kterou je otevřen port. Může vyžádat přístup pouze k porty, které jsou nakonfigurované v jenom v zásadách čas. Každý z portů je maximální povolená doba odvozené od jenom v zásadách čas.
5. Vyberte **otevřít porty**.

> [!NOTE]
> Když uživatel požaduje přístup k virtuálnímu počítači, Security Center zkontroluje, zda má uživatel [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) oprávnění, které poskytují přístup pro zápis pro virtuální počítač. Pokud budou mít oprávnění k zápisu, jeho žádost se schválí.
>
>

> [!NOTE]
> Pokud uživatel, který žádá o přístup je za proxy server, nemusí fungovat možnost "Moje IP adresy". Může být potřeba definovat plný rozsah organizace.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Úpravy jenom v zásadách přístupu čas

Můžete změnit Virtuálního počítače existující jenom v zásadách čas přidáním a konfigurace nový port otevřete tohoto virtuálního počítače nebo změnou druhý parametr, související s již chráněné portu.

Chcete-li upravit stávající jenom v zásadách čas virtuálního počítače, **konfigurovaná** karta se používá:

1. V části **virtuální počítače**, vyberte virtuální počítač port na přidáte kliknutím na tři tečky v řádku tohoto virtuálního počítače. Tím otevřete nabídku.
2. Vyberte **upravit** v nabídce. Tím se otevře **JIT konfiguraci přístupu**.

  ![Upravení zásady][8]

3. V části **JIT konfiguraci přístupu**, můžete buď upravit existující nastavení, již chráněné portu kliknutím na její port, nebo můžete vybrat **přidat**. Tím se otevře **konfiguraci portů přidat**.

  ![Přidat na port][7]

4. V části **konfiguraci portů přidat**, určit port, protokol typu, povolená zdrojové adresy IP a maximální doba požadavku.
5. Vyberte **OK**.
6. Vyberte **Uložit**.

## <a name="auditing-just-in-time-access-activity"></a>Auditování, právě aktivity přístup času.

Můžete získat přehledy aktivity virtuálního počítače pomocí protokolů hledání. Pokud chcete zobrazit protokoly:

1. V části **těsně v čas virtuálních počítačů přístup**, vyberte **konfigurovaná** kartě.
2. V části **virtuální počítače**, vyberte virtuální počítač k zobrazení informací o kliknutím na tři tečky v řádku tohoto virtuálního počítače. Tím otevřete nabídku.
3. Vyberte **protokol aktivit** v nabídce. Tím se otevře **protokol aktivit**.

  ![Vyberte protokol aktivit][9]

  **Protokol aktivit** poskytuje filtrované zobrazení předchozí operace pro tento virtuální počítač společně s čas, datum a předplatného.

  ![Protokol aktivit zobrazení][5]

Informace protokolu si můžete stáhnout tak, že vyberete **kliknutím sem stáhnete všechny položky jako sdílený svazek clusteru**.

Upravit filtry a vyberte **použít** vytvořit vyhledávání a protokolu.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Pomocí jenom na dobu přístup virtuálních počítačů pomocí prostředí PowerShell

Chcete-li použít jenom v době řešení pomocí prostředí PowerShell, zajistěte, aby byla [nejnovější](/powershell/azure/install-azurerm-ps) prostředí Azure PowerShell verze.
Až to uděláte, musíte nainstalovat [nejnovější](https://aka.ms/asc-psgallery) Azure Security Center z Galerie prostředí PowerShell.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Konfigurace jenom v zásadách čas pro virtuální počítač

Ke konfiguraci jenom v zásadách čas na konkrétní virtuální počítač, budete muset spustit tento příkaz v relaci prostředí PowerShell: Set-ASCJITAccessPolicy.
Postupujte podle dokumentace rutiny Další informace.

### <a name="requesting-access-to-a-vm"></a>Žádají o přístup do virtuálního počítače

Pro přístup k konkrétní virtuální počítač, který je chráněný právě v doba řešení, budete muset spustit tento příkaz v relaci prostředí PowerShell: vyvolání ASCJITAccess.
Postupujte podle dokumentace rutiny Další informace.

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli, jak jenom na dobu přístup virtuálních počítačů v Security Center pomáhá, že můžete řídit přístup k virtuální počítače Azure.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- [Nastavení zásad zabezpečení](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
- [Sledování stavu zabezpečení](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
- [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
- [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
- [Security Center – nejčastější dotazy](security-center-faq.md) – přečtěte si nejčastější dotazy o použití této služby.
- [Blog o zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
