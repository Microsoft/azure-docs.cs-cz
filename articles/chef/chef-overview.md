---
title: Používání chefa s Azure
description: Úvod k konfiguraci a testování infrastruktury Azure pomocí Chefa
keywords: azure, chef, devops, virtuální stroje, přehled, automatizace
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586338"
---
# <a name="using-chef-with-azure"></a>Používání chefa s Azure
[Chef](https://www.chef.io) je výkonná automatizační platforma, která transformuje infrastrukturu virtuálních strojů v Azure na kód. Chef automatizuje konfiguraci, nasazení a spravování infrastruktury v celé síti bez ohledu na její velikost.

Tento článek popisuje výhody použití Chef ke správě infrastruktury Azure.

## <a name="chef-extension-on-azure"></a>Rozšíření chef v Azure
Zřídit virtuální počítač s Chef Klient běží jako služba na pozadí s [chef rozšíření](https://docs.microsoft.com/azure/chef/chef-extension-portal) na webu [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Po zřízení jsou tyto virtuální počítače připravené ke spravovanému serverem Chef.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Použijte pracovní stanici Chef přímo v Azure Cloud Shellu! Spusťte všechny své nástroje Chef a InSpec přímo z Cloud Shellu. Příkazy Chef můžete využít z následujících stran:

* [Kuchař](https://docs.chef.io/ctl_chef.html)
* [Kuchyně](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [Nůž](https://docs.chef.io/knife.html)
* [kuchařský styl](https://docs.chef.io/cookstyle.html)
* [kuchař-běh](https://www.chef.sh/docs/chef-workstation/getting-started/)

Zkombinujte naše příkazové nástroje s dalšími `git`nástroji `az-cli`dostupnými v prostředí Cloud Shell, jako jsou aplikace , a , a `terraform`napište svou infrastrukturu a automatizaci dodržování předpisů z prohlížeče.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatizace infrastruktury, aplikací a dodržování předpisů s jednou platformou
Společnosti vyžadují rychlost, rychlost a bezpečnost, aby mohly konkurovat na digitálním trhu. Šéfkuchař a Microsoft společně pomáhají jednotlivcům, týmům a podnikům dosáhnout všech těchto věcí. S jednou platformou Chef Automate teď můžete automatizovat a průběžně poskytovat infrastrukturu, aplikace a dodržování předpisů v celém svém sídle Microsoftu.

## <a name="test-drive-chef-automate-on-azure"></a>Automatizace testovací jízdy chef v Azure
[Řešení Chef Automate Azure Marketplace,](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) podporované šéfkuchařem, umožňuje vytvářet, nasazovat a spravovat infrastrukturu a aplikace společně. Jedním kliknutím získáte okamžitý přístup ke všem komerčním funkcím, které jsou součástí chef automate; získejte komplexní přehled o celém vozovém parku, povolte nepřetržité dodržování předpisů a spravujte všechny změny pomocí jednotného pracovního postupu.

## <a name="next-steps"></a>Další kroky

* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefa](chef-automation.md)
