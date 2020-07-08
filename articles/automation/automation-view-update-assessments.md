---
title: Zobrazit Azure Automation posouzení aktualizací
description: V tomto článku se dozvíte, jak zobrazit posouzení aktualizací pro nasazení Update Management.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83830595"
---
# <a name="view-update-assessments"></a>Zobrazení posouzení aktualizací

V Azure Automation účtu vyberte **Update Management** a zobrazte stav vašich počítačů.

Toto zobrazení poskytuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve sloupci **dodržování předpisů** si můžete prohlédnout čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenosti agenta aktualizace** můžete zobrazit stav agenta aktualizace. Pokud se vyskytne problém, vyberte odkaz pro řešení potíží, které vám pomůžou problém vyřešit.

Pokud chcete spustit prohledávání protokolů, které vrátí informace o počítači, aktualizaci nebo nasazení, vyberte odpovídající položku v seznamu. Otevře se podokno prohledávání protokolu s dotazem na vybranou položku.

![Update Management výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se všechny informace o počtu počítačů, které vyžadují aktualizaci, podrobnosti o operačním systému a odkazu. V podokně prohledávání protokolu se zobrazí také další podrobnosti o aktualizacích.

![Chybějící aktualizace](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Práce s klasifikací aktualizací

V následujících tabulkách jsou uvedeny podporované klasifikace aktualizací v Update Management s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Classification  |Description  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro konkrétní problémy, které řeší kritické chyby, které nesouvisí se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problémy související se zabezpečením pro konkrétní produkt.        |
|Kumulativní aktualizace     | Sady oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Sady oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|nástroje     | Nástroje nebo funkce, které pomůžou dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikací nebo souborů, které jsou aktuálně nainstalovány.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classification  |Description  |
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

Obecné informace najdete v tématu [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
