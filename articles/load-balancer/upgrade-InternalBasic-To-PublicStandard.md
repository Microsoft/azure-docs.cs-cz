---
title: Upgrade ze základní veřejné na standardní veřejné – Azure Load Balancer
description: Tento článek ukazuje, jak upgradovat Azure Basic Internal Load Balancer na standardní veřejný vyrovnávání zatížení
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 346fc3d5a4e7b165caafd9847b9797abae0c9113
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659981"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Upgrade interního vytápěče zatížení Azure – je vyžadováno odchozí připojení
[Azure Standard Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o skladové jednotce vykladače zatížení naleznete v [tabulce porovnání](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus). Vzhledem k tomu, že standardní interní vydělávač zatížení neposkytuje odchozí připojení, poskytujeme řešení pro vytvoření standardního veřejného vytáčíče zatížení.

Upgrade má čtyři fáze:

1. Migrace konfigurace do standardního nástroje pro vyrovnávání veřejného zatížení
2. Přidání virtuálních aplikací do back-endových fondů standardního veřejného vyvažovače zatížení
3. Vytvoření odchozího pravidla v systému Vyrovnávání zatížení pro odchozí připojení
4. Nastavení pravidel skupiny zabezpečení sítě pro podsítě/virtuální sítě, která by měla být zdržet se nebo do Internetu

Tento článek popisuje migraci konfigurace. Přidání virtuálních počítačů do back-endových fondů se může lišit v závislosti na konkrétním prostředí. Jsou však [k dispozici](#add-vms-to-backend-pools-of-standard-load-balancer)některá obecná doporučení na vysoké úrovni .

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provádí následující akce:

* Vytvoří standardní vyrovnávání veřejného zatížení skladové položky ve skupině prostředků a umístění, které zadáte.
* Bezproblémově zkopíruje konfigurace základního nástroje pro vyrovnávání vnitřního zatížení skladové položky do nově vytvořeného standardního nástroje pro vyrovnávání veřejného zatížení.

### <a name="caveatslimitations"></a>Upozornění\Omezení

* Skript podporuje upgrade internal load balancer, kde je vyžadováno odchozí připojení. Pokud odchozí připojení není vyžadováno pro některý z virtuálních stránek, naleznete na [této stránce](upgrade-basicInternal-standard.md) pro osvědčený postup.
* Standardní vyrovnávání zatížení má novou veřejnou adresu. Není možné přesunout IP adresy přidružené k existujícímu základnímu internímu vytápěčům zatížení bez problémů do standardního veřejného vytácení zatížení, protože mají různé sku.
* Pokud standardní vyrovnávání zatížení je vytvořen v jiné oblasti, nebudete moci přidružit virtuálních discích existující ve staré oblasti k nově vytvořené standardní vyrovnávání zatížení. Chcete-li toto omezení obejít, nezapomeňte vytvořit nový virtuální virtuální virtuální město v nové oblasti.
* Pokud nástroj pro vyrovnávání zatížení nemá žádnou konfiguraci front-endu IP nebo back-endového fondu, pravděpodobně dosáhnete chyby při spuštění skriptu.  Ujistěte se, že nejsou prázdné.

## <a name="download-the-script"></a>Stáhněte si skript

Stáhněte si skript pro migraci z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0).
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
   * **newlocation: [String]: Povinné** – Toto je umístění, ve kterém bude vytvořen standardní vyrovnávání zatížení. Doporučujeme dědění stejnéumístění zvoleného základního vytápěče zatížení standardní vyrovnávání zatížení pro lepší přidružení s jinými existujícími prostředky.
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

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Vytvoření pravidel nsg pro virtuální sítě, která se zdrží komunikace z internetu nebo k internetu
Pokud se chcete zdržet internetového provozu z dosažení virtuálních počítačů, můžete vytvořit [pravidlo nsg](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) na síťové rozhraní virtuálních počítačů.

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení se skriptem Azure PowerShell pro migraci konfigurace z v1 na v2?

Ano. Viz [Upozornění/omezení](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná skript Azure PowerShellu také přenosy ze základního vytálení zatížení na nově vytvořený standardní vyrovnávání zatížení?

Ne. Skript Azure PowerShell emituje jenom konfiguraci. Skutečná migrace provozu je vaší odpovědností a řídíte se.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Narazil jsem na nějaké problémy s použitím tohoto skriptu. Jak mohu získat pomoc?
  
Můžete poslat e-mail na slbupgradesupport@microsoft.com, otevřete případ podpory s podporou Azure nebo dělat obojí.

## <a name="next-steps"></a>Další kroky

[Další informace o standardním balanceru](load-balancer-overview.md)
