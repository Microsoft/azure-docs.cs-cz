---
title: Kurz vytvoření infrastruktury pro Service Fabric cluster na virtuálních počítačích Azure – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se naučíte nastavit infrastrukturu virtuálních počítačů Azure pro spuštění clusteru Service Fabric.
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
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: c9dd9cf0f0fb6d20d6837b07ab46d376e379ca25
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177728"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Kurz: vytvoření infrastruktury virtuálních počítačů Azure pro hostování Service Fabricho clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na virtuálních počítačích Azure a nainstalujete do něj aplikaci.

Tento kurz je první částí série. V tomto článku vygenerujete prostředky virtuálních počítačů Azure, které jsou potřeba pro hostování samostatného clusteru Service Fabric. V dalších článcích bude třeba nainstalovat samostatnou sadu Service Fabric, nainstalovat do clusteru ukázkovou aplikaci a nakonec cluster vyčistit.

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření sady instancí AzureVM
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete předplatné Azure.  Pokud účet ještě nemáte, můžete ho vytvořit tak, že přejdete na [Azure Portal](https://portal.azure.com) .

## <a name="create-azure-virtual-machine-instances"></a>Vytvoření instancí virtuálních počítačů Azure

1. Přihlaste se k Azure Portal a vyberte **virtuální počítače** (ne Virtual Machines (Classic)).

   ![Azure Portal virtuálního počítače][az-console]

2. Vyberte tlačítko **Přidat** , ve kterém se otevře formulář **vytvořit virtuální počítač** .

3. Na kartě **základy** si nezapomeňte zvolit předplatné a skupinu prostředků, které chcete (doporučuje se použít novou skupinu prostředků).

4. Změňte typ **obrázku** na **Windows Server 2016 Datacenter**. 
 
5. Změňte **Velikost** instance na **Standard DS2 v2**. Nastavte **uživatelské jméno** a **heslo**správce, které označuje, co jsou.

6. Nechejte teď blokovaná **pravidla portů pro příchozí provoz** ; v další části je nakonfigurujeme.

7. Na kartě **sítě** vytvořte novou **Virtual Network** a poznamenejte si její jméno.

8. V dalším kroku nastavte **skupinu zabezpečení sítě síťové karty** na **Upřesnit**. Vytvořte novou skupinu zabezpečení, která zaznamená její název, a vytvořte následující pravidla, která povolí přenos TCP z libovolného zdroje:

   ![SF – příchozí][sf-inbound]

   * Port `3389`pro RDP a ICMP (základní připojení).
   * Porty `19000-19003`pro Service Fabric.
   * Porty `19080-19081`pro Service Fabric.
   * Port `8080`pro požadavky webového prohlížeče.

   > [!TIP]
   > Pro propojení virtuálních počítačů do Service Fabric je třeba, aby virtuální počítače hostující vaši infrastrukturu měly stejné přihlašovací údaje.  Existují dva běžné způsoby, jak zajistit konzistenci přihlašovacích údajů: připojení všech virtuálních počítačů do stejné domény nebo nastavení stejného hesla správce na každém počítači. Naštěstí Azure umožňuje, aby se všechny virtuální počítače ve stejné **virtuální síti** snadno připojovaly, takže budeme mít jistotu, že všechny naše instance budou ve stejné síti.

9. Přidejte další pravidlo. Nastavte zdroj jako **značku služby** a nastavte značku zdrojové služby na **VirtualNetwork**. Service Fabric vyžaduje, aby byly pro komunikaci v rámci clusteru otevřené následující porty: 135137-139, 445, 20 001-20031, 20606-20861.

   ![síť VNet – příchozí][vnet-inbound]

10. Ostatní možnosti jsou přijatelné ve výchozím stavu. Zkontrolujte je, pokud chcete, a pak spusťte virtuální počítač.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Vytváření dalších instancí pro cluster Service Fabric

Spusťte ještě dva **Virtual Machines**. Ujistěte se, že jste zachovali stejné nastavení, jak je popsáno v předchozí části. Zachovejte zejména stejné uživatelské jméno a heslo správce. Nepodařilo se znovu vytvořit skupinu zabezpečení **Virtual Network** a **síťový adaptér** . Vyberte ty, které jste už vytvořili z rozevírací nabídky. Nasazování všech instancí může trvat několik minut.

## <a name="connect-to-your-instances"></a>Připojení k vašim instancím

1. V části **virtuální počítač** vyberte jednu z vašich instancí.

2. Na kartě **Přehled** si poznamenejte *privátní* IP adresu. Pak klikněte na **připojit**.

3. Na kartě **RDP** si všimněte, že používáme veřejnou IP adresu a port 3389, který jsme konkrétně otevřeli dřív. Stáhněte soubor RDP.
 
4. Otevřete soubor RDP a po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste zadali v nastavení virtuálního počítače.

5. Jakmile budete připojeni k instanci, je třeba ověřit, zda byl spuštěn vzdálený registr, a otevřít požadované porty.

6. Pro otevření portů v bráně firewall zase slouží tento příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139
   ```

7. Tento postup opakujte pro své jiné instance a znovu se zaznamenáte o privátních IP adresách.

## <a name="verify-your-settings"></a>Ověření nastavení

1. Pokud chcete ověřit základní připojení, připojte se k některému z virtuálních počítačů pomocí protokolu RDP.

2. Otevřete **příkazový řádek** z tohoto virtuálního počítače a pak pomocí příkazu příkazu pro připojení z jednoho virtuálního počítače k jinému nahraďte níže uvedenou IP adresu jednou z privátních IP adres, které jste si poznamenali dříve (ne IP adresa virtuálního počítače, ke kterému jste se už připojili).

   ```
   ping 172.31.20.163
   ```

   Pokud jeho výstup vypadá takto `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` a opakuje se čtyřikrát, pak spojení mezi instancemi funguje.

   Teď jsou vaše instance správně připravené pro Service Fabric.

## <a name="next-steps"></a>Další kroky

V první části série jste zjistili, jak spustit tři instance virtuálních počítačů Azure a jak je nakonfigurovat pro instalaci Service Fabric:

> [!div class="checklist"]
> * Vytvoření sady instancí virtuálních počítačů Azure
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
