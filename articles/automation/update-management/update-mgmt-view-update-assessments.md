---
title: Zobrazit Azure Automation posouzení aktualizací
description: V tomto článku se dozvíte, jak zobrazit posouzení aktualizací pro nasazení Update Management.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450124"
---
# <a name="view-update-assessments"></a>Zobrazení posouzení aktualizací

V Update Management můžete zobrazit informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

V Update Management můžete zobrazit informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací.

[![Update Management výchozí zobrazení](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

Chcete-li zobrazit posouzení aktualizace, proveďte následující kroky.

1. V Azure Portal přejděte na **účty Automation** a v seznamu vyberte svůj účet Automation se Update Management povolenou.

2. V účtu Automation v levém podokně vyberte **Správa aktualizací** .

3. Aktualizace pro vaše prostředí jsou uvedené na stránce **Správa aktualizací** . Pokud se nějaké aktualizace identifikují jako chybějící, zobrazí se na kartě **chybějící aktualizace** jejich seznam.

   Ve sloupci **dodržování předpisů** můžete vidět čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenost aktualizace agenta** můžete zobrazit stav agenta aktualizace. Pokud se vyskytne problém, vyberte odkaz pro řešení potíží, které vám pomůžou problém vyřešit.

4. V části **informační odkaz**vyberte odkaz pro aktualizaci, abyste otevřeli článek podpory, který vám poskytne důležité informace o této aktualizaci.

     [![Zobrazit stav aktualizace](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. Kliknutím kamkoli jinde v aktualizaci otevřete podokno prohledávání protokolu. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Můžete upravit tento dotaz nebo vytvořit vlastní dotaz pro zobrazení podrobných informací.

    [![Zobrazit výsledky dotazu protokolu](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se všechny informace o počtu počítačů, které vyžadují aktualizaci, podrobnosti o operačním systému a odkazu. V podokně prohledávání protokolu se zobrazí také další podrobnosti o aktualizacích.

![Chybějící aktualizace](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Práce s klasifikací aktualizací

V následujících tabulkách jsou uvedeny podporované klasifikace aktualizací v Update Management s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro konkrétní problémy, které řeší kritické chyby, které nesouvisí se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problémy související se zabezpečením pro konkrétní produkt.        |
|Kumulativní aktualizace     | Sady oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Sady oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|Nástroje     | Nástroje nebo funkce, které pomůžou dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikací nebo souborů, které jsou aktuálně nainstalovány.        |

### <a name="linux"></a>Linux

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo které nejsou aktualizacemi zabezpečení.        |

Pro Linux může Update Management rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu a současně zobrazuje data posouzení. (Tato členitost je možná kvůli obohacení dat v cloudu.) Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemají CentOS tyto informace dostupné ve verzích RTM produktu. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management se může opravit na základě klasifikací:

```bash
sudo yum -q --security check-update
```

V současné době není podporována žádná podporovaná metoda pro povolení nativní klasifikace – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří tuto funkci povolili, poskytne jenom nejlepší podpora.

Chcete-li klasifikovat aktualizace na Red Hat Enterprise verze 6, je nutné nainstalovat modul plug-in Yumu-Security. V Red Hat Enterprise Linux 7 je modul plug-in již součástí samotného Yumu, není nutné nic instalovat. Další informace najdete v následujícím [článku znalostní báze](https://access.redhat.com/solutions/10021)Red Hat.

## <a name="next-steps"></a>Další kroky

Další fází procesu je [nasazení aktualizací](update-mgmt-deploy-updates.md) do počítačů, které nedodržují předpisy, a přezkoumání výsledků nasazení.
