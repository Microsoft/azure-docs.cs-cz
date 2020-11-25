---
title: Upgrade ze základní veřejné na standardní veřejné Azure Load Balancer
description: V tomto článku se dozvíte, jak upgradovat veřejné Load Balancer Azure z úrovně Basic SKU na standard SKU.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: dd0617536147787f436e5817f3f2367a19ba6aa4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009275"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgrade veřejné Load Balancer Azure
[Azure Standard Load Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o Load Balancer SKU najdete v tématu [srovnávací tabulka](./skus.md#skus).

V upgradu existují tři fáze:

1. Migrace konfigurace
2. Přidání virtuálních počítačů do back-endovéch fondů Standard Load Balancer

Tento článek popisuje migraci konfigurace. Přidávání virtuálních počítačů do back-end fondů se může lišit v závislosti na konkrétním prostředí. [Jsou však k dispozici](#add-vms-to-backend-pools-of-standard-load-balancer)některá obecná doporučení.

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provede následující akce:

* Vytvoří standardní SKU Load Balancer ve skupině prostředků a umístění, které zadáte.
* Bezproblémově kopíruje konfiguraci základní SKU Load Balancer nově vytvořeným Standard Load Balancer.
* Vytvoří výchozí odchozí pravidlo, které umožňuje odchozí připojení.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skript podporuje pouze upgrade veřejné Load Balancer. Pro interní základní Load Balancer upgrade najdete pokyny v [této stránce](./upgrade-basicinternal-standard.md) .
* Standard Load Balancer má novou veřejnou adresu. Je možné, že nebudete moct bez problémů přesunout IP adresy přidružené k existujícím základním Load Balancerm Standard Load Balancer, protože mají jiné SKU.
* Pokud se standardní nástroj pro vyrovnávání zatížení vytvoří v jiné oblasti, nebudete moct k nově vytvořeným Standard Load Balancer přidružit virtuální počítače existující ve staré oblasti. Pokud chcete toto omezení obejít, nezapomeňte vytvořit nový virtuální počítač v nové oblasti.
* Pokud vaše Load Balancer nemá front-end IP konfiguraci ani back-end fond, pravděpodobně při spuštění skriptu dojde k chybě. Ujistěte se prosím, že nejsou prázdné.

## <a name="download-the-script"></a>Stáhnout skript

Stáhněte si skript migrace z  [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>Použití skriptu

V závislosti na nastaveních a preferencích místního prostředí PowerShellu jsou k dispozici dvě možnosti:

* Pokud nemáte nainstalované moduly AZ pro Azure nebo si nejste připustili odinstalaci modulů AZ pro Azure, nejlepší možností je použít `Install-Script` možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure AZ, nejlepším řešením je stáhnout skript a spustit ho přímo.

Pokud chcete zjistit, jestli máte nainstalované moduly Azure AZ, spusťte `Get-InstalledModule -Name az` . Pokud nevidíte žádné nainstalované moduly AZ, můžete použít `Install-Script` metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody Install-Script

Pokud chcete použít tuto možnost, musíte mít v počítači nainstalované moduly AZ pro Azure. Pokud jsou nainstalovány, následující příkaz zobrazí chybu. Můžete buď odinstalovat moduly AZ pro Azure, nebo použít jinou možnost ke stažení skriptu ručně a jeho spuštění.
  
Spusťte skript s následujícím příkazem:

`Install-Script -Name AzurePublicLBUpgrade`

Tento příkaz nainstaluje také požadované moduly AZ Modules.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure AZ a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete ručně stáhnout skript pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript se stáhne jako nezpracovaný soubor nupkg. Pokud chcete skript nainstalovat z tohoto souboru nupkg, přečtěte si téma [Ruční stažení balíčku](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Pomocí nástroje `Import-Module Az` importujte moduly AZ.

1. Projděte si požadované parametry:

   * **oldRgName: [String]: povinné** – jedná se o skupinu prostředků pro stávající základní Load Balancer, kterou chcete upgradovat. Tuto řetězcovou hodnotu zjistíte tak, že přejdete na Azure Portal, vyberete svůj základní zdroj Load Balancer a kliknete na **Přehled** nástroje pro vyrovnávání zatížení. Skupina prostředků se nachází na dané stránce.
   * **oldLBName: [String]: povinné** – Toto je název vašeho stávajícího základního nástroje pro vyrovnávání zatížení, který chcete upgradovat. 
   * **newrgName: [String]: povinné** – jedná se o skupinu prostředků, ve které se vytvoří Standard Load Balancer. Může se jednat o novou skupinu prostředků nebo o tu existující. Pokud vyberete existující skupinu prostředků, Všimněte si, že název Load Balancer musí být v rámci skupiny prostředků jedinečný. 
   * **NewLocation: [String]: povinné** – Toto je umístění, ve kterém se vytvoří Standard Load Balancer. Doporučuje se dědit stejné umístění zvoleného základního Load Balancer do Standard Load Balancer pro lepší přidružení k ostatním existujícím prostředkům.
   * **newLBName: [String]: Required** – jedná se o název Standard Load Balancer, který se má vytvořit.
1. Spusťte skript s použitím příslušných parametrů. Dokončení může trvat pět až 7 minut.

    **Příklad**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Přidání virtuálních počítačů do back-endovéch fondů Standard Load Balancer

Nejprve dvakrát ověřte, že skript úspěšně vytvořil novou standardní veřejnou Load Balancer s přesnou konfigurací, která je migrována ze základní veřejné Load Balancer. Můžete to ověřit z Azure Portal.

Nezapomeňte poslat malý objem provozu prostřednictvím Standard Load Balancer jako ruční test.
  
Tady je několik scénářů, jak můžete nakonfigurovat virtuální počítače do back-endovéch fondů nově vytvořených standardních veřejných Load Balancer a naše doporučení pro každé z nich:

* **Přesunování stávajících virtuálních počítačů ze back-endového fondu starých základních Load Balancer do back-endu nově vytvořených standardních veřejných Load Balancer**.
    1. Pokud chcete provádět úkoly v rámci tohoto rychlého startu, přihlaste se k [Azure Portal](https://portal.azure.com).
 
    1. V nabídce vlevo vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **nově vytvořenou Standard Load Balancer** .
   
    1. V části **Nastavení** vyberte **back-endové fondy**.
   
    1. Vyberte back-end fond, který se shoduje se back-end fondem základního Load Balancer, vyberte následující hodnotu: 
      - **Virtuální počítač**: rozevírací seznam a výběr virtuálních počítačů z odpovídajícího back-end fondu základní Load Balancer.
    1. Vyberte **Uložit**.
    >[!NOTE]
    >U virtuálních počítačů, které mají veřejné IP adresy, budete muset nejprve vytvořit standardní IP adresy, kde není zaručena stejná IP adresa. Zruší přidružení virtuálních počítačů ze základních IP adres a přidruží je k nově vytvořeným standardním IP adresám. Pak budete moct postupovat podle pokynů pro přidání virtuálních počítačů do back-endového fondu Standard Load Balancer. 

* **Vytváření nových virtuálních počítačů pro přidání do back-endovéch fondů nově vytvořených standardních veřejných Load Balancer**.
    * Další pokyny k vytvoření virtuálního počítače a jeho přidružení k Standard Load Balancer najdete [tady](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Vytvoření odchozího pravidla pro odchozí připojení

Postupujte podle [pokynů](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) pro vytvoření odchozího pravidla, abyste mohli
* Definice odchozího překladu adres (NAT) od začátku
* Škálujte a optimalizujte chování stávajícího odchozího překladu adres (NAT).

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení Azure PowerShell skriptu pro migraci konfigurace z V1 na v2?

Ano. Podívejte se na [Upozornění a omezení](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná Azure PowerShell skript také přenos z mé základní Load Balancer na nově vytvořenou Standard Load Balancer?

Ne. Azure PowerShell skript migruje pouze konfiguraci. Skutečná migrace provozu je vaší zodpovědností a vaším ovládacím prvkem.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazili jsme na některé problémy s použitím tohoto skriptu. Jak získám pomoc?
  
Můžete odeslat e-mail slbupgradesupport@microsoft.com , otevřít případ podpory s podporou Azure nebo obojí.

## <a name="next-steps"></a>Další kroky

[Informace o Standard Load Balancer](load-balancer-overview.md)