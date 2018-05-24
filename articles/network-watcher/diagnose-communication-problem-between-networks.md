---
title: Diagnostika potíží s komunikací mezi sítěmi – kurz – Azure Portal | Microsoft Docs
description: Zjistěte, jak pomocí možnosti diagnostiky VPN Network Watcher diagnostikovat potíže s komunikací mezi virtuální sítí Azure, která je připojená k místní síti nebo k jiné virtuální síti přes bránu virtuální sítě Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d89c5a3f2545edd7c02b67fa9d2e2b78937a9791
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Kurz: Diagnostika potíží s komunikací mezi sítěmi na portálu Azure Portal

Brána virtuální sítě připojuje virtuální síť Azure k místní síti nebo k jiné virtuální síti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Diagnostikovat potíže s bránou virtuální sítě pomocí možnosti diagnostiky VPN Network Watcher
> * Diagnostikovat potíže s připojením brány
> * Vyřešit potíže s bránou

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat diagnostiku VPN, musí být spuštěná brána VPN. Pokud bránu VPN pro diagnostiku ještě nepoužíváte, můžete ji nasadit pomocí [skriptu PowerShellu](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Skript PowerShellu můžete spustit z těchto umístění:
    - **Místní instalace PowerShellu**: Skript vyžaduje modul AzureRM PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, podívejte se na článek o [instalaci Azure PowerShellu](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
    - **Azure Cloud Shell**: Služba [Azure Cloud Shell](https://shell.azure.com/powershell) má nainstalovanou a nakonfigurovanou nejnovější verzi PowerShellu a přihlásí vás k Azure.

Vytvoření brány VPN pomocí tohoto skriptu trvá přibližně hodinu. Ve zbývajících krocích se předpokládá, že diagnostikovanou bránou je brána nasazená pomocí tohoto skriptu. Pokud tedy diagnostikujete jinou vlastní bránu, výsledky se budou lišit.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud už máte Network Watcher v oblasti USA – východ povolený, přeskočte na část [Diagnostika brány](#diagnose-a-gateway).

1. Na webu Azure Portal vyberte **Všechny služby**. Do **pole filtru** zadejte *Network Watcher*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích, vyberte ji.
2. Rozbalte **Oblasti** a potom vedle **USA – východ** vyberte **...** (stejně jako v následujícím obrázku):

    ![Povolení Network Watcheru](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Vyberte **Povolit Network Watcher**.

## <a name="diagnose-a-gateway"></a>Diagnostika brány

1. Na levé straně portálu vyberte **Všechny služby**.
2. Do pole **Filtr** začněte psát *network watcher*. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
3. V části **NÁSTROJE PRO DIAGNOSTIKU SÍTĚ** vyberte **Diagnostika VPN**.
4. Vyberte **Účet úložiště** a pak vyberte účet úložiště, do kterého se mají zapisovat diagnostické informace.
5. V seznamu **Účty úložiště** vyberte účet úložiště, který chcete použít. Pokud účet úložiště ještě nemáte, vytvořte ho tak, že vyberete **+ Účet úložiště**, zadáte nebo vyberete požadované informace a pak vyberete **Vytvořit**. Pokud jste vytvořili bránu VPN pomocí skriptu uvedeného v části [Požadavky](#prerequisites), můžete se rozhodnout, že účet úložiště vytvoříte ve stejné skupině prostředků *TestRG1*, která se používá pro tuto bránu.
6. V seznamu **Kontejnery** vyberte kontejner, který chcete použít, a pak vyberte **Vybrat**. Pokud žádné kontejnery nemáte, vyberte **+ Kontejner**, zadejte název kontejneru a vyberte **OK**.
7. Vyberte bránu a pak vyberte **Zahájit řešení potíží**. Jak znázorňuje následující obrázek, test se spustí pro bránu s názvem **Vnet1GW**:

    ![Diagnostika VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Po spuštění testu se tam, kde ve sloupci **STAV ŘEŠENÍ POTÍŽÍ** byla na předchozím obrázku hodnota **Nespuštěno**, zobrazí hodnota **Spuštěno**. Test může trvat několik minut.
9. Prohlédněte si stav po dokončení testu. Následující obrázek ukazuje výsledky se stavem po dokončení diagnostického testu:

    ![Status](./media/diagnose-communication-problem-between-networks/status.png)

    Ve sloupci **STAV ŘEŠENÍ POTÍŽÍ** je hodnota **Není v pořádku** a na kartě **Stav** můžete vidět také **souhrn** a **podrobnosti** potíží.
10. Další informace zobrazí diagnostika VPN po výběru karty **Akce**. V příkladu na následujícím obrázku vám diagnostika VPN oznamuje, že byste měli stav jednotlivých připojení zkontrolovat:

  ![Akce](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnostika připojení brány

Brána se připojuje k jiným sítím přes připojení brány. Pro úspěšnou komunikaci mezi virtuální sítí a připojenou sítí je nutné, aby brána i připojení brány byly v pořádku.

1. Proveďte znovu krok 7 z části [Diagnostika brány](#diagnose-a-gateway), přičemž tentokrát vyberte připojení. V následujícím příkladu se testuje připojení s názvem **VNet1toSite1**:

    ![Připojení](./media/diagnose-communication-problem-between-networks/connection.png)

    Test trvá několik minut.
2. Po dokončení testu připojení obdržíte výsledky, které se budou podobat výsledkům zobrazeným na následujících obrázcích na kartách **Stav** a **Akce**:

    ![Stav připojení](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Akce připojení](./media/diagnose-communication-problem-between-networks/connection-action.png)

    Diagnostika VPN vás na kartě **Stav** informuje o tom, k jakým potížím došlo, a na kartě **Akce** uvede několik možností, co může být příčinou těchto potíží.

    Pokud je testovanou bránou brána nasazená pomocí [skriptu](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) uvedeného v části [Požadavky](#prerequisites), týkají se údaje na kartě **Stav** a první dvě položky na kartě **Akce** právě daného problému. Skript nakonfiguruje pro zařízení místní brány VPN zástupnou IP adresu 23.99.221.164.

    Pokud chcete problém vyřešit, je potřeba zajistit, aby místní brána VPN byla [nakonfigurovaná správně](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), a změnit IP adresu nakonfigurovanou pro bránu místní sítě pomocí skriptu na skutečnou veřejnou adresu místní brány VPN.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili bránu VPN pomocí skriptu uvedeného v části [Požadavky](#prerequisites) výhradně pro účely tohoto kurzu a už ji nepotřebujete, odstraňte příslušnou skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *TestRG1*. Když se ve výsledcích hledání zobrazí skupina prostředků **TestRG1**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *TestRG1* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak diagnostikovat potíže s bránou virtuální sítě. Síťovou komunikaci do a z virtuálního počítače můžete protokolovat, aby bylo možné v protokolu vyhledávat anomálie. Postup najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Protokolování síťového provozu do a z virtuálního počítače](network-watcher-nsg-flow-logging-portal.md)