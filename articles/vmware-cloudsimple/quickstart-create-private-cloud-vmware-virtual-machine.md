---
title: Rychlý Start – vytvoření virtuálního počítače VMware v privátním cloudu pro službu AVS
description: Popisuje, jak vytvořit virtuální počítač VMware v privátním cloudu služby AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cbe88afc4f566bad4bacb408346d4dd25a2f6c96
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020059"
---
# <a name="create-vmware-virtual-machines-on-your-avs-private-cloud"></a>Vytvoření virtuálních počítačů VMware v privátním cloudu služby AVS

Pokud chcete vytvořit virtuální počítače v privátním cloudu služby AVS, začněte přístupem k portálu služby AVS z Azure Portal.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Přístup k portálu pro funkci AVS

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby AVS**.
3. Vyberte službu AVS, na které chcete vytvořit privátní cloud služby AVS.
4. Na stránce **Přehled** klikněte na **Přejít na portál služby AVS** a otevřete novou kartu prohlížeče pro portál AVS. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure. 

    ![Spustit portál AVS](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Spustit web vCenter web – uživatelské rozhraní

Teď můžete spustit vCenter, abyste nastavili virtuální počítače a zásady.

Pro přístup k serveru vCenter spusťte z portálu AVS. Na domovské stránce v části **běžné úlohy**klikněte na **Spustit klienta vSphere**. Vyberte privátní cloud AVS a pak klikněte na **Spustit klienta vSphere** v privátním cloudu AVS.

   ![Spustit klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Nahrání šablony ISO nebo vSphere

  > [!WARNING]
  > Pro nahrání ISO použijte klienta vSphere HTML5. Použití klienta Flash může mít za následek chybu.

1. Získejte šablonu ISO nebo vSphere, kterou chcete nahrát na vCenter, abyste vytvořili virtuální počítač a měli ho k dispozici v místním systému.
2. V vCenter klikněte na ikonu **disku** a vyberte **vsanDatastore**. Klikněte na **soubory** a pak na **Nová složka**.
    ![vCenter ISO](media/vciso00.png)

3. Vytvořte složku s názvem soubory ISO a Templates.

4. Přejděte do složky soubory ISO v soubory ISO a šablonách a klikněte na **nahrát soubory**. Podle pokynů na obrazovce nahrajte soubor ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Vytvoření virtuálního počítače v vCenter

1. V vCenter klikněte na ikonu **hostitelé a clustery** .

2. Klikněte pravým tlačítkem na **úlohy** a vyberte **Nový virtuální počítač**.
    ![nový virtuální počítač](media/vcvm01.png)

3. Vyberte **vytvořit nový virtuální počítač** a klikněte na **Další**.
    ![nový virtuální počítač](media/vcvm02.png)

4. Pojmenujte počítač, vyberte umístění **virtuálního počítače úlohy** a klikněte na **Další**.
    ![nový virtuální počítač](media/vcvm03.png)

5. Vyberte výpočetní prostředek **úlohy** a klikněte na **Další**.
    ![nový virtuální počítač](media/vcvm04.png)

6. Vyberte **vsanDatastore** a klikněte na **Další**.
    ![nový virtuální počítač](media/vcvm05.png)

7. Zachovejte výchozí výběr kompatibility ESXi 6,5 a klikněte na tlačítko **Další**.
    ![nový virtuální počítač](media/vcvm06.png)

8. Pro vytvářený virtuální počítač vyberte hostovaný operační systém ISO a klikněte na **Další**.
    ![nový virtuální počítač](media/vcvm07.png)

9. Vyberte možnost pevný disk a síť. V případě nové jednotky CD/DVD vyberte **soubor ISO úložiště dat**. Pokud chcete na tomto virtuálním počítači povolený provoz z veřejné IP adresy, vyberte síť jako **virtuální počítač-1**.
    ![nový virtuální počítač](media/vcvm08.png)

10. Otevře se okno výběru. Vyberte soubor, který jste dříve nahráli do složky soubory ISO a Templates a klikněte na **OK**.
    ![nový virtuální počítač](media/vcvm10.png)

11. Zkontrolujte nastavení a kliknutím na **OK** vytvořte virtuální počítač.
    ![nový virtuální počítač](media/vcvm11.png)

Virtuální počítač je teď přidaný do výpočetních prostředků úlohy a je připravený k použití. 
![nový virtuální počítač](media/vcvm12.png)

Základní nastavení je nyní dokončeno. Svůj privátní cloud služby AVS můžete začít používat podobně jako v místní infrastruktuře virtuálních počítačů.

Následující části obsahují volitelné informace o nastavení serverů DNS a DHCP pro úlohy služby AVS privátní cloud a změnu výchozí konfigurace sítě.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Přidat uživatele a zdroje identity do vCenter (volitelné)

AVS přiřadí výchozí uživatelský účet vCenter s uživatelským jménem `cloudowner@AVS.local`. Není potřeba žádné další nastavení účtu, abyste mohli začít. Modul AVS obvykle přiřazuje správcům oprávnění, která potřebují k provádění běžných operací. Nastavte místní službu Active Directory nebo Azure AD jako [Další zdroj identity](set-vcenter-identity.md) v privátním cloudu služby AVS.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Vytvoření serveru DNS a DHCP (volitelné)

Aplikace a úlohy, které běží v prostředí privátního cloudu služby AVS, vyžadují překlad IP adres a služby DHCP pro vyhledání a přiřazení IP adresy. K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS. Virtuální počítač v vCenter můžete nakonfigurovat tak, aby poskytoval tyto služby ve vašem privátním cloudovém prostředí služby AVS.

Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN

* Nastavení směrování na místní nebo internetové servery DNS

* Vytvoření virtuálního počítače pomocí šablony virtuálního počítače nebo ISO

Následující odkazy poskytují pokyny k nastavení serverů DHCP a DNS v systémech Linux a Windows.

#### <a name="linux-based-dns-server-setup"></a>Instalace serveru DNS se systémem Linux

Linux nabízí různé balíčky pro nastavení serverů DNS. Tady je odkaz na pokyny pro nastavení Open-Source serveru DNS BIND.

[Příklad nastavení](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Instalace založená na systému Windows

Tato témata Microsoftu popisují, jak nastavit server Windows jako server DNS a jako server DHCP.

[Windows Server jako server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Přizpůsobení konfigurace sítě (volitelné)

Síťové stránky na portálu služby AVS umožňují zadat konfiguraci pro tabulky brány firewall a veřejné IP adresy pro virtuální počítače.

### <a name="allocate-public-ips"></a>Přidělit veřejné IP adresy

1. Přejděte na **síť > veřejné IP adresy** na portálu AVS.
2. Klikněte na **přidělit veřejnou IP adresu**.
3. Zadejte název pro identifikaci položky IP adresy.
4. Ponechte výchozí umístění.
5. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
6. Zadejte místní IP adresu, pro kterou chcete přiřadit veřejnou IP adresu.
7. V případě potřeby zadejte přidružený název DNS.
8. Klikněte na **Done** (Hotovo).

    ![Veřejná IP adresa](media/quick-create-pc-public-ip.png)

Spustí se úkol přidělení veřejné IP adresy. Stav úlohy můžete zjistit na stránce **aktivity > úlohy** . Po dokončení přidělení se nová položka zobrazí na stránce veřejné IP adresy.

Virtuální počítač, na který musí být tato IP adresa namapovaná, musí být nakonfigurovaný s místní adresou uvedenou výše. Postup konfigurace IP adresy je specifický pro operační systém virtuálního počítače. Správný postup najdete v dokumentaci k operačnímu systému virtuálního počítače.

#### <a name="example"></a>Příklad:

Tady jsou například podrobnosti pro Ubuntu 16,04.

Přidejte statickou metodu do konfigurace rodiny adres inet v souboru/etc/Network/Interfaces. Změňte hodnoty adresa, maska sítě a brány. V tomto příkladu používáme rozhraní eth0, interní IP adresu 192.168.24.10, adresu brány 192.168.24.1 a síťovou masku 255.255.255.0. V případě vašeho prostředí jsou dostupné informace o podsíti uvedené v uvítacím e-mailu.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Rozhraní zakážete ručně.

```
sudo ifdown eth0
```
Ručně znovu povolte rozhraní.

```
sudo ifup eth0
```

Ve výchozím nastavení je veškerý příchozí provoz z Internetu **odepřený**. Pokud chcete otevřít libovolný jiný port, vytvořte [tabulku brány firewall](firewall.md).

Po konfiguraci interní IP adresy jako statické IP adresy ověřte, že se můžete připojit k Internetu z virtuálního počítače.

```
ping 8.8.8.8
```
Ověřte také, že se můžete připojit k virtuálnímu počítači z Internetu pomocí veřejné IP adresy.

Zajistěte, aby všechna iptable pravidla na virtuálním počítači neblokovala příchozí port 80.
        
```
netstat -an | grep 80
```

Spusťte server http, který naslouchá na portu 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

– nebo –

```
python3 -m http.server 80
```
Spusťte prohlížeč na ploše a najeďte na port 80 pro veřejnou IP adresu, abyste mohli procházet soubory na VIRTUÁLNÍm počítači.

### <a name="default-avs-firewall-rules-for-public-ip"></a>Výchozí pravidla brány firewall pro funkci AVS pro veřejnou IP adresu

* Přenosy VPN: veškerý provoz mezi (od/do) sítě VPN a všech sítí pro správu úloh a sítí pro správu je povolený.
* Interní provoz privátního cloudu služby AVS: veškerý provoz směřující do sítě (od/do) a síť pro správu (zobrazené výše) je povolený.
* Internetový provoz:
  * Veškerý příchozí provoz z Internetu je odepřený pro úlohy sítě a sítě pro správu.
  * Veškerý odchozí provoz na Internetu ze sítí úloh nebo ze sítě pro správu je povolen.

Můžete také upravit způsob zabezpečení provozu pomocí funkce pravidla brány firewall. Další informace najdete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

## <a name="install-solutions-optional"></a>Nainstalovat řešení (volitelné)

Můžete nainstalovat řešení do privátního cloudu služby AVS, abyste plně využili své prostředí pro vCenter privátního cloudu pro funkci AVS. Pro ochranu virtuálních počítačů můžete nastavit zálohování, zotavení po havárii, replikaci a další funkce. Mezi příklady patří VMware Site Recovery Manager (VMware SRM) a replikace Veeam Backup &.

Chcete-li nainstalovat řešení, musíte požádat o další oprávnění po určitou dobu. Viz [Eskalace oprávnění](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
* [Nastavení bran sítě VPN v síti služby AVS](vpn-gateway.md)
