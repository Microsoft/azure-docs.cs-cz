---
title: Informace o zařízení Kolektoru ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje informace o zařízení Kolektoru ve službě Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 7a17bed165a5a8ff15a122a1376d1a3a5e17d45f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700923"
---
# <a name="about-the-collector-appliance"></a>Informace o zařízení Kolektoru

 Tento článek obsahuje informace o Azure Migrate Collector.

Azure Migrate Collector je zjednodušené zařízení, který slouží ke zjištění serveru vCenter v místním prostředí pro účely posouzení s [Azure Migrate](migrate-overview.md) service, před migrací do Azure.  

## <a name="discovery-method"></a>Metoda zjišťování

Dříve byly dvě možnosti pro zařízení kolektoru, jednorázové zjišťování a průběžná zjišťování. Model jednorázového zjišťování už se nepoužívá jako spoléhal na nastavení statistiky vCenter serveru pro shromažďování dat o výkonu (vyžaduje statistiky nastavení nastavili na úroveň 3) a shromažďují také průměr čítačů (místo ve špičce), které výsledkem snížení velikosti. Model průběžná zjišťování zajišťuje detailní data o shromažďování a výsledkem přesné určení velikosti kvůli shromažďování čítačů ve špičce. Níže je, jak to funguje:

Zařízení kolektoru je trvalým připojením k projektu Azure Migrate a průběžně shromažďuje údaje o výkonu virtuálních počítačů.

- Kolektor průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase každých 20 sekund.
- Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod každých 15 minut.
- Tato data vytváří bod zařízení vybere nejvyšší hodnotu z 20 sekund vzorků a odešle ji do Azure.
- Tento model nejsou závislé na nastavení statistiky vCenter Server ke shromažďování dat výkonu.
- Můžete zastavit průběžné profilování v kdykoli z kolekce.

**Rychlé posouzení:** S průběžná zjišťování zařízení, jakmile se dokončí zjišťování (trvá několik hodin v závislosti na počtu virtuálních počítačů), můžete okamžitě vytvořit posouzení. Protože shromažďování dat výkonu spustí, když zahájíte zjišťování, pokud chcete pro rychlé posouzení, měli vybrat kritérium určení velikosti v posouzení jako *jako místní*. Vyhodnocení na základě výkonu doporučujeme počkejte alespoň jeden den a poté rutinního zjišťování tak, aby Získejte doporučení k reliable velikosti.

Zařízení průběžně pouze shromažďuje údaje o výkonu, nezjistí změny konfigurace v místním prostředí (tj. Přidání virtuálního počítače, odstranění, přidání disku atd.). Pokud dojde ke změně konfigurace v místním prostředí, následujícím způsobem můžete zajistit, že se změny projeví na portálu:

- Přidání položek (virtuálních počítačů, disků, jader atd.): Pro provedení těchto změn na webu Azure Portal, můžete zastavit zjišťování ze zařízení a znovu spustit. Tím se zajistí, že se změny aktualizují v projektu Azure Migrate.

- Odstranění virtuálních počítačů: Kvůli způsobu, jakým je navržena na zařízení se neprojeví odstranění virtuálních počítačů i v případě zastavení a spuštění zjišťování. Důvodem je, že se data z dalších zjišťování připojují ke starším zjišťováním, a nepřepisují se. V takovém případě můžete virtuální počítač na portálu jednoduše ignorovat tak, že ho odeberete ze své skupiny a přepočítáte posouzení.

> [!NOTE]
> Jednorázové zjišťování zařízení je nyní zastaralý a tato metoda spoléhal na vCenter serveru nastavení statistiky dostupnosti bodu dat výkonu shromážděných čítačů průměrný výkon, které umožňují snížení velikosti virtuálních počítačů pro migraci do Azure.

## <a name="deploying-the-collector"></a>Nasazení Kolektoru

Nasazení pomocí šablony OVF zařízení Kolektoru:

- Stáhněte šablonu OVF z projektu Azure Migrate na webu Azure Portal. Importujte stažený soubor do vCenter serveru, nastavení zařízení Kolektoru virtuálního počítače.
- Z OVF nastaví VMware virtuální počítač s 8 jader, 16 GB paměti RAM a jeden disk 80 GB. Operační systém je Windows Server 2016 (64 bitů).
- Při spuštění Kolektoru, abyste měli jistotu, že kolektor může připojit k Azure Migrate spustit řadu kontrol požadovaných součástí.

- [Další informace](tutorial-assessment-vmware.md#create-the-collector-vm) o vytváření kolekcí.


## <a name="collector-prerequisites"></a>Požadavky na kolekce

Kolekce musí úspěšně projít několik kontroly splnění podmínek se můžete připojit ke službě Azure Migrate přes internet a nahrávání zjištěné data.

- **Ověření cloudu Azure**: Kolektoru je potřeba vědět, ke které máte v úmyslu migrovat cloudu Azure.
    - Pokud máte v úmyslu migrovat do cloudu Azure Government, vyberte Azure Government.
    - Vyberte Azure globální, pokud máte v úmyslu migrovat do komerčním cloudu Azure.
    - Založené na cloudu tady zadané, pošle zařízení zjištěná metadata do příslušných koncových bodů.
- **Zkontrolujte připojení k Internetu**: Kolektor může připojit k Internetu přímo nebo prostřednictvím proxy serveru.
    - Kontrola předpokladů ověří připojení k [povinné a nepovinné adresy URL](#urls-for-connectivity).
    - Pokud máte přímé připojení k Internetu, není žádná konkrétní akce požadované, než je zajistit, aby kolektor přístup požadované adresy URL.
    - Pokud připojujete přes proxy server, Všimněte si, [níže uvedené požadavky](#connect-via-a-proxy).
- **Zkontrolujte synchronizaci času**: Kolektor synchronizovat s internetovým časovým serverem k zajištění, že ověření požadavků na službu.
    - Adrese portal.azure.com. adresa url by měla být dosažitelný z Kolektoru, tak, aby čas může být ověřen.
    - Pokud počítač není synchronizovaný, musíte změnit čas na virtuálním počítači Kolektoru tak, aby odpovídaly aktuálním časem. Provedete to otevřete Správce řádek na virtuálním počítači, spusťte **w32tm /tz** ke kontrole časové pásmo. Spustit **w32tm/resync** synchronizovat čas.
- **Kontrola spuštění služby kolektoru**:  Služba Azure Migrate Collector by měl běžet na virtuálním počítači Kolektoru.
    - Tato služba je spuštěna automaticky při spuštění počítače.
    - Pokud služba není spuštěná, spusťte ji pomocí ovládacího panelu.
    - Služba Collector připojí k serveru vCenter, shromažďuje data metadata a výkonu virtuálních počítačů a odesílá je do služby Azure Migrate.
- **Zkontrolovat VMware PowerCLI 6.5 je nainstalovaný**: Modul prostředí PowerShell VMware PowerCLI 6.5 musí nainstalovat na virtuálním počítači Kolektoru, tak, aby mohl komunikovat s vCenter Server.
    - Kolektor může získat přístup k adresám URL požadovaným pro instalaci modulu, je instalace automaticky během nasazení Kolektoru.
    - Pokud kolekce nelze nainstalovat modul během nasazení, je nutné [nainstalujte ho ručně](#install-vwware-powercli-module-manually).
- **Zkontrolujte připojení k serveru vCenter**: Kolekce musí být schopen systému vCenter Server a dotazy na virtuální počítače, jejich metadata a čítače výkonu. [Ověřte požadavky](#connect-to-vcenter-server) pro připojení.


### <a name="connect-to-the-internet-via-a-proxy"></a>Připojení k Internetu přes proxy server

- Pokud proxy server vyžaduje ověření, můžete zadat uživatelské jméno a heslo při nastavování Kolektoru.
- IP adresu nebo plně kvalifikovaný název domény Proxy serveru by měl být zadán jako *http://IPaddress* nebo *http://FQDN*.
- Podporuje se jen proxy protokolu HTTP. Servery proxy server založený na protokolu HTTPS nepodporuje Kolektoru.
- Pokud proxy server je prověřuje zachycovací proxy server, musíte importovat certifikát proxy serveru na virtuálním počítači Kolektoru.
    1. Přejděte ve virtuálním počítači kolektoru **nabídky Start** > **spravovat certifikáty počítače**.
    2. V nástroji pro certifikáty v části **certifikáty - místní počítač**, Najít **Důvěryhodní vydavatelé** > **certifikáty**.

        ![Nástroje pro certifikáty](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. Zkopírujte certifikát proxy serveru na virtuálním počítači kolektoru. Možná budete muset získat od správce sítě.
    4. Dvojitým kliknutím otevřete certifikát a klikněte na tlačítko **nainstalovat certifikát**.
    5. V Průvodci importem certifikátu > Store umístění, zvolte **místního počítače**.

    ![Umístění úložiště certifikátů](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. Vyberte **všechny certifikáty umístit v následujícím úložišti** > **Procházet** > **Důvěryhodní vydavatelé**. Klikněte na tlačítko **Dokončit** importujte certifikát.

    ![Úložiště certifikátů](./media/concepts-intercepting-proxy/certificate-store.png)

    7. Zkontrolujte, že je certifikát importován podle očekávání a zkontrolujte, že internetové připojení kontrolu požadovaných součástí funguje podle očekávání.


### <a name="urls-for-connectivity"></a>Adresy URL pro připojení

Kontrola připojení se ověří pomocí připojení k seznamu adres URL.

**Adresa URL** | **Podrobnosti**  | **Kontrola požadovaných součástí**
--- | --- | ---
*.portal.azure.com | Vztahuje se na Azure Global. Zkontroluje připojení pomocí služby Azure a synchronizaci času. | Přístup k je vyžadována adresa URL.<br/><br/> Kontrola předpokladů selže, pokud neexistuje žádná připojení.
*.portal.azure.us | Platí jenom pro Azure Government. Zkontroluje připojení pomocí služby Azure a synchronizaci času. | Přístup k je vyžadována adresa URL.<br/><br/> Kontrola předpokladů selže, pokud neexistuje žádná připojení.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Použít ke stažení modulu PowerShell vCenter PowerCLI. | Přístup k adresám URL, které jsou volitelné.<br/><br/> K selhání kontroly požadavků.<br/><br/> Instalace modulu automatické na virtuálním počítači Kolektoru se nezdaří. Bude nutné ručně nainstalovat modul.


### <a name="install-vmware-powercli-module-manually"></a>Ruční instalace VMware PowerCLI modulu

1. Nainstalovat s použitím modulu [tyto kroky](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Tyto kroky popisují online i offline instalace.
2. Pokud virtuální počítač Kolektoru je offline a nainstalovat modul na jiném počítači s přístupem k Internetu, musíte zkopírovat soubory VMware.* z tohoto počítače k virtuálnímu počítači Kolektoru.
3. Po dokončení instalace můžete restartovat požadované součásti kontroly potvrďte, že je nainstalovaný PowerCLI.

### <a name="connect-to-vcenter-server"></a>Připojit k vCenter Serveru

Kolektor se připojí k serveru vCenter a dotazy na metadata virtuálního počítače a čítače výkonu. Zde je, co potřebujete pro připojení.

- Jsou podporovány pouze vCenter Server verze 5.5, 6.0, 6.5 a 6.7.
- Potřebujete účet jen pro čtení s oprávnění uvedená níže pro zjišťování. Zjišťování je přístupný pouze datacentra, které jsou přístupné pomocí účtu.
- Ve výchozím nastavení připojení k serveru vCenter s plně kvalifikovaný název domény nebo IP adresu. Pokud systém vCenter Server naslouchá na jiném portu, k němu připojíte pomocí formuláře *IPAddress:Port_Number* nebo *FQDN:Port_Number*.
- Kolekce by měly mít síť dohled k serveru vCenter.

#### <a name="account-permissions"></a>Oprávnění účtu

**Účet** | **Oprávnění**
--- | ---
Alespoň uživatel jen pro čtení účtu | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only   


## <a name="collector-communications"></a>Kolekce komunikace

Kolektor komunikuje dle souhrnu v následující diagram a tabulky.

![Diagram komunikace kolekcí](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Kolekce komunikuje se službou** | **Port** | **Podrobnosti**
--- | --- | ---
Služba Azure Migrate | TCP 443 | Kolekce komunikuje se službou Azure Migrate přes SSL 443.
vCenter Server | TCP 443 | Kolekce musí být schopný komunikovat s systému vCenter Server.<br/><br/> Ve výchozím nastavení připojí k serveru vCenter na 443.<br/><br/> Pokud systém vCenter Server naslouchá na jiném portu, tento port by měl být k dispozici jako odchozí port na Kolektoru.
Protokol RDP | TCP 3389 |

## <a name="collected-metadata"></a>Shromáždila se metadata

Zařízení kolektoru zjistí následující metadat konfigurace pro každý virtuální počítač. Konfigurační data pro virtuální počítače jsou k dispozici hodinu po spuštění zjišťování.

- Zobrazovaný název virtuálního počítače (v systému vCenter Server)
- Cesta inventáře Virtuálního počítače (hostitel/složku v systému vCenter Server)
- IP adresa
- Adresa MAC
- Operační systém
- Počet jader, disků, síťových adaptérů
- Velikost paměti, velikosti disků
- Čítače výkonu virtuálních počítačů, disku a sítě.

### <a name="performance-counters"></a>Čítače výkonu

 Zařízení kolektoru shromažďuje následující čítače výkonu pro každý virtuální počítač z hostitele ESXi v intervalech 20 sekund. Tyto čítače jsou čítačů vCenter a i když terminologii říká průměr, 20 sekund ukázky jsou čítačů v reálném čase. Data o výkonu pro virtuální počítače se spustí poté jsou dostupné na portálu, dvě hodiny po mají spustila zjišťování. Důrazně se doporučuje počkejte alespoň den před vytvořením posouzení založená na výkon získat přesné doporučení pro správné velikosti. Pokud chcete pro rychlé provést synchronizaci dříve, můžete vytvořit posouzení s kritérium určení velikosti jako *jako místní* které nebude považovat za data o výkonu pro určení správné velikosti.

**Counter** |  **Dopad na posouzení**
--- | ---
cpu.usage.average | Doporučené velikosti virtuálních počítačů a náklady  
mem.usage.average | Doporučené velikosti virtuálních počítačů a náklady  
virtualDisk.read.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.write.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberReadAveraged.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberWriteAveraged.average | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
net.received.average | Vypočítá velikost virtuálního počítače                          
net.transmitted.average | Vypočítá velikost virtuálního počítače     

Úplný seznam čítačů VMware shromážděná službou Azure Migrate je k dispozici níže:

**Kategorie** |  **Metadata** | **Element datapoint vCenter**
--- | --- | ---
Podrobnosti o počítači | ID virtuálního počítače | vm.Config.InstanceUuid
Podrobnosti o počítači | název virtuálního počítače | vm.Config.Name
Podrobnosti o počítači | vCenter Server ID | VMwareClient.InstanceUuid
Podrobnosti o počítači |  Popis virtuálního počítače |  virtuální počítač. Summary.Config.Annotation
Podrobnosti o počítači | Název produktu licencí | vm.Client.ServiceContent.About.LicenseProductName
Podrobnosti o počítači | Typ operačního systému | virtuální počítač. Summary.Config.GuestFullName
Podrobnosti o počítači | Verze operačního systému | virtuální počítač. Summary.Config.GuestFullName
Podrobnosti o počítači | Typ spuštění | vm.Config.Firmware
Podrobnosti o počítači | Počet jader | vm.Config.Hardware.NumCPU
Podrobnosti o počítači | Paměť v megabajtech | virtuální počítač. Config.Hardware.MemoryMB
Podrobnosti o počítači | Počet disků | virtuální počítač. Config.Hardware.Device.ToList(). FindAll(x => x is VirtualDisk).count
Podrobnosti o počítači | Seznam velikost disků | virtuální počítač. Config.Hardware.Device.ToList(). FindAll (x = > x je VirtualDisk)
Podrobnosti o počítači | Seznamu síťových adaptérů | virtuální počítač. Config.Hardware.Device.ToList(). FindAll (x = > x je VirtualEthernetCard)
Podrobnosti o počítači | Využití procesoru | cpu.usage.average
Podrobnosti o počítači | Využití paměti | mem.usage.average
Podrobnosti o disku (na disk) | Hodnota klíče disku | disk. Klíč
Podrobnosti o disku (na disk) | Počet jednotek disku | disk. UnitNumber
Podrobnosti o disku (na disk) | Hodnota klíče kontroleru disku | disk.ControllerKey.Value
Podrobnosti o disku (na disk) | GB zřízené | virtualDisk.DeviceInfo.Summary
Podrobnosti o disku (na disk) | Název disku | Tato hodnota je generována pomocí disku. UnitNumber, disk. Klíč a disku. ControllerKey.Value
Podrobnosti o disku (na disk) | Počet operací čtení za sekundu | virtualDisk.numberReadAveraged.average
Podrobnosti o disku (na disk) | Počet operací zápisu za sekundu | virtualDisk.numberWriteAveraged.average
Podrobnosti o disku (na disk) | MB za sekundu propustnost čtení | virtualDisk.read.average
Podrobnosti o disku (na disk) | MB za sekundu propustnosti zápisu | virtualDisk.write.average
Podrobnosti síťového adaptéru (pro síťové rozhraní) | Název síťového adaptéru | síťový adaptér Klíč
Podrobnosti síťového adaptéru (pro síťové rozhraní) | Adresa MAC | ((VirtualEthernetCard)nic).MacAddress
Podrobnosti síťového adaptéru (pro síťové rozhraní) | Adresy IPv4 | virtuální počítač. Guest.Net
Podrobnosti síťového adaptéru (pro síťové rozhraní) | IPv6 adresy | virtuální počítač. Guest.Net
Podrobnosti síťového adaptéru (pro síťové rozhraní) | MB za sekundu propustnost čtení | net.received.average
Podrobnosti síťového adaptéru (pro síťové rozhraní) | MB za sekundu propustnosti zápisu | net.transmitted.average
Podrobnosti o cestě inventáře | Název | container.GetType().Name
Podrobnosti o cestě inventáře | Typ podřízený objekt | container.ChildType
Podrobnosti o cestě inventáře | Podrobné referenční informace | container.MoRef
Podrobnosti o cestě inventáře | Cesta úplnou inventarizaci. | kontejner. Název s úplnou cestu
Podrobnosti o cestě inventáře | Podrobnosti o nadřazené | Container.Parent
Podrobnosti o cestě inventáře | Podrobnosti složky pro každý virtuální počítač | ((Folder)container).ChildEntity.Type
Podrobnosti o cestě inventáře | Podrobnosti o datovém centru pro každou složku virtuálního počítače | ((Datacenter)container).VmFolder
Podrobnosti o cestě inventáře | Podrobnosti o datovém centru pro každou složku hostitele | ((Datacenter)container).HostFolder
Podrobnosti o cestě inventáře | Podrobnosti o clusteru pro každého hostitele | ((ClusterComputeResource)container).Host)
Podrobnosti o cestě inventáře | Podrobnosti hostitele pro každý virtuální počítač | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Zabezpečení zařízení Kolektoru

Doporučujeme následující postup k zabezpečení zařízení Kolektoru:

- Nemáte sdílet nebo s neoprávněnými osobami někam nezaložili hesla správce.
- Vypněte zařízení, když se nepoužívá.
- Umístíte zařízení do zabezpečené síti.
- Po dokončení migrace, odstraňte instanci zařízení.
- Kromě toho po migraci, také odstraňte záložní soubory disku (Vmdk), jako disky mít přihlašovací údaje k vCenter do mezipaměti na ně.

## <a name="os-license-in-the-collector-vm"></a>Licence operačního systému ve virtuálním počítači kolektoru

Kolektor se dodává s zkušební licence Windows serveru 2012 R2, který je platný po dobu 180 dnů. Pokud vyprší platnost zkušební období pro váš virtuální počítač kolektoru, doporučujeme stáhnout nové soubory OVA a vytvořit nové zařízení.

## <a name="updating-the-os-of-the-collector-vm"></a>Aktualizujte operační systém virtuálního počítače Kolektoru

I když zařízení kolektoru zkušební licence po dobu 180 dnů, je potřeba průběžně aktualizovat operačního systému na zařízení, aby se zabránilo automatické vypnutí dolů na zařízení.

- Pokud kolekce není aktualizován po dobu 60 dnů, se spustí automaticky vypnutí počítače.
- Pokud je funkce zjišťování spuštěna, počítače nebudou vypnout, i v případě, že jste předali 60 dnů. Tento počítač bude vypnutý, po dokončení zjišťování.
- Pokud po dobu více než 60 dnů jste použili Kolektoru, doporučujeme počítač aktualizoval na celou dobu spuštění aktualizace Windows.

## <a name="upgrading-the-collector-appliance-version"></a>Upgrade verze zařízení Kolektoru

Kolekce můžete upgradovat na nejnovější verzi bez stáhnout soubor OVA znovu.

1. Stáhněte si [nejnovější uvedené balíček s upgradem](concepts-collector-upgrade.md)
2. Tak, aby byl zabezpečený stažené oprav hotfix, otevřete okno příkazového řádku pro správce a spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor ZIP. Vygenerovaná hodnota hash by měla odpovídat symbolem hash uvedených proti konkrétní verzi:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (příklady použití C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Soubor zip zkopírujte do Azure migrovat virtuální počítač kolektoru (zařízení kolektoru).
4. Klikněte pravým tlačítkem na soubor zip a vyberte Extrahovat vše.
5. Klikněte pravým tlačítkem na Setup.ps1 a vyberte spustit s prostředím PowerShell a postupujte podle pokynů na obrazovce instalace aktualizace.

## <a name="discovery-process"></a>Proces zjišťování

Po nastavení zařízení, můžete spustit zjišťování. Zde je, jak to funguje:

- Spustit zjišťování. oborem. Všechny virtuální počítače v cestě inventáře vCenter zadaná, bude zjištěno.
    - V každém okamžiku nastaven jeden obor.
    - Rozsah může zahrnovat 1 500 virtuálních počítačů nebo méně.
    - Rozsah může být datacenter, složka nebo hostiteli ESXi.
- Po připojení k serveru vCenter připojíte tak, že zadáte projekt migrace pro kolekci.
- Zjištění virtuálních počítačů a jejich metadat a výkonu data se odesílají do Azure. Tyto akce jsou součástí úlohy kolekce.
    - Zařízení Kolektoru je přiřazena určité ID kolekce, která je pro daný počítač trvalé zjišťování.
    - Spuštěné úlohy kolekce je zadané ID konkrétní relace. ID změní pro každou úlohu kolekce a lze použít pro řešení potíží s.

## <a name="next-steps"></a>Další postup

[Nastavení posouzení pro místní virtuální počítače VMware](tutorial-assessment-vmware.md)
