---
title: Upgrade ze základního interního na standardní interní Azure Load Balancer
description: V tomto článku se dozvíte, jak upgradovat interní Load Balancer Azure z úrovně Basic SKU na standard SKU.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770392"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgradovat interní Load Balancer Azure – nevyžaduje se žádné odchozí připojení.
[Azure Standard Load Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o Load Balancer SKU najdete v tématu [srovnávací tabulka](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Tento článek představuje skript prostředí PowerShell, který vytvoří Standard Load Balancer se stejnou konfigurací jako základní Load Balancer a migrací provozu ze základní Load Balancer na Standard Load Balancer.

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provede následující akce:

* Vytvoří standardní interní SKU Load Balancer v umístění, které zadáte. Všimněte si, že standardní interní Load Balancer nebude poskytovat žádné [odchozí připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) .
* Bezproblémově kopíruje konfiguraci základní SKU Load Balancer do nově vytvořené Standard Load Balancer.
* Můžete bez problémů přesunout privátní IP adresy ze základních Load Balancer do nově vytvořené Standard Load Balancer.
* Virtuální počítače můžete bez problémů přesunout ze back-endu základního Load Balancer do fondu back-endu Standard Load Balancer

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skript podporuje pouze interní Load Balancer upgrade, pokud není nutné žádné odchozí připojení. Pokud jste pro některé z vašich virtuálních počítačů vyžádali [odchozí připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) , přečtěte si prosím na této [stránce](upgrade-InternalBasic-To-PublicStandard.md) pokyny. 
* Pokud se standardní nástroj pro vyrovnávání zatížení vytvoří v jiné oblasti, nebudete moct k nově vytvořeným Standard Load Balancer přidružit virtuální počítače existující ve staré oblasti. Pokud chcete toto omezení obejít, nezapomeňte vytvořit nový virtuální počítač v nové oblasti.
* Pokud vaše Load Balancer nemá front-end IP konfiguraci ani back-end fond, pravděpodobně při spuštění skriptu dojde k chybě. Ujistěte se, že nejsou prázdné.

## <a name="download-the-script"></a>Stáhnout skript

Stáhněte si skript migrace z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Použití skriptu

V závislosti na nastaveních a preferencích místního prostředí PowerShellu jsou k dispozici dvě možnosti:

* Pokud nemáte nainstalované moduly AZ pro Azure nebo si nejste připustili odinstalaci modulů AZ pro Azure, nejlepší možností je použít `Install-Script` možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure AZ, nejlepším řešením je stáhnout skript a spustit ho přímo.

Pokud chcete zjistit, jestli máte nainstalované moduly Azure AZ, spusťte `Get-InstalledModule -Name az`. Pokud nevidíte žádné nainstalované moduly AZ, můžete použít `Install-Script` metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody install-Script

Pokud chcete použít tuto možnost, musíte mít v počítači nainstalované moduly AZ pro Azure. Pokud jsou nainstalovány, následující příkaz zobrazí chybu. Můžete buď odinstalovat moduly AZ pro Azure, nebo použít jinou možnost ke stažení skriptu ručně a jeho spuštění.
  
Spusťte skript s následujícím příkazem:

`Install-Script -Name AzureILBUpgrade`

Tento příkaz nainstaluje také požadované moduly AZ Modules.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure AZ a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete ručně stáhnout skript pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript se stáhne jako nezpracovaný soubor nupkg. Pokud chcete skript nainstalovat z tohoto souboru nupkg, přečtěte si téma [Ruční stažení balíčku](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Pomocí `Import-Module Az` nástroje importujte moduly AZ.

1. Projděte si požadované parametry:

   * **RgName: [String]: povinné** – jedná se o skupinu prostředků pro stávající základní Load Balancer a nové Standard Load Balancer. Tuto řetězcovou hodnotu zjistíte tak, že přejdete na Azure Portal, vyberete svůj základní zdroj Load Balancer a kliknete na **Přehled** nástroje pro vyrovnávání zatížení. Skupina prostředků se nachází na dané stránce.
   * **oldLBName: [String]: povinné** – Toto je název vašeho stávajícího základního nástroje pro vyrovnávání zatížení, který chcete upgradovat. 
   * **NewLocation: [String]: povinné** – Toto je umístění, ve kterém se vytvoří Standard Load Balancer. Doporučuje se dědit stejné umístění zvoleného základního Load Balancer do Standard Load Balancer pro lepší přidružení k ostatním existujícím prostředkům.
   * **newLBName: [String]: Required** – jedná se o název Standard Load Balancer, který se má vytvořit.
1. Spusťte skript s použitím příslušných parametrů. Dokončení může trvat pět až 7 minut.

    **Případě**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení Azure PowerShell skriptu pro migraci konfigurace z V1 na v2?

Ano. Podívejte se na [Upozornění a omezení](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná Azure PowerShell skript také přenos z mé základní Load Balancer na nově vytvořenou Standard Load Balancer?

Ano, migruje provoz. Pokud byste chtěli migrovat provoz osobně, použijte [Tento skript](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) , který pro vás nepřesouvá virtuální počítače.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazili jsme na některé problémy s použitím tohoto skriptu. Jak získám pomoc?
  
Můžete odeslat e-mail slbupgradesupport@microsoft.com, otevřít případ podpory s podporou Azure nebo obojí.

## <a name="next-steps"></a>Další kroky

[Informace o Standard Load Balancer](load-balancer-overview.md)
