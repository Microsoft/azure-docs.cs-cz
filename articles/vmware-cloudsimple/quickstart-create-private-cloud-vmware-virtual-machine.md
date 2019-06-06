---
title: Rychlý start – vytvoření virtuálního počítače s VMware na privátní Cloud
description: Popisuje postup vytvoření a virtuálních počítačů VMware na CloudSimple privátního cloudu
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5837fd615b8dbf32ff289a2e0d09da9db51a908d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481501"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Vytvoření virtuálních počítačů VMware na privátní Cloud

K vytvoření virtuálních počítačů na privátní Cloud, začněte tím, že přístup k portálu CloudSimple z portálu Azure portal.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

1. Vyberte **Všechny služby**.
2. Vyhledejte **CloudSimple služby**.
3. Vyberte službu CloudSimple, na kterém chcete vytvořit privátní Cloud.
4. Z **přehled** klikněte na **přejít na portál CloudSimple** otevřít na nové kartě prohlížeče CloudSimple portálu.  Pokud se zobrazí výzva, přihlaste se pomocí Azure přihlášení pověření.  

    ![Spusťte portál CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Spuštění webové uživatelské rozhraní serveru vCenter

Teď můžete spustit vCenter do nastavení virtuálních počítačů a zásad.

Pro přístup k serveru vCenter, spusťte z portálu CloudSimple. Na domovské stránce v části **běžné úkoly**, klikněte na tlačítko **spuštění vSphere klienta**.  Vyberte privátní Cloud a poté klikněte na tlačítko **spuštění vSphere klienta** na privátní Cloud.

   ![Spuštění vSphere klienta](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Nahrát šablonu ISO nebo vSphere

> [!WARNING]
> Pro nahrávání ISO použijte klienta vSphere HTML5.  Pomocí Flash klienta může mít za následek chybu.

1. Získáte ISO nebo vSphere šablonu, kterou chcete odeslat k vCenter pro vytvoření virtuálního počítače a mít k dispozici v místním systému.

2. V systému vCenter, klikněte na tlačítko **disku** ikonu a vyberte **vsanDatastore**. Klikněte na tlačítko **soubory** a potom klikněte na tlačítko **novou složku**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Vytvořte složku pro ukládání souborů ISO.

4. Přejděte do nové složky, které vytvořili a klikněte na tlačítko **nahrát soubory**. Použijte na obrazovce pokyny pro nahrání bitovou kopii ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Vytvoření virtuálního počítače v systému vCenter

1. V systému vCenter, klikněte na tlačítko **hostitelé a clustery** ikonu.

2. Klikněte pravým tlačítkem na **úlohy** a vyberte **nový virtuální počítač**.
    
    ![Vytvoření virtuálního počítače](media/create-vcenter-virtual-machine-01.png)

3. Vyberte **vytvořit nový virtuální počítač** a klikněte na tlačítko **Další**.

    ![Průvodce novým virtuálním počítačem](media/create-vcenter-virtual-machine-02.png)

4. Název počítače, vyberte **pracovního vytížení Virtuálního počítače** složky a klikněte na tlačítko **Další**.

    ![Vyberte název a složku](media/create-vcenter-virtual-machine-03.png)

5. Vyberte **úlohy** výpočetních prostředků a klikněte na tlačítko **Další**.

    ![Vyberte výpočetní prostředek](media/create-vcenter-virtual-machine-04.png)

6. Vyberte **vsanDatastore** a klikněte na tlačítko **Další**.

    ![Výběr úložiště](media/create-vcenter-virtual-machine-05.png)

7. Ponechte výchozí výběr kompatibility ESXi 6.5 a klikněte na **Další**.

    ![Vyberte kompatibility](media/create-vcenter-virtual-machine-06.png)

8. Vyberte hostovaný operační systém normy ISO pro virtuální počítač a klikněte na tlačítko **Další**.

    ![Přizpůsobení hostovaného operačního systému](media/create-vcenter-virtual-machine-07.png)

9. Vyberte pevný disk a možnosti sítě. Nový disk CD/DVD jednotku, vyberte **soubor Datastore ISO**.  Pokud chcete povolit provoz z veřejné IP adresy k tomuto virtuálnímu počítači, vyberte v síti jako **virtuální počítač 1**.

    ![Vyberte vlastní nastavení hardwaru](media/create-vcenter-virtual-machine-08.png)

10. Otevře se okno pro výběr. Vyberte soubor, který byl dříve odeslán do složky šablony a soubory ISO a klikněte na tlačítko **OK**.

    ![Vybrat soubor ISO](media/create-vcenter-virtual-machine-10.png)

11. Zkontrolujte nastavení a klikněte na tlačítko **OK** k vytvoření virtuálního počítače.

    ![Projděte si možnosti](media/create-vcenter-virtual-machine-11.png)

Virtuální počítač je teď přidaná do výpočetní prostředky, které úlohy a je připravená k použití. 

![Nový virtuální počítač ve vCenter](media/create-vcenter-virtual-machine-12.png)

Základní instalace je dokončena. Chcete začít používat vaše privátního cloudu, podobně jako jak byste použili vaši místní infrastrukturu virtuálních počítačů.

Následující části obsahují volitelné informace o nastavení služby DNS a DHCP servery pro privátní Cloud úlohy a upravit výchozí konfiguraci sítě.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Přidat uživatele a Identity zdroje k vCenter (volitelné)

CloudSimple přiřadí výchozí vCenter uživatelský účet s uživatelským jménem **cloudowner@cloudsimple.local** . Není žádné další účet nastavení požadovaná pro vám umožní začít.  Správci CloudSimple obvykle přiřadí oprávnění, které potřebují k provádění běžných operací.  Nastavit místní služby active directory nebo Azure AD jako [zdroj dalších identity](https://docs.azure.cloudsimple.com/set-vcenter-identity/) na privátní Cloud.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Vytvoření serveru DNS a DHCP (volitelné)

Aplikace a úlohy běžící v prostředí privátního cloudu vyžadují překlad a služby DHCP pro vyhledávání a přiřazování IP adres. Správné infrastruktury služby DNS a DHCP se vyžaduje k poskytování těchto služeb. Konfigurace virtuálního počítače na vCenter, který poskytuje tyto služby ve vašem prostředí privátního cloudu.

### <a name="prerequisites"></a>Požadavky

* Skupina distribuovaných portů pomocí sítě VLAN nakonfigurované

* Nastavit tak, aby v místním nebo servery DNS internetové trasy

* Šablonu virtuálního počítače nebo ISO k vytvoření virtuálního počítače

Na následujících odkazech najdete pokyny nastavení serverů DHCP a DNS na Linuxu a Windows.

### <a name="linux-based-dns-server-setup"></a>Nastavení serveru DNS se systémem Linux

Linux nabízí různé balíčky pro instalaci serverů DNS.  Tady se odkaz na pokyny k nastavení serveru DNS vytvořit VAZBU open source.

[Příklad nastavení](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Instalace na základě Windows

Tyto Microsoft články popisují, jak nastavit službu Windows server jako DNS server a jako DHCP server.
<br>
[Windows Server jako DNS Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server jako DHCP Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Úprava síťové konfigurace (nepovinné)

Na stránkách sítě na portálu CloudSimple vám umožňují určit konfiguraci brány firewall na tabulky a veřejné IP adresy pro virtuální počítače.

### <a name="allocate-public-ips"></a>Přidělení veřejné IP adresy

1. Přejděte do **sítě > veřejné IP adresy** CloudSimple portálu.
2. Klikněte na tlačítko **přidělení veřejné IP adresy**.
3. Zadejte název pro identifikaci záznam IP adresy.
4. Vyberte umístění privátního cloudu.
5. Chcete-li změnit časový limit nečinnosti v případě potřeby pomocí posuvníku.
6. Zadejte místní IP adresa, pro kterou chcete přiřadit veřejnou IP adresu.
7. V případě potřeby, zadejte odpovídající název DNS.
8. Klikněte na **Done** (Hotovo).

    ![Veřejná IP adresa](media/quick-create-pc-public-ip.png)

Začne úloha spojeným s přidělováním veřejnou IP adresu. Můžete zkontrolovat stav úlohy **aktivity > úlohy** stránky. Po dokončení přidělení nový záznam se zobrazí na stránce veřejné IP adresy.

Virtuální počítač, do které musí být namapována tato IP adresa je potřeba nakonfigurovat místní adresou výše uvedené. Postup konfigurace IP adresy je specifické pro operační systém virtuálního počítače. V dokumentaci pro váš operační systém virtuálního počítače pro správný postup.

#### <a name="example"></a>Příklad:

Například tady jsou uvedené podrobnosti pro Ubuntu 16.04.

Přidat statická metoda inet řady konfiguraci adresy v souboru ```/etc/network/interfaces```. Změňte hodnoty adresa, netmask a brány. V tomto příkladu používáme rozhraní eth0, interní IP adresa 192.168.24.10, adresa brány 192.168.24.1 a síťová maska 255.255.255.0. 

Upravit ```interfaces``` souboru.

```
sudo vi /etc/network/interfaces
```

Aktualizace v následující části v ```interfaces``` souboru.

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

Zakážete rozhraní.

```
sudo ifdown eth0
```

Znovu povolte rozhraní.

```
sudo ifup eth0
```

Ve výchozím nastavení, je veškerý příchozí provoz z Internetu **odepřen**. Pokud chcete otevřít libovolný port, vytvořte [brány firewall tabulky](https://docs.azure.cloudsimple.com/firewall/).

Po dokončení konfigurace interní IP adresa jako statickou IP adresu, ověřte, že může přístup k Internetu z virtuálního počítače.

```
ping 8.8.8.8
```

Ověřte, že můžete oslovit virtuálnímu počítači z Internetu pomocí veřejné IP adresy.

Ujistěte se, že nejsou žádná pravidla brány firewall (iptable) na virtuálním počítači blokuje port 80 příchozí.

```
netstat -an | grep 80
```

Spusťte server http, která naslouchá na portu 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

nebo

```
python3 -m http.server 80
```

Na ploše spusťte prohlížeč a přejděte na port 80 pro veřejnou IP adresu pro prohlížení souborů ve vašem virtuálním počítači. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Výchozí pravidla brány firewall CloudSimple pro veřejné IP adresy

* Provoz VPN: Je povolen veškerý provoz mezi (z/do) sítě VPN a všechny pracovního vytížení sítě a sítě pro správu.
* Vnitřní provoz privátního cloudu: Je povolen veškerý provoz typu east-west mezi (z/do) pracovního vytížení sítě a sítě pro správu (viz výše).
* Přenosy z Internetu:
    * K zatížení sítě a sítě pro správu je odepřen veškerý příchozí provoz z Internetu.
    * Všechny odchozí provoz do Internetu z pracovního vytížení sítě nebo síť pro správu je povolen.

Můžete také upravit způsob, jakým je zabezpečený provoz, pomocí funkce pravidla brány Firewall. Další informace najdete v tématu [nastavení brány firewall na tabulky a pravidla](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Nainstalujte řešení (volitelné)
Řešení můžete nainstalovat na vaše CloudSimple privátního cloudu, chcete-li využívají všech výhod privátního cloudu vCenter prostředí. Můžete nastavit zálohování, zotavení po havárii, replikace a další funkce k ochraně virtuálních počítačů. Mezi příklady patří VMware Site Recovery Manager (VMware SRM) a Veeam zálohování a replikace.

Pokud chcete nainstalovat řešení, musíte požádat o další oprávnění po omezenou dobu. Zobrazit [zvýšení úrovně oprávnění](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojit k místní síti pomocí Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Nastavení VPN Gateway v síti CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
