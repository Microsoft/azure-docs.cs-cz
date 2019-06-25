---
title: Kurz vytváření infrastruktury pro Service Fabric cluster na virtuálních počítačích Azure – Azure Service Fabric | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit infrastrukturu virtuálních počítačů Azure pro spuštění clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276003"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Kurz: Vytvoření infrastruktury virtuálního počítače Azure k hostování clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této řadě kurzů vytvoření samostatného clusteru hostovaných na virtuálních počítačích Azure a nainstalovat aplikaci na něj.

Tento kurz je první částí série. V tomto článku se generování prostředků virtuálních počítačů Azure vyžaduje pro hostování vašeho samostatného clusteru Service Fabric. V dalších článcích bude třeba nainstalovat samostatnou sadu Service Fabric, nainstalovat do clusteru ukázkovou aplikaci a nakonec cluster vyčistit.

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvořit sadu instancí AzureVM
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure.  Pokud ještě nemáte účet, přejděte [webu Azure portal](https://portal.azure.com) k jejímu vytvoření.

## <a name="create-azure-virtual-machine-instances"></a>Vytvoření instance virtuálních počítačů Azure

1. Přihlaste se k Azure portal a vyberte **virtuálních počítačů** (ne virtuální počítače (classic)).

   ![Azure portal virtuálního počítače][az-console]

2. Vyberte **přidat** tlačítko, které se otevře **vytvoření virtuálního počítače** formuláře.

3. V **Základy** kartu, zvolte předplatném a skupině prostředků můžete určit (pomocí nové skupiny prostředků je doporučeno).

4. Změnit **Image** typ, který **systému Windows Server 2016 Datacenter**. 
 
5. Změnit instanci **velikost** k **Standard DS2 v2**. Nastavte správce **uživatelské jméno** a **heslo**, poznamenat, že jsou.

6. Nechte **příchozí pravidla portů** blokované prozatím; nakonfigurujeme v další části.

7. V **sítě** kartu, vytvořte nový **virtuální sítě** a poznamenejte si její název.

8. Dále nastavte **skupinu zabezpečení sítě NIC** k **Upřesnit**. Vytvořit novou skupinu zabezpečení, zobrazí jeho název a vytvořte následující pravidla pro umožnění provoz TCP z libovolného zdroje:

   ![příchozí SF][sf-inbound]

   * Port `3389`pro protokol RDP a protokol ICMP (základní připojení).
   * Porty `19000-19003`, pro Service Fabric.
   * Porty `19080-19081`, pro Service Fabric.
   * Port `8080`, požadavky na webový prohlížeč.

   > [!TIP]
   > Pro propojení virtuálních počítačů do Service Fabric je třeba, aby virtuální počítače hostující vaši infrastrukturu měly stejné přihlašovací údaje.  Existují dva běžné způsoby, jak zajistit konzistenci přihlašovacích údajů: připojení všech virtuálních počítačů do stejné domény nebo nastavení stejného hesla správce na každém počítači. Naštěstí vám Azure povolí všechny virtuální počítače ve stejném **virtuální síť** snadno připojit, takže jsme bude obsahovat všechny naše instance ve stejné síti.

9. Přidáte jiné pravidlo. Nastavit zdroj, který má být **značka služby** a nastavte značka zdrojové služby **VirtualNetwork**. Service Fabric vyžaduje, aby následující porty otevřené pro komunikaci v rámci clusteru: 135,137-139,445,20001-20031,20606-20861.

   ![příchozí virtuální sítě][vnet-inbound]

10. Zbývající možnosti jsou přijatelné ve svém výchozím stavu. Zkontrolujte je, pokud chcete a pak spusťte virtuální počítač.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Vytvoření více instancí pro váš cluster Service Fabric

Spusťte dvě další **virtuálních počítačů**, zkontrolujte, zda chcete zachovat stejné nastavení uvedených v předchozí části. Zvlášť zachovat stejné správce uživatelské jméno a heslo. **Virtuální sítě** a **skupinu zabezpečení sítě NIC** by neměl být znovu vytvořen, vyberte ty, které jste už vytvořili, z rozevírací nabídky. Může trvat několik minut, než se všechny vaše instance má být nasazen.

## <a name="connect-to-your-instances"></a>Připojení k instancím

1. Vyberte jednu z instancí **virtuálního počítače** oddílu.

2. V **přehled** kartu, poznamenejte si *privátní* IP adresu. Potom klikněte na **připojit**.

3. V **RDP** kartu, mějte na paměti, že používáme veřejnou IP adresu a port 3389, které konkrétně otevřeme dříve. Stáhněte soubor RDP.
 
4. Otevřete soubor RDP a po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste zadali v nastavení virtuálního počítače.

5. Po připojení k instanci, je potřeba ověřit, zda byl spuštěn vzdálený registr, povolit protokol SMB a otevřít nezbytné porty pro SMB a vzdáleného registru.

   Pokud chcete povolit protokol SMB, to je příkaz prostředí PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Pro otevření portů v bráně firewall zase slouží tento příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Tento postup opakujte pro další instance, znovu zobrazí privátní IP adresy.

## <a name="verify-your-settings"></a>Ověřte nastavení

1. Ověření základní konektivity, připojte k virtuálním počítačům pomocí protokolu RDP.

2. Otevřete **příkazového řádku** z v rámci tohoto virtuálního počítače, pak pomocí příkazu ping připojit z jednoho virtuálního počítače do jiného, nahraďte následující IP adresu s jedním z privátních IP adres můžete jste si předtím poznamenali (ne IP adresu virtuálního počítače jste připojeni k již).

   ```
   ping 172.31.20.163
   ```

   Pokud jeho výstup vypadá takto `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` a opakuje se čtyřikrát, pak spojení mezi instancemi funguje.

3. Nyní následujícím příkazem ověřte, jestli funguje sdílení SMB:

   ```
   net use * \\172.31.20.163\c$
   ```

   Výstupem by mělo být `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Vaše instance jsou teď správně připravena pro Service Fabric.

## <a name="next-steps"></a>Další postup

V druhé části série jste zjistili, jak můžete spustit tři instance virtuálních počítačů Azure a je nakonfigurován pro instalaci Service Fabric:

> [!div class="checklist"]
> * Vytvořit sadu instancí virtuálních počítačů Azure
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

Pokračujte druhou částí konfigurace služby Service Fabric v clusteru.

> [!div class="nextstepaction"]
> [Instalace služby Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
