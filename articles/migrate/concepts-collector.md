---
title: Informace o zařízení Kolektoru ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje informace o zařízení Kolektoru ve službě Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 3c40fd97540d8529c95c7d18d2c3155dd37717e9
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945412"
---
# <a name="about-the-collector-appliance"></a>Informace o zařízení Kolektoru

 Tento článek obsahuje informace o Azure Migrate Collector.

Azure Migrate Collector je zjednodušené zařízení, který slouží ke zjištění serveru vCenter v místním prostředí pro účely posouzení s [Azure Migrate](migrate-overview.md) service, před migrací do Azure.  


## <a name="deploying-the-collector"></a>Nasazení Kolektoru

Nasazení pomocí šablony OVF zařízení Kolektoru:

- Stáhněte šablonu OVF z projektu Azure Migrate na webu Azure Portal. Importujte stažený soubor do vCenter serveru, nastavení zařízení Kolektoru virtuálního počítače.
- Z OVF nastaví VMware virtuální počítač s 4 jádra, 8 GB paměti RAM a jeden disk 80 GB. Operační systém je Windows Server 2012 R2 (64bitová verze).
- Při spuštění Kolektoru, abyste měli jistotu, že kolektor může připojit k Azure Migrate spustit řadu kontrol požadovaných součástí.

- [Další informace](tutorial-assessment-vmware.md#create-the-collector-vm) o vytváření kolekcí.


## <a name="collector-prerequisites"></a>Požadavky na kolekce

Kolekce musí úspěšně projít několik kontroly splnění podmínek se můžete připojit ke službě Azure Migrate přes internet a nahrávání zjištěné data.

- **Zkontrolujte připojení k Internetu**: The kolekcí může připojit k Internetu přímo nebo prostřednictvím proxy serveru.
    - Kontrola předpokladů ověří připojení k [povinné a nepovinné adresy URL](#connect-to-urls).
    - Pokud máte přímé připojení k Internetu, není žádná konkrétní akce požadované, než je zajistit, aby kolektor přístup požadované adresy URL.
    - Pokud připojujete přes proxy server, Všimněte si, [níže uvedené požadavky](#connect-via-a-proxy).
- **Zkontrolujte synchronizaci času**: The kolekcí synchronizovat s internetovým časovým serverem k zajištění ověření požadavků na službu.
    - Adrese portal.azure.com. adresa url by měla být dosažitelný z Kolektoru, tak, aby čas může být ověřen.
    - Pokud počítač není synchronizovaný, musíte změnit čas na virtuálním počítači Kolektoru tak, aby odpovídaly aktuálním časem. Provedete to otevřete Správce řádek na virtuálním počítači, spusťte **w32tm /tz** ke kontrole časové pásmo. Spustit **w32tm/resync** synchronizovat čas.
- **Kontrola spuštění služby kolektoru**: Služba Azure Migrate Collector by měl běžet na virtuálním počítači Kolektoru.
    - Tato služba je spuštěna automaticky při spuštění počítače.
    - Pokud služba není spuštěná, spusťte ji pomocí ovládacího panelu.
    - Služba Collector připojí k serveru vCenter, shromažďuje data metadata a výkonu virtuálních počítačů a odesílá je do služby Azure Migrate.
- **Zkontrolovat VMware PowerCLI 6.5 je nainstalovaný**: musí být nainstalovaný modul VMware PowerCLI 6.5 Powershellu na virtuálním počítači Kolektoru, aby mohl komunikovat s vCenter Server.
    - Kolektor může získat přístup k adresám URL požadovaným pro instalaci modulu, je instalace automaticky během nasazení Kolektoru.
    - Pokud kolekce nelze nainstalovat modul během nasazení, je nutné [nainstalujte ho ručně](#install-vwware-powercli-module-manually).
- **Zkontrolujte připojení k serveru vCenter**: The kolekce musí být schopen systému vCenter Server a dotazy na virtuální počítače, jejich metadata a čítače výkonu. [Ověřte požadavky](#connect-to-vcenter-server) pro připojení.


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




### <a name="connect-to-urls"></a>Připojení k adresám URL

Kontrola připojení se ověří pomocí připojení k seznamu adres URL.

**Adresa URL** | **Podrobnosti**  | **Kontrola požadovaných součástí**
--- | --- | ---
*.portal.azure.com | Zkontroluje připojení pomocí služby Azure a synchronizaci času. | Přístup k je vyžadována adresa URL.<br/><br/> Kontrola předpokladů selže, pokud neexistuje žádná připojení.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *. powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Použít ke stažení modulu PowerShell vCenter PowerCLI. | Přístup k adresám URL, které jsou volitelné.<br/><br/> K selhání kontroly požadavků.<br/><br/> Instalace modulu automatické na virtuálním počítači Kolektoru se nezdaří. Bude nutné ručně nainstalovat modul.


### <a name="install-vmware-powercli-module-manually"></a>Ruční instalace VMware PowerCLI modulu

1. Nainstalovat s použitím modulu [tyto kroky](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Tyto kroky popisují online i offline instalace.
2. Pokud virtuální počítač Kolektoru je offline a nainstalovat modul na jiném počítači s přístupem k Internetu, musíte zkopírovat soubory VMware.* z tohoto počítače k virtuálnímu počítači Kolektoru.
3. Po dokončení instalace můžete restartovat požadované součásti kontroly potvrďte, že je nainstalovaný PowerCLI.

### <a name="connect-to-vcenter-server"></a>Připojit k vCenter Serveru

Kolektor se připojí k serveru vCenter a dotazy na metadata virtuálního počítače a čítače výkonu. Zde je, co potřebujete pro připojení.

- Jsou podporovány pouze vCenter Server verze 5.5, 6.0 a 6.5.
- Potřebujete účet jen pro čtení s oprávnění uvedená níže pro zjišťování. Zjišťování je přístupný pouze datacentra, které jsou přístupné pomocí účtu.
- Ve výchozím nastavení připojení k serveru vCenter s plně kvalifikovaný název domény nebo IP adresu. Pokud systém vCenter Server naslouchá na jiném portu, k němu připojíte pomocí formuláře *IPAddress:Port_Number* nebo *FQDN:Port_Number*.
- Ke shromažďování dat výkonu úložiště a sítě, nastavení Statistika pro vCenter Server musí být nastavené na úroveň 3.
- Pokud úroveň je nižší než tři, Princip zjišťování, ale data o výkonu nebudou shromažďovány. Mohou být shromažďovány čítače, ale druhý se nastaví na hodnotu nula.
- Pokud nejsou shromažďována data o výkonu úložiště a sítě, jsou doporučení pro interní hodnocení velikost dat na základě výkonu procesoru a paměti a na konfigurační data pro disk a síťové adaptéry.
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


## <a name="discovery-methods"></a>Metody zjišťování

Existují dvě metody, které zařízení Kolektoru lze použít pro zjišťování, jednorázově nebo průběžná zjišťování.


### <a name="one-time-discovery"></a>Jednorázové zjišťování

Komunikuje se jednorázově do systému vCenter Server ke shromažďování metadat virtuální počítače kolektor. Pomocí této metody:

- Zařízení není nepřetržitě připojeny k projektu Azure Migrate.
- Změny v místním prostředí se neprojeví v Azure Migrate po dokončení zjišťování. Tak, aby odrážela všechny změny, budete muset znovu zjišťovat stejné prostředí, ve stejném projektu.
- Tato metoda zjišťování budete muset nastavení statistiky v systému vCenter Server na úrovni 3.
- Po nastavení úrovně na tři, to trvá až jeden den generovat čítače výkonu. Proto doporučujeme, abyste spustili zjišťování po jeden den.
- Při shromažďování dat výkonu pro virtuální počítač, na zařízení se spoléhá na výkon historických dat uložených v systému vCenter Server. Shromažďuje historie výkonu za poslední měsíc.
- Azure Migrate shromažďuje průměrnou čítače (spíše než čítače ve špičce) pro jednotlivé metriky, což může vést ke snížení velikosti.

### <a name="continuous-discovery"></a>Průběžné zjišťování

Zařízení Kolektoru je trvalým připojením k projektu Azure Migrate a průběžně shromažďuje údaje o výkonu virtuálních počítačů.

- Kolektor průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase každých 20 sekund.
- Tento model nejsou závislé na nastavení statistiky vCenter Server ke shromažďování dat výkonu.
- Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod každých 15 minut.
- Tato data vytváří bod zařízení vybere nejvyšší hodnotu z 20 sekund vzorků a odešle ji do Azure.
- Můžete zastavit průběžné profilování v kdykoli z kolekce.

Mějte na paměti, že zařízení průběžně pouze shromažďuje údaje o výkonu, nezjistí změny konfigurace v místním prostředí (tj. Přidání virtuálního počítače, odstranění, přidání disku atd.). Pokud dojde ke změně konfigurace v místním prostředí, můžete provést následující tak, aby odrážely změny v portálu:

1. Přidání položek (virtuálních počítačů, disků, jader atd.): pro provedení těchto změn na webu Azure Portal, můžete zastavit zjišťování ze zařízení a znovu spustit. Tím se zajistí, že změny jsou aktualizovány v projektu Azure Migrate.

2. Odstranění virtuálních počítačů: z důvodu způsob je navržena na zařízení, odstranění virtuální počítače se projeví i v případě zastavení a spuštění zjišťování. Je to proto, že data z dalších zjišťování se připojí k starší zjišťování a nebyly přepsány. V takovém případě můžete jednoduše ignorovat virtuálního počítače na portálu ho odebrat ze skupiny a přepočítání posouzení.

> [!NOTE]
> Průběžná zjišťování funkce je ve verzi preview. Nastavení statistiky vCenter Server není nastaven na úroveň 3, doporučujeme použít tuto metodu.


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

### <a name="collected-metadata"></a>Shromáždila se metadata

Zařízení kolektoru zjistí následující statické metadata pro virtuální počítače:

- Zobrazovaný název virtuálního počítače (v systému vCenter Server)
- Cesta inventáře Virtuálního počítače (hostitel/složku v systému vCenter Server)
- IP adresa
- Adresa MAC
- Operační systém
- Počet jader, disků, síťových adaptérů
- Velikost paměti, velikosti disků
- Čítače výkonu virtuálních počítačů, disku a sítě.

#### <a name="performance-counters"></a>Čítače výkonu

- **Jednorázově**: když čítače se shromažďují, aby jednorázově, pamatujte na Tyhle věci:

    - Může trvat až 15 minut pro shromažďování a odesílání metadat konfigurace do projektu.
    - Po shromáždění dat konfigurace, může trvat až hodinu, údaje o výkonu k dispozici na portálu.
    - Po metadat je k dispozici na portálu, zobrazí se seznam virtuálních počítačů a můžete začít vytvářet skupiny pro posouzení.
- **Průběžná zjišťování**: průběžné zjišťování, pamatujte na Tyhle věci:
    - Konfigurační data pro virtuální počítač je k dispozici hodinu po spuštění zjišťování
    - Údaje o výkonu se spustí poté jsou dostupné po 2 hodinách.
    - Po spuštění zjišťování pro zařízení, která má být profilována prostředí před vytvořením posouzení alespoň den počkejte.

**Čítač** | **Úroveň** | **Úroveň podle zařízení** | **Dopad na posouzení**
--- | --- | --- | ---
CPU.Usage.average | 1 | Není k dispozici | Doporučené velikosti virtuálních počítačů a náklady  
mem.usage.average | 1 | Není k dispozici | Doporučené velikosti virtuálních počítačů a náklady  
virtualDisk.read.average | 2 | 2 | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.write.average | 2 | 2  | Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberReadAveraged.average | 1 | 3 |  Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
virtualDisk.numberWriteAveraged.average | 1 | 3 |   Vypočítá velikost disku, náklady na úložiště, velikost virtuálního počítače
net.received.average | 2 | 3 |  Vypočítá velikost virtuálního počítače                          |
net.transmitted.average | 2 | 3 | Vypočítá velikost virtuálního počítače     

## <a name="next-steps"></a>Další postup

[Nastavení posouzení pro místní virtuální počítače VMware](tutorial-assessment-vmware.md)
