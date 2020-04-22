---
title: Upgrade ze základní interní na standardní interní – Azure Load Balancer
description: Tento článek ukazuje, jak upgradovat Azure Internal Load Balancer ze základní skladové položky na standardní skladovou položku
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770392"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgrade interního vytápěče zatížení Azure – není vyžadováno žádné odchozí připojení
[Azure Standard Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o skladové jednotce vykladače zatížení naleznete v [tabulce porovnání](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Tento článek zavádí skript prostředí PowerShell, který vytváří standardní nástroj pro vyrovnávání zatížení se stejnou konfigurací jako základní nástroj pro vyrovnávání zatížení spolu s migrací provozu ze základního nástroje pro vyrovnávání zatížení na standardní nástroj pro vyrovnávání zatížení.

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provádí následující akce:

* Vytvoří standardní interní vydělávač zatížení skladové položky v zadaném umístění. Všimněte si, že standardní interní vytápěč zatížení neposkytne žádné [odchozí připojení.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)
* Bezproblémově zkopíruje konfigurace základního nástroje pro vyrovnávání zatížení skladové položky do nově vytvořeného standardního nástroje pro vyrovnávání zatížení.
* Bez problémů přesuňte privátní IP adresy ze základního vytálení zatížení do nově vytvořeného standardního vytáceče zatížení.
* Bezproblémový přesun virtuálních her z back-endového fondu základního vykladače zatížení do back-endového fondu standardního vykladače zatížení

### <a name="caveatslimitations"></a>Upozornění\Omezení

* Skript podporuje pouze upgrade internal load balancer, kde není vyžadováno žádné odchozí připojení. Pokud jste pro některé virtuální počítače požadovali [odchozí připojení,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) přečtěte si pokyny na této [stránce.](upgrade-InternalBasic-To-PublicStandard.md) 
* Pokud standardní vyrovnávání zatížení je vytvořen v jiné oblasti, nebudete moci přidružit virtuálních discích existující ve staré oblasti k nově vytvořené standardní vyrovnávání zatížení. Chcete-li toto omezení obejít, nezapomeňte vytvořit nový virtuální virtuální virtuální město v nové oblasti.
* Pokud nástroj pro vyrovnávání zatížení nemá žádnou konfiguraci front-endu IP nebo back-endového fondu, pravděpodobně dosáhnete chyby při spuštění skriptu. Ujistěte se, že nejsou prázdné.

## <a name="download-the-script"></a>Stáhněte si skript

Stáhněte si skript pro migraci z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Použití skriptu

V závislosti na místním nastavení prostředí prostředí PowerShellu a předvolbách máte dvě možnosti:

* Pokud nemáte nainstalované moduly Azure Az nebo vám nevadí odinstalovat moduly Azure Az, je `Install-Script` nejlepší použít možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure Az, je nejlepší stáhnout skript a spustit přímo.

Chcete-li zjistit, zda máte nainstalované `Get-InstalledModule -Name az`moduly Azure Az, spusťte . Pokud nevidíte žádné nainstalované Moduly Az, můžete `Install-Script` použít metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody Install-Script

Chcete-li použít tuto možnost, nesmíte mít v počítači nainstalované moduly Azure Az. Pokud jsou nainstalovány, zobrazí následující příkaz chybu. Můžete buď odinstalovat moduly Azure Az, nebo použít jinou možnost ke stažení skriptu ručně a spustit jej.
  
Spusťte skript pomocí následujícího příkazu:

`Install-Script -Name AzureILBUpgrade`

Tento příkaz také nainstaluje požadované moduly Az.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure Az a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete skript stáhnout ručně pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript je stažen jako nezpracovaný soubor nupkg. Chcete-li nainstalovat skript z tohoto souboru nupkg, přečtěte [si téma Ruční stahování balíčků](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Slouží `Import-Module Az` k importu modulů Az.

1. Zkontrolujte požadované parametry:

   * **rgName: [String]: Povinné** – Toto je skupina prostředků pro stávající základní vyrovnávání zatížení a nový standardní vyrovnávání zatížení. Chcete-li najít tuto hodnotu řetězce, přejděte na portál Azure, vyberte zdroj základnínástroje pro vyrovnávání zatížení a klikněte na **přehled** nástroje pro vyrovnávání zatížení. Skupina prostředků se nachází na této stránce.
   * **oldLBName: [String]: Povinné** – Toto je název stávajícího základního vyvažovače, který chcete upgradovat. 
   * **newlocation: [String]: Povinné** – Toto je umístění, ve kterém bude vytvořen standardní vyrovnávání zatížení. Doporučujeme dědit stejné umístění vybraného základního nástroje pro vyrovnávání zatížení standardnímu nástroje pro vyrovnávání zatížení pro lepší přidružení k jiným existujícím prostředkům.
   * **newLBName: [String]: Povinné** – Toto je název pro standardní vyrovnávání zatížení, které mají být vytvořeny.
1. Spusťte skript pomocí příslušných parametrů. Dokončení může trvat pět až sedm minut.

    **Příklad**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení se skriptem Azure PowerShell pro migraci konfigurace z v1 na v2?

Ano. Viz [Upozornění/omezení](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná skript Azure PowerShellu také přenosy ze základního vytálení zatížení na nově vytvořený standardní vyrovnávání zatížení?

Ano, migruje provoz. Pokud chcete migrovat provoz osobně, použijte [tento skript,](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) který nepřesune virtuální chod za vás.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazil jsem na nějaké problémy s použitím tohoto skriptu. Jak mohu získat pomoc?
  
Můžete poslat e-mail na slbupgradesupport@microsoft.com, otevřete případ podpory s podporou Azure nebo dělat obojí.

## <a name="next-steps"></a>Další kroky

[Další informace o standardním balanceru](load-balancer-overview.md)
