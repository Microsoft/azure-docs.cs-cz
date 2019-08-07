---
title: Rychlý Start – vytvoření virtuálního počítače VMware v privátním cloudu
description: Popisuje, jak vytvořit a virtuální počítač VMware v privátním cloudu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7d21035fc3e9e80344264b9fde21820162f376d3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816703"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Vytváření virtuálních počítačů VMware v privátním cloudu

Pokud chcete vytvořit virtuální počítače v privátním cloudu, začněte přístupem k portálu CloudSimple z Azure Portal.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. Na stránce **Přehled** klikněte na **Přejít na portál CloudSimple** a otevřete novou kartu prohlížeče pro portál CloudSimple.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure.  

    ![Spustit portál CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Spustit web vCenter web – uživatelské rozhraní

Teď můžete spustit vCenter, abyste nastavili virtuální počítače a zásady.

Pokud chcete získat přístup k serveru vCenter, začněte na portálu CloudSimple. Na domovské stránce v části **běžné úlohy**klikněte na **Spustit klienta vSphere**.  Vyberte privátní cloud a pak klikněte na **Spustit klienta vSphere** v privátním cloudu.

   ![Spustit klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Nahrání šablony ISO nebo vSphere

> [!WARNING]
> V případě nahrávání ISO použijte klienta vSphere HTML5.  Použití klienta Flash může mít za následek chybu.

1. Získejte šablonu ISO nebo vSphere, kterou chcete nahrát na vCenter, abyste vytvořili virtuální počítač a měli ho k dispozici v místním systému.

2. V vCenter klikněte na ikonu **disku** a vyberte **vsanDatastore**. Klikněte na **soubory** a pak na **Nová složka**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Vytvořte složku pro ukládání souborů ISO.

4. Přejděte do nové složky, kterou jste vytvořili, a klikněte na **nahrát soubory**. Podle pokynů na obrazovce nahrajte soubor ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Vytvoření virtuálního počítače v vCenter

1. V vCenter klikněte na ikonu **hostitelé a clustery** .

2. Klikněte pravým tlačítkem na **úlohy** a vyberte **Nový virtuální počítač**.
    
    ![Vytvoření virtuálního počítače](media/create-vcenter-virtual-machine-01.png)

3. Vyberte **vytvořit nový virtuální počítač** a klikněte na **Další**.

    ![Průvodce novým virtuálním počítačem](media/create-vcenter-virtual-machine-02.png)

4. Pojmenujte počítač, vyberte složku pro **virtuální počítač úlohy** a klikněte na **Další**.

    ![Vybrat název a složku](media/create-vcenter-virtual-machine-03.png)

5. Vyberte výpočetní prostředek **úlohy** a klikněte na **Další**.

    ![Vybrat výpočetní prostředek](media/create-vcenter-virtual-machine-04.png)

6. Vyberte **vsanDatastore** a klikněte na **Další**.

    ![Výběr úložiště](media/create-vcenter-virtual-machine-05.png)

7. Zachovejte výchozí výběr kompatibility ESXi 6,5 a klikněte na tlačítko **Další**.

    ![Vybrat kompatibilitu](media/create-vcenter-virtual-machine-06.png)

8. Vyberte hostovaný operační systém pro virtuální počítač ISO a klikněte na **Další**.

    ![Přizpůsobení hostovaného operačního systému](media/create-vcenter-virtual-machine-07.png)

9. Vyberte možnost pevný disk a síť. V případě nové jednotky CD/DVD vyberte **soubor ISO úložiště dat**.  Pokud chcete na tomto virtuálním počítači povolený provoz z veřejné IP adresy, vyberte síť jako **VM-1**.

    ![Vybrat vlastní nastavení hardwaru](media/create-vcenter-virtual-machine-08.png)

10. Otevře se okno výběru. Vyberte soubor, který jste dříve nahráli do složky soubory ISO a Templates a klikněte na **OK**.

    ![Vybrat ISO](media/create-vcenter-virtual-machine-10.png)

11. Zkontrolujte nastavení a kliknutím na **OK** vytvořte virtuální počítač.

    ![Zkontrolovat možnosti](media/create-vcenter-virtual-machine-11.png)

Virtuální počítač je teď přidaný do výpočetních prostředků úlohy a je připravený k použití. 

![Nový virtuální počítač v vCenter](media/create-vcenter-virtual-machine-12.png)

Základní nastavení je nyní dokončeno. Svůj privátní Cloud můžete začít používat podobně jako v místní infrastruktuře virtuálních počítačů.

Další části obsahují volitelné informace o nastavení serverů DNS a DHCP pro úlohy privátního cloudu a úpravách výchozí konfigurace sítě.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Přidat uživatele a zdroje identity do vCenter (volitelné)

CloudSimple přiřadí výchozímu uživatelskému účtu vCenter **cloudowner@cloudsimple.local** s uživatelským jménem. Není potřeba žádné další nastavení účtu, abyste mohli začít.  CloudSimple obvykle přiřazuje správcům oprávnění, která potřebují k provádění běžných operací.  Nastavte místní službu Active Directory nebo Azure AD jako [Další zdroj identity](https://docs.azure.cloudsimple.com/set-vcenter-identity/) v privátním cloudu.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Vytvoření serveru DNS a DHCP (volitelné)

Aplikace a úlohy běžící v prostředí privátního cloudu vyžadují překlad IP adres a služby DHCP pro vyhledání a přiřazení IP adresy. K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS. Virtuální počítač v vCenter můžete nakonfigurovat tak, aby poskytoval tyto služby ve vašem prostředí privátního cloudu.

### <a name="prerequisites"></a>Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN

* Směrování nastavené na místní nebo internetové servery DNS

* Vytvoření virtuálního počítače pomocí šablony virtuálního počítače nebo ISO

Následující odkazy poskytují pokyny k nastavení serverů DHCP a DNS v systémech Linux a Windows.

### <a name="linux-based-dns-server-setup"></a>Instalace serveru DNS se systémem Linux

Linux nabízí různé balíčky pro nastavení serverů DNS.  Tady je odkaz na pokyny pro nastavení Open-Source serveru DNS BIND.

[Příklad nastavení](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Instalace založená na systému Windows

Tyto články společnosti Microsoft popisují, jak nastavit server Windows jako server DNS a jako server DHCP.
<br>
[Windows Server jako server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Přizpůsobení konfigurace sítě (volitelné)

Síťové stránky na portálu CloudSimple umožňují zadat konfiguraci pro tabulky brány firewall a veřejné IP adresy pro virtuální počítače.

### <a name="allocate-public-ips"></a>Přidělit veřejné IP adresy

1. Přejděte na **síť > veřejné IP adresy** na portálu CloudSimple.
2. Klikněte na **přidělit veřejnou IP adresu**.
3. Zadejte název pro identifikaci položky IP adresy.
4. Vyberte umístění vašeho privátního cloudu.
5. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
6. Zadejte místní IP adresu, pro kterou chcete přiřadit veřejnou IP adresu.
7. V případě potřeby zadejte přidružený název DNS.
8. Klikněte na **Done** (Hotovo).

    ![Veřejná IP adresa](media/quick-create-pc-public-ip.png)

Spustí se úkol přidělení veřejné IP adresy. Stav úlohy můžete zjistit na stránce **aktivity > úlohy** . Po dokončení přidělení se nová položka zobrazí na stránce veřejné IP adresy.

U virtuálního počítače, na který musí být tato IP adresa namapovaná, je nutné nakonfigurovat místní adresu uvedenou výše. Postup konfigurace IP adresy je specifický pro operační systém virtuálního počítače. Správný postup najdete v dokumentaci k operačnímu systému virtuálního počítače.

#### <a name="example"></a>Příklad

Tady jsou například podrobnosti pro Ubuntu 16,04.

Do konfigurace rodiny adres inet v souboru ```/etc/network/interfaces```přidejte statickou metodu. Změňte hodnoty adresa, maska sítě a brány. V tomto příkladu používáme rozhraní eth0, interní IP adresu 192.168.24.10, adresu brány 192.168.24.1 a síťovou masku 255.255.255.0. 

```interfaces``` Upravte soubor.

```
sudo vi /etc/network/interfaces
```

Aktualizujte následující oddíl v ```interfaces``` souboru.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Zakáže rozhraní.

```
sudo ifdown eth0
```

Znovu povolte rozhraní.

```
sudo ifup eth0
```

Ve výchozím nastavení je veškerý příchozí provoz z Internetu **odepřený**. Pokud chcete otevřít libovolný jiný port, vytvořte [tabulku brány firewall](https://docs.azure.cloudsimple.com/firewall/).

Po konfiguraci interní IP adresy jako statické IP adresy ověřte, že se můžete připojit k Internetu z virtuálního počítače.

```
ping 8.8.8.8
```

Ověřte, že se můžete připojit k virtuálnímu počítači z Internetu pomocí veřejné IP adresy.

Zajistěte, aby všechna pravidla brány firewall (iptable) ve virtuálním počítači neblokovala příchozí port 80.

```
netstat -an | grep 80
```

Spusťte server http, který naslouchá na portu 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

or

```
python3 -m http.server 80
```

Spusťte prohlížeč na ploše a najeďte na port 80 pro veřejnou IP adresu, abyste mohli procházet soubory na virtuálním počítači. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Výchozí pravidla brány firewall CloudSimple pro veřejnou IP adresu

* Provoz sítě VPN: Veškerý provoz mezi (od/do) sítě VPN a všech sítí pro správu úloh a sítí pro správu je povolen.
* Vnitřní provoz privátního cloudu: Veškerý provoz v oblasti Východ a západ mezi sítěmi úloh (mezi/a) a sítí pro správu (zobrazené výše) je povolen.
* Internetový provoz:
    * Veškerý příchozí provoz z Internetu je odepřený pro úlohy sítě a sítě pro správu.
    * Veškerý odchozí provoz na Internetu ze sítí úloh nebo ze sítě pro správu je povolen.

Můžete také upravit způsob zabezpečení provozu pomocí funkce pravidla brány firewall. Další informace najdete v tématu [Nastavení tabulek a pravidel brány firewall](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Nainstalovat řešení (volitelné)
Můžete nainstalovat řešení do privátního cloudu CloudSimple, abyste mohli plně využít prostředí vašeho privátního cloudu vCenter. Pro ochranu virtuálních počítačů můžete nastavit zálohování, zotavení po havárii, replikaci a další funkce. Mezi příklady patří VMware Site Recovery Manager (VMware SRM) a replikace Veeam Backup &.

Chcete-li nainstalovat řešení, musíte požádat o další oprávnění po určitou dobu. Viz [Eskalace oprávnění](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Nastavení bran sítě VPN v síti CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
