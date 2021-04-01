---
title: Upgrade ze základní veřejné na standardní veřejné Azure Load Balancer
description: V tomto článku se dozvíte, jak upgradovat veřejné Load Balancer Azure z úrovně Basic SKU na standard SKU.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612826"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgrade veřejné Load Balancer Azure
[Azure Standard Load Balancer](load-balancer-overview.md) nabízí bohatou sadu funkcí a vysokou dostupnost prostřednictvím redundance zóny. Další informace o Load Balancer SKU najdete v tématu [srovnávací tabulka](./skus.md#skus).

Existují dva fáze upgradu:

1. Změňte metodu přidělování IP adres z dynamické na statickou.
2. Spuštěním skriptu PowerShellu dokončete upgrade a migraci provozu.

## <a name="upgrade-overview"></a>Přehled upgradu

K dispozici je skript Azure PowerShell, který provede následující akce:

* Vytvoří standardní SKU Load Balancer se zadaným umístěním ve stejné skupině prostředků základní Standard Load Balancer.
* Provede upgrade veřejné IP adresy ze základní SKU na místo na úrovni Standard.
* Bezproblémově kopíruje konfiguraci základní SKU Load Balancer nově vytvořeným Standard Load Balancer.
* Vytvoří výchozí odchozí pravidlo, které umožňuje odchozí připojení.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skript podporuje pouze upgrade veřejné Load Balancer. Pro interní základní Load Balancer upgrade najdete pokyny v [této stránce](./upgrade-basicinternal-standard.md) .
* Před spuštěním skriptu musí být metoda přidělení veřejné IP adresy změněna na "static". 
* Pokud vaše Load Balancer nemá front-end IP konfiguraci ani back-end fond, pravděpodobně při spuštění skriptu dojde k chybě. Ujistěte se prosím, že nejsou prázdné.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Změnit metodu přidělení veřejné IP adresy na statickou

* * * Tady jsou doporučené kroky:

    1. Pokud chcete provádět úkoly v rámci tohoto rychlého startu, přihlaste se k [Azure Portal](https://portal.azure.com).
 
    1. V nabídce vlevo vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **základní veřejnou IP adresu přidruženou k základní Load Balancer** .
   
    1. V části **Nastavení** vyberte **Konfigurace**.
   
    1. V části **přiřazení** vyberte **statické**.
    1. Vyberte **Uložit**.
    >[!NOTE]
    >U virtuálních počítačů, které mají veřejné IP adresy, budete muset nejprve vytvořit standardní IP adresy, kde není zaručena stejná IP adresa. Zruší přidružení virtuálních počítačů ze základních IP adres a přidruží je k nově vytvořeným standardním IP adresám. Pak budete moct postupovat podle pokynů pro přidání virtuálních počítačů do back-endového fondu Standard Load Balancer. 

* **Vytváření nových virtuálních počítačů pro přidání do back-endovéch fondů nově vytvořených standardních veřejných Load Balancer**.
    * Další pokyny k vytvoření virtuálního počítače a jeho přidružení k Standard Load Balancer najdete [tady](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Stáhnout skript

Stáhněte si skript migrace z  [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
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
   * **newLBName: [String]: Required** – jedná se o název Standard Load Balancer, který se má vytvořit.
1. Spusťte skript s použitím příslušných parametrů. Dokončení může trvat pět až 7 minut.

    **Příklad**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Vytvoření odchozího pravidla pro odchozí připojení

Postupujte podle [pokynů](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) pro vytvoření odchozího pravidla, abyste mohli
* Definice odchozího překladu adres (NAT) od začátku
* Škálujte a optimalizujte chování stávajícího odchozího překladu adres (NAT).

## <a name="common-questions"></a>Časté dotazy

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existují nějaká omezení Azure PowerShell skriptu pro migraci konfigurace z V1 na v2?

Ano. Podívejte se na [Upozornění a omezení](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Jak dlouho trvá upgrade?

Dokončení skriptu obvykle trvá přibližně 5-10 minut a může trvat delší dobu, v závislosti na složitosti konfigurace Load Balancer. V případě potřeby proto mějte na paměti, že v případě potřeby převzetí služeb při selhání naplánujte.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Přepíná Azure PowerShell skript také přenos z mé základní Load Balancer na nově vytvořenou Standard Load Balancer?

Ano. Skript Azure PowerShell neupgraduje pouze veřejnou IP adresu, zkopíruje konfiguraci ze základního do Standard Load Balancer, ale také migruje virtuální počítač na pozadí nově vytvořených standardních veřejných Load Balancer. 

## <a name="next-steps"></a>Další kroky

[Informace o Standard Load Balancer](load-balancer-overview.md)
