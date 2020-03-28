---
title: Vytvoření infrastruktury pro cluster na virtuálních počítačích Azure
description: V tomto kurzu se dozvíte, jak nastavit infrastrukturu virtuálních počítačích Azure pro spuštění clusteru Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614005"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Kurz: Vytvoření infrastruktury virtuálních počítačích Azure pro hostování clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na virtuálních počítačích Azure a nainstalujete do něj aplikaci.

Tento kurz je první částí série. V tomto článku vygenerujete prostředky virtuálního počítače Azure potřebné k hostování samostatného clusteru Service Fabric. V dalších článcích bude třeba nainstalovat samostatnou sadu Service Fabric, nainstalovat do clusteru ukázkovou aplikaci a nakonec cluster vyčistit.

V první části tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sady instancí AzureVM
> * Úprava skupiny zabezpečení
> * Přihlášení k jedné z instancí
> * Příprava instance pro Service Fabric

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure.  Pokud ještě nemáte účet, přejděte na [portál Azure](https://portal.azure.com) a vytvořte ho.

## <a name="create-azure-virtual-machine-instances"></a>Vytváření instancí virtuálních strojů Azure

1. Přihlaste se k portálu Azure a vyberte **virtuální počítače** (ne virtuální počítače (klasické)).

   ![Virtuální počítač portálu Azure][az-console]

2. Vyberte tlačítko **Přidat,** které otevře formulář **Vytvořit virtuální počítač.**

3. Na kartě **Základy** nezapomeňte vybrat požadované předplatné a skupinu prostředků (doporučujeme použít novou skupinu prostředků).

4. Změňte typ **obrazu** na **Windows Server 2016 Datacenter**. 
 
5. Změňte **velikost** instance na **Standardní DS2 v2**. Nastavte **uživatelské jméno** a **heslo**správce a uveďte, o co se jedná.

6. Ponechte **pravidla příchozího portu** prozatím blokovaná. nakonfigurujeme je v další části.

7. Na kartě **Síť** vytvořte novou **virtuální síť** a poznamenejte si její název.

8. Dále nastavte **skupinu zabezpečení sítě NIC** na **upřesnit**. Vytvořte novou skupinu zabezpečení s uznaním jejího názvu a vytvořte následující pravidla, která umožní přenos protokolu TCP z libovolného zdroje:

   ![sf-příchozí][sf-inbound]

   * port `3389`, pro PRV a ICMP (základní připojení).
   * Porty `19000-19003`, pro service fabric.
   * Porty `19080-19081`, pro service fabric.
   * Port `8080`, pro požadavky webového prohlížeče.

   > [!TIP]
   > Pro propojení virtuálních počítačů do Service Fabric je třeba, aby virtuální počítače hostující vaši infrastrukturu měly stejné přihlašovací údaje.  Existují dva běžné způsoby, jak zajistit konzistenci přihlašovacích údajů: připojení všech virtuálních počítačů do stejné domény nebo nastavení stejného hesla správce na každém počítači. Naštěstí Azure umožňuje všem virtuálním počítačům ve stejné **virtuální síti** snadné připojení, takže budeme mít jistotu, že všechny naše instance ve stejné síti.

9. Přidejte další pravidlo. Nastavte zdroj jako **výrobní číslo** a nastavte zdrojovou značku služby na **VirtualNetwork**. Service Fabric vyžaduje, aby následující porty byly otevřené pro komunikaci v rámci clusteru: 135,137-139,445,20001-20031,20606-20861.

   ![příchozí virtuální síť][vnet-inbound]

10. Zbývající možnosti jsou přijatelné ve výchozím stavu. Zkontrolujte je, pokud chcete, a pak spusťte virtuální počítač.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Vytváření dalších instancí pro cluster Service Fabric

Spusťte další dva **virtuální počítače a**ujistěte se, že zachováte stejná nastavení uvedená v předchozí části. Zejména udržovat stejné uživatelské jméno správce a heslo. Skupina zabezpečení **sítě virtuální sítě** a síťové sítě by **neměla** být znovu vytvořena. vrozené v rozevírací nabídce. Nasazení každé instance může trvat několik minut.

## <a name="connect-to-your-instances"></a>Připojení k instancím

1. V části **Virtuální počítač** vyberte jednu ze svých instancí.

2. Na kartě **Přehled** poznamenejte *si privátní* IP adresu. Potom klepněte na tlačítko **Připojit**.

3. Na kartě **RDP** si všimněte, že používáme veřejnou IP adresu a port 3389, který jsme konkrétně otevřeli dříve. Stáhněte soubor RDP.
 
4. Otevřete soubor RDP a po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste zadali v nastavení virtuálního počítače.

5. Jakmile jste připojeni k instanci, je třeba ověřit, že byl spuštěn vzdálený registr, povolit smb a otevřít požadované porty pro smb a vzdálený registr.

   Chcete-li povolit SMB, jedná se o příkaz Prostředí PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Pro otevření portů v bráně firewall zase slouží tento příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Tento postup opakujte pro ostatní instance a znovu uvěnujte usebe soukromé IP adresy.

## <a name="verify-your-settings"></a>Ověření nastavení

1. Chcete-li ověřit základní připojení, připojte se k jednomu z virtuálních zařízení pomocí rdp.

2. Otevřete **příkazový řádek** z tohoto virtuálního počítačů, pak pomocí příkazu ping pro připojení z jednoho virtuálního uživatele do druhého, nahradí níže uvedenou IP adresu s jedním z privátních IP adres, které jste si poznamenali dříve (ne IP virtuálního virtuálního počítačů, které jste již připojeni).

   ```
   ping 172.31.20.163
   ```

   Pokud jeho výstup vypadá takto `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` a opakuje se čtyřikrát, pak spojení mezi instancemi funguje.

3. Nyní následujícím příkazem ověřte, jestli funguje sdílení SMB:

   ```
   net use * \\172.31.20.163\c$
   ```

   Výstupem by mělo být `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Nyní jsou vaše instance správně připraveny pro service fabric.

## <a name="next-steps"></a>Další kroky

V první části řady jste se naučili, jak spustit tři instance virtuálních virtuálních zařízení Azure a nakonfigurovat je pro instalaci Service Fabric:

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
