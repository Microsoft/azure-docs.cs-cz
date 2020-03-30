---
title: Úvodní příručka – vytvoření virtuálního počítače Azure VMware v privátním cloudu – řešení Azure VMware by CloudSimple
description: Popisuje, jak vytvořit virtuální počítač VMware Azure v privátním cloudu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566144"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Vytváření virtuálních počítačů VMware v privátním cloudu

Pokud chcete vytvářet virtuální počítače ve vašem privátním cloudu, začněte přístupem na portál CloudSimple z portálu Azure.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché služby**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. Na stránce **Přehled** klikněte na **Přejít na portál CloudSimple** a otevřete novou kartu prohlížeče pro portál CloudSimple.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů k Azure.  

    ![Spuštění portálu CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Spuštění webového uživatelského zařízení vCenter

Nyní můžete spustit vCenter nastavit virtuální počítače a zásady.

Chcete-li získat přístup k programu vCenter, začněte z portálu CloudSimple. Na domovské stránce klikněte v části **Běžné úkoly**na **tlačítko Spustit klienta vSphere**.  Vyberte privátní cloud a klikněte na **Spustit klienta vSphere** v privátním cloudu.

   ![Spuštění klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Nahrání šablony ISO nebo vSphere

  > [!WARNING]
  > Pro nahrávání ISO použijte klienta vSphere HTML5.  Použití klienta Flash může mít za následek chybu.

1. Získejte šablonu ISO nebo vSphere, kterou chcete nahrát do centra, abyste vytvořili virtuální počítač a měli ho k dispozici v místním systému.
2. V programu vCenter klepněte na ikonu **Disk** a vyberte **vsanDatastore**. Klepněte na **položku Soubory** a potom na **položku Nová složka**.
    ![vCenter ISO](media/vciso00.png)

3. Vytvořte složku s názvem ISO a šablony.

4. Přejděte do složky ISO v ISO a šablonách a klepněte na tlačítko **Nahrát soubory**. Podle pokynů na obrazovce nahrajte ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Vytvoření virtuálního počítače ve virtuálním počítači

1. V programu vCenter klikněte na ikonu **Hostitelé a clustery.**

2. Klikněte pravým tlačítkem myši na **Pracovní vytížení** a vyberte **Nový virtuální počítač**.
    ![Nový virtuální virtuální město](media/vcvm01.png)

3. Vyberte **Vytvořit nový virtuální počítač** a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm02.png)

4. Pojmenujte počítač, vyberte umístění **virtuálního počítače s pracovní zátěží** a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm03.png)

5. Vyberte výpočetní prostředek **úlohy** a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm04.png)

6. Vyberte **vsanDatastore** a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm05.png)

7. Zachovat výchozí výběr kompatibility ESXi 6.5 a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm06.png)

8. Vyberte hostovaného operačního příkazu ISO pro vytvářený virtuální ho disponizém a klepněte na tlačítko **Další**.
    ![Nový virtuální virtuální město](media/vcvm07.png)

9. Vyberte možnosti pevného disku a sítě. V případě nové jednotky CD/DVD vyberte **soubor ISO datastore**.  Pokud chcete povolit provoz z veřejné IP adresy do tohoto virtuálního počítačů, vyberte síť jako **vm-1**.
    ![Nový virtuální virtuální město](media/vcvm08.png)

10. Otevře se okno výběru. Vyberte soubor, který jste dříve nahráli do složky ISO a šablony, a klepněte na tlačítko **OK**.
    ![Nový virtuální virtuální město](media/vcvm10.png)

11. Zkontrolujte nastavení a kliknutím na **OK** vytvořte virtuální ho.
    ![Nový virtuální virtuální město](media/vcvm11.png)

Virtuální počítač se teď přidá do výpočetních prostředků úlohy a je připravený k použití. 
![Nový virtuální virtuální město](media/vcvm12.png)

Základní nastavení je nyní dokončeno. Privátní cloud můžete začít používat podobně jako byste mohli používat místní infrastrukturu virtuálních aplikací.

Následující části obsahují volitelné informace o nastavení serverů DNS a DHCP pro úlohy privátního cloudu a úpravě výchozí konfigurace sítě.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Přidat uživatele a zdroje identit do vCenter (volitelné)

CloudSimple přiřadí výchozí uživatelský účet vCenter s uživatelským jménem `cloudowner@cloudsimple.local`. Abyste mohli začít, není nutné žádné další nastavení účtu.  CloudSimple obvykle přiřazuje správcům oprávnění, která potřebují k provádění normálních operací.  Nastavte svůj místní active directory nebo Azure AD jako [další zdroj identity](set-vcenter-identity.md) ve vašem privátním cloudu.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Vytvoření serveru DNS a DHCP (volitelné)

Aplikace a úlohy spuštěné v prostředí privátního cloudu vyžadují překlad názvů a služby DHCP pro vyhledávání a přiřazení IP adres. K poskytování těchto služeb je vyžadována správná infrastruktura DHCP a DNS. Můžete nakonfigurovat virtuální počítač v vCenter poskytovat tyto služby ve vašem prostředí privátního cloudu.

Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN

* Směrování nastavení na místní nebo internetové servery DNS

* Šablona virtuálního počítače nebo ISO pro vytvoření virtuálního počítače

Následující odkazy obsahují pokyny k nastavení serverů DHCP a DNS v systémech Linux a Windows.

#### <a name="linux-based-dns-server-setup"></a>Nastavení serveru DNS založeného na Linuxu

Linux nabízí různé balíčky pro nastavení DNS serverů.  Zde je odkaz na pokyny pro nastavení open-source BIND DNS server.

[Příklad nastavení](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Instalace systému Windows

Tato témata společnosti Microsoft popisují, jak nastavit server se systémem Windows jako server DNS a jako server DHCP.

[Windows Server jako DNS Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Systém Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Přizpůsobení konfigurace sítě (volitelné)

Síťové stránky na portálu CloudSimple umožňují určit konfiguraci pro tabulky brány firewall a veřejné IP adresy pro virtuální počítače.

### <a name="allocate-public-ips"></a>Přidělení veřejných IP služeb

1. Přejděte na **> veřejné IP síti na** portálu CloudSimple.
2. Klepněte na **tlačítko Přidělit veřejnou adresu IP**.
3. Zadejte název pro identifikaci položky IP adresy.
4. Zachovat výchozí umístění.
5. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
6. Zadejte místní ADRESU IP, ke které chcete přiřadit veřejnou IP adresu.
7. V případě potřeby zadejte přidružený název DNS.
8. Klikněte na **Done** (Hotovo).

    ![Veřejná IP adresa](media/quick-create-pc-public-ip.png)

Začíná úkol přidělení veřejné IP adresy. Stav úkolu můžete zkontrolovat na stránce **Aktivita > úkoly.** Po dokončení přidělení se nová položka zobrazí na stránce Veřejné IP adresy.

Virtuální počítače, na který musí být tato adresa IP mapována, musí být nakonfigurován s výše uvedenou místní adresou. Postup konfigurace adresy IP je specifický pro operační systém virtuálního počítače. Správný postup našlápnete do dokumentace k operačnímu systému virtuálního počítače.

#### <a name="example"></a>Příklad

Například zde jsou podrobnosti o Ubuntu 16.04.

Přidejte statickou metodu do konfigurace rodiny adres inet v souboru /etc/network/interfaces. Změňte hodnoty adresy, masky sítě a brány. V tomto příkladu používáme rozhraní eth0, interní IP adresu 192.168.24.10, adresu brány 192.168.24.1 a masku sítě 255.255.255.0. Pro vaše prostředí jsou dostupné informace o podsíti uvedeny v uvítacím e-mailu.

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
Ručně zakažte rozhraní.

```
sudo ifdown eth0
```
Ručně znovu povolte rozhraní.

```
sudo ifup eth0
```

Ve výchozím nastavení **je**odepřen veškerý příchozí provoz z Internetu . Chcete-li otevřít jakýkoli jiný port, vytvořte [tabulku brány firewall](firewall.md).

Po konfiguraci interní IP adresy jako statické IP adresy ověřte, že se můžete dostat k Internetu z virtuálního počítačů.

```
ping 8.8.8.8
```
Také ověřte, že můžete dosáhnout virtuálního počítačů z Internetu pomocí veřejné IP adresy.

Ujistěte se, že všechna pravidla iptable na virtuálním počítači neblokují příchozí port 80.
        
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
Spusťte prohlížeč na ploše a přejděte na port 80, aby veřejná IP adresa procházet soubory na vašem virtuálním počítači.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Výchozí pravidla brány firewall CloudSimple pro veřejnou IP adresu

* Provoz VPN: Veškerý provoz mezi VPN (z/do) a všemi sítěmi pracovních vytížení a sítí pro správu je povolen.
* Interní provoz privátního cloudu: Veškerý východozápadní provoz mezi sítěmi úloh (z/do) a sítí pro správu (viz výše) je povolen.
* Internetový provoz:
  * Veškerý příchozí provoz z Internetu je odepřen sítím pracovního vytížení a síti pro správu.
  * Veškerý odchozí přenos do Internetu ze sítí úloh nebo ze sítě pro správu je povolen.

Můžete také upravit způsob, jakým je provoz zabezpečen, pomocí funkce Pravidla brány firewall. Další informace naleznete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

## <a name="install-solutions-optional"></a>Instalační řešení (volitelně)

Do privátního cloudu CloudSimple můžete nainstalovat řešení, abyste mohli plně využívat prostředí private cloudu vCenter. Můžete nastavit zálohování, zotavení po havárii, replikaci a další funkce k ochraně virtuálních počítačů. Mezi příklady patří Správce obnovení webu společnosti VMware (VMware SRM) a Veeam Backup & Replication.

Chcete-li nainstalovat řešení, musíte požádat o další oprávnění po omezenou dobu. Viz [Eskalovat oprávnění](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
* [Nastavení bran VPN v síti CloudSimple](vpn-gateway.md)
