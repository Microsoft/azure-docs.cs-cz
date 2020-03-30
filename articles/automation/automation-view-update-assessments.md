---
title: Zobrazení hodnocení aktualizací správy aktualizací Azure
description: Tento článek popisuje, jak zobrazit hodnocení aktualizací pro nasazení aktualizací.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278412"
---
# <a name="view-azure-update-management-update-assessments"></a>Zobrazení hodnocení aktualizací správy aktualizací Azure

Ve svém účtu Azure Automation vyberte **Službu správy aktualizací,** chcete-li zobrazit stav vašich počítačů.

Toto zobrazení obsahuje informace o vašich počítačích, chybějících aktualizacích, nasazeních aktualizací a plánovaných nasazeních aktualizací. Ve sloupci **COMPLIANCE** uvidíte, kdy byl stroj naposledy vyhodnocen. Ve sloupci **PŘIPRAVENOST AKTUALIZACE AGENTA** můžete zobrazit stav aktualizačního agenta. Pokud se u vás nese problém, vyberte odkaz, který chcete přejít k dokumentaci k řešení potíží, která vám pomůže problém vyřešit.

Chcete-li spustit hledání protokolu, které vrací informace o počítači, aktualizaci nebo nasazení, vyberte odpovídající položku v seznamu. Otevře se podokno **Hledání protokolu** s dotazem na vybranou položku:

![Výchozí zobrazení aktualizovat správu](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Výběrem **možnosti Chybějící aktualizace** zobrazíte seznam aktualizací, které ve vašich počítačích chybí. Každá aktualizace je uvedena a může být vybrána. Zobrazí se informace o počtu počítačů, které vyžadují aktualizaci, podrobnosti o operačním systému a odkaz na další informace. Podokno **hledání protokolu** také zobrazuje další podrobnosti o aktualizacích.

![Chybějící aktualizace](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Update classifications

V následujících tabulkách jsou uvedeny podporované klasifikace aktualizací ve správě aktualizací s definicí pro každou klasifikaci.

### <a name="windows"></a>Windows

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizace pro konkrétní problém, který řeší kritickou chybu nesouvisející se zabezpečením.        |
|Aktualizace zabezpečení     | Aktualizace problému souvisejícího se zabezpečením týkajícího se produktu.        |
|Kumulativní aktualizace     | Kumulativní sada oprav hotfix, které jsou zabaleny společně pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu, které jsou distribuovány mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sada oprav hotfix, které jsou použity v aplikaci.        |
|Aktualizace definic     | Aktualizace virů nebo jiných definičních souborů.        |
|Nástroje     | Nástroj nebo funkce, která pomáhá dokončit jeden nebo více úkolů.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je aktuálně nainstalován.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classification  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro konkrétní problém nebo problém související se zabezpečením produktu.         |
|Další aktualizace     | Všechny ostatní aktualizace, které nejsou kritické povahy nebo které nejsou aktualizace zabezpečení.        |

Pro Linux může správa aktualizací rozlišovat mezi důležitými aktualizacemi a aktualizacemi zabezpečení v cloudu při zobrazování dat hodnocení. (Tato rozlišovací schopnost je možná z důvodu obohacení dat v cloudu.) Pro opravy, Správa aktualizací spoléhá na klasifikační data k dispozici na počítači. Na rozdíl od jiných distribucí centos nemá tyto informace k dispozici ve verzích RTM produktu. Pokud máte počítače CentOS nakonfigurované tak, aby vracely data zabezpečení pro následující příkaz, správa aktualizací může opravit na základě klasifikací:

```bash
sudo yum -q --security check-update
```

V současné době neexistuje žádná podporovaná metoda, která by umožnila dostupnost nativních dat klasifikace v CentOS. V současné době je zákazníkům, kteří tuto funkci povolili sami, poskytována pouze podpora s maximálním úsilím.

Chcete-li klasifikovat aktualizace na Red Hat Enterprise verze 6, musíte nainstalovat plugin mňam-security. Na Red Hat Enterprise Linux 7, plugin je již součástí yum sám, není třeba nic instalovat. Další informace naleznete v následujícím [článku znalostní báze](https://access.redhat.com/solutions/10021)Red Hat .

## <a name="next-steps"></a>Další kroky

Po zobrazení všech hodnocení aktualizací můžete naplánovat nasazení aktualizace podle pokynů na [webu Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
