---
title: Azure pomocí Chefu
description: Úvod do nakonfigurovat a otestovat infrastrukturou Azure pomocí Chefu
ms.service: virtual-machines-linux
keywords: Azure, chef, devops, virtuální počítače, přehled, automatizovat
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: be1e7ab953c55581645a9702fc4759cb603e7ecc
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357857"
---
# <a name="using-chef-with-azure"></a>Azure pomocí Chefu
[Chef](http://www.chef.io) je výkonným Automatizačním platforma, která transformuje infrastruktury virtuálních počítačů v Azure do kódu. Chef automatizuje, jak je infrastruktura nakonfigurované, nasazují a spravují v síti, bez ohledu na velikost.

Tento článek popisuje výhody použití Chef ke správě infrastruktury Azure.

## <a name="chef-extension-on-azure"></a>Rozšíření Chef v Azure
Zřízení virtuálního počítače pomocí Chefu klienta spuštění jako služby na pozadí s [Chef rozšíření](https://docs.microsoft.com/en-us/azure/chef/chef-extension-portal) na webu Azure Portal. Po zřízení se tyto virtuální počítače jsou připravené na správu serverem Chef.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Pracovní stanice pomocí Chefu přímo ve službě Azure Cloud Shell! Spusťte všechny nástroje Chef a InSpec vpravo od služby Cloud Shell. Můžete využít příkazy Chef od:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinovat našich nástrojích příkazového s dalšími nástroji k dispozici ve službě Cloud Shell, jako například `git`, `az-cli`, a `terraform`a zapisovat automatizace infrastruktury a dodržování předpisů z prohlížeče.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatizace infrastruktury, aplikací a dodržování předpisů s jednou platformou
Společnosti vyžadují rychlost, rychlost a bezpečný přístup z více zaregistrovaní digital marketplace. Chef a Microsoft společně pomáhají jednotlivce, týmy i velké podniky provést všechny tyto věci. Pomocí jedné platformě Chef Automate, můžete nyní automatizovat a průběžně doručovat infrastruktury, aplikací a dodržování předpisů ve vaší majetku společnosti Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Vyzkoušejte si Chef Automate v Azure
Podporuje Chef, [řešení Azure Marketplace automatizaci Chef](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) umožňuje vytvořit, nasadit a spravovat infrastrukturu a aplikace společně. Jedním kliknutím získáte okamžitý přístup ke všem komerčním funkcím, které jsou součástí Chef Automate; Získejte potřebný přehled začátku do konce napříč celou řadu typů, umožníte průběžné dodržování předpisů a správu všech změn pomocí unifikovaného pracovního postupu.

## <a name="next-steps"></a>Další postup

* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefu](/azure/virtual-machines/windows/chef-automation)
