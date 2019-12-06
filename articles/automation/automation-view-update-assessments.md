---
title: Zobrazit posouzení aktualizací pro Azure Update Management
description: Tento článek popisuje, jak zobrazit posouzení aktualizací pro nasazení aktualizací.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d27df57e9371f16a15d3a18b7722598062377d88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850512"
---
# <a name="view-azure-update-management-update-assessments"></a>Zobrazit posouzení aktualizací pro Azure Update Management

V Azure Automation účtu vyberte **Update Management** a zobrazte stav vašich počítačů.

Toto zobrazení poskytuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve sloupci **dodržování předpisů** si můžete prohlédnout čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenosti agenta aktualizace** můžete zobrazit stav agenta aktualizace. Pokud se vyskytne problém, vyberte odkaz pro řešení potíží, které vám pomůžou problém vyřešit.

Pokud chcete spustit prohledávání protokolů, které vrátí informace o počítači, aktualizaci nebo nasazení, vyberte odpovídající položku v seznamu. Otevře se podokno **prohledávání protokolu** s dotazem na vybranou položku:

![Update Management výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se všechny informace o počtu počítačů, které vyžadují aktualizaci, podrobnosti o operačním systému a odkazu. V podokně **prohledávání protokolu** se zobrazí také další podrobnosti o aktualizacích.

![Chybějící aktualizace](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klasifikace aktualizací

V následujících tabulkách jsou uvedeny klasifikace aktualizací v Update Management s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro určitý problém, která řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace pro problém související se zabezpečením určitého produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny dohromady pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které se aplikují na aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|Nástroje     | Nástroj nebo funkce, které pomáhají dokončit jednu nebo více úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux-2"></a>Linux

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo které nejsou aktualizacemi zabezpečení.        |

Pro Linux může Update Management rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu a současně zobrazuje data posouzení. (Tato členitost je možná kvůli obohacení dat v cloudu.) Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemají CentOS tyto informace dostupné ve verzích RTM produktu. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management se může opravit na základě klasifikací:

```bash
sudo yum -q --security check-update
```

V současné době není podporována žádná podporovaná metoda pro povolení nativní klasifikace – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří tuto funkci povolili, poskytne jenom nejlepší podpora.

## <a name="next-steps"></a>Další kroky

Po zobrazení všech posouzení aktualizací můžete naplánovat nasazení aktualizací podle kroků v části [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
