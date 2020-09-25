---
title: Zobrazit Azure Automation posouzení aktualizací
description: V tomto článku se dozvíte, jak zobrazit posouzení aktualizací pro nasazení Update Management.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2e32fc7c1872bf18b7f1c995f281a9b09ec45dc8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264447"
---
# <a name="view-update-assessments-in-update-management"></a>Zobrazit posouzení aktualizací v Update Management

V Update Management můžete zobrazit informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Můžete zobrazit informace o posouzení vymezené na vybraný virtuální počítač Azure, z vybraného serveru s povoleným obloukem nebo z účtu Automation ve všech nakonfigurovaných počítačích a serverech.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Pokud chcete zobrazit posouzení aktualizací z virtuálního počítače Azure, přejděte na **Virtual Machines** a vyberte svůj virtuální počítač ze seznamu. V nabídce vlevo vyberte **aktualizace Host +** host a pak na stránce **aktualizace hosta +** host vyberte **Přejít na Update Management** .

V Update Management můžete zobrazit informace o počítači, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací.

[![Zobrazení posouzení Update Management pro virtuální počítač Azure](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm.png)](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Pokud chcete zobrazit posouzení aktualizací ze serveru s podporou ARC, přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj server. V nabídce vlevo vyberte **aktualizace Host a Host**. Na stránce **aktualizace hosta + hostitel** vyberte **Přejít na Update Management**.

V Update Management můžete zobrazit informace o počítači s povolenou ARC, chybějící aktualizace, nasazení aktualizací a plánovaném nasazení aktualizací.

[![Zobrazení posouzení Update Management pro servery s podporou ARC](./media/update-mgmt-view-update-assessments/update-assessment-arc-server.png)](./media/update-mgmt-view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Pokud chcete zobrazit posouzení aktualizací pro všechny počítače, včetně serverů s podporou ARC z vašeho účtu Automation, přejděte na **účty Automation** a v seznamu vyberte svůj účet automation se Update Management povolenou. V účtu Automation v nabídce vlevo vyberte **Update Management** .

Aktualizace pro vaše prostředí jsou uvedené na stránce **Správa aktualizací** . Pokud se nějaké aktualizace identifikují jako chybějící, zobrazí se na kartě **chybějící aktualizace** jejich seznam.

[![Update Management výchozí zobrazení](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

Ve sloupci **dodržování předpisů** můžete vidět čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenost aktualizace agenta** můžete zobrazit stav agenta aktualizace. Pokud se vyskytne problém, vyberte odkaz pro řešení potíží, které vám pomůžou problém vyřešit.

V části **informační odkaz**vyberte odkaz pro aktualizaci, abyste otevřeli článek podpory, který vám poskytne důležité informace o této aktualizaci.

[![Zobrazit stav aktualizace](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

Kliknutím kamkoli jinde v aktualizaci otevřete podokno prohledávání protokolu. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Můžete upravit tento dotaz nebo vytvořit vlastní dotaz pro zobrazení podrobných informací.

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
|nástroje     | Nástroje nebo funkce, které pomůžou dokončit jednu nebo více úloh.        |
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
