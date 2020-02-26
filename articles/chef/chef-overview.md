---
title: Použití systému pro práci s Azure
description: Úvod k konfiguraci a testování infrastruktury Azure pomocí systému pro použití
keywords: Azure, DevOps, virtuální počítače, přehled, automatizace
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586338"
---
# <a name="using-chef-with-azure"></a>Použití systému pro práci s Azure
[](https://www.chef.io) Nástroj pro automatizaci je výkonná platforma pro automatizaci, která transformuje infrastrukturu virtuálních počítačů v Azure do kódu. Tento příkaz automatizuje způsob konfigurace, nasazení a správy infrastruktury v rámci vaší sítě bez ohledu na její velikost.

V tomto článku se dozvíte o výhodách používání systému pro správu a správu infrastruktury Azure.

## <a name="chef-extension-on-azure"></a>Rozšíření pro Azure v Azure
Zajistěte, aby byl virtuální počítač se spuštěným klientem systému pro službu na pozadí s [rozšířením](https://docs.microsoft.com/azure/chef/chef-extension-portal) pro přístup k systému pro počítače v [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Po zřízení jsou tyto virtuální počítače připravené na správu pomocí serveru pro správu.

## <a name="chef-cloud-shell"></a>Cloud Shell pro.
Použijte přímo v Azure Cloud Shell. Spusťte všechny vaše nástroje a inspecer z Cloud Shell. Můžete využít příkazy pro příkaz \ z:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [uspořádání](https://docs.chef.io/ctl_kitchen.html)
* [INSPEC](https://www.inspec.io/docs/reference/cli/)
* [nůž](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [příkaz pro spuštění](https://www.chef.sh/docs/chef-workstation/getting-started/)

Pomocí dalších nástrojů, které jsou k dispozici v Cloud Shell, jako jsou `git`, `az-cli`a `terraform`, Zkombinujte naše příkazové nástroje a v prohlížeči zapište infrastrukturu a automatizaci dodržování předpisů.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatizace infrastruktury, aplikací a kompatibility s jednou platformou
Společnosti vyžadují rychlost, rychlost a bezpečnost k konkurenci v digitálním tržišti. Všichni uživatelé, týmy a podniky Microsoftu a pomáhat s nimi budou plnit všechny tyto věci. S jednou platformou a nástrojem pro automatizaci teď můžete automatizovat a průběžně doručovat infrastrukturu, aplikace a dodržování předpisů v rámci vaší společnosti Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test ROM – automatizace v Azure
Díky tomu, že je k dispozici, nabízí [řešení pro automatizaci Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) , které umožňuje sestavovat, nasazovat a spravovat infrastrukturu a aplikace v rámci spolupráce. Jedním kliknutím získáte okamžitý přístup ke všem komerčním funkcím, které jsou součástí funkce pro automatizaci. Získejte kompletní přehled napříč celým loďstvem, umožněte průběžné dodržování předpisů a spravujte veškerou změnu pomocí sjednoceného pracovního postupu.

## <a name="next-steps"></a>Další kroky

* [Vytvoření virtuálního počítače s Windows v Azure pomocí systému pro vytváření počítačů](chef-automation.md)
