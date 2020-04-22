---
title: Upgrade ze základní veřejné na standardní veřejné – Azure Load Balancer
description: Tento článek ukazuje, jak upgradovat Azure Public Load Balancer ze základní skladové položky na standardní skladovou položku
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a2d6f41756d87e43ac7db9e6a8670c453920c834
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770373"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgrade Azure Public Load Balancer
[Azure Standard Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o skladové jednotce vykladače zatížení naleznete v [tabulce porovnání](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Upgrade má tři fáze:

1. Migrace konfigurace
2. Přidání virtuálních měn do back-endových fondů standardního vyrovnávání zatížení

Tento článek popisuje migraci konfigurace. Přidání virtuálních počítačů do back-endových fondů se může lišit v závislosti na konkrétním prostředí. Jsou však [k dispozici](#add-vms-to-backend-pools-of-standard-load-balancer)některá obecná doporučení na vysoké úrovni .

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provádí následující akce:

* Vytvoří standardní účetní čítič zatížení skladové položky ve skupině prostředků a umístění, které zadáte.
* Bezproblémově zkopíruje konfigurace základního nástroje pro vyrovnávání zatížení skladových položk do nově vytvořeného standardního nástroje pro vyrovnávání zatížení.
* Vytvoří výchozí odchozí pravidlo, které umožňuje odchozí připojení.

### <a name="caveatslimitations"></a>Upozornění\Omezení

* Skript podporuje pouze upgrade veřejného vyvažovače zatížení. Informace o interním základním vyrovnávání zatížení naleznete v [této stránce.](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard)
* Standardní vyrovnávání zatížení má novou veřejnou adresu. Není možné přesunout IP adresy přidružené k existujícímu základnímu vytácení zatížení bez problémů do standardního vykladače zatížení, protože mají různé sku.
* Pokud standardní vyrovnávání zatížení je vytvořen v jiné oblasti, nebudete moci přidružit virtuálních discích existující ve staré oblasti k nově vytvořené standardní vyrovnávání zatížení. Chcete-li toto omezení obejít, nezapomeňte vytvořit nový virtuální virtuální virtuální město v nové oblasti.
* Pokud nástroj pro vyrovnávání zatížení nemá žádnou konfiguraci front-endu IP nebo back-endového fondu, pravděpodobně dosáhnete chyby při spuštění skriptu. Ujistěte se, že nejsou prázdné.

## <a name="download-the-script"></a>Stáhněte si skript

Stáhněte si skript pro migraci z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>Použití skriptu

V závislosti na místním nastavení prostředí prostředí PowerShellu a předvolbách máte dvě možnosti:

* Pokud nemáte nainstalované moduly Azure Az nebo vám nevadí odinstalovat moduly Azure Az, je `Install-Script` nejlepší použít možnost ke spuštění skriptu.
* Pokud potřebujete zachovat moduly Azure Az, je nejlepší stáhnout skript a spustit přímo.

Chcete-li zjistit, zda máte nainstalované `Get-InstalledModule -Name az`moduly Azure Az, spusťte . Pokud nevidíte žádné nainstalované Moduly Az, můžete `Install-Script` použít metodu.

### <a name="install-using-the-install-script-method"></a>Instalace pomocí metody Install-Script

Chcete-li použít tuto možnost, nesmíte mít v počítači nainstalované moduly Azure Az. Pokud jsou nainstalovány, zobrazí následující příkaz chybu. Můžete buď odinstalovat moduly Azure Az, nebo použít jinou možnost ke stažení skriptu ručně a spustit jej.
  
Spusťte skript pomocí následujícího příkazu:

`Install-Script -Name AzurePublicLBUpgrade`

Tento příkaz také nainstaluje požadované moduly Az.  

### <a name="install-using-the-script-directly"></a>Instalace pomocí skriptu přímo

Pokud máte nainstalované některé moduly Azure Az a nemůžete je odinstalovat (nebo je nechcete odinstalovat), můžete skript stáhnout ručně pomocí karty **Ruční stažení** v odkazu ke stažení skriptu. Skript je stažen jako nezpracovaný soubor nupkg. Chcete-li nainstalovat skript z tohoto souboru nupkg, přečtěte [si téma Ruční stahování balíčků](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Spuštění skriptu:

1. Slouží `Connect-AzAccount` k připojení k Azure.

1. Slouží `Import-Module Az` k importu modulů Az.

1. Zkontrolujte požadované parametry:

   * **oldRgName: [String]: Povinné** – Toto je skupina prostředků pro stávající základní vyrovnávání zatížení, které chcete upgradovat. Chcete-li najít tuto hodnotu řetězce, přejděte na portál Azure, vyberte zdroj základnínástroje pro vyrovnávání zatížení a klikněte na **přehled** nástroje pro vyrovnávání zatížení. Skupina prostředků se nachází na této stránce.
   * **oldLBName: [String]: Povinné** – Toto je název stávajícího základního vyvažovače, který chcete upgradovat. 
   * **newrgName: [String]: Povinné** – Toto je skupina prostředků, ve které bude vytvořen standardní vyrovnávání zatížení. Může se jedná o novou skupinu prostředků nebo existující skupinu prostředků. Pokud vyberete existující skupinu prostředků, všimněte si, že název správce zatížení musí být jedinečný v rámci skupiny prostředků. 
   * **newlocation: [String]: Povinné** – Toto je umístění, ve kterém bude vytvořen standardní vyrovnávání zatížení. Doporučujeme dědit stejné umístění vybraného základního nástroje pro vyrovnávání zatížení standardnímu nástroje pro vyrovnávání zatížení pro lepší přidružení k jiným existujícím prostředkům.
   * **newLBName: [String]: Povinné** – Toto je název pro standardní vyrovnávání zatížení, které mají být vytvořeny.
1. Spusťte skript pomocí příslušných parametrů. Dokončení může trvat pět až sedm minut.

    **Příklad**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Přidání virtuálních měn do back-endových fondů standardního vyrovnávání zatížení

Nejprve zkontrolujte, zda skript úspěšně vytvořil nový standardní nástroj pro vyrovnávání veřejného zatížení s přesnou konfigurací přenesenou z nástroje pro vyrovnávání veřejného zatížení. Můžete to ověřit z webu Azure Portal.

Ujistěte se, že poslat malé množství provozu prostřednictvím standardního vyrovnávání zatížení jako ruční test.
  
Tady je několik scénářů, jak přidat virtuální počítače do back-endových fondů nově vytvořeného standardního vyrovnávání veřejného vyrovnávání zatížení, a naše doporučení pro každý z nich:

* **Přesunutí existujících virtuálních mích z back-endových fondů starého základního vyvažovače veřejného zatížení do back-endových fondů nově vytvořeného standardního vyvažovače veřejného zatížení**.
    1. Chcete-li provést úkoly v tomto rychlém startu, přihlaste se na [portál Azure](https://portal.azure.com).
 
    1. V levém menu vyberte **Všechny zdroje** a ze seznamu zdrojů vyberte nově vytvořený standardní **systém vyrovnávání zatížení.**
   
    1. V části **Nastavení**vyberte **Back-endové fondy**.
   
    1. Vyberte back-endový fond, který odpovídá back-endovému fondu základního vytápětku zatížení, vyberte následující hodnotu: 
      - **Virtuální počítač**: Rozbalte a vyberte virtuální počítače z odpovídajícího back-endového fondu základního nástroje pro vyrovnávání zatížení.
    1. Vyberte **Uložit**.
    >[!NOTE]
    >Pro virtuální virtuální společnosti, které mají veřejné IP adresy, budete muset nejprve vytvořit standardní IP adresy, kde není zaručena stejná IP adresa. Odpojte virtuální počítačů od základních IP adres a přidružte je k nově vytvořeným standardním IP adresám. Potom budete moci postupovat podle pokynů k přidání virtuálních her do back-endového fondu standardního vykladače zatížení. 

* **Vytváření nových virtuálních discích, které chcete přidat do back-endových fondů nově vytvořeného standardního veřejného vyvažovače zatížení**.
    * Další pokyny k vytvoření virtuálního počítače a jeho přidružení ke standardnímu vyvažovači zatížení naleznete [zde](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Vytvoření odchozího pravidla pro odchozí připojení

Podle [pokynů](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) vytvořte odchozí pravidlo, abyste mohli
* Definujte odchozí NAT od začátku.
* Škálování a ladění chování existujícího odchozího nat.

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení se skriptem Azure PowerShell pro migraci konfigurace z v1 na v2?

Ano. Viz [Upozornění/omezení](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná skript Azure PowerShellu také přenosy ze základního vytálení zatížení na nově vytvořený standardní vyrovnávání zatížení?

Ne. Skript Azure PowerShell emituje jenom konfiguraci. Skutečná migrace provozu je vaší odpovědností a řídíte se.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazil jsem na nějaké problémy s použitím tohoto skriptu. Jak mohu získat pomoc?
  
Můžete poslat e-mail na slbupgradesupport@microsoft.com, otevřete případ podpory s podporou Azure nebo dělat obojí.

## <a name="next-steps"></a>Další kroky

[Další informace o standardním balanceru](load-balancer-overview.md)
