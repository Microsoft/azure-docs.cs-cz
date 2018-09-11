---
title: Zařízení kolektoru ve službě Azure Migrate | Dokumentace Microsoftu
description: Poskytuje přehled zařízení Kolektoru a jeho konfiguraci.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: dae6cc9a55049e2b44291eb105288b33a1db9e7b
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325528"
---
# <a name="collector-appliance"></a>Zařízení kolektoru

[Azure Migrate](migrate-overview.md) posuzuje místní úlohy pro migraci do Azure. Tento článek obsahuje informace o tom, jak pomocí zařízení Kolektoru.

## <a name="overview"></a>Přehled

Azure Migrate Collector je zjednodušené zařízení, který slouží ke zjišťování vCenter v místním prostředí. Toto zařízení zjistí místních počítačů VMware a odešle jejich metadata do služby Azure Migrate.

Zařízení Kolektoru je OVF, který si můžete stáhnout z projektu Azure Migrate. Vytvoření instance virtuálního počítače VMware s 4 jádra, 8 GB paměti RAM a jeden disk 80 GB. Operační systém zařízení je Windows Server 2012 R2 (64bitová verze).

Pomocí následujících kroků můžete vytvořit kolektor zde - [vytvoření virtuálního počítače kolektor](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="discovery-methods"></a>Metody zjišťování

Existují dvě metody, ve kterých zjistíte v místním prostředí:

a. **Jednorázově:** kolekcí tohoto modelu komunikuje s vCenter Server ke shromažďování metadat virtuální počítače. Pro shromažďování dat o výkonu virtuálních počítačů závisí na výkonu historických datech uložených v systému vCenter Server a shromažďuje historie výkonu za poslední měsíc. V tomto modelu Azure Migrate shromažďuje čítač Průměrná (oproti čítače ve špičce) pro jednotlivé metriky, [Další informace] (https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected) o čítače výkonu shromážděné službou Azure Migrate. Protože jde o jednorázovou zjišťování, zařízení v tomto případě není nepřetržitě připojeny k projektu. Proto se neprojeví změny v místním prostředí ve službě Azure Migrate, po dokončení zjišťování. Pokud chcete změny tak, aby odrážely, budete muset provést opakované zjišťování stejného prostředí do stejného projektu.

b. **Průběžná zjišťování:** zařízení kolektoru pro tento model je trvalým připojením k projektu Azure Migrate. Průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase na každých 20 sekund. Zařízení potom zobrazí – až 20 sekund ukázky a vytvoří jeden datový bod pro každých 15 minut výběrem maximální hodnotu, která se posílají do Azure. Tento model není závislý na nastavení statistiky systému vCenter Server pro shromažďování dat o výkonu. Můžete zastavit průběžné kdykoli profilace ze zařízení.

> [!NOTE]
> Průběžná zjišťování funkce je ve verzi preview.

## <a name="collector-communication-diagram"></a>Diagram komunikace kolekcí

![Diagram komunikace kolekcí](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Komponenta      | Komunikace s   | Požadovaný port                            | Důvod                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Kolektor      | Služba Azure Migrate | TCP 443                                  | Kolekce by měl být schopen komunikovat se službou service přes port SSL 443. |
| Kolektor      | vCenter Server        | Výchozí port 443                             | Kolekce by měl být schopen komunikovat se serverem vCenter. Ve výchozím nastavení připojení k vCenter na 443. Pokud server vCenter naslouchá na jiném portu, tento port by měl být k dispozici jako odchozí port na kolektoru |
| Kolektor      | Protokol RDP|   | TCP 3389 | Pro, abyste mohli pro protokol RDP do počítače Kolektoru |

## <a name="collector-pre-requisites"></a>Požadavky na kolekce

Kolekce je potřeba předat několik nepotlačí kontroly, aby se může připojit ke službě Azure Migrate a nahrát zjištěná data. Tento článek vypadá na jednotlivé požadavky a rozumí, proč se vyžaduje.

### <a name="internet-connectivity"></a>Připojení k internetu

Zařízení kolektoru musí být připojené k Internetu, abychom mohli poslat informace o zjištěných počítačů. Na počítači můžete připojit k Internetu v jednom z následujících způsobů.

1. Můžete nakonfigurovat kolekcí s přímým přístupem připojený k Internetu.
2. Můžete nakonfigurovat kolekce pro připojení prostřednictvím serveru proxy.
    * Pokud proxy server vyžaduje ověření, můžete zadat uživatelské jméno a heslo v nastavení připojení.
    * IP adresu nebo plně kvalifikovaný název domény Proxy serveru by měl být ve tvaru http://IPaddress nebo http://FQDN. Se podporuje jen proxy protokolu http.

> [!NOTE]
> Servery proxy server založený na protokolu HTTPS nepodporuje kolektoru.

#### <a name="internet-connectivity-with-intercepting-proxy"></a>Připojení k Internetu pomocí proxy zachycení

Pokud proxy server, který používáte pro připojení k Internetu je prověřuje zachycovací proxy server, musíte importovat certifikát proxy serveru do vašeho virtuálního počítače kolektoru. Toto jsou pokyny, jak můžete importovat certifikát do virtuálního počítače kolektoru.

1. Přejděte ve virtuálním počítači kolektoru **nabídky Start** a vyberte a otevřete **spravovat certifikáty počítače**.
2. V nástroji pro certifikáty, v levém podokně v části **certifikáty - místní počítač**, Najít **Důvěryhodní vydavatelé**. V části **Důvěryhodní vydavatelé**, klikněte na tlačítko **certifikáty** zobrazíte seznam certifikátů v podokně na pravé straně.

    ![Nástroje pro certifikáty](./media/concepts-intercepting-proxy/certificates-tool.png)

3. Zkopírujte certifikát proxy serveru na virtuálním počítači kolektoru. Bude pravděpodobně nutné kontaktovat správce týmu sítě ve vaší organizaci k získání tohoto certifikátu.
4. Dvakrát klikněte na certifikát, který chcete otevřít. Klikněte na tlačítko **nainstalovat certifikát**. Tím přejdete na Průvodce importem certifikátu.
5. V Průvodci importem certifikátu pro Store umístění, zvolte **místního počítače**. **Klikněte na tlačítko Další**.

    ![Umístění úložiště certifikátů](./media/concepts-intercepting-proxy/certificate-store-location.png)

6. Zvolte možnost **všechny certifikáty umístit v následujícím úložišti**. Klikněte na tlačítko **Procházet** a vyberte **Důvěryhodní vydavatelé** ze seznamu certifikátů, které vytvoříte. Klikněte na **Další**.

    ![Úložiště certifikátů](./media/concepts-intercepting-proxy/certificate-store.png)

7. Klikněte na **Dokončit**. To certifikát budete importovat.
8. Volitelně můžete ověřit, že je certifikát importován tak, že otevřete nástroj certifikáty jako v kroku 1 a 2 výše.
9. V aplikaci Azure Migrate collector ověřte, že kontrolu požadovaných součástí připojení k Internetu je úspěšné.


#### <a name="whitelisting-urls-for-internet-connection"></a>Přidávání na seznam povolených adres URL pro připojení k Internetu

Předběžné kontroly je úspěšný, pokud kolektor může připojit k Internetu prostřednictvím zadané nastavení. Kontrola připojení se ověří pomocí připojení k seznamu adres URL, jak je uvedeno v následující tabulce. Pokud používáte jakýkoli proxy server brány firewall na základě adresy URL k řízení odchozího připojení, nezapomeňte do seznamu povolených IP adres, že tyto požadované adresy URL:

**Adresa URL** | **Účel**  
--- | ---
*.portal.azure.com | Vyžaduje se ověřilo připojení ke službě Azure a ověřit synchronizaci času problémy.

Kromě toho kontroly také pokusí o ověření připojení k následujícím adresám URL, ale není selhání kontroly, pokud není k dispozici. Konfigurace seznamu povolených IP adres pro následující adresy URL je volitelný, ale budete muset provést ruční kroky ke zmírnění předběžné kontroly.

**Adresa URL** | **Účel**  | **Co když není seznam povolených**
--- | --- | ---
*.oneget.org:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.
*.windows.net:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.
*.windowsazure.com:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.
*. powershellgallery.com:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.
*.msecnd.net:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.
*.visualstudio.com:443 | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu. | PowerCLI instalace se nezdaří. Ručně nainstalujte modul.

### <a name="time-is-in-sync-with-the-internet-server"></a>Čas je synchronizovaný s internetový server

Kolektor by měl být synchronizovaný s internetovým časovým serverem k zajištění, že ověření požadavků na službu. Adrese portal.azure.com. adresa url by měla být dosažitelný z Kolektoru, tak, aby čas může být ověřen. Pokud počítač není synchronizovaný, budete muset změnit čas na virtuálním počítači Kolektoru tak, aby odpovídaly aktuálního času, následujícím způsobem:

1. Otevřete příkazový řádek správce ve virtuálním počítači.
1. Ke kontrole časové pásmo, spusťte w32tm /tz.
1. Chcete-li synchronizovat čas, spusťte w32tm/resync.

### <a name="collector-service-should-be-running"></a>By měla být spuštěná služba kolektoru

Na počítači musí běžet služba Azure Migrate Collector. Tato služba je spuštěna automaticky při spuštění počítače. Pokud služba není spuštěná, můžete spustit *Azure Migrate Collector* service ovládacích panelech. Služba Collector je zodpovědný připojit k serveru vCenter, sběr strojových dat metadata a výkonu a odesílat do služby.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

Modul powershell VMware PowerCLI musí být nainstalovaný tak, aby kolektor může komunikovat se serverem vCenter a dotazy na podrobnosti o počítači a jejich data o výkonu. Modul prostředí powershell je automaticky stažen a nainstalován jako součást předběžné kontroly. Automatické stahování vyžaduje několik seznamu povolených adres URL, jinak je nutné zadat buď přidáním k nim přístup, nebo ruční instalace modulu.

Instalace modulu ručně pomocí následujících kroků:

1. K instalaci PowerCli v kolekci bez připojení k Internetu, postupujte podle kroků uvedených v [tento odkaz](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Po instalaci modulu prostředí PowerShell v jiném počítači, který má přístup k Internetu, zkopírujte soubory VMware.* z tohoto počítače do počítače kolektor.
3. Restartujte kontroly splnění podmínek a potvrďte, že je nainstalovaný PowerCLI.

## <a name="connecting-to-vcenter-server"></a>Připojení k serveru vCenter

Kolektor by měl připojit k vCenter serveru a mít možnost provádět dotazy pro virtuální počítače, jejich metadat a jejich čítači výkonu. Tato data se používá v projektu pro výpočet posouzení.

1. Pro připojení k serveru vCenter, účet jen pro čtení s oprávněními, jak je uvedeno v následující tabulce je možné spustit zjišťování.

    |Úkol  |Požadované role a účet  |Oprávnění  |
    |---------|---------|---------|
    |Zjišťování na základě zařízení kolektoru    | Budete potřebovat alespoň uživatel jen pro čtení        |Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only         |

2. Zjišťování je přístupný pouze datacentra, které jsou přístupné pro zadaný účet vCenter.
3. Je třeba zadat plně kvalifikovaný název domény nebo IP adresu pro připojení k serveru vCenter server vCenter. Ve výchozím nastavení se připojí přes port 443. Pokud jste nakonfigurovali server vCenter pro naslouchání na jiné číslo portu, můžete je zadat jako součást adresu serveru ve formuláři IPAddress:Port_Number nebo FQDN:Port_Number.
4. Nastavení statistiky systému vCenter Server by měla nastavit na úroveň 3, před zahájením nasazení. Pokud úroveň je nižší než 3, se dokončí zjišťování, ale nebudou shromažďovat údaje o výkonu pro úložiště a sítě. Doporučení posouzení velikostí v tomto případě bude vycházet údaje o výkonu pro využití procesoru a paměti a pouze konfigurační data pro disk a síťové adaptéry. [Přečtěte si další](./concepts-collector.md) na jaká data se shromažďují a jak to ovlivní posouzení.
5. Kolekce by měly mít síť dohled k serveru vCenter.

> [!NOTE]
> Pouze verze serveru vCenter Server 5.5, 6.0 a 6.5 se oficiálně podporuje.

> [!IMPORTANT]
> Doporučujeme nastavit nejvyšší běžné úroveň (3) pro úroveň statistiky tak, aby všechny čítače se shromažďují správně. Je nutné nastavit na nižší úrovni vCenter, může se zbytkem nastavena na hodnotu 0 úplně, shromažďují pouze několik čítače. Posouzení pak může zobrazit, neúplná data.

### <a name="selecting-the-scope-for-discovery"></a>Výběr rozsahu zjišťování

Po připojení k serveru vCenter, můžete vybrat obor zjišťování. Že se vybere rozsah zjistí všechny virtuální počítače z cesty zadané vCenter inventáře.

1. Rozsah může být datové centrum, složku nebo hostiteli ESXi.
2. Můžete vybrat pouze jeden obor. Pokud chcete vybrat další virtuální počítače, můžete dokončit jednu zjišťování a restartujte proces zjišťování pomocí nového oboru.
3. Můžete vybrat jenom obor, který má *menší než 1 500 virtuálních počítačů*.

## <a name="specify-migration-project"></a>Zadejte projekt migrace

Místní server vCenter je připojený, a je zadán obor, můžete nyní zadat podrobnosti projektu migrace, které je třeba použít pro zjišťování a posouzení. Zadejte ID a klíč projektu a připojení.

## <a name="start-discovery-and-view-collection-progress"></a>Spustit zjišťování a zobrazit průběh shromažďování

Jakmile se spustí zjišťování zjištění serveru vCenter virtuálních počítačů a jejich metadat a výkonu data se odesílají na server. Stav průběhu poskytuje také následující ID:

1. ID collectoru: Jedinečný Identifikátor, který je přiřazen do svého počítače kolektor. Toto ID se nezmění přes různá zjišťování pro daný počítač. Toto ID v případě selhání můžete použít při hlášení problému na Microsoft Support.
2. ID relace: Jedinečné ID pro spuštěné úlohy kolekce. Po dokončení úlohy zjišťování mohou odkazovat na stejné ID relace na portálu. Toto ID se změní pro každou kolekci úloh. V případě selhání můžete toto ID nahlásit společnosti Microsoft Support.

### <a name="what-data-is-collected"></a>Jaká data se shromažďují?

Zařízení kolektoru zjistí následující statické metadata o vybrané virtuální počítače.

1. Název zobrazení virtuálních počítačů (na serveru vCenter)
2. Cesta inventáře Virtuálního počítače (hostitele nebo složku v systému vCenter)
3. IP adresa
4. Adresa MAC
5. Operační systém
5. Počet jader, disků, síťových adaptérů
6. Velikost paměti, velikosti disků
7. A z čítačů výkonu virtuálních počítačů, disku a sítě, jak je uvedeno v následující tabulce.

Následující tabulka uvádí pro model na čas zjišťování, čítače přesné údaje o výkonu, které se budou shromažďovat a také výsledky posouzení, které jsou by to vliv na konkrétní čítač nejsou shromažďována.

Průběžná zjišťování, čítače se shromažďují v reálném čase (intervalu 20 sekund), takže není žádná závislost na úroveň statistiky vCenter. Zařízení potom zobrazí – až 20 sekund ukázky vytvořte jeden datový bod pro každých 15 minut tak, že vyberete hodnotu ve špičce ukázky 20 sekund a odesílá je do Azure.

|Čítač                                  |Úroveň    |Úroveň podle zařízení  |Hodnocení dopadu                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |Není k dispozici                |Doporučené velikosti virtuálních počítačů a náklady                    |
|mem.usage.average                        | 1       |Není k dispozici                |Doporučené velikosti virtuálních počítačů a náklady                    |
|virtualDisk.read.average                 | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.write.average                | 2       |2                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Velikost disku, náklady na úložiště a velikost virtuálního počítače         |
|net.received.average                     | 2       |3                 |Cena za virtuální počítače velikosti a sítě                        |
|net.transmitted.average                  | 2       |3                 |Cena za virtuální počítače velikosti a sítě                        |

> [!WARNING]
> Jednorázové zjišťování Pokud jste právě nastavili vyšší úroveň statistiky, ji bude trvat až jeden den generovat čítače výkonu. Proto doporučujeme spustit zjišťování po jednom dni. Průběžná zjišťování modelu počkejte aspoň jeden den po spuštění zjišťování pro zařízení profilu prostředí a pak vytvořit posouzení.

### <a name="time-required-to-complete-the-collection"></a>Čas potřebný k dokončení kolekce

**Jednorázově**

V tomto modelu kolektoru shromažďuje historie konfigurace a výkonu virtuálních počítačů ze systému vCenter Server a odesílá je do projektu. Zařízení v tomto případě není nepřetržitě připojeny k projektu. Podle počtu virtuálních počítačů ve vybraném oboru, trvá až 15 minut k odeslání konfigurace metadat do projektu. Po konfiguraci metadata jsou k dispozici na portálu, můžete zobrazit seznam počítačů na portálu a začít vytvářet skupiny. Shromážděná data konfigurace, může trvat až jednu hodinu, než bude k dispozici na portálu, data o výkonu podle počtu virtuálních počítačů ve vybraném oboru.

**Průběžná zjišťování**

V tomto modelu konfigurační data z místních virtuálních počítačů je k dispozici po spuštění 1 hodinu aktivuje data zjišťování a výkonu, poté jsou dostupné po 2 hodinách. Protože se jedná průběžné modelu, kolektor průběžně odesílá data o výkonu do projektu Azure Migrate.

## <a name="locking-down-the-collector-appliance"></a>Uzamčení zařízení kolektoru
Doporučujeme spustit průběžné aktualizace Windows na zařízení kolektoru. Pokud kolekce není aktualizován po dobu 60 dnů, se spustí automatické vypínání na počítači kolektoru. Pokud je funkce zjišťování spuštěna, počítače nebudou vypnout, i v případě, že je po jeho 60 dnů. Příspěvek zjišťování úlohy dokončí, počítač se vypne. Pokud používáte více než 45 dnů kolektoru, doporučujeme počítač aktualizoval na celou dobu spuštění aktualizace Windows.

Doporučujeme následující kroky a zabezpečit vaše zařízení
1. Sdílet nebo s neoprávněnými osobami někam nezaložili hesla správce.
2. Vypněte zařízení, když se nepoužívá.
3. Umístíte zařízení do zabezpečené síti.
4. Po dokončení migrace práce, odstraňte instanci zařízení. Nezapomeňte si také odstranit disk, na kterém soubory (Vmdk), jako disky může mít přihlašovací údaje k vCenter do mezipaměti na ně.

## <a name="how-to-upgrade-collector"></a>Postup upgradu kolekce

Kolekce můžete upgradovat na nejnovější verzi bez stáhnout soubor OVA jednou znovu.

1. Stáhněte si nejnovější [balíček s upgradem](https://aka.ms/migrate/col/upgrade_9_14) (verze 1.0.9.14).
2. Tak, aby byl zabezpečený stažené oprav hotfix, otevřete okno příkazového řádku pro správce a spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor ZIP. Vygenerovaná hodnota hash by měla odpovídat symbolem hash uvedených proti konkrétní verzi:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (příklady použití C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Soubor zip zkopírujte do Azure migrovat virtuální počítač kolektoru (zařízení kolektoru).
4. Klikněte pravým tlačítkem na soubor zip a vyberte Extrahovat vše.
5. Klikněte pravým tlačítkem na Setup.ps1 a vyberte spustit s prostředím PowerShell a postupujte podle pokynů na obrazovce instalace aktualizace.

### <a name="list-of-updates"></a>Seznam aktualizací

#### <a name="upgrade-to-version-10914"></a>Upgrade na verzi 1.0.9.14

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="upgrade-to-version-10913"></a>Upgrade na verzi 1.0.9.13

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Upgrade na verzi 1.0.9.11

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Upgradovat na verze 1.0.9.7:

Hodnota hash hodnoty pro upgrade [balíček 1.0.9.7:](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Další postup

[Nastavení posouzení pro místní virtuální počítače VMware](tutorial-assessment-vmware.md)
