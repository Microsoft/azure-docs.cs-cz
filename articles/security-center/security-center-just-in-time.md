---
title: Čas virtuálního počítače přístup k podle potřeby ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument ukazuje jak včasný přístup k virtuálním počítačům v Azure Security Center pomáhá řídit přístup k vašim virtuálním počítačům Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2018
ms.author: rkarlin
ms.openlocfilehash: 2a079456813a67eb40d5cf42bcdd2c91fbc631d3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297035"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Správa přístupu k virtuálním počítačům pomocí metody právě včas

Právě v čase virtuálních počítačů (VM) přístupu slouží k zamezit příchozímu provozu na virtuální počítače Azure, tím omezit vystavení útokům při poskytování snadného přístupu pro připojení k virtuálním počítačům v případě potřeby.

> [!NOTE]
> Podle potřeby v čase funkce je dostupná na úrovni Standard služby Security Center.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Útoku

Útok hrubou silou útokům běžně cílové porty pro správu jako prostředek k získání přístupu k virtuálnímu počítači. V případě úspěchu, útočník může převzít kontrolu na virtuálním počítači a navázání proniknutí do vašeho prostředí.

Chcete-li omezit množství času, který je otevřený port je jedním ze způsobů, aby se snížila zranitelnost vůči útoku hrubou silou. Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Když za běhu je povolený, Security Center používá [skupinu zabezpečení sítě](../virtual-network/security-overview.md#security-rules) pravidel (NSG) omezující přístup k portům pro správu, takže nemůže být cílem útočníků.

![Pouze v případě čas][1]

## <a name="how-does-just-in-time-access-work"></a>Jak dočasný přístup v funguje?

Pokud je povolený přístup JIT (právě včas), Security Center uzamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla NSG. Vyberete porty na virtuálním počítači, do které se uzamkne příchozí provoz dolů. Tyto porty se řídí podle potřeby v čase řešení.

Když uživatel požádá o přístup k virtuálnímu počítači, Security Center kontroluje, zda má uživatel [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) oprávnění, které poskytují přístup pro zápis pro virtuální počítač. Pokud mají oprávnění k zápisu, jeho žádost se schválí a Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (Nsg) a povolení příchozí komunikace na vybrané porty množství času, který jste zadali. Po vypršení doby obnoví Security Center do předchozích stavů skupiny zabezpečení sítě. Tato připojení, které jsou už navázalo se nepřerušily, ale.

> [!NOTE]
> Security Center dočasný přístup virtuálních počítačů v současné době podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Další informace o modelu nasazení classic a Resource Manageru najdete v článku [Azure Resource Manageru a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Pomocí dočasný přístup v

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně vyberte **čas přístupu k virtuálním počítačům podle potřeby**.

![Včasný přístup k virtuálním počítačům dlaždici][2]

**Čas přístupu k virtuálním počítačům podle potřeby** otevře se okno.

![Včasný přístup k virtuálním počítačům dlaždici][10]

V části **Přístup k virtuálním počítačům podle potřeby** se zobrazí informace o stavu vašich virtuálních počítačů:

- **Nakonfigurované** – Virtuální počítače s nakonfigurovanou podporou přístupu podle potřeby. Dat je za poslední týden a pro každý virtuální počítač obsahuje číslo schválené žádosti, datum posledního přístupu a čas posledního uživatele.
- **Doporučené** – Virtuální počítače, které můžou podporovat přístup podle potřeby, ale ještě tak nebyly nakonfigurované. Doporučujeme povolit jenom v řízení přístupu k virtuálnímu počítači čas k těmto virtuálním počítačům. Zobrazit [konfigurace v zásadách přístupu čas](#configuring-a-just-in-time-access-policy).
- **Žádné doporučení** – Mezi důvody, proč virtuální počítač nemusí být doporučený, patří:
  - Chybějící NSG – Řešení přístupu podle potřeby vyžaduje existenci NSG.
  - Klasický virtuální počítač – Přístup k virtuálním počítačům podle potřeby v Security Center aktuálně podporuje pouze virtuální počítače nasazené prostřednictvím Azure Resource Manageru. Nasazení classic není podporována podle potřeby v čase řešení.
  - Jiné – Virtuální počítač je v této kategorii, pokud je řešení přístupu podle potřeby vypnuté v zásadách zabezpečení předplatného nebo skupiny prostředků nebo pokud virtuální počítač nemá veřejnou IP adresu a NSG.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurace zásady přístupu čas

Výběr virtuálních počítačů, které chcete povolit:

1. V části **čas přístupu k virtuálním počítačům podle potřeby**, vyberte **doporučená** kartu.

  ![Povolit přístup pouze v][3]

2. V části **virtuální počítač**, vyberte virtuální počítače, které chcete povolit. To umístí značku zaškrtnutí vedle virtuálního počítače.
3. Vyberte **povolit JIT na virtuálních počítačích**.
4. Vyberte **Uložit**.

### <a name="default-ports"></a>Výchozí porty

Zobrazí se výchozí porty, které Security Center doporučuje povolení za běhu.

1. V části **čas přístupu k virtuálním počítačům podle potřeby**, vyberte **doporučená** kartu.

  ![Zobrazit výchozí porty][6]

2. V části **virtuálních počítačů**, vyberte virtuální počítač. To umístí značku zaškrtnutí vedle virtuálního počítače a otevře **konfigurace přístupu k virtuálnímu počítači podle potřeby**. V tomto okně se zobrazí výchozí porty.

### <a name="add-ports"></a>Přidání portů

V části **konfigurace přístupu k virtuálnímu počítači podle potřeby**, můžete také přidat a nakonfigurovat nový port, na kterém chcete povolit v čase řešení.

1. V části **konfigurace přístupu k virtuálnímu počítači podle potřeby**vyberte **přidat**. Tím se otevře **přidání konfigurace portu**.

  ![Konfigurace portu][7]

2. V části **přidání konfigurace portu**, identifikujte port, typ protokolu, povolené zdrojové IP adresy a maximální čas požadavku.

  Povolené zdrojové IP adresy jsou rozsahy IP adres s povolením získat přístup po schválení žádosti.

  Maximální čas požadavku je maximální časový interval, můžete konkrétní port otevřený.

3. Vyberte **OK**.

## <a name="requesting-access-to-a-vm"></a>Žádost o přístup k virtuálnímu počítači

Chcete-li požádat o přístup k virtuálnímu počítači:

1. V části **čas přístupu k virtuálním počítačům podle potřeby**, vyberte **nakonfigurováno** kartu.
2. V části **virtuálních počítačů**, vyberte virtuální počítače, které chcete povolit přístup. To umístí značku zaškrtnutí vedle virtuálního počítače.
3. Vyberte **žádat o přístup**. Tím se otevře **žádat o přístup**.

  ![Žádost o přístup k virtuálnímu počítači][4]

4. V části **žádat o přístup**, nakonfigurujte pro každý virtuální počítač porty otevřete společně se zdrojovou IP adresu, která se otevře port pro a časový interval, pro které se otevře port. Můžete požádat o přístup jenom na porty, které jsou nakonfigurované v podle potřeby v zásadách čas. Každý z portů je maximální povolená doba odvozený od podle potřeby v zásadách čas.
5. Vyberte **otevřít porty**.

> [!NOTE]
> Když uživatel požádá o přístup k virtuálnímu počítači, Security Center kontroluje, zda má uživatel [řízení přístupu na základě Role (RBAC)](../role-based-access-control/role-assignments-portal.md) oprávnění, které poskytují přístup pro zápis pro virtuální počítač. Pokud mají oprávnění k zápisu, jeho žádost se schválí.
>
>

> [!NOTE]
> Pokud uživatel, který žádá o přístup, je za proxy serverem, nemusí fungovat možnost "My IP adresy". Může být nutné definovat celou škálu organizace.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Úpravy počítačům v zásadách přístupu čas

Můžete změnit Virtuálního počítače existující jenom v zásadách čas přidáním a nakonfigurování nový port otevřete pro tento virtuální počítač nebo změnou všechny ostatní parametry související s port už chráněný.

Pokud chcete upravit stávající jenom v zásadách čas virtuálního počítače, **nakonfigurováno** karta se používá:

1. V části **virtuálních počítačů**, vyberte virtuální počítač přidat port do tím, že kliknete na tři tečky v rámci řádku pro tento virtuální počítač. Otevře se nabídka.
2. Vyberte **upravit** v nabídce. Tím se otevře **konfigurace přístupu k virtuálnímu počítači podle potřeby**.

  ![Upravení zásady][8]

3. V části **konfigurace přístupu k virtuálnímu počítači podle potřeby**, stávající nastavení už chráněných portu můžete upravit buď kliknutím na jeho port, nebo můžete vybrat **přidat**. Tím se otevře **přidání konfigurace portu**.

  ![Přidání portu][7]

4. V části **přidání konfigurace portu**, identifikujte port, protokol typu povolené zdrojové IP adresy a maximální doba požadavku.
5. Vyberte **OK**.
6. Vyberte **Uložit**.

## <a name="auditing-just-in-time-access-activity"></a>Auditování jenom v aktivitě čas přístupu

Můžete získat přehled o aktivitách virtuálního počítače pomocí prohledávání protokolů. Chcete-li zobrazit protokoly:

1. V části **čas přístupu k virtuálním počítačům podle potřeby**, vyberte **nakonfigurováno** kartu.
2. V části **virtuálních počítačů**, vyberte virtuální počítač k zobrazení informací o po kliknutí na tři tečky v rámci řádku pro tento virtuální počítač. Otevře se nabídka.
3. Vyberte **protokolu aktivit** v nabídce. Tím se otevře **protokolu aktivit**.

  ![Vyberte protokol aktivit][9]

  **Protokol aktivit** poskytuje filtrované zobrazení předchozích operací pro tento virtuální počítač spolu s čas, datum a předplatné.

  ![Zobrazení protokolu aktivit][5]

Informace protokolu můžete stáhnout tak, že vyberete **kliknutím sem stáhnete všechny položky jako CSV**.

Upravit filtry a vybrat **použít** vytvoříte vyhledávání a protokolu.

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>Pomocí metody právě včas přístup k virtuálním počítačům přes rozhraní REST API

Podle potřeby přístup podle potřeby funkce se dá použít prostřednictvím rozhraní API služby Azure Security Center. Můžete získat informace o nakonfigurovaných virtuálních počítačů, přidat nové, požádejte o přístup k virtuálnímu počítači a další, přes toto rozhraní API. Zobrazit [zásad přístupu k síti Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), další informace o podle potřeby v čase rozhraní REST API.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Konfigurace zásad čas pro virtuální počítač

Nakonfigurujte čas zásad na konkrétní virtuální počítač, budete potřebovat ke spuštění tohoto příkazu v relaci prostředí PowerShell: Set-ASCJITAccessPolicy.
Postupujte podle dokumentace k rutinám Další informace.

### <a name="requesting-access-to-a-vm"></a>Žádost o přístup k virtuálnímu počítači

Pro přístup k konkrétní virtuální počítač, který je pak chráněn rozhraním podle potřeby v době řešení, budete potřebovat ke spuštění tohoto příkazu v relaci prostředí PowerShell: vyvolat ASCJITAccess.
Postupujte podle dokumentace k rutinám Další informace.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak za běhu přístup k virtuálním počítačům v Security Center pomáhá, že se že můžete řídit přístup k vašim virtuálním počítačům Azure.

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

- [Nastavení zásad zabezpečení](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
- [Sledování stavu zabezpečení](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
- [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
- [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
- [Security Center – nejčastější dotazy](security-center-faq.md) – přečtěte si nejčastější dotazy k používání této služby.
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
