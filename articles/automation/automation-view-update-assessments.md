---
title: Zobrazit posouzení aktualizací pro Azure Update Management
description: Tento článek popisuje, jak zobrazit posouzení aktualizací pro nasazení aktualizací.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377717"
---
# <a name="view-azure-update-management-update-assessments"></a>Zobrazit posouzení aktualizací pro Azure Update Management

V účtu Automation vyberte **Update Management** a zobrazte stav vašich počítačů.

Toto zobrazení poskytuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve **sloupci dodržování předpisů**si můžete prohlédnout čas, kdy byl počítač naposledy vyhodnocen. Ve sloupci **připravenosti agenta aktualizace** můžete zjistit, zda stav agenta aktualizace. Pokud dojde k nějakému problému, vyberte odkaz pro řešení potíží, které vám pomůžou zjistit, jaké kroky je potřeba k vyřešení tohoto problému.

Pokud chcete spustit prohledávání protokolů, které vrátí informace o počítači, aktualizaci nebo nasazení, vyberte položku v seznamu. Otevře se podokno **prohledávání protokolu** s dotazem na vybranou položku:

![Update Management výchozí zobrazení](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** a zobrazte tak seznam aktualizací, které na vašich počítačích chybí. Každá aktualizace je uvedena a je možné ji vybrat. Zobrazí se informace o počtu počítačů, které vyžadují aktualizaci, o operačním systému a o odkazech. V podokně **prohledávání protokolu** se zobrazí další podrobnosti o aktualizacích.

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
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou v podstatě důležité nebo nejsou aktualizace zabezpečení.        |

V případě systému Linux může Update Management rozlišovat mezi kritickými a bezpečnostními aktualizacemi v cloudu a současně zobrazovat data posouzení z důvodu obohacení dat v cloudu. Pro opravy Update Management spoléhá na data klasifikace, která jsou k dispozici v počítači. Na rozdíl od jiných distribucí nemá CentOS k dispozici tyto informace v poli. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, Update Management bude možné provést opravy na základě klasifikací.

```bash
sudo yum -q --security check-update
```

V tuto chvíli není podporovaná metoda, která umožňuje povolit nativní klasifikaci – dostupnost dat v CentOS. V tuto chvíli se zákazníkům, kteří si ji sami povolili, poskytne jenom osvědčená podpora.

## <a name="next-steps"></a>Další kroky

Po zobrazení všech posouzení aktualizací můžete naplánovat nasazení aktualizací podle kroků v části [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).